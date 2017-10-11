---
title: "Схема подписки для службы \"Сетка событий Azure\""
description: "В этой статье описаны свойства для подписки на событие в службе \"Сетка событий Azure\"."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/17/2017
ms.author: babanisa
ms.openlocfilehash: eff2352066a76010d6d882a7b7e1961870cd2d46
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2017
---
# <a name="event-grid-subscription-schema"></a>Схема подписки для службы "Сетка событий"

Чтобы создать подписку для службы "Сетка событий", отправьте запрос на выполнение операции по созданию подписки на события. Используйте следующий формат:

```
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

Например, чтобы создать подписку на события для учетной записи хранения с именем `examplestorage` в группе ресурсов `examplegroup`, используйте следующий формат:

```
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

В этой статье приведены свойства и схема для основного текста запроса.
 
## <a name="event-subscription-properties"></a>Свойства подписки на события

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| ресурс destination | object | Объект, который определяет конечную точку. |
| фильтр | object | Необязательное поле для фильтрации событий по типам. |

### <a name="destination-object"></a>Объект destination

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| endpointType | string | Тип конечной точки для подписки (веб-перехватчик или HTTP, концентратор событий либо очередь). | 
| endpointUrl | string |  | 

### <a name="filter-object"></a>Объект filter

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| includedEventTypes | array | Выполняет сопоставление, если тип события, указанный в сообщении о событии, полностью соответствует одному из этих типов. Вызывает ошибку, если имя события не соответствует зарегистрированному имени типа для источника события. По умолчанию соответствует всем типам событий. |
| subjectBeginsWith | string | Фильтр соответствия префиксу для поля темы в сообщении о событии. Строка по умолчанию или пустая строка соответствует всем типам. | 
| subjectEndsWith | string | Фильтр соответствия суффиксу для поля темы в сообщении о событии. Строка по умолчанию или пустая строка соответствует всем типам. |
| subjectIsCaseSensitive | string | Управляет сопоставлением с учетом регистра в фильтрах. |


## <a name="example-subscription-schema"></a>Пример схемы подписки

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "blobCreated", "blobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "subjectIsCaseSensitive": "true"
    }
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о [службе "Сетка событий Azure"](overview.md).