---
title: Процедура изменения (переноса) номера SKU для основного типа узла на больший номер SKU | Документация Майкрософт
description: Узнайте, как обновить или перенести номер SKU для основного типа узла на больший номер SKU с помощью команд PowerShell и команд интерфейса командной строки.
services: service-fabric
documentationcenter: .net
author: v-rachiw
manager: navya
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2018
ms.author: v-rachiw
ms.openlocfilehash: 8d5b560068a9e0bc0169bfdb98c5e939e34a3b8c
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34274033"
---
# <a name="upgrademigrate-the-sku-for-primary-node-type-to-higher-sku"></a>Обновление или перенос номера SKU для основного типа узла на больший номер SKU

В этой статье описывается, как обновить или перенести номер SKU основного типа узла кластера Service Fabric на больший номер SKU с помощью Azure PowerShell.

## <a name="add-a-new-virtual-machine-scale-set"></a>Добавление нового масштабируемого набора виртуальных машин 

Разверните новый масштабируемый набор виртуальных машин и подсистему балансировки нагрузки. Конфигурация расширения Service Fabric (особенно тип узла) нового масштабируемого набора виртуальных машин должна быть такой же, как и у старого масштабируемого набора, который вы обновляете. В обозревателе SF убедитесь, что новые узлы доступны. 

### <a name="azure-powershell"></a>Azure PowerShell
В следующем примере используется Azure PowerShell для развертывания обновленного шаблона Resource Manager *template.json* с помощью группы ресурсов *myResourceGroup*.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile \template\template.json 
```

В следующем примере показано, как изменить шаблон json, чтобы добавить в существующий кластер ресурс нового масштабируемого набора виртуальных машин с указанным типом основного узла.

```json
        {
            "apiVersion": "[variables('vmssApiVersion')]",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[parameters('vmNodeType2Name')]",
            "location": "[parameters('computeLocation')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType2Name')))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "overprovision": "[parameters('overProvision')]",
                "upgradePolicy": {
                    "mode": "Automatic"
                },
                "virtualMachineProfile": {
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "[concat(parameters('vmNodeType2Name'),'_ServiceFabricNode')]",
                                "properties": {
                                    "type": "ServiceFabricNode",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                                    },
                                    "publisher": "Microsoft.Azure.ServiceFabric",
                                    "settings": {
                                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                                        "dataPath": "D:\\\\SvcFab",
                                        "durabilityLevel": "Bronze",
                                        "enableParallelJobs": true,
                                        "nicPrefixOverride": "[parameters('subnet0Prefix')]",
                                        "certificate": {
                                            "thumbprint": "[parameters('certificateThumbprint')]",
                                            "x509StoreName": "[parameters('certificateStoreValue')]"
                                        }
                                    },
                                    "typeHandlerVersion": "1.0"
                                }
                            },
                            {
                                "name": "[concat('VMDiagnosticsVmExt','_vmNodeType2Name')]",
                                "properties": {
                                    "type": "IaaSDiagnostics",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                        "storageAccountEndPoint": "https://core.windows.net/"
                                    },
                                    "publisher": "Microsoft.Azure.Diagnostics",
                                    "settings": {
                                        "WadCfg": {
                                            "DiagnosticMonitorConfiguration": {
                                                "overallQuotaInMB": "50000",
                                                "EtwProviders": {
                                                    "EtwEventSourceProviderConfiguration": [
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Actors",
                                                            "scheduledTransferKeywordFilter": "1",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableActorEventTable"
                                                            }
                                                        },
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Services",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                            }
                                                        }
                                                    ],
                                                    "EtwManifestProviderConfiguration": [
                                                        {
                                                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                            "scheduledTransferLogLevelFilter": "Information",
                                                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricSystemEventTable"
                                                            }
                                                        }
                                                    ]
                                                }
                                            }
                                        },
                                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                                    },
                                    "typeHandlerVersion": "1.5"
                                }
                            }
                        ]
                    },
                    "networkProfile": {
                        "networkInterfaceConfigurations": [
                            {
                                "name": "[concat(parameters('nicName'), '-2')]",
                                "properties": {
                                    "ipConfigurations": [
                                        {
                                            "name": "[concat(parameters('nicName'),'-',2)]",
                                            "properties": {
                                                "loadBalancerBackendAddressPools": [
                                                    {
                                                        "id": "[variables('lbPoolID2')]"
                                                    }
                                                ],
                                                "loadBalancerInboundNatPools": [
                                                    {
                                                        "id": "[variables('lbNatPoolID2')]"
                                                    }
                                                ],
                                                "subnet": {
                                                    "id": "[variables('subnet0Ref')]"
                                                }
                                            }
                                        }
                                    ],
                                    "primary": true
                                }
                            }
                        ]
                    },
                    "osProfile": {
                        "adminPassword": "[parameters('adminPassword')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "computernamePrefix": "[parameters('vmNodeType2Name')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[parameters('sourceVaultValue')]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateStore": "[parameters('certificateStoreValue')]",
                                        "certificateUrl": "[parameters('certificateUrlValue')]"
                                    }
                                ]
                            }
                        ]
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[parameters('vmImagePublisher')]",
                            "offer": "[parameters('vmImageOffer')]",
                            "sku": "[parameters('vmImageSku')]",
                            "version": "[parameters('vmImageVersion')]"
                        },
                        "osDisk": {
                            "caching": "ReadOnly",
                            "createOption": "FromImage",
                            "managedDisk": {
                                "storageAccountType": "[parameters('storageAccountType')]"
                            }
                        }
                    }
                }
            },
            "sku": {
                "name": "[parameters('vmNodeType2Size')]",
                "capacity": "[parameters('nt2InstanceCount')]",
                "tier": "Standard"
            },
            "tags": {
                "resourceType": "Service Fabric",
                "clusterName": "[parameters('clusterName')]"
            }
        },
