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
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Управление виртуальными машинами в наборе масштабирования виртуальных машин

Azure PowerShell предоставляет разнообразные гибкие возможности для управления ресурсами в Microsoft Azure. Задачи, описанные в этой статье, можно использовать для управления ресурсами виртуальных машин в наборе масштабирования виртуальных машин.

- [Вывод информации о наборе масштабирования виртуальных машин](#displayvm)
- [Запуск виртуальной машины в наборе масштабирования](#start)
- [Остановка виртуальной машины в наборе масштабирования](#stop)
- [Перезапуск виртуальной машины в наборе масштабирования](#restart)
- [Удаление виртуальной машины из набора масштабирования](#delete)

Для выполнения любых задач, связанных с управлением виртуальной машиной в наборе масштабирования, необходимо знать идентификатор экземпляра этой машины. Определить идентификатор экземпляра виртуальной машины в наборе масштабирования можно с помощью [обозревателя ресурсов Azure](https://resources.azure.com). Обозреватель ресурсов также используется для проверки состояния выполненных задач.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>Вывод информации о наборе масштабирования виртуальных машин

Вы можете получить общие сведения о наборе масштабирования, которые также называются представлением экземпляра. Вы также можете получить более подробную информацию, например, о ресурсах в наборе.

В приведенной ниже команде замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, а *scale set name* — именем набора масштабирования виртуальных машин. Затем выполните эту команду.

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Результат буде выглядеть примерно так:

    Sku                      :  {
                                  "name": "Standard_A0",
                                  "tier": "Standard",
                                  "capacity": 4
                                }
    UpgradePolicy            :  {
                                  "mode": "Manual"
                                }
    VirtualMachineProfile    :  {
                                  "osProfile": {
                                    "computerNamePrefix": "myvmss1",
                                    "adminUserName": "user1",
                                    "adminPassword": null,
                                    "customData": null,
                                    "windowsConfiguration": {
                                      "provisionVMAgent": true,
                                      "enableAutomaticUpdates": true,
                                      "timeZone": null,
                                      "additionalUnattendContent": null,
                                      "winRM": null
                                    }
                                    "linuxConfiguration": null,
                                    "secrets": []
                                  },
                                  "storageProfile": {
                                    "imageReference": {
                                      "publisher": "MicrosoftWindowsServer",
                                      "offer": "WindowsServer",
                                      "sku": "2012-R2-Datacenter",
                                      "version": "latest"
                                    },
                                    "osDisk": {
                                      "name": "vmssosdisk",
                                      "caching": "ReadOnly",
                                      "createOption": "FromImage",
                                      "osType": null,
                                      "image": null,
                                      "vhdContainers": [
                                        "https://amyst1.blob.core.windows.net/vmss",
                                        "https://gmyst1.blob.core.windows.net/vmss",
                                        "https://mmyst1.blob.core.windows.net/vmss",
                                        "https://smyst1.blob.core.windows.net/vmss",
                                        "https://ymyst1.blob.core.windows.net/vmss"
                                      ]
                                    }
                                  },
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myresnc2",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "ip1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/myresvn1/subnets/myressn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/backendAddressPools/bepool1"
                                              }
                                            ],
                                            "properties.loadBalancerInboundNatPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/inboundNatPools/natpool1"
                                              }
                                            ],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": [
                                      {
                                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                                        "properties.publisher": "Microsoft.Azure.Diagnostics",
                                        "properties.type": "IaaSDiagnostics",
                                        "properties.typeHandlerVersion": "1.5",
                                        "properties.autoUpgradeMinorVersion": true,
                                        "properties.settings": {
                                          "xmlCfg": "{encoded configuration}",
                                          "storageAccount": "amyst1"
                                        },
                                        "properties.protectedSettings": null,
                                        "properties.provisioningState": null,
                                        "id": null
                                      }
                                    ]
                                  }
                                }
    ProvisioningState           : Succeeded
    Id                          : /subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                        : myvmss1
	Type                        : Microsoft.Compute/virtualMachineScaleSets
	Location                    : westus
	Tags.Count                  : 0
	Tags                        :

Чтобы получить общие сведения, замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, а *scale set name* — именем набора масштабирования виртуальных машин. Затем выполните эту команду.

	Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

Результат буде выглядеть примерно так:

    VirtualMachine   :  {
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
    Extensions.Count :  1
    Extensions       :  {
                          "name": "Microsoft.Insights.VMDiagnosticsSettings",
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
	Statuses.Count   :  1
	Statuses         :  {
                          "code": "ProvisioningState/succeeded",
                          "level": "Info",
                          "displayStatus": "Provisioning succeeded",
                          "message": null,
                          "time": "2016-03-14T20:29:37.170809Z"
                        }

## <a id="start"></a>Запуск виртуальной машины в наборе масштабирования

В приведенной ниже команде замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, *VM scale set name* — именем набора масштабирования, а *instance id* — идентификатором виртуальной машины, которую нужно перезапустить. Затем выполните эту команду.

    Start-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

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

## <a id="stop"></a>Остановка виртуальной машины в наборе масштабирования

В приведенной ниже команде замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, *scale set name* — именем набора масштабирования, а *instance id* — идентификатором виртуальной машины, которую нужно остановить. Затем выполните эту команду.

	Stop-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

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

## <a id="restart"></a>Перезапуск виртуальной машины в наборе масштабирования

В приведенной ниже команде замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, *scale set name* — именем набора масштабирования, а *instance id* — идентификатором виртуальной машины, которую нужно перезапустить. Затем выполните эту команду.

	Restart-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

## <a id="delete"></a>Удаление виртуальной машины из набора масштабирования

В приведенной ниже команде замените *resource group name* именем группы ресурсов, содержащей набор масштабирования виртуальных машин, *scale set name* — именем набора масштабирования, а *instance id* — идентификатором виртуальной машины, которую нужно удалить из набора масштабирования. Затем выполните эту команду.

	Remove-AzureRmVmssVM -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId "instance id"

<!---HONumber=AcomDC_0427_2016-->