---
title: Схемы службы "Сетка событий Azure" для событий Служб мультимедиа
description: В этой статье приведены свойства событий Служб мультимедиа, используемых со службой "Сетка событий Azure".
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782663"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Схемы службы "Сетка событий Azure" для событий Служб мультимедиа

В этой статье приведены схемы и свойства событий Служб мультимедиа.

## <a name="media-services-job-state-change-event"></a>Событие изменения состояния задания Служб мультимедиа

В этой статье приведены схема и свойства события изменения состояния задания Служб мультимедиа.  

### <a name="available-event-types"></a>Доступные типы событий

**Задание** Служб мультимедиа выдает следующие типы событий:

| Тип события | ОПИСАНИЕ |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Вызывается после изменения состояния задания. |

### <a name="example-event"></a>Пример события

В следующем примере показана схема завершенного события изменения состояния задания: 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>Свойства события

Событие содержит следующие высокоуровневые данные:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| Раздел | строка | Это значение предоставляет служба "Сетка событий". |
| subject | строка | Путь к ресурсу в ресурсе учетной записи Служб мультимедиа. |
| eventType | строка | Один из зарегистрированных типов событий для этого источника событий. Пример: Microsoft.Media.JobStateChange. |
| eventTime | строка | Время создания события с учетом времени поставщика в формате UTC. |
| id | строка | Уникальный идентификатор события. |
| data | object | Данные события Служб мультимедиа. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| previousState | строка | Состояние задания перед событием. |
| state | строка | Новое состояние задания в этом событии. Пример: Queued: The Job is awaiting resources (В очереди: задание ожидает ресурсы) или (Запланировано: задание готово к запуску).|

Возможные значения состояния задания: *В очереди*, *Запланировано*, *Обработка*, *Завершено*, *Ошибка*, *Отменено*, *Отмена*.

## <a name="next-steps"></a>Дополнительная информация

[Зарегистрируйтесь на получение событий изменения состояния задания](job-state-events-cli-how-to.md).
