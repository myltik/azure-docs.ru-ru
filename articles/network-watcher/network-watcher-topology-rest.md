---
title: Просмотр топологии Наблюдателя за сетями (REST API) | Документация Майкрософт
description: В этой статье вы узнаете, как создать запрос к топологии сети с помощью REST API.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: de9af643-aea1-4c4c-89c5-21f1bf334c06
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fefeae4e816994d3ee69d6ac1c1cbe6cf8bbd06e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
ms.locfileid: "23036679"
---
# <a name="view-network-watcher-topology-with-rest-api"></a>Просмотр топологии Наблюдателя за сетями с помощью REST API

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

В этом сценарии вы получите сведения о топологии. Чтобы вызвать REST API при помощи командлетов PowerShell, вам потребуется ARMClient. Пакет ARMClient можно скачать на сайте [Chocolatey](https://chocolatey.org/packages/ARMClient).

В этом сценарии предполагается, что вы создали Наблюдатель за сетями в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями).

## <a name="scenario"></a>Сценарий

В сценарии, описанном в этой статье, вы получаете ответ топологии для указанной группы ресурсов.

## <a name="log-in-with-armclient"></a>выполните вход с помощью ARMClient;

Войдите в ARMClient, используя учетные данные Azure.

```PowerShell
armclient login
```

## <a name="retrieve-topology"></a>Получение топологии

Ниже приведен пример кода, используемого для запроса топологии из REST API.  Он параметризован таким образом, чтобы обеспечить гибкость при создании примера.  Замените значения в угловых скобках (\< \>).

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>" # Resource group name to run topology on
$NWresourceGroupName = "<resource group name>" # Network Watcher resource group name
$networkWatcherName = "<network watcher name>"
$requestBody = @"
{
    'targetResourceGroupName': '${resourceGroupName}'
}
"@

armclient POST "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/topology?api-version=2016-07-01" $requestBody
```

Ниже приведен пример сокращенного ответа, возвращенного при получении топологии для группы ресурсов.

```json
{
  "id": "ecd6c860-9cf5-411a-bdba-512f8df7799f",
  "createdDateTime": "2017-01-18T04:13:07.1974591Z",
  "lastModified": "2017-01-17T22:11:52.3527348Z",
  "resources": [
    {
      "name": "virtualNetwork1",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{virtualNetworkName}",
      "location": "westcentralus",
      "associations": [
        {
          "name": "{subnetName}",
          "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/(virtualNetworkName)/subnets
/{subnetName}",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "webtestnsg-wjplxls65qcto",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}
s65qcto",
      "associationType": "Associated"
    },
    ...
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в статье [Визуализация журналов потоков для групп безопасности сети с помощью Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

