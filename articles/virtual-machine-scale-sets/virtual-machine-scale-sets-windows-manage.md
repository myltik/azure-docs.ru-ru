---
title: "Управление виртуальными машинами в масштабируемом наборе виртуальных машин | Документация Майкрософт"
description: "Узнайте, как управлять виртуальными машинами в наборе масштабирования виртуальных машин с помощью Azure PowerShell."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 88541f6054df1476866d68fdc1c9690a73ada83c
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Управление виртуальными машинами в масштабируемом наборе виртуальных машин
Задачи, описанные в этой статье, можно использовать для управления виртуальными машинами в масштабируемом наборе виртуальных машин.

Для выполнения большинства задач, связанных с управлением виртуальной машиной в масштабируемом наборе, необходимо знать идентификатор экземпляра этой машины. Определить идентификатор экземпляра виртуальной машины в наборе масштабирования можно с помощью [обозревателя ресурсов Azure](https://resources.azure.com) . Обозреватель ресурсов также используется для проверки состояния выполненных задач.

Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azure/overview).

## <a name="display-information-about-a-scale-set"></a>Отображение информации о масштабируемом наборе
Вы можете получить общие сведения о наборе масштабирования, которые также называются представлением экземпляра. Вы также можете получить более подробную информацию, например, о ресурсах в масштабируемом наборе.

Замените заключенные в кавычки значения именами группы ресурсов и масштабируемого набора, а затем выполните следующую команду:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Результат буде выглядеть примерно так:

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded

Замените заключенные в кавычки значения именами группы ресурсов и масштабируемого набора. Замените *#* идентификатором экземпляра виртуальной машины, сведения о которой вы хотите получить. Затем выполните эту команду:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Результат будет выглядеть примерно так:

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded

## <a name="start-a-virtual-machine-in-a-scale-set"></a>Запуск виртуальной машины в наборе масштабирования
Замените заключенные в кавычки значения именами группы ресурсов и масштабируемого набора. Замените *#* идентификатором виртуальной машины, которую вы хотите запустить. Затем выполните эту команду:

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

В обозревателе ресурсов видно, что экземпляр находится в состоянии **выполняется**.

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

Все виртуальные машины в масштабируемом наборе можно запустить, не используя параметр -InstanceId.

## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Остановка виртуальной машины в наборе масштабирования
Замените заключенные в кавычки значения именами группы ресурсов и масштабируемого набора. Замените *#* идентификатором виртуальной машины, которую вы хотите остановить. Затем выполните эту команду:

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

В обозревателе ресурсов видно, что экземпляр находится в состоянии **освобождено**.

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

Чтобы остановить виртуальную машину, не отменяя распределение, используйте параметр -StayProvisioned. Все виртуальные машины в наборе можно остановить, не используя параметр -InstanceId.

## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Перезапуск виртуальной машины в наборе масштабирования
Замените заключенные в кавычки значения именами группы ресурсов и масштабируемого набора. Замените *#* идентификатором виртуальной машины, которую вы хотите перезапустить. Затем выполните эту команду:

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Все виртуальные машины в наборе можно перезапустить, не используя параметр -InstanceId.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Удаление виртуальной машины из набора масштабирования
Замените заключенные в кавычки значения именами группы ресурсов и масштабируемого набора. Замените *#* идентификатором виртуальной машины, которую вы хотите удалить. Затем выполните эту команду:  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Весь набор масштабирования виртуальных машин можно удалить, не используя параметр -InstanceId.

## <a name="change-the-capacity-of-a-scale-set"></a>Изменение емкости масштабируемого набора
Можно добавлять или удалять виртуальные машины, изменяя емкость набора. Выберите масштабируемый набор, который необходимо изменить, задайте желаемое значение емкости, а затем обновите масштабируемый набор с новым значением емкости. В этих командах замените заключенные в кавычки значения именами группы ресурсов и масштабируемого набора.

    $vmss = Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
    $vmss.sku.capacity = 5
    Update-AzureRmVmss -ResourceGroupName "resource group name" -Name "scale set name" -VirtualMachineScaleSet $vmss 

При удалении виртуальных машин из масштабируемого набора виртуальные машины с более высоким значениям идентификатора удаляются первыми.


