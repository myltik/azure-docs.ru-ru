## <a name="nic"></a>Сетевая карта
Ресурс сетевой карты обеспечивает сетевое взаимодействие с существующей подсетью в ресурсе виртуальной сети. Хотя вы можете создать сетевую карту как отдельный объект, ее необходимо связать с другим объектом, чтобы обеспечить возможность связи. Сетевую карту можно использовать для подключения виртуальной машины к подсети, общему IP-адресу или балансировщику нагрузки.  

| Свойство | Описание | Примеры значений |
| --- | --- | --- |
| **virtualMachine** |Виртуальная машина, с которой сопоставлена сетевая карта. |/subscriptions/{guid}/../Microsoft.Compute/virtualMachines/vm1 |
| **macAddress** |MAC-адрес для сетевой карты |любое значение от 4 до 30 |
| **networkSecurityGroup** |Группа NSG, связанная с сетевой картой |/subscriptions/{guid}/../Microsoft.Network/networkSecurityGroups/myNSG1 |
| **dnsSettings** |Параметры DNS для сетевой карты. |См. раздел [Общедоступный IP-адрес](#Public-IP-address). |

Сетевая карта, или сетевой адаптер, представляет сетевой интерфейс, который может быть связан с виртуальной машиной. У виртуальной машины может быть одна или несколько сетевых карт.

![Сетевая карта на одной виртуальной машине](./media/resource-groups-networking/Figure3.png)

### <a name="ip-configurations"></a>Конфигурации IP
У сетевых карт есть дочерний объект с именем **ipConfigurations** , содержащий следующие свойства:

| Свойство | Описание | Примеры значений |
| --- | --- | --- |
| **subnet** |Подсеть, к которой подключена сетевая карта. |/subscriptions/{guid}/../Microsoft.Network/virtualNetworks/myvnet1/subnets/mysub1 |
| **privateIPAddress** |IP-адрес для сетевой карты в подсети |10.0.0.8 |
| **privateIPAllocationMethod** |Метод выделения IP-адресов |Динамический или статический |
| **enableIPForwarding** |Можно ли использовать сетевую карту для маршрутизации |Значение true или false |
| **primary** |Является ли эта сетевая карта основной для виртуальной машины |Значение true или false |
| **publicIPAddress** |PIP, сопоставленный с сетевой картой |См. раздел [Параметры DNS](#DNS-settings). |
| **loadBalancerBackendAddressPools** |Пулы адресов серверной части, с которыми сопоставлена сетевая карта | |
| **loadBalancerInboundNatRules** |Правила входящего трафика NAT для балансировщика нагрузки, с которыми сопоставлена сетевая карта | |

Пример общедоступного IP-адреса в формате JSON:

    {
        "name": "lb-nic1-be",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkInterfaces",
        "location": "eastus",
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "ipConfigurations": [
                {
                    "name": "NIC-config",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
                    "etag": "W/\"0027f1a2-3ac8-49de-b5d5-fd46550500b1\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "privateIPAddress": "10.0.0.4",
                        "privateIPAllocationMethod": "Dynamic",
                        "subnet": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
                            }
                        ]
                    }
                }
            ],
            "dnsSettings": { ... },
            "macAddress": "00-0D-3A-10-F1-29",
            "enableIPForwarding": false,
            "primary": true,
            "virtualMachine": {
                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
            }
        }
    }

### <a name="additional-resources"></a>Дополнительные ресурсы
* Прочитайте [справочную документацию по REST API](https://msdn.microsoft.com/library/azure/mt163579.aspx) для сетевых карт.

