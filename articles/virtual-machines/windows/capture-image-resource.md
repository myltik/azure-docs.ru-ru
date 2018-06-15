---
title: Создание управляемого образа в Azure | Документация Майкрософт
description: Создание управляемого образа универсальной виртуальной машины или виртуального жесткого диска в Azure. Образы можно использовать для создания нескольких виртуальных машин, использующих управляемые диски.
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
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 4445787fd559c6d0a6dfc891910cb9a139a6907e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31602575"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Создание управляемого образа универсальной виртуальной машины в Azure

Ресурс управляемого образа можно создать из универсальной виртуальной машины, которая хранится в виде управляемого диска или неуправляемых дисков в учетной записи хранения. Затем образ можно использовать для создания нескольких виртуальных машин. 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Подготовка виртуальной машины Windows к использованию с помощью Sysprep

Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. Сведения о Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Дополнительные сведения см. в статье [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка серверных ролей в Sysprep).

> [!IMPORTANT]
> После выполнения sysprep на виртуальной машине она считается *обобщенной* и ее нельзя перезапускать. Процесс обобщения виртуальной машины необратим. Если необходимо сохранить исходную виртуальную машину в рабочем состоянии, нужно [скопировать виртуальную машину](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) и обобщить ее копию. 
>
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


## <a name="create-a-managed-image-in-the-portal"></a>Создание управляемого образа на портале 

1. Откройте [портал](https://portal.azure.com).
2. В меню слева щелкните "Виртуальные машины" и выберите виртуальную машину из списка.
3. На странице виртуальной машины в верхнем меню щелкните **Запись**.
3. В поле **Имя** введите имя, которое вы хотите использовать для образа.
4. В поле **Группа ресурсов** выберите **Создать** и введите имя или выберите **Использовать существующую** и выберите из раскрывающегося списка используемую группу ресурсов.
5. Если вы хотите удалить исходную виртуальную машину после создания образа, выберите **Automatically delete this virtual machine after creating the image** (Автоматически удалить эту виртуальную машину после создания образа).
6. Закончив, щелкните **Создать**.
16. После создания образа вы увидите его в виде ресурса **Image** в списке ресурсов группы ресурсов.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Создание образа виртуальной машины с помощью PowerShell

Создание образа непосредственно из виртуальной машины гарантирует, что он будет содержать все ее диски, включая диск ОС и диски данных. В этом примере показано, как создать управляемый образ из виртуальной машины,которая использует управляемые диски.


Прежде чем начать, убедитесь, что у вас установлена последняя версия модуля PowerShell AzureRM.Compute. Для работы с этой статьей требуется модуль AzureRM 5.7.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.


> [!NOTE]
> Перед сохранением образа в хранилище, избыточном в пределах зоны, его необходимо создать в регионе, который поддерживает [зоны доступности](../../availability-zones/az-overview.md) и в котором конфигурация образа содержит параметр `-ZoneResilient`.


1. Создайте несколько переменных.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Убедитесь, что виртуальная машина была освобождена.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Теперь задайте для виртуальной машины состояние **Generalized**(Универсальная). 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Получите виртуальную машину. 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Создайте конфигурацию образа.

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Создайте образ.

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 
## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Создание образа из управляемого диска с помощью PowerShell

Если требуется только создать образ диска ОС, это можно сделать, указав идентификатор управляемого диска в качестве диска операционной системы.

    
1. Создайте несколько переменных. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Получите виртуальную машину.

   ```azurepowershell-interactive
   $vm = Get-AzureRmVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Получите идентификатор управляемого диска.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Создайте конфигурацию образа.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Создайте образ.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Создание образа из моментального снимка с помощью PowerShell

Вы можете создать управляемый образ из моментального снимка универсальной виртуальной машины.

    
1. Создайте несколько переменных. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Получите моментальный снимок.

   ```azurepowershell-interactive
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Создайте конфигурацию образа.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Создайте образ.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-image-from-a-vhd-in-a-storage-account"></a>Создание образа из VHD в учетной записи хранения

Создайте управляемый образ с помощью универсального виртуального жесткого диска операционной системы в учетной записи хранения. Вам потребуется URI виртуального жесткого диска в учетной записи хранения в формате https://*mystorageaccount*.blob.core.windows.net/*container*/*vhd_filename.vhd*. В этом примере мы используем VHD, который находится в *mystorageaccount* в контейнере с именем *vhdcontainer*, а имя файла VHD — *osdisk.vhd*.


1.  Сначала задайте общие параметры.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Остановите и освободите виртуальную машину.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Пометьте виртуальную машину как универсальную.

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  Создайте образ с помощью универсального виртуального жесткого диска ОС.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Дополнительная информация
- Теперь вы можете [создать виртуальную машину из универсального управляемого образа](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

