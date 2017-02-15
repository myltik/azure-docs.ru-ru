---
title: "Передача виртуального жесткого диска Windows для Resource Manager | Документация Майкрософт"
description: "Узнайте, как с помощью модели развертывания Resource Manager передать виртуальный жесткий диск виртуальной машины Windows из локальной среды в Azure. Виртуальный жесткий диск (VHD) можно передать с обобщенной или специализированной виртуальной машины."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 192c8e5a-3411-48fe-9fc3-526e0296cf4c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 5aa6b2149170ef04af0ebde957feda5630c5d5eb


---
# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Передача виртуального жесткого диска Windows с локальной виртуальной машины в Azure
В этой статье показано, как создать и передать виртуальный жесткий диск (VHD) Windows, который затем можно использовать для создания виртуальной машины Azure. Виртуальный жесткий диск можно передать с универсальной или специализированной виртуальной машины. 

Дополнительные сведения о дисках и виртуальных жестких дисках в Azure см. в статье [О дисках и виртуальных жестких дисках для виртуальных машин Azure](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prepare-the-vm"></a>Подготовка виртуальной машины
В Azure можно передавать как универсальные, так и специализированные виртуальные жесткие диски. Для обоих типов требуется предварительная подготовка виртуальной машины.

* **Универсальный виртуальный жесткий диск**. С универсального VHD с помощью Sysprep удалены все сведения вашей личной учетной записи. Если вы планируете использовать виртуальный жесткий диск в качестве образа для создания виртуальных машин, то выполните следующие действия:
  
  * [Подготовьте виртуальный жесткий диск Windows к передаче в Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
  * [Подготовьте виртуальную машину к использованию с помощью Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* **Специализированный виртуальный жесткий диск**. На специализированном VHD сохраняются учетные записи пользователей, приложения и другие данные о состоянии исходной виртуальной машины. Если вы планируете использовать виртуальный жесткий диск "как есть" для создания виртуальной машины, то необходимо выполнить следующие действия: 
  
  * [Подготовьте виртуальный жесткий диск Windows к передаче в Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Не выполняйте** подготовку виртуальной машины к использованию с помощью Sysprep.
  * Удалите все гостевые инструменты и агенты виртуализации, которые установлены на виртуальной машине (т. е. инструменты VMware).
  * Убедитесь, что виртуальная машина настроена на получение IP-адреса и параметров DNS через DHCP. Таким образом, сервер будет получать IP-адрес в виртуальной сети при запуске. 

## <a name="log-in-to-azure"></a>Вход в Azure
Если вы еще не установили PowerShell версии 1.4 или выше, то см. статью [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Установка и настройка Azure PowerShell).

1. Откройте Azure PowerShell и войдите в свою учетную запись Azure. Откроется всплывающее окно для ввода данных учетной записи Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Получите идентификаторы доступных вам подписок.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Укажите соответствующую подписку с помощью идентификатора. Замените `<subscriptionID>` идентификатором правильной подписки.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Получение учетной записи хранения
Вам необходима учетная запись хранения Azure для хранения переданного образа виртуальной машины. Можно использовать существующую учетную запись хранения или создать новую. 

Чтобы отобразить список доступных учетных записей хранения, введите:

```powershell
Get-AzureRmStorageAccount
```

Если вы хотите использовать существующую учетную запись хранения, то перейдите к разделу [Отправка образа виртуальной машины](#upload-the-vm-vhd-to-your-storage-account).

Если требуется создать учетную запись хранения, то выполните описанные ниже действия.

1. Необходимо указать имя группы ресурсов, в которой будет создана учетная запись хранения. Чтобы найти все группы ресурсов, существующие в вашей подписке, введите:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Чтобы создать группу ресурсов с именем **myResourceGroup** в регионе **Западная часть США**, введите:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. С помощью командлета [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) создайте в этой группе ресурсов учетную запись хранения с именем **mystorageaccount**:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    Допустимые значения -SkuName перечислены ниже.
   
   * **Standard_LRS** — локально избыточное хранилище. 
   * **Standard_ZRS** — хранилище, избыточное в пределах зоны.
   * **Standard_GRS** — геоизбыточное хранилище. 
   * **Standard_RAGRS** — геоизбыточное хранилище с доступом только для чтения. 
   * **Premium_LRS** — локально избыточное хранилище уровня "Премиум". 

## <a name="upload-the-vhd-to-your-storage-account"></a>Передача виртуального жесткого диска в учетную запись хранения
Используйте командлет [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx), чтобы передать образ в контейнер в учетной записи хранения. В этом примере файл **myVHD.vhd** передается из расположения `"C:\Users\Public\Documents\Virtual hard disks\"` в учетную запись хранения **mystorageaccount**, входящую в группу ресурсов **myResourceGroup**. Файл будет помещен в контейнер с именем **mycontainer**; новое имя файла — **myUploadedVHD.vhd**.

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

## <a name="next-steps"></a>Дальнейшие действия
* [Создание в Azure виртуальной машины на основе универсального виртуального жесткого диска](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Создание в Azure виртуальной машины на основе специализированного виртуального жесткого диска](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), который подключается как диск ОС в процессе создания виртуальной машины.




<!--HONumber=Dec16_HO2-->


