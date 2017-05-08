---
title: "Создание управляемого диска из виртуального жесткого диска в Azure | Документация Майкрософт"
description: "Создание управляемого диска из виртуального жесткого диска, который в данный момент находится в учетной записи хранения Azure, в модели развертывания с помощью Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 0a6e034da0445e94178e04cecc3ce09d86d2d29a
ms.lasthandoff: 04/27/2017


---

# <a name="create-managed-disks-from-unmanaged-disks-in-a-storage-account"></a>Создание управляемых дисков из неуправляемых дисков в учетной записи хранения

Управляемый диск можно создать из существующего диска данных или диска ОС, который в данный момент находится в учетной записи хранения Azure. Можно также создавать пустой диск, который можно использовать в качестве нового диска данных для виртуальной машины. 

## <a name="before-you-begin"></a>Перед началом работы
Если вы используете PowerShell, убедитесь, что у вас установлена последняя версия модуля PowerShell AzureRM.Compute. Выполните следующую команду, чтобы установить ее.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Дополнительные сведения см. в разделе [об управлении версиями Azure PowerShell](/powershell/azure/overview).


## <a name="create-a-managed-disk-from-a-vhd-in-an-azure-storage-account"></a>Создание управляемого диска из виртуального жесткого диска в учетной записи хранения Azure

В приведенном примере мы создаем управляемый диск из виртуального жесткого диска и назначаем его параметру **$disk1** для последующего использования. 

Управляемый диск будет создан в расположении **Западная часть США** в группе ресурсов **myResourceGroup**. Диск будет называться **myDisk**, и для его создания будет использован виртуальный жесткий диск **myDisk.vhd**, ранее переданный в учетную запись хранения **mystorageaccount**. Управляемые диск будет создан в локально избыточном хранилище (LRS) уровня "Премиум". Для параметра **-AccountType** управляемых дисков доступны только значения StandardLRS и PremiumLRS. 

1.  Задайте некоторые параметры.

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $diskName = "myDisk"
    $vhdUri = "https://mystorageaccount.blob.core.windows.net/vhds/myDisk.vhd"
    ```

2. Создайте диск данных. 
    ```powershell
    $disk1 = New-AzureRmDisk -DiskName $diskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $vhdUri) -ResourceGroupName $rgName
    ```
    
    

## <a name="create-an-empty-data-disk-as-a-managed-disk"></a>Создание пустого диска данных в качестве управляемого диска

В приведенном примере мы создаем пустой диск данных в качестве управляемого диска и назначаем его параметру **$dataDisk2** для последующего использования. Пустой диск данных должен быть инициализирован. Для этого после подключения диска данных к запущенной виртуальной машине следует войти на нее и использовать diskmgmt.msc или [удаленно использовать WinRM и сценарий](attach-disk-ps.md#initialize-the-disk).

Пустой диск данных будет создан в расположении **Западно-центральная часть США** в группе ресурсов **myResourceGroup**. Диск будет называться **myEmptyDataDisk**. Пустой диск будет создан в локально избыточном хранилище (LRS) уровня "Премиум". Для параметра **-AccountType** управляемых дисков доступны только значения StandardLRS и PremiumLRS.

Размер диска в этом примере составляет 128 ГБ, но вам следует выбрать размер, который соответствует требованиям любых приложений, запускаемых на виртуальной машине.

1.  Задайте некоторые параметры.

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $dataDiskName = "myEmptyDataDisk"
    ```

2. Создайте диск данных.
    ```powershell
    $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Empty -DiskSizeGB 128) -ResourceGroupName $rgName
    ```
    
## <a name="next-steps"></a>Дальнейшие действия    
- К имеющейся виртуальной машине можно [подключить диск данных](attach-disk-portal.md).

