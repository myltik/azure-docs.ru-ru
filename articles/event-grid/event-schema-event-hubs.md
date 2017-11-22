---
title: "Схема событий концентраторов в службе \"Сетка событий Azure\""
description: "Описание свойств для событий концентраторов, используемых со службой \"Сетка событий Azure\""
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: tomfitz
ms.openlocfilehash: 80959ee589a1cfcf317a98c3bafd7f92c796fc2d
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Схема событий службы "Сетка событий Azure" для концентраторов

В этой статье описаны свойства и схема для событий концентраторов. Основные сведения о схеме событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

### <a name="available-event-types"></a>Доступные типы событий

Тип события **Microsoft.EventHub.CaptureFileCreated** возникает в службе "Концентраторы событий" при создании файла записи.

## <a name="example-event"></a>Пример события

Этот пример события демонстрирует схему события концентраторов, возникающего, когда файл сохраняется при помощи функции записи: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="event-properties"></a>Свойства события

Событие содержит следующие данные верхнего уровня:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| Раздел | string | Полный путь к ресурсу для источника событий. Это поле защищено от записи. |
| subject | string | Определенный издателем путь к субъекту событий. |
| eventType | string | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | string | Время создания события с учетом времени поставщика в формате UTC. |
| id | string | Уникальный идентификатор события. |
| data | object | Данные события концентратора. |

Объект данных имеет следующие свойства:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| fileUrl | string | Путь к файлу записи. |
| fileType | string | Тип файла записи. |
| partitionId | string | Идентификатор сегмента. |
| sizeInBytes | целое число | Размер файла. |
| eventCount | целое число | Число событий в файле. |
| firstSequenceNumber | целое число | Наименьший порядковый номер в очереди. |
| lastSequenceNumber | целое число | Последний порядковый номер в очереди. |
| firstEnqueueTime | string | Первые данные времени в очереди. |
| lastEnqueueTime | string | Последние данные времени в очереди. |

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о службе "Сетка событий Azure" см. в [этой статье](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
* Сведения об обработке событий в концентраторах см. в статье [Потоковая передача больших данных в хранилище данных](event-grid-event-hubs-integration.md).