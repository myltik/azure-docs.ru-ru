---
title: Просмотр топологии Наблюдателя за сетями (Azure CLI) | Документация Майкрософт
description: В этой статье вы узнаете, как создать запрос к топологии сети с помощью Azure CLI.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 654c23e567d81bff1cb0c3091ba3d8f96f0a3eda
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
ms.locfileid: "23037109"
---
# <a name="view-network-watcher-topology-with-azure-cli"></a>Просмотр топологии Наблюдателя за сетями с помощью Azure CLI

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

Возможность просмотра топологии Наблюдателя за сетями позволяет получить визуальное представление сетевых ресурсов в подписке. На портале эта визуализация отображается автоматически. Сведения в представлении топологии на портале можно получить с помощью PowerShell.
Эта возможность обеспечивает адаптивность данных, так как в таком случае их можно использовать в других средствах для создания визуализации.

В этой статье используется кроссплатформенной Azure CLI 1.0, доступный для Windows, Mac и Linux. Наблюдатель за сетями в настоящее время использует Azure CLI 1.0 в качестве интерфейса командной строки.

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

В этом сценарии командлет `network watcher topology` используется для получения сведений о топологии. Вы также можете просмотреть статью о том, как [получить сведения о топологии сети с помощью REST API](network-watcher-topology-rest.md).

В этом сценарии предполагается, что вы создали Наблюдатель за сетями в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями).

## <a name="scenario"></a>Сценарий

В сценарии, описанном в этой статье, вы получаете ответ топологии для указанной группы ресурсов.

## <a name="retrieve-topology"></a>Получение топологии

Командлет `network watcher topology` получает сведения о топологии для указанной группы ресурсов. Добавьте аргумент --json, чтобы просмотреть выходные данные в формате JSON.

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>Результаты

В возвращенных результатах имеется имя свойства Resources, в котором содержится текст ответа JSON для командлета `network watcher topology`.  Ответ содержит ресурсы в группе безопасности сети и их связи (то есть значения Contains, Associated).

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о правилах безопасности, применяемых к сетевым ресурсам, см. в статье [Security group view overview](network-watcher-security-group-view-overview.md) (Обзор представления группы безопасности).
