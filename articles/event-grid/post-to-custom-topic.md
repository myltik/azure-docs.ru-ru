---
title: Публикация события в пользовательском разделе для службы "Сетка событий Azure"
description: В этой статье объясняется, как опубликовать событие в пользовательском разделе для службы "Сетка событий Azure"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/05/2018
ms.author: tomfitz
ms.openlocfilehash: 1c23aef0773ffddbc26e4090ecf137b632394ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Публикация в пользовательском разделе для службы "Сетка событий Azure"

В этой статье объясняется, как опубликовать событие в пользовательском разделе. В ней также представлен формат записей и данные событий. [Соглашение об уровне обслуживания (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) применяется только к записям, которые соответствуют ожидаемому формату.

## <a name="endpoint"></a>Конечная точка

При отправке запроса HTTP POST в пользовательский раздел используйте URI в таком формате: `https://<topic-endpoint>?api-version=2018-01-01`.

Например, допустимый URI — `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Чтобы получить конечную точку для пользовательского раздела с помощью Azure CLI, используйте следующую команду:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Чтобы получить конечную точку для пользовательского раздела с помощью Azure PowerShell, используйте следующую команду:

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Заголовок

Добавьте в запрос значение заголовка `aeg-sas-key`, содержащее ключ для аутентификации.

Например, допустимое значение заголовка — `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Чтобы получить ключ для пользовательского раздела с помощью Azure CLI, используйте следующую команду:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Чтобы получить ключ для пользовательского раздела с помощью PowerShell, используйте следующую команду:

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Данные событий

Для пользовательского раздела данные верхнего уровня должны содержать те же поля, что и стандартные определенные события ресурса. Одно из этих свойств — свойство data, содержащее свойства, уникальные для пользовательского раздела. Свойства для этого объекта data определяет издатель событий. Используйте следующую схему:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Описание этих свойств см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

Ниже приведен пример допустимой схемы данных события:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Ответ

После отправки данных в конечную точку раздела вы получите ответ. Ответ — это стандартный код ответа HTTP. Ниже приведены некоторые распространенные ответы:

|Результат  |Ответ  |
|---------|---------|
|Успешно  | 200 ОК  |
|Неправильная конечная точка | 404 — не найдено |
|Недопустимый ключ доступа | 401 — недостаточно прав |
|Неправильный формат данных события | 400 — недопустимый запрос |

Текст сообщений об ошибках имеет следующий формат:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь со сведениями о [мониторинге доставки сообщений в службе "Сетка событий"](monitor-event-delivery.md).
* Дополнительные сведения о ключе аутентификации см. в статье [Сетка событий: безопасность и проверка подлинности](security-authentication.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
