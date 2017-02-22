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
ms.sourcegitcommit: b54a95c4f81d9a981912e1b596a817dc6ad56334
ms.openlocfilehash: 52684fe3212454abbfb0cf9d1c67759fce9a1549


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


## <a name="next-steps"></a>Дальнейшие действия
При наличии проблем с развертыванием ознакомьтесь с информацией об [устранении распространенных ошибок при развертывании Azure с помощью Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).




<!--HONumber=Feb17_HO2-->


