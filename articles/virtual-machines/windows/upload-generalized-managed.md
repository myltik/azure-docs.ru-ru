---
title: Создание управляемой виртуальной машины Azure на основе универсальных локальных дисков VHD | Документация Майкрософт
description: Отправка универсального диска VHD в Azure и создание виртуальных машин с его помощью в модели развертывания Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: cynthn
ms.openlocfilehash: 919b1bef1f1337ad188e26476da8c5a5ef029224
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Отправка универсального диска VHD и создание виртуальных машин с его помощью в Azure

В этой статье описывается использование PowerShell для отправки диска VHD универсальной виртуальной машины в Azure, создание образа на основе диска VHD и создание виртуальной машины на основе этого образа. Вы можете отправить диск VHD, экспортированный из локального инструмента виртуализации или из другого облака. При использовании [Управляемых дисков](managed-disks-overview.md) для новой виртуальной машины упрощается управление виртуальными машинами и обеспечивается более высокий уровень доступности виртуальной машины, размещенной в группе доступности. 

Если необходимо использовать пример скрипта, см. статью [Sample script to upload a VHD to Azure and create a new VM](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) (Пример скрипта для отправки VHD в Azure и создания виртуальной машины).

## <a name="before-you-begin"></a>Перед началом работы

- Прежде чем передавать в Azure виртуальный жесткий диск любого типа, следует выполнить инструкции из статьи [Подготовка диска VHD или VHDX для Windows к отправке в Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Прежде чем начать миграцию на [Управляемые диски](managed-disks-overview.md), внимательно изучите [этот раздел](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).
- Для работы с этой статьей требуется модуль AzureRM 5.6 или более поздней версии. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM.Compute`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="generalize-the-source-vm-using-sysprep"></a>Подготовка исходной виртуальной машины к использованию с помощью Sysprep

Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. Сведения о Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Дополнительные сведения см. в статье [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка серверных ролей в Sysprep).

> [!IMPORTANT]
> Если вы еще не отправили виртуальный жесткий диск в Azure и собираетесь запустить Sysprep первый раз, прежде чем это делать, [подготовьте виртуальную машину](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Выполните вход на виртуальную машину Windows.
2. Откройте окно командной строки с правами администратора. Измените каталог на **%windir%\system32\sysprep** и запустите файл `sysprep.exe`.
3. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.
4. В разделе **Параметры завершения работы** выберите **Завершение работы**.
5. Последовательно выберите **ОК**.
   
    ![Запуск Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. После выполнения всех необходимых действий Sysprep завершает работу виртуальной машины. Не перезапускайте виртуальную машину.


## <a name="get-the-storage-account"></a>Получение учетной записи хранения

Вам необходима учетная запись хранения Azure для хранения переданного образа виртуальной машины. Можно использовать существующую учетную запись хранения или создать новую. 

Если для создания управляемого диска для виртуальной машины будет использоваться виртуальный жесткий диск, то расположение учетной записи хранения должно совпадать с расположением, в котором будет создаваться виртуальная машина.

Чтобы отобразить список доступных учетных записей хранения, введите:

```azurepowershell
Get-AzureRmStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Передача виртуального жесткого диска в учетную запись хранения

Используйте командлет [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx), чтобы передать VHD в контейнер в учетной записи хранения. В этом примере файл *myVHD.vhd* передается из расположения *C:\Users\Public\Documents\Virtual hard disks\"* в учетную запись хранения *mystorageaccount*, входящую в группу ресурсов *myResourceGroup*. Файл будет помещен в контейнер с именем *mycontainer*; новое имя файла — *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


В случае успешного выполнения отобразится ответ, который выглядит следующим образом.

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

В зависимости от сетевого подключения и размера VHD-файла выполнение этой команды может занять некоторое время.

### <a name="other-options-for-uploading-a-vhd"></a>Другие возможности загрузки виртуального жесткого диска
 
Вы также можете передать виртуальный жесткий диск в учетную запись хранения с помощью одного из следующих инструментов:

- [AzCopy](http://aka.ms/downloadazcopy)
- [API копирования BLOB-объекта хранилища Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Обозреватель хранилищ Azure для передачи больших двоичных объектов](https://azurestorageexplorer.codeplex.com/)
- [Справочник по API REST служб хранилища импорта и экспорта](https://msdn.microsoft.com/library/dn529096.aspx)
-   Мы рекомендуем использовать службу импорта и экспорта, если предполагаемое время передачи больше 7 дней. Чтобы оценить предполагаемое время передачи на основе размера данных и средства передачи, используйте компонент [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html). 
    Импорт и экспорт можно использовать для копирования в учетную запись хранения класса Standard. Необходимо осуществить копирование из учетной записи хранения Standard в учетную запись хранения Premium с помощью схожего с AzCopy средства.

> [!IMPORTANT]
> Если для отправки виртуального жесткого диска в Azure вы используете службу AzCopy, перед запуском сценария убедитесь, что задано значение [/BlobType:page](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append). Если в качестве места назначения выступает большой двоичный объект и этот параметр не указан, то по умолчанию AzCopy создает блочный BLOB-объект.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Создание управляемого образа на основе переданного виртуального жесткого диска 

Создайте управляемый образ с помощью универсального виртуального жесткого диска ОС. Подставьте собственные значения.


Сначала задайте некоторые параметры.

```powershell
$location = "East US" 
$imageName = "myImage"
```

Создайте образ с помощью универсального виртуального жесткого диска ОС.

```powershell
$imageConfig = New-AzureRmImageConfig `
   -Location $location
$imageConfig = Set-AzureRmImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzureRmImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Создание виртуальной машины

Теперь, когда образ готов, из него можно создать одну или несколько виртуальных машин. В этом примере создается виртуальная машина *myVM* из образа *myImage* в группе ресурсов *myResourceGroup*.


```powershell
New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Дополнительная информация

Чтобы войти на новую виртуальную машину, найдите ее на [портале](https://portal.azure.com), нажмите кнопку **Подключение**и откройте RDP-файл "Удаленный рабочий стол". Для входа на новую виртуальную машину используйте учетные данные для входа в новую виртуальную машину. Дополнительные сведения см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

