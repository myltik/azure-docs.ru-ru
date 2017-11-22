---
title: "Схема событий группы ресурсов службы \"Сетка событий Azure\""
description: "Описание свойств для событий группы ресурсов, используемых со службой \"Сетка событий Azure\""
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: c7435d87f9aaa906c3f6758186b64f3458cb9716
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Схема событий службы "Сетка событий Azure" для групп ресурсов

В этой статье описаны свойства и схема для событий группы ресурсов. См. общие сведения о [схеме событий службы "Сетка событий Azure"](event-schema.md).

Подписки и группы ресурсов Azure выдают одинаковые типы событий. Типы событий связаны с изменениями в ресурсах. Основное отличие заключается в том, что группы ресурсов выдают события для ресурсов в пределах группы ресурсов, а подписки Azure — для ресурсов в пределах всей подписки. 

## <a name="available-event-types"></a>Доступные типы событий

Группы ресурсов выдают события управления из Azure Resource Manager, например при создании виртуальной машины или удалении учетной записи хранения.

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | Возникает при успешном создании или обновлении ресурса. |
| Microsoft.Resources.ResourceWriteFailure | Возникает при неудачном создании или обновлении ресурса. |
| Microsoft.Resources.ResourceWriteCancel | Возникает при отмене создания или обновления ресурса. |
| Microsoft.Resources.ResourceDeleteSuccess | Возникает при успешном удалении ресурса. |
| Microsoft.Resources.ResourceDeleteFailure | Возникает при неудачном удалении ресурса. |
| Microsoft.Resources.ResourceDeleteCancel | Возникает при отмене удаления ресурса. Это событие происходит при отмене развертывания шаблона. |

## <a name="example-event"></a>Пример события

В следующем примере показана схема события создания ресурса: 

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```

Схема события удаления ресурса аналогична:

```json
[{
  "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  }
}]
```

## <a name="event-properties"></a>Свойства события

Событие содержит следующие основные данные:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| Раздел | string | Полный путь к ресурсу для источника событий. Это поле защищено от записи. |
| subject | string | Определенный издателем путь к субъекту событий. |
| eventType | string | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | string | Время создания события с учетом времени поставщика в формате UTC. |
| id | string | Уникальный идентификатор события. |
| data | object | Данные события группы ресурсов. |

Объект данных имеет следующие свойства:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| authorization | string | Запрошенная авторизация для операции. |
| claims | string | Свойства утверждений. |
| correlationId | string | Идентификатор операции для устранения неполадок. |
| httpRequest | string | Подробные сведения об операции. |
| resourceProvider | string | Поставщик ресурсов, выполняющий операцию. |
| resourceUri | string | URI ресурса в операции. |
| operationName | string | Операция, которая выполнена. |
| status | string | Состояние операции. |
| subscriptionId | string | Идентификатор подписки ресурса. |
| tenantId | string | Идентификатор клиента ресурса. |

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
