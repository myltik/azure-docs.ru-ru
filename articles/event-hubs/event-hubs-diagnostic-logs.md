---
title: Журналы диагностики концентраторов событий Azure | Документация Майкрософт
description: Узнайте, как настроить журналы диагностики для концентраторов событий в Azure.
keywords: ''
documentationcenter: ''
services: event-hubs
author: banisadr
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 451fc42f573db2b60985912cfa63617e04f09e6c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
ms.locfileid: "28932721"
---
# <a name="event-hubs-diagnostic-logs"></a>Журналы диагностики концентраторов событий

Для концентраторов событий Azure можно просмотреть журналы двух типов.

* **[Журналы действий.](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)** Эти журналы содержат сведения об операциях, выполняемых с заданием. Данные журналы всегда включены.
* **[Журналы диагностики.](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)** Вы можете настроить журналы диагностики, чтобы получать подробные сведения обо всем, что происходит с заданием. Журналы диагностики охватывают действия с момента создания задания до его удаления, включая обновления и действия, которые происходят во время выполнения задания.

## <a name="enable-diagnostic-logs"></a>Включение ведения журнала диагностики для веб-приложений в службе приложений Azure

По умолчанию журналы диагностики отключены. Включение журналов диагностики

1.  На [портале Azure](https://portal.azure.com) в разделе **Мониторинг и управление** щелкните **Журналы диагностики**.

    ![Панель навигации к журналам диагностики](./media/event-hubs-diagnostic-logs/image1.png)

2.  Выберите ресурс, который необходимо отслеживать.

3.  Щелкните **Включить диагностику**.

    ![Включение журналов диагностики](./media/event-hubs-diagnostic-logs/image2.png)

4.  Для параметра **Состояние** щелкните **Вкл**.

    ![Изменение состояния журналов диагностики](./media/event-hubs-diagnostic-logs/image3.png)

5.  Настройте нужную цель для архивирования, например учетную запись хранения, концентратор событий или Azure Log Analytics.

6.  Сохраните новые параметры диагностики.

Новые параметры вступят в силу в течение 10 минут. После этого журналы появятся в настроенной цели для архивирования на панели **Журналы диагностики**.

Дополнительные сведения о настройке системы диагностики доступны в [обзоре журналов диагностики Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-categories"></a>Категории журналов диагностики

Концентраторы событий записывают журналы диагностики двух категорий.

* **Журналы архивирования.** Журналы, связанные с архивами концентраторов событий, в частности, с ошибками архива.
* **Операционные журналы.** Сведения о том, что происходит во время операций в концентраторах событий, в частности, тип операции, включая создание концентратора событий, используемые ресурсы, а также состояние операции.

## <a name="diagnostic-logs-schema"></a>Схема журналов диагностики

Все журналы хранятся в формате JSON (нотация объектов JavaScript). Каждая запись содержит строковые поля, использующие формат, описанный в следующих разделах.

### <a name="archive-logs-schema"></a>Схема архивных журналов

Строки JSON архивных журналов содержат элементы, перечисленные в следующей таблице.

ИМЯ | ОПИСАНИЕ
------- | -------
TaskName | Описание задачи, завершившейся сбоем.
ActivityId | Внутренний идентификатор, используемый для отслеживания.
trackingId | Внутренний идентификатор, используемый для отслеживания.
ResourceId | Идентификатор ресурса Azure Resource Manager.
eventHub | Полное имя концентратора событий (включает в себя имя пространства имен).
partitionId | Секция концентратора событий, в которую записываются данные.
archiveStep | ArchiveFlushWriter
startTime | Время начала сбоя.
failures | Количество произошедших сбоев.
durationInSeconds | Продолжительность сбоя.
Message | Сообщение об ошибке.
category | ArchiveLogs

Ниже приведен пример строки JSON журнала архивирования.

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>Схема операционных журналов

Строки JSON операционного журнала содержат элементы, перечисленные в приведенной ниже таблице.

ИМЯ | ОПИСАНИЕ
------- | -------
ActivityId | Внутренний идентификатор, используемый для отслеживания.
EventName | Имя операции.  
ResourceId | Идентификатор ресурса Azure Resource Manager.
SubscriptionId | Идентификатор подписки.
EventTimeString | Время операции.
EventProperties | Свойства операции.
Status | Состояние операции.
Caller | Объект, вызвавший операцию (портал Azure или клиент управления).
category | OperationalLogs

Ниже приведен пример строки JSON операционного журнала.

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Дополнительная информация
* [Что такое концентраторы событий Azure?](event-hubs-what-is-event-hubs.md)
* [Общие сведения об API концентраторов событий](event-hubs-api-overview.md)
* [Приступая к работе с концентраторами событий](event-hubs-dotnet-standard-getstarted-send.md)
