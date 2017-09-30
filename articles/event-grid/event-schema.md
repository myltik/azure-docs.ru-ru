---
title: "Схема событий службы \"Сетка событий Azure\""
description: "Описание свойств для событий, используемых со службой \"Сетка событий Azure\""
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/18/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: a61357b6ba75566e0ad4d3300cc602333ece0563
ms.contentlocale: ru-ru
ms.lasthandoff: 09/20/2017

---

# <a name="azure-event-grid-event-schema"></a>Схема событий службы "Сетка событий Azure"

В этой статье описаны свойства и схема для событий. События включают пять обязательных свойств строки и обязательный объект данных. Свойства являются общими для всех событий от любого издателя. Объект данных содержит свойства, характерные для каждого издателя. В случае с системными разделами эти свойства относятся к поставщику ресурсов, включая службу хранилища Azure или концентраторы событий Azure.

События отправляются в службу "Сетка событий Azure" в массиве, который может содержать несколько объектов событий. Если отправляется только одно событие, длина массива составляет 1. Общий размер массива не может превышать 1 МБ. Размер каждого события в массиве ограничен 64 КБ.
 
## <a name="event-properties"></a>Свойства события

Все события будут содержать следующие одинаковые данные верхнего уровня:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| Раздел | string | Полный путь к ресурсу для источника событий. Это поле защищено от записи. |
| subject | string | Определенный издателем путь к субъекту событий. |
| eventType | string | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | string | Время создания события с учетом времени поставщика в формате UTC. |
| id | string | Уникальный идентификатор события. |
| data | object | Данные события, относящиеся к поставщику ресурсов. |

## <a name="available-event-sources"></a>Доступные источники событий

Следующие источники событий публикуют события для использования с помощью службы "Сетка событий Azure":

* Группы ресурсов (операции управления)
* Подписки Azure (операции управления)
* Концентраторы событий
* Пользовательские разделы

## <a name="azure-subscriptions"></a>Подписки Azure

Подписки Azure теперь могут создавать события управления из Azure Resource Manager, например при создании виртуальной машины или удалении учетной записи хранения.

### <a name="available-event-types"></a>Доступные типы событий

- **Microsoft.Resources.ResourceWriteSuccess**: возникает при успешном создании или обновлении ресурса.  
- **Microsoft.Resources.ResourceWriteFailure**: возникает при ошибке создания или обновления ресурса.  
- **Microsoft.Resources.ResourceWriteCancel**: возникает при отмене создания или обновления ресурса.  
- **Microsoft.Resources.ResourceDeleteSuccess**: возникает при успешном удалении ресурса.  
- **Microsoft.Resources.ResourceDeleteFailure**: возникает при ошибке удаления ресурса.  
- **Microsoft.Resources.ResourceDeleteCancel**: возникает при отмене удаления ресурса. Это происходит при отмене развертывания шаблона.

### <a name="example-event-schema"></a>Пример схемы событий

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
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



## <a name="resource-groups"></a>Группы ресурсов

Группы ресурсов теперь могут создавать события управления из Azure Resource Manager, например при создании виртуальной машины или удалении учетной записи хранения.

### <a name="available-event-types"></a>Доступные типы событий

- **Microsoft.Resources.ResourceWriteSuccess**: возникает при успешном создании или обновлении ресурса.  
- **Microsoft.Resources.ResourceWriteFailure**: возникает при ошибке создания или обновления ресурса.  
- **Microsoft.Resources.ResourceWriteCancel**: возникает при отмене создания или обновления ресурса.  
- **Microsoft.Resources.ResourceDeleteSuccess**: возникает при успешном удалении ресурса.  
- **Microsoft.Resources.ResourceDeleteFailure**: возникает при ошибке удаления ресурса.  
- **Microsoft.Resources.ResourceDeleteCancel**: возникает при отмене удаления ресурса. Это происходит при отмене развертывания шаблона.

### <a name="example-event"></a>Пример события

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
        "httpRequest":"",
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



## <a name="event-hubs"></a>Концентраторы событий

События концентраторов событий сейчас создаются, только когда файл автоматически отправляется в хранилище с помощью функции записи.

### <a name="available-event-types"></a>Доступные типы событий

- **Microsoft.EventHub.CaptureFileCreated**: возникает при создании файла записи.

### <a name="example-event"></a>Пример события

Этот пример события демонстрирует схему события концентраторов событий, возникающего, когда функция записи сохраняет файл: 

```json
[
    {
        "topic": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{event-hubs-ns}",
        "subject": "eventhubs/eh1",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-07-11T00:55:55.0120485Z",
        "id": "bd440490-a65e-4c97-8298-ef1eb325673c",
        "data": {
            "fileUrl": "https://gridtest1.blob.core.windows.net/acontainer/eventgridtest1/eh1/1/2017/07/11/00/54/54.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 0,
            "eventCount": 0,
            "firstSequenceNumber": -1,
            "lastSequenceNumber": -1,
            "firstEnqueueTime": "0001-01-01T00:00:00",
            "lastEnqueueTime": "0001-01-01T00:00:00"
        },
    }
]

```


## <a name="azure-blob-storage"></a>Хранилище больших двоичных объектов Azure

>[!IMPORTANT]
>Для использования событий хранилища BLOB-объектов следует зарегистрироваться для предварительной версии событий хранилища BLOB-объектов. Дополнительные сведения о программе предварительной версии см. в статье [Реагирование на события хранилища BLOB-объектов (предварительная версия)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview#join-the-preview).  


### <a name="available-event-types"></a>Доступные типы событий

- **Microsoft.Storage.BlobCreated**: возникает при создании большого двоичного объекта.
- **Microsoft.Storage.BlobDeleted**: возникает при удалении большого двоичного объекта.

### <a name="example-event"></a>Пример события

Этот пример события демонстрирует схему события хранилища, возникающего при создании большого двоичного объекта: 

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
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
]
```




## <a name="custom-topics"></a>Пользовательские разделы

Полезные данные пользовательских событий определяются пользователем. Они могут быть представлены любым правильно отформатированным объектом JSON. Данные верхнего уровня должны содержать те же поля, что и стандартные определенные события ресурса. При публикации событий в настраиваемые разделы рекомендуется моделировать субъект событий для маршрутизации и фильтрации.

### <a name="example-event"></a>Пример события

В следующем примере показано событие для пользовательского раздела:
````json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.EventGrid/topics/myeventgridtopic",
    "subject": "/myapp/vehicles/motorcycles",    
    "id": "b68529f3-68cd-4744-baa4-3c0498ec19e2",
    "eventType": "recordInserted",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "data":{
      "make": "Ducati",
      "model": "Monster"
    }
  }
]

````

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о Сетке событий Azure см. в [этой статье](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).

