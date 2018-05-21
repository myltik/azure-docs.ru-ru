---
title: Сеть для масштабируемых наборов виртуальных машин Azure | Документация Майкрософт
description: Конфигурация свойств сети для масштабируемых наборов виртуальных машин Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: negat
ms.openlocfilehash: 1db4c7ae78320eb08b2aa0b9da701d9678baf798
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Сеть для масштабируемых наборов виртуальных машин Azure

При развертывании масштабируемого набора виртуальных машин Azure с помощью портала определенные свойства сети заданы по умолчанию, например Azure Load Balancer с правилами преобразования сетевых адресов для входящих подключений. В этой статье описывается, как использовать некоторые расширенные сетевые функции, которые можно настроить с помощью наборов масштабирования.

Вы можете настроить все функции, описанные в этой статье, с помощью шаблонов Azure Resource Manager. Кроме того, будут добавлены примеры Azure CLI и PowerShell для выбранных компонентов. Используйте интерфейс командной строки версии 2.10 и PowerShell 4.2.0 или более поздней версии.

## <a name="accelerated-networking"></a>Ускорение работы в сети
Ускорение работы в сети Azure достигается за счет виртуализации ввода-вывода с единым корнем (SR-IOV) для виртуальной машины. Дополнительные сведения см. в разделах об ускорении работы в сети для виртуальных машин [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) или [Linux](../virtual-network/create-vm-accelerated-networking-cli.md). Чтобы использовать ускоренную сеть с масштабируемыми наборами, в настройках networkInterfaceConfigurations масштабируемого набора задайте для параметра enableAcceleratedNetworking значение **true**. Например: 
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="create-a-scale-set-that-references-an-existing-azure-load-balancer"></a>Создание набора масштабирования, который ссылается на существующую службу Azure Load Balancer
Если набор масштабирования создается с помощью портала Azure, для большинства параметров конфигурации создается новая подсистема балансировки нагрузки. Если вы создаете набор масштабирования, которому необходимо ссылаться на существующую подсистему балансировки нагрузки, вы можете создать его с помощью интерфейса командной строки. Следующий пример сценария создает подсистему балансировки нагрузки, а затем набор масштабирования, который ссылается на нее:
```bash
az network lb create -g lbtest -n mylb --vnet-name myvnet --subnet mysubnet --public-ip-address-allocation Static --backend-pool-name mybackendpool

az vmss create -g lbtest -n myvmss --image Canonical:UbuntuServer:16.04-LTS:latest --admin-username negat --ssh-key-value /home/myuser/.ssh/id_rsa.pub --upgrade-policy-mode Automatic --instance-count 3 --vnet-name myvnet --subnet mysubnet --lb mylb --backend-pool-name mybackendpool

```

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>Создание масштабируемого набора, который ссылается на шлюз приложений
Чтобы создать масштабируемый набор, который использует шлюз приложений, создайте ссылку на серверный пул адресов шлюза приложений в разделе ipConfigurations в масштабируемом наборе, как в этой конфигурации шаблона ARM:
```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> Обратите внимание, что шлюз приложений должен находиться в той же виртуальной сети, что и масштабируемый набор, но в другой подсети.


## <a name="configurable-dns-settings"></a>Настраиваемые параметры DNS
По умолчанию наборы масштабирования принимают определенные параметры DNS виртуальной сети и подсети, в которых они были созданы. Однако вы можете настроить параметры DNS непосредственно для набора масштабирования.

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Создание масштабируемого набора с настраиваемыми DNS-серверами
Чтобы создать масштабируемый набор с настраиваемой конфигурацией DNS с помощью интерфейса командной строки 2.0, добавьте аргумент **--dns-servers** в команду **vmss create**, за которой следуют разделенные пробелами IP-адреса сервера. Например: 
```bash
--dns-servers 10.0.0.6 10.0.0.5
```
Чтобы настроить пользовательские DNS-серверы в шаблоне Azure, добавьте свойство dnsSettings в раздел networkInterfaceConfigurations набора масштабирования. Например: 
```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Создание масштабируемого набора с настраиваемыми именами доменов виртуальных машин
Чтобы создать масштабируемый набор с настраиваемым DNS-именем для виртуальных машин с помощью CLI 2.0, добавьте аргумент **--vm-domain-name** в команду **vmss create**, за которой следует строка, представляющая доменное имя.

Чтобы настроить доменное имя в шаблоне Azure, добавьте свойство **dnsSettings** в раздел масштабируемого набора **networkInterfaceConfigurations**. Например: 

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": "true",
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

