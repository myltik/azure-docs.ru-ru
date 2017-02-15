---
title: "Управление виртуальными машинами с помощью Resource Manager и PowerShell | Документация Майкрософт"
description: "Управление виртуальными машинами с помощью Azure Resource Manager и PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 48930854-7888-4e4c-9efb-7d1971d4cc14
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 63e822de6ae50be33590048140e06e89526282ee


---
# <a name="manage-azure-virtual-machines-using-resource-manager-and-powershell"></a>Управление виртуальными машинами Azure с помощью диспетчера ресурсов и PowerShell
## <a name="install-azure-powershell"></a>Установка Azure PowerShell
Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="set-variables"></a>Задание переменных
Для всех команд в статье требуется имя группы ресурсов, в которой находится виртуальная машина, и имя управляемой виртуальной машины. Замените значение **$rgName** именем группы ресурсов, содержащей виртуальную машину. Замените значение **$vmName** именем виртуальной машины. Создайте переменные.

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## <a name="display-information-about-a-virtual-machine"></a>Отображение информации о виртуальной машине
Получите сведения о виртуальной машине.

    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Результат будет выглядеть примерно так:

    ResourceGroupName        : rg1
    Id                       : /subscriptions/{subscription-id}/resourceGroups/
                               rg1/providers/Microsoft.Compute/virtualMachines/vm1
    Name                     : vm1
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {
                                  "id": "/subscriptions/{subscription-id}/resourceGroups/
                                  rg1/providers/Microsoft.Compute/availabilitySets/av1"
                               }
    Extensions               : []
    HardwareProfile          : {
                                  "vmSize": "Standard_A0"
                               }
    InstanceView             : null
    NetworkProfile           : {
                                  "networkInterfaces": [
                                    {
                                      "properties.primary": null,
                                      "id": "/subscriptions/{subscription-id}/resourceGroups/
                                      rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                    }
                                  ]
                               }
    OSProfile                : {
                                  "computerName": "vm1",
                                  "adminUsername": "myaccount1",
                                  "adminPassword": null,
                                  "customData": null,
                                  "windowsConfiguration": {
                                    "provisionVMAgent": true,
                                    "enableAutomaticUpdates": true,
                                    "timeZone": null,
                                    "additionalUnattendContents": [],
                                    "winRM": null
                                  },
                                  "linuxConfiguration": null,
                                  "secrets": []
                               }
    Plan                     : null
    ProvisioningState        : Succeeded
    StorageProfile           : {
                                  "imageReference": {
                                    "publisher": "MicrosoftWindowsServer",
                                    "offer": "WindowsServer",
                                    "sku": "2012-R2-Datacenter",
                                    "version": "latest"
                                  },
                                  "osDisk": {
                                    "osType": "Windows",
                                    "encryptionSettings": null,
                                    "name": "osdisk",
                                    "vhd": {
                                      "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                    }
                                    "image": null,
                                    "caching": "ReadWrite",
                                    "createOption": "FromImage"
                                  }
                                  "dataDisks": [],
                               }
    DataDiskNames            : {}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## <a name="stop-a-virtual-machine"></a>Остановка виртуальной машины
Остановите работающую виртуальную машину.

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Появится запрос на подтверждение:

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Введите **Y** , чтобы остановить виртуальную машину.

Через несколько минут будут выданы примерно следующие данные:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:11:57 PM
    EndTime    : 9/13/2016 12:14:40 PM

## <a name="start-a-virtual-machine"></a>запуск виртуальной машины
Запустите виртуальную машину, если она остановлена.

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Через несколько минут будут выданы примерно следующие данные:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:32:55 PM
    EndTime    : 9/13/2016 12:35:09 PM

Если требуется перезапустить виртуальную машину, которая уже работает, воспользуйтесь командлетом **Restart-AzureRmVM**, описанным ниже.

## <a name="restart-a-virtual-machine"></a>Перезапуск виртуальной машины
Перезапустите работающую виртуальную машину.

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Результат будет выглядеть примерно так:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:54:40 PM
    EndTime    : 9/13/2016 12:55:54 PM

## <a name="delete-a-virtual-machine"></a>удаление виртуальной машины
Удалите виртуальную машину.  

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [!NOTE]
> Чтобы пропустить запрос на подтверждение, используйте параметр **–Force** .
> 
> 

Если параметр -Force не использовался, то появится запрос на подтверждение:

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Результат будет выглядеть примерно так:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="update-a-virtual-machine"></a>Обновление виртуальной машины
В этом примере показано, как изменить размер виртуальной машины.

    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

Результат будет выглядеть примерно так:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

Список доступных размеров виртуальных машин в Azure см. в [этой статье](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Добавление диска данных в виртуальную машину
В этом примере показано, как добавить диск данных в существующую виртуальную машину.

    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

Добавляемый вами диск не инициализирован. Чтобы инициализировать этот диск, можно войти в него и использовать управление дисками. Если при создании диска вы установили на него WinRM и сертификат, то вы можете инициализировать диск удаленно с помощью PowerShell. Можно также использовать расширение пользовательского сценария. 

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

Файл сценария может содержать примерно следующий код для инициализации дисков:

    $disks = Get-Disk |   Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { ([char]$_) }
    $count = 0
    $labels = @("data1","data2")

    foreach($d in $disks) {
        $driveLetter = $letters[$count].ToString()
        $d | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] `
            -Confirm:$false -Force 
        $count++
    }

## <a name="next-steps"></a>Дальнейшие действия
При наличии проблем с развертыванием ознакомьтесь со статьей [об устранении неполадок развертывания групп ресурсов с помощью портала Azure](../resource-manager-troubleshoot-deployments-portal.md).




<!--HONumber=Dec16_HO2-->


