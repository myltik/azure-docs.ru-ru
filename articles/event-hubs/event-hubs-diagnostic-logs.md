---
title: "Журналы диагностики концентраторов событий Azure | Документация Майкрософт"
description: "Узнайте, как анализировать журналы диагностики из концентраторов событий в Microsoft Azure."
keywords: 
documentationcenter: 
services: event-hubs
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 4d7d0e1f5ffb395bf91bbdac1ab4b9ec3f9dee1c
ms.openlocfilehash: cb8c7930ee423543c91366de64220ee55609a4cf


---
# <a name="event-hub-diagnostic-logs"></a>Журналы диагностики концентраторов событий

## <a name="introduction"></a>Введение
Концентраторы событий предоставляют журналы двух типов: 
* [журналы действий](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), которые всегда включены и содержат информацию об операциях, выполняемых заданиями;
* [журналы диагностики](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), которые настраиваются пользователем и содержат более полную информацию обо всем, что происходит с заданием после его создания, при обновлении, выполнении и вплоть до удаления.

## <a name="how-to-enable-diagnostic-logs"></a>Как включить журналы диагностики
По умолчанию журналы диагностики **отключены**. Чтобы включить их, выполните следующее.

Войдите на портал Azure и перейдите к колонке задания потоковой передачи. Выберите колонку "Журналы диагностики" в разделе "Мониторинг".

![Перемещение к колонке журналов диагностики](./media/event-hubs-diagnostic-logs/image1.png)  

Затем щелкните ссылку "Включить диагностику".

![Включение журналов диагностики](./media/event-hubs-diagnostic-logs/image2.png)

В появившемся окне "Диагностика" измените состояние на "Включено".

![Изменение состояния журналов диагностики](./media/event-hubs-diagnostic-logs/image3.png)

Настройте требуемую цель архивации (учетная запись хранения, концентратор событий, Log Analytics) и выберите категории журналов, которые нужно собирать ("Выполнение", "Разработка"). Сохраните новую конфигурацию системы диагностики.

После сохранения конфигурация вступит в силу примерно через 10 минут. После этого журналы начнут отображаться в настроенной цели архивации, которую можно просмотреть в колонке "Журналы диагностики".

Дополнительные сведения о настройке системы диагностики доступны на странице про [журналы диагностики](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostic-logs-categories"></a>Категории журналов диагностики
Существует две категории журналов диагностики, которые в настоящее время можно собирать:

* **ArchivalLogs**: записывает в журнал события, связанные с архивом концентратора событий, в частности, с ошибками архива.
* **OperationalLogs**: записывает в журнал события, связанные с операциями концентратора событий, в частности с типом операций, например созданием концентратора событий, используемыми ресурсами, а также с состоянием операций.

## <a name="diagnostic-logs-schema"></a>Схема журналов диагностики
Все журналы хранятся в формате JSON, и каждая запись содержит строковые поля, имеющие следующий формат:


### <a name="archive-error-schema"></a>Схема ошибок архива
Имя | Описание
------- | -------
TaskName | Описание задачи, завершившейся сбоем
ActivityId | Внутренний идентификатор для отслеживания
trackingId | Внутренний идентификатор для отслеживания
resourceId | Идентификатор ресурса ARM
eventHub | Полное имя концентратора событий (включает в себя имя пространства имен)
partitionId | Идентификатор секции, записываемый в концентратор событий
archiveStep | ArchiveFlushWriter
startTime | Время начала сбоя
failures | Количество произошедших сбоев
durationInSeconds | Продолжительность сбоя
Message | Сообщение об ошибке
category | ArchiveLogs

#### <a name="example-archive-log"></a>Пример журнала архивирования

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

### <a name="operation-logs-schema"></a>Схема журналов операций
Имя | Описание
------- | -------
ActivityId | Внутренний идентификатор для отслеживания
EventName | Имя операции           
resourceId | Идентификатор ресурса ARM
SubscriptionId | идентификатор подписки;
EventTimeString | Время операции
EventProperties | Свойства операции
Состояние | Состояние операции
Caller | Объект, вызывающий операцию (портал или клиент управления)
category | OperationalLogs

#### <a name="example-operation-log"></a>Пример журнала операций

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

## <a name="next-steps"></a>Дальнейшие действия
* [Что такое концентраторы событий Azure?](event-hubs-what-is-event-hubs.md)
* [Общие сведения об API концентраторов событий](event-hubs-api-overview.md)
* [Приступая к работе с концентраторами событий](event-hubs-csharp-ephcs-getstarted.md)


<!--HONumber=Feb17_HO1-->


