---
title: Просмотр топологии Наблюдателя за сетями (PowerShell) | Документация Майкрософт
description: В этой статье вы узнаете, как создать запрос к топологии сети с помощью PowerShell.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 84e925b4461e55e570e9848bf03d3d352bfff898
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
ms.locfileid: "23036959"
---
# <a name="view-network-watcher-topology-with-powershell"></a>Просмотр топологии Наблюдателя за сетями с помощью PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

Возможность просмотра топологии Наблюдателя за сетями позволяет получить визуальное представление сетевых ресурсов в подписке. На портале эта визуализация отображается автоматически. Сведения в представлении топологии на портале можно получить с помощью PowerShell.
Эта возможность обеспечивает адаптивность данных, так как в таком случае их можно использовать в других средствах для создания визуализации.

Взаимодействие моделируется по двум связям.

- **Вложение.** Например, виртуальная сеть содержит подсеть, которая, в свою очередь, содержит сетевую карту.
- **Связь.** Например, сетевая карта связана с виртуальной машиной.

В следующем списке представлены свойства, которые возвращаются при запросе REST API топологии.

* **name** — имя группы ресурсов.
* **id** — универсальный код ресурса (URI) группы ресурсов.
* **location** — расположение ресурса.
* **associations** — список связей объекта, на который указывает ссылка.
    * **name** — имя ресурса, на который указывает ссылка.
    * **resourceId** — универсальный код ресурса (URI), на который ссылается связь.
    * **associationType** — значение, которое ссылается на связь между дочерним и родительским объектом. Допустимые значения: **Contains** или **Associated**.

## <a name="before-you-begin"></a>Перед началом работы

В этом сценарии командлет `Get-AzureRmNetworkWatcherTopology` используется для получения сведений о топологии. Вы также можете просмотреть статью о том, как [получить сведения о топологии сети с помощью REST API](network-watcher-topology-rest.md).

В этом сценарии предполагается, что вы создали Наблюдатель за сетями в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями).

## <a name="scenario"></a>Сценарий

В сценарии, описанном в этой статье, вы получаете ответ топологии для указанной группы ресурсов.

## <a name="retrieve-network-watcher"></a>Извлечение Наблюдателя за сетями

Сначала необходимо получить экземпляр Наблюдателя за сетями. Переменная `$networkWatcher` передается в командлет `Get-AzureRmNetworkWatcherTopology`.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>Получение топологии

Командлет `Get-AzureRmNetworkWatcherTopology` получает сведения о топологии для указанной группы ресурсов.

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>Результаты

В возвращенных результатах имеется имя свойства Resources, в котором содержится текст ответа JSON для командлета `Get-AzureRmNetworkWatcherTopology`.  Ответ содержит ресурсы в группе безопасности сети и их связи (то есть значения Contains, Associated).

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в статье [Визуализация журналов потоков для групп безопасности сети с помощью Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).


