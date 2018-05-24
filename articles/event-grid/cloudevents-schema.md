---
title: Использование службы "Сетка событий Azure" с событиями в схеме CloudEvents
description: В этой статье описана установка схемы CloudEvents для событий в службе "Сетка событий Azure".
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: babanisa
ms.openlocfilehash: 23187fbc230e384984085d330bfbfbc90cc9f945
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Использование схемы CloudEvents со службой "Сетка событий"

В дополнение к [схеме событий по умолчанию](event-schema.md), служба "Сетка событий Azure" поддерживает события в [схеме CloudEvents JSON](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](http://cloudevents.io/) — [открытая стандартная спецификация](https://github.com/cloudevents/spec/blob/master/spec.md) для описания данных событий в общем формате.

CloudEvents упрощает взаимодействие, предоставляя общую схему событий для публикации и использования событий на основе облака. Эта схема предоставляет обычные средства, стандартные способы маршрутизации и обработки событий, а также универсальные методы десериализации внешней схемы событий. Общая схема позволяет легко интегрировать работу на разных платформах.

Создание CloudEvents — это [совместная работа](https://github.com/cloudevents/spec/blob/master/community/contributors.md) нескольких компаний, включая корпорацию Майкрософт и компанию [Cloud Native Compute Foundation](https://www.cncf.io/). В настоящее время доступна версия 0.1.

В этой статье описывается использование схемы CloudEvents со службой "Сетка событий".

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Схема CloudEvent

Ниже приведен пример события хранилища BLOB-объектов Azure в формате CloudEvents.

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
}
```

Для CloudEvents v0.1 доступны следующие свойства.

| CloudEvents        | type     | Пример значения JSON             | ОПИСАНИЕ                                                        | Сопоставление службы "Сетка событий"
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| eventType          | Строка   | "com.example.someevent"          | Тип вхождений                                   | eventType
| eventTypeVersion   | Строка   | "1.0"                            | Версия eventType (необязательно)                            | dataVersion
| cloudEventsVersion | Строка   | "0.1"                            | Версия спецификации CloudEvents, которую использует событие        | *сквозное*
| источник             | URI      | "/mycontext"                     | Описание источника событий                                       | topic#subject
| eventID            | Строка   | "1234-1234-1234"                 | Идентификатор события                                                    | id
| eventTime          | Timestamp| "2018-04-05T17:31:00Z"           | Метка времени, когда произошло событие (необязательно)                    | eventTime
| schemaURL          | URI      | "https://myschema.com"           | Ссылка на схему, к которой привязан атрибут данных (необязательно) | *не используется*
| сontentType        | Строка   | "application/json"               | Описание формата кодирования данных (необязательно)                       | *не используется*
| extensions         | Сопоставление      | { "extA": "vA", "extB", "vB" }  | Любые дополнительные метаданные (необязательно)                                 | *не используется*
| data               | Объект.   | { "objA": "vA", "objB", "vB" }  | Полезные данные события (необязательно)                                       | data

Дополнительные сведения см. в разделе [Context Attributes](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes) (Атрибуты контекста).

## <a name="configure-event-grid-for-cloudevents"></a>Настройка сетки событий для CloudEvents

В настоящее время служба "Сетка событий Azure" поддерживает предварительную версию ввода и вывода данных в формате JSON CloudEvents в **центрально-западной части США**, **центральной части США** и **Северной Европе**.

Службу "Сетка событий" можно использовать для ввода и вывода событий в схеме CloudEvents. Кроме того, CloudEvents можно использовать для системных событий, таких как события хранилища BLOB-объектов и Центра Интернета вещей, а также настраиваемые события. CloudEvents также может преобразовать эти события (в другой формат и обратно) при передаче по сети.


| Схема ввода       | Схема вывода
|--------------------|---------------------
| Формат CloudEvents | Формат CloudEvents
| Формат сетки событий  | Формат CloudEvents
| Формат CloudEvents | Формат сетки событий
| Формат сетки событий  | Формат сетки событий

Для всех схем событий служба "Сетка событий" требует проверки при публикации в разделе сетки событий и создании подписки на события. Дополнительные сведения см. в разделе [Сетка событий: безопасность и проверка подлинности](security-authentication.md).

### <a name="input-schema"></a>Схема ввода

Чтобы установить схему ввода в настраиваемый раздел в CloudEvents, при создании раздела используйте в Azure CLI следующий параметр: `--input-schema cloudeventv01schema`. Теперь настраиваемый раздел ожидает входящие события в формате CloudEvents v0.1.

Чтобы создать раздел службы сетки событий, выполните следующую команду.

```azurecli
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

Текущая версия CloudEvents не поддерживает пакетную обработку событий. При публикации в разделе с помощью схемы CloudEvent необходимо публиковать каждое событие отдельно.

### <a name="output-schema"></a>Схема вывода

Чтобы установить схему вывода CloudEvents в подписке на события, во время создания подписки на события используйте в Azure CLI следующий параметр: `--event-delivery-schema cloudeventv01schema`. События для этой подписки на события доставляются в формате CloudEvents v0.1.

Чтобы создать подписку на события, выполните следующую команду:

```azurecli
az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

Текущая версия CloudEvents не поддерживает пакетную обработку событий. Подписка на события, настроенная для схемы CloudEvent, получает каждое событие отдельно.

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь со сведениями о [мониторинге доставки сообщений в службе "Сетка событий"](monitor-event-delivery.md).
* Мы рекомендуем вам протестировать CloudEvents и [внести свой вклад](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) в усовершенствование схемы, оставляя комментарии.
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