Выходные данные для DNS-имени отдельной виртуальной машины будут представлены следующим образом: 
```
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>Общедоступный IPv4 на каждую виртуальную машину
Как правило, виртуальным машинам в масштабируемом наборе Azure не требуются собственные общедоступные IP-адреса. Для большинства сценариев наиболее безопасно, а также экономически целесообразно связать общедоступный IP-адрес с подсистемой балансировки нагрузки или отдельной виртуальной машиной (также называемой jumpbox), которая затем (при необходимости) направит входящие подключения на виртуальные машины в масштабируемом наборе (например, через правила преобразования сетевых адресов для входящих подключений).

Тем не менее, в некоторых сценариях виртуальным машинам в масштабируемом наборе обязательно нужны собственные общедоступные IP-адреса. В качестве примера можно привести игры, в которых консоли необходимо установить прямое подключение к облачной виртуальной машине, выполняющей физическую обработку игр. Другим примером можно назвать случай, когда виртуальным машинам необходимо установить внешние подключения между собой в регионах в распределенной базе данных.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Создание масштабируемого набора с общедоступным IP-адресом на виртуальную машину
Чтобы создать масштабируемый набор, который назначает общедоступный IP-адрес каждой виртуальной машине с помощью CLI 2.0, добавьте параметр **--public-ip-per-vm** в команду **vmss create**. 

Чтобы создать масштабируемый набор с помощью шаблона Azure версии API ресурса Microsoft.Compute/virtualMachineScaleSets должна быть по крайней мере **30-03-2017**. Добавьте свойство JSON **publicIpAddressConfiguration** в раздел ipConfigurations масштабируемого набора. Например: 

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```
Пример шаблона: [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Запрос общедоступных IP-адресов виртуальных машин в масштабируемом наборе
Чтобы получить список общедоступных IP-адресов, назначенных виртуальным машинам в масштабируемом наборе, с помощью CLI 2.0, используйте команду **az vmss list-instance-public-ips**.

Используйте команду _Get-AzureRmPublicIpAddress_, чтобы вывести список общедоступных IP-адресов масштабируемого набора, используя PowerShell. Например: 
```PowerShell
PS C:\> Get-AzureRmPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

Вы также можете запрашивать общедоступные IP-адреса, напрямую ссылаясь на идентификатор ресурса конфигурации общедоступного IP-адреса. Например: 
```PowerShell
PS C:\> Get-AzureRmPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Чтобы запросить общедоступные IP-адреса, назначенные виртуальным машинам в масштабируемом наборе, используйте [обозреватель ресурсов Azure](https://resources.azure.com) или Azure REST API версии **30-03-2017** или более поздней.

Чтобы просмотреть общедоступные IP-адреса для масштабируемого набора с помощью обозревателя ресурсов, ознакомьтесь с разделом **publicipaddresses** в масштабируемом наборе. Например: https://resources.azure.com/subscriptions/_your_sub_id_/resourceGroups/_your_rg_/providers/Microsoft.Compute/virtualMachineScaleSets/_your_vmss_/publicipaddresses

```
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Выходные данные примера:
```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Несколько IP-адресов на сетевой адаптер
У каждого сетевого адаптера, подключенного к виртуальной машине в масштабируемом наборе, может быть одна или несколько конфигураций IP-адресов. Каждая конфигурация получает один частный IP-адрес. Кроме того, каждой конфигурации также может быть присвоен один ресурс общедоступного IP-адреса. Чтобы узнать, сколько IP-адресов можно назначить сетевому адаптеру, а также сколько общедоступных IP-адресов можно использовать в подписке Azure, ознакомьтесь с [ограничениями Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="multiple-nics-per-virtual-machine"></a>Несколько сетевых адаптеров на виртуальную машину
У вам может быть до 8 сетевых адаптеров на виртуальную машину в зависимости от размера машины. Узнать о максимальном количестве сетевых адаптеров на машину вы можете в статье [Размеры виртуальных машин Windows в Azure](../virtual-machines/windows/sizes.md). Пример ниже представляет сетевой профиль в масштабируемом наборе, отображающий несколько записей сетевых адаптеров, а также несколько общедоступных IP-адресов на виртуальную машину:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": "true",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": "false",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg-per-scale-set"></a>Группа безопасности сети на масштабируемый набор
Группы безопасности сети можно применить непосредственно к масштабируемому набору, добавив ссылку в раздел конфигурации сетевого интерфейса свойств виртуальной машины в масштабируемом наборе.

Например:  
```
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о виртуальных сетях Azure см. в [этой статье](../virtual-network/virtual-networks-overview.md).
