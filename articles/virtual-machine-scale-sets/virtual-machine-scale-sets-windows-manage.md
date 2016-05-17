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
	ms.date="04/26/2016"
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

    Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
    UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
    VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
    ProvisioningState     : Succeeded
    OverProvision         :
    Id                    : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                  : myvmss1
    Type                  : Microsoft.Compute/virtualMachineScaleSets
    Location              : centralus
    Tags                  :

В приведенной ниже команде замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, *scale set name* — именем набора масштабирования виртуальных машин, а *#* — идентификатором экземпляра виртуальной машины, сведения о которой вы хотите получить. Затем выполните эту команду.

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Результат буде выглядеть примерно так:

    InstanceId         : 1
    Sku                : Microsoft.Azure.Management.Compute.Models.Sku
    LatestModelApplied : True
    InstanceView       :
    HardwareProfile    :
    StorageProfile     : Microsoft.Azure.Management.Compute.Models.StorageProfile
    OsProfile          : Microsoft.Azure.Management.Compute.Models.OSProfile
    NetworkProfile     : Microsoft.Azure.Management.Compute.Models.NetworkProfile
    DiagnosticsProfile :
    AvailabilitySet    :
    ProvisioningState  : Succeeded
    LicenseType        :
    Plan               :
    Resources          :
    Id                 : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.
                         Compute/virtualMachineScaleSets/myvmss1/virtualMachines/1
    Name               : myvmss1_1
    Type               : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location           : centralus
    Tags               :
        
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

<!---HONumber=AcomDC_0504_2016-->