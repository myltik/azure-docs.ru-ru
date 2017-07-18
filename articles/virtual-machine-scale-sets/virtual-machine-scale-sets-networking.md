---
title: "Сеть для масштабируемых наборов виртуальных машин Azure | Документация Майкрософт"
description: "Конфигурация свойств сети для масштабируемых наборов виртуальных машин Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/06/2017
ms.author: guybo
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 1c9487be5415d05a8699f458259d872591280d3d
ms.contentlocale: ru-ru
ms.lasthandoff: 07/11/2017


---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Сеть для масштабируемых наборов виртуальных машин Azure

При развертывании масштабируемого набора виртуальных машин Azure с помощью портала определенные свойства сети заданы по умолчанию, например Azure Load Balancer с правилами преобразования сетевых адресов для входящих подключений. В этой статье описывается, как использовать некоторые расширенные сетевые функции, которые можно настроить с помощью наборов масштабирования.

Вы можете настроить все функции, описанные в этой статье, с помощью шаблонов Azure Resource Manager. Для выбранных функций также включены примеры Azure CLI. Используйте интерфейс командной строки Azure версии за июль 2017 г. Скоро будут добавлены дополнительные примеры для интерфейса командной строки Azure и PowerShell.

## <a name="accelerated-networking"></a>Ускорение работы в сети
[Ускорение работы в сети](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-create-vm-accelerated-networking) Azure повышает производительность сети с помощью использования виртуализации ввода-вывода с единым корнем (SR-IOV) для виртуальной машины. Чтобы использовать ускоренную сеть с масштабируемыми наборами, в настройках networkInterfaceConfigurations масштабируемого набора задайте для параметра enableAcceleratedNetworking значение _true_. Например:
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

## <a name="configurable-dns-settings"></a>Настраиваемые параметры DNS
По умолчанию наборы масштабирования принимают определенные параметры DNS виртуальной сети и подсети, в которых они были созданы. Однако вы можете настроить параметры DNS непосредственно для набора масштабирования.

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Создание масштабируемого набора с настраиваемыми DNS-серверами
Чтобы создать масштабируемый набор с настраиваемой конфигурацией DNS с помощью CLI 2.0, добавьте аргумент --dns-servers в команду _vmss create_, за которой следуют разделенные пробелами IP-адреса сервера. Например:
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
Чтобы создать масштабируемый набор с настраиваемым DNS-именем для виртуальных машин с помощью CLI 2.0, добавьте аргумент _--vm-domain-name_ в команду _vmss create_, за которой следует строка, представляющая доменное имя.

Чтобы настроить доменное имя в шаблоне Azure, добавьте свойство dnsSettings в раздел набора масштабирования networkInterfaceConfigurations. Например:

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
<vmname><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="ipv6-preview-for-public-ips-and-load-balancer-pools"></a>Предварительная версия IPv6 для общедоступных IP-адресов и пулов подсистемы балансировки нагрузки
Вы можете настроить общедоступные IP-адреса IPv6 в Azure Load Balancer, а также реализовать маршрутизацию подключений к пулам серверной части масштабируемых наборов виртуальных машин. Чтобы использовать протокол IPv6 (в настоящее время в предварительной версии), необходимо сначала создать ресурс с общедоступным адресом IPv6. Например:
```json
{
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[parameters('ipv6PublicIPAddressName')]",
    "location": "[parameters('location')]",
    "properties": {
        "publicIPAddressVersion": "IPv6",
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
            "domainNameLabel": "[parameters('dnsNameforIPv6LbIP')]"
        }
    }
}
```
Далее при необходимости нужно настроить интерфейсные конфигурации IP-адресов подсистемы балансировки нагрузки для IPv4 и IPv6:

```json
"frontendIPConfigurations": [
    {
        "name": "LoadBalancerFrontEndIPv6",
        "properties": {
            "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('ipv6PublicIPAddressName'))]"
            }
        }
    }
]
```
Определите необходимые пулы серверной части:
```json
"backendAddressPools": [
    {
        "name": "BackendPoolIPv4"
    },
    {
        "name": "BackendPoolIPv6"
    }
]
```
Определите правила подсистемы балансировки нагрузки:
```json
{
    "name": "LBRuleIPv6-46000",
    "properties": {
        "frontendIPConfiguration": {
            "id": "[variables('ipv6FrontEndIPConfigID')]"
        },
        "backendAddressPool": {
            "id": "[variables('ipv6LbBackendPoolID')]"
        },
        "protocol": "tcp",
        "frontendPort": 46000,
        "backendPort": 60001,
        "probe": {
            "id": "[variables('ipv4ipv6lbProbeID')]"
        }
    }
}
```
Наконец используйте ссылку на пул IPv6 в разделе IPConfigurations сетевых свойств масштабируемого набора:
```json
{
    "name": "ipv6IPConfig",
    "properties": {
        "privateIPAddressVersion": "IPv6",
        "loadBalancerBackendAddressPools": [
            {
                "id": "[variables('ipv6LbBackendPoolID')]"
            }
        ]
    }
}
```

## <a name="public-ipv4-per-virtual-machine"></a>Общедоступный IPv4 на каждую виртуальную машину
Как правило, виртуальным машинам в масштабируемом наборе Azure не требуются собственные общедоступные IP-адреса. Для большинства сценариев наиболее безопасно, а также экономически целесообразно связать общедоступный IP-адрес с подсистемой балансировки нагрузки или отдельной виртуальной машиной (также называемой jumpbox), которая затем (при необходимости) направит входящие подключения на виртуальные машины в масштабируемом наборе (например, через правила преобразования сетевых адресов для входящих подключений).

Тем не менее, в некоторых сценариях виртуальным машинам в масштабируемом наборе обязательно нужны собственные общедоступные IP-адреса. В качестве примера можно привести игры, в которых консоли необходимо установить прямое подключение к облачной виртуальной машине, выполняющей физическую обработку игр. Другим примером можно назвать случай, когда виртуальным машинам необходимо установить внешние подключения между собой в регионах в распределенной базе данных.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Создание масштабируемого набора с общедоступным IP-адресом на виртуальную машину
Чтобы создать масштабируемый набор, который назначает общедоступный IP-адрес каждой виртуальной машине с помощью CLI 2.0, добавьте параметр _--public-ip-per-vm_ в команду _vmss create_. 

Чтобы создать масштабируемый набор с помощью шаблона Azure, версия API ресурса Microsoft.Compute/virtualMachineScaleSets должна быть по крайней мере 2017-03-30. Добавьте свойство JSON _publicIpAddressConfiguration_ в раздел ipConfigurations масштабируемого набора. Например:

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
Чтобы получить список общедоступных IP-адресов, назначенных виртуальным машинам в масштабируемом наборе, с помощью CLI 2.0, используйте команду _az vmss list-instance-public-ips_.

Вы также можете запросить общедоступные IP-адреса, назначенные виртуальным машинам в масштабируемом наборе, с помощью [обозревателя ресурсов Azure](https://resources.azure.com) или Azure REST API версии _2017-03-30_ и выше.

Чтобы просмотреть общедоступные IP-адреса для масштабируемого набора с помощью обозревателя ресурсов, ознакомьтесь с разделом _publicipaddresses_ в масштабируемом наборе. Например, https://resources.azure.com/subscriptions/_ИД_подсистемы_/resourceGroups/_ваша_группа_ресурсов_/providers/Microsoft.Compute/virtualMachineScaleSets/_ваша_vmss_/publicipaddresses

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

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в статье [Виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md).

