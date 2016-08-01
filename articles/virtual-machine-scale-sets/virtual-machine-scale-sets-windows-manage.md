<properties
	pageTitle="Управление виртуальными машинами в наборе масштабирования виртуальных машин | Microsoft Azure"
	description="Узнайте, как управлять виртуальными машинами в наборе масштабирования виртуальных машин с помощью Azure PowerShell."
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="davidmu"/>

# Управление виртуальными машинами в наборе масштабирования виртуальных машин

Задачи, описанные в этой статье, можно использовать для управления ресурсами виртуальных машин в наборе масштабирования виртуальных машин.

Для выполнения любых задач, связанных с управлением виртуальной машиной в наборе масштабирования, необходимо знать идентификатор экземпляра этой машины. Определить идентификатор экземпляра виртуальной машины в наборе масштабирования можно с помощью [обозревателя ресурсов Azure](https://resources.azure.com). Обозреватель ресурсов также используется для проверки состояния выполненных задач.

Сведения о том, как установить последнюю версию Azure PowerShell, выбрать нужную подписку и войти в учетную запись Azure, см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Вывод информации о наборе масштабирования виртуальных машин

Вы можете получить общие сведения о наборе масштабирования, которые также называются представлением экземпляра. Вы также можете получить более подробную информацию, например, о ресурсах в наборе.

В приведенной ниже команде замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, а *scale set name* — именем набора масштабирования виртуальных машин. Затем выполните эту команду.

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
    
В приведенной ниже команде замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, *scale set name* — именем набора масштабирования виртуальных машин, а *#* — идентификатором экземпляра виртуальной машины, сведения о которой вы хотите получить. Затем выполните эту команду.

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Результат буде выглядеть примерно так:

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
        
## Запуск виртуальной машины в наборе масштабирования

В приведенной ниже команде замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, *scale set name* — именем набора масштабирования, а *#* — идентификатором виртуальной машины, которую нужно запустить. Затем выполните эту команду.

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

Все виртуальные машины в наборе можно запустить, не используя параметр -InstanceId.
    
## Остановка виртуальной машины в наборе масштабирования

В приведенной ниже команде замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, *scale set name* — именем набора масштабирования, а *#* — идентификатором виртуальной машины, которую нужно остановить. Затем выполните эту команду.

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
    
## Перезапуск виртуальной машины в наборе масштабирования

В приведенной ниже команде замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, *scale set name* — именем набора масштабирования, а *#* — идентификатором виртуальной машины, которую нужно перезапустить. Затем выполните эту команду.

	Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
Все виртуальные машины в наборе можно перезапустить, не используя параметр -InstanceId.

## Удаление виртуальной машины из набора масштабирования

В приведенной ниже команде замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, *scale set name* — именем набора масштабирования, а *#* — идентификатором виртуальной машины, которую нужно удалить из набора масштабирования. Затем выполните эту команду.

	Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Весь набор масштабирования виртуальных машин можно удалить, не используя параметр -InstanceId.

<!---HONumber=AcomDC_0720_2016-->