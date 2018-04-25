---
title: Поиск следующего прыжка с помощью возможности определения следующего прыжка Наблюдателя за сетями Azure (REST) | Документация Майкрософт
description: В этой статье описано, как определить тип следующего прыжка и IP-адрес, используя возможность определения следующего прыжка с помощью Azure REST API.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b541cd5cb7e49468af2c522b16c3a3b9fe75fd54
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2018
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>Определите тип следующего прыжка, используя возможность определения следующего прыжка Наблюдателя за сетями с помощью Azure REST API.

> [!div class="op_single_selector"]
> - [портал Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Определение следующего прыжка — это возможность Наблюдателя за сетями, позволяющая определить тип следующего прыжка и IP-адрес на основе указанной виртуальной машины. Эта возможность используется, чтобы определить путь передачи исходящего трафика виртуальной машины (шлюз, Интернет или виртуальные сети) к месту назначения.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы вызвать REST API при помощи командлетов PowerShell, вам потребуется ARMClient. Пакет ARMClient можно скачать на сайте [Chocolatey](https://chocolatey.org/packages/ARMClient).

В этом сценарии предполагается, что вы создали Наблюдатель за сетями в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями).

## <a name="scenario"></a>Сценарий

В сценарии, описанном в этой статье, используется определение следующего прыжка — возможность Наблюдателя за сетями, которая позволяет определить тип следующего перехода и IP-адрес для ресурса. Дополнительные сведения об определении следующего прыжка см. в [этой статье](network-watcher-next-hop-overview.md).

Вам предстоит:

* получить следующий прыжок для виртуальной машины.

## <a name="log-in-with-armclient"></a>выполните вход с помощью ARMClient;

Войдите в ARMClient, используя учетные данные Azure.

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Получение виртуальной машины

Выполните следующий скрипт, чтобы получить сведения о виртуальной машине. Эти данные потребуются при выполнении следующего прыжка.

Выполните приведенный ниже код, указав в нем значения следующих переменных:

- **subscriptionId** — идентификатор используемой подписки.
- **resourceGroupName** — имя группы ресурсов, в которой содержатся виртуальные машины.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Идентификатор виртуальной машины используется в следующем примере:

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>Получение следующего прыжка

После создания заголовка авторизации вы можете получить следующий прыжок для виртуальной машины. Чтобы выполнить пример кода, необходимо заменить приведенные ниже значения.

> [!Important]
> При вызовах REST API в URI запроса следует указать имя группы ресурсов, в которой содержится Наблюдатель за сетями, а не диагностируемые ресурсы.

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> Для получения следующего прыжка необходимо, чтобы ресурс виртуальной машины был выделен.

## <a name="results"></a>Результаты

Ниже приведен пример выходных данных. Результаты содержат следующие значения:

* **nextHopType.** Этот параметр может иметь следующие значения: Internet, VirtualAppliance, VirtualNetworkGateway, VnetLocal, HyperNetGateway или None.
* **nextHopIpAddress.** IP-адрес следующего прыжка.
* **routeTableId.** Возможные значения: универсальный код ресурса (URI) таблицы маршрутизации, связанной с маршрутом, или *системный маршрут*, если не задан определяемый пользователем маршрут.

Результаты возвращаются в формате JSON.

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>Дополнительная информация

Определив следующий прыжок для виртуальной машины, вы можете просмотреть параметры безопасности сетевых ресурсов. Дополнительные сведения см. в [этой статье](network-watcher-security-group-view-overview.md).