```


## <a name="remove-old-virtual-machine-scale-set"></a>Удаление старого масштабируемого набора виртуальных машин

Отключите экземпляры виртуальных машин старого масштабируемого набора виртуальных машин набор, из которого вы собираетесь удалить узлы. Для выполнения этой операции потребоваться длительное время. Вы можете отключить все узлы сразу или по очереди. Подождите, пока все узлы не будут отключены. 

### <a name="azure-powershell"></a>Azure PowerShell
В следующем примере используется PowerShell для Azure Service Fabric для отключения экземпляра узла *NTvm1_0* из старого масштабируемого набора виртуальных машин *NTvm1*.

```powershell
Disable-ServiceFabricNode -NodeName NTvm1_0 -Intent RemoveNode 
```

Удалите весь масштабируемый набор. Подождите, пока состояние подготовки масштабируемого набора не станет успешным. 

### <a name="azure-powershell"></a>Azure PowerShell
В следующем примере используется Azure PowerShell для удаления всего масштабируемого набора *NTvm1* из группы ресурсов *myResourceGroup*.

```powershell
Remove-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName NTvm1
```

## <a name="remove-load-balancer-related-to-old-scale-set"></a>Удаление подсистемы балансировки нагрузки, связанной со старым масштабируемым набором

Удалите подсистему балансировки нагрузки, связанную со старым масштабируемым набором. Это действие приведет к кратковременному простою кластера.

### <a name="azure-powershell"></a>Azure PowerShell
В следующем примере используется Azure PowerShell для удаления подсистемы балансировки нагрузки *LB-myCluster-NTvm1*, связанной со старым масштабируемым набором из группы ресурсов *myResourceGroup*.

```powershell
Remove-AzureRmLoadBalancer -Name LB-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

## <a name="remove-public-ip-related-to-old-scale-set"></a>Удаление общедоступного IP-адреса, связанного со старым масштабируемым набором

Сохраните параметры DNS общедоступного IP-адреса, связанного со старым масштабируемым набором, в переменной, а затем удалите этот общедоступный IP-адрес.

### <a name="azure-powershell"></a>Azure PowerShell
В следующем примере используется Azure PowerShell для сохранения параметров DNS общедоступного IP-адреса *LBIP-myCluster-NTvm1* в переменной и удаления этого IP-адреса.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn
Remove-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

## <a name="update-public-ip-address-related-to-new-scale-set"></a>Обновление общедоступного IP-адреса, связанного со старым масштабируемым набором

Обновите параметры DNS общедоступного IP-адреса, связанного с новым масштабируемым набором, с помощью параметров DNS общедоступного IP-адреса, связанного со старым масштабируемым набором.

### <a name="azure-powershell"></a>Azure PowerShell
В следующем примере используется Azure PowerShell для обновления параметров DNS общедоступного IP-адреса *LBIP-myCluster-NTvm3* с помощью параметров DNS, сохраненных в переменных на предыдущем шаге.

```powershell
$PublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP 
```

## <a name="remove-knowledge-of-service-fabric-node-from-fm"></a>Удаление сведений об узле Service Fabric из диспетчера отработки отказов

Сообщите Service Fabric, что узлы, которые не работают, были удалены из кластера. (Выполните эту команду для всех экземпляров виртуальных машин старого масштабируемого набора. Если уровень устойчивости старого масштабируемого набора виртуальных машин — серебряный или золотой, этот шаг может не понадобиться, так как он выполняется системой автоматически.)

### <a name="azure-powershell"></a>Azure PowerShell
В следующем примере используется PowerShell для Azure Service Fabric для уведомления Service Fabric о том, что узел *NTvm1_0* был удален.

```powershell
Remove-ServiceFabricNodeState -NodeName NTvm1_0
```