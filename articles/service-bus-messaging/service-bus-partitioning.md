---
title: "Создание секционированных очередей и разделов служебной шины Azure | Документация Майкрософт"
description: "В этой статье описывается, как секционировать очереди и разделы служебной шины с помощью нескольких брокеров сообщений."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a0c7d5a2-4876-42cb-8344-a1fc988746e7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/07/2017
ms.author: sethm;hillaryc
ms.openlocfilehash: 5a4e69ea7e13cb017f8fb432c524c6a8ce9228a8
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2017
---
# <a name="partitioned-queues-and-topics"></a>Секционированные очереди и разделы
Служебная шина Azure использует несколько посредников сообщений для обработки сообщений и несколько хранилищ сообщений для их хранения. Обычная очередь или раздел обрабатываются одним брокером сообщений и сохраняются в одном хранилище сообщений. *Секции* служебной шины позволяют секционировать очереди, разделы или *сущности обмена сообщениями* между несколькими брокерами сообщений и хранилищами сообщений. Это означает, что общая пропускная способность секционированной сущности больше не ограничивается производительностью одного брокера сообщений или хранилища сообщений. Кроме того, при возникновении временного сбоя хранилища сообщений секционированная очередь или раздел останутся доступными. Секционированные очереди и разделы могут предоставлять все расширенные функции служебной шины, в том числе поддержку транзакций и сеансов.

Дополнительные сведения о внутренних компонентах служебной шины см. в статье [Архитектура служебной шины][Service Bus architecture].

Секционирование включается по умолчанию при создании сущности во всех очередях и разделах на уровне обмена сообщениями "Стандартный" и "Премиум". Сущности уровня обмена сообщениями "Стандартный"можно создать без секционирования, но очереди и разделы в пространстве имен уровня "Премиум" всегда секционируются. Этот параметр невозможно отключить. 

Параметр секционирования в имеющейся очереди или разделе на уровне "Стандартный" или "Премиум" изменить невозможно. Его можно задать только при создании сущности.

## <a name="how-it-works"></a>Принцип работы

Каждая секционированная очередь или раздел состоит из нескольких фрагментов. Фрагменты хранятся в разных хранилищах сообщений и обрабатываются разными посредниками сообщений. При отправке сообщения в секционированную очередь или раздел служебная шина назначает сообщение в один из фрагментов. Выбор фрагмента осуществляется произвольно или по ключу секции, который может указать отправитель.

Если клиент хочет получить сообщение из секционированной очереди или подписки секционированного раздела, служебная шина запрашивает все фрагменты сообщений и возвращает получателю первое сообщение, полученное из любого хранилища сообщений. Служебная шина кэширует другие сообщения и возвращает их при получении дополнительных запросов. Клиент ничего не знает о секционировании, для него поведение секционированных очередей или разделов (например, чтение, завершение, откладывание, недоставление или предварительная выборка) аналогично поведению обычной сущности.

При отправке или получении сообщения из секционированной очереди или раздела не возникает никаких дополнительных затрат.

## <a name="enable-partitioning"></a>Включение секционирования

Для использования секционированных очередей и разделов служебной шины Azure необходимо использовать пакет SDK для Azure 2.2 или более поздней версии или указать `api-version=2013-10` в HTTP-запросах.

### <a name="standard"></a>Standard

На уровне обмена сообщениями "Стандартный" можно создавать очереди и разделы служебной шины размером в 1, 2, 3, 4 или 5 ГБ (по умолчанию — 1 ГБ). Если секционирование включено, то для каждого гигабайта служебной шиной создается 16 копий (секций) сущности. Поэтому при создании очереди размером 5 ГБ с 16 разделами максимальный размер очереди составит 80 ГБ (5 \* 16). Чтобы просмотреть максимальный размер секционированной очереди или раздела, откройте соответствующую запись на [портале Azure][Azure portal] в колонке **Обзор** для этой сущности.

### <a name="premium"></a>Premium

В пространстве имен уровня "Премиум" можно создавать очереди и разделы служебной шины размером в 1, 2, 3, 4, 5, 10, 20, 40 или 80 ГБ (по умолчанию — 1 ГБ). Если секционирование включено по умолчанию, служебная шина создает две секции на каждую сущность. Чтобы просмотреть максимальный размер секционированной очереди или раздела, откройте соответствующую запись на [портале Azure][Azure portal] в колонке **Обзор** для этой сущности.

Дополнительные сведения о секционировании на уровне обмена сообщениями "Премиум" см. в статье [Уровни обмена сообщениями через служебную шину Premium и Standard](service-bus-premium-messaging.md). 

### <a name="create-a-partitioned-entity"></a>Создание секционированной сущности

Создать секционированную очередь или раздел можно несколькими способами. При создании очереди или раздела из приложения можно включить секционирование очереди или раздела, задав значение true для свойства [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] или [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] **true**, соответственно. Эти свойства должны быть заданы при создании очереди или раздела. Как упоминалось ранее, для имеющийся очереди или раздела изменить эти свойства невозможно. Например:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Кроме того, секционированную очередь или раздел можно создать на [портале Azure][Azure portal] или в Visual Studio. При создании очереди или раздела на портале флажок **Включить секционирование**, расположенный в колонке **Создать** очереди или раздела, установлен по умолчанию. Этот параметр можно отключить только в сущности уровня "Стандартный". При секционировании на уровне "Премиум" он включен всегда. В Visual Studio установите флажок **Включить секционирование** в диалоговом окне **Новая очередь** или **Новый раздел**.

## <a name="use-of-partition-keys"></a>Использование ключей секции
Когда сообщение добавляется в секционированную очередь или раздел, служебная шина проверяет наличие ключа секции. Если ключ есть, то фрагмент выбирается на основе этого ключа. Если ключа нет, то фрагмент выбирается на основе внутреннего алгоритма.

### <a name="using-a-partition-key"></a>Использование ключа секции
В некоторых сценариях, например для сессий или транзакций, необходимо, чтобы сообщения хранились в определенном фрагменте. Для всех этих сценариев необходимо использовать ключ секции. Все сообщения, использующие один и тот же ключ секции, назначаются одному и тому же фрагменту. Если фрагмент временно недоступен, служебная шина возвращает ошибку.

В зависимости от сценария в качестве ключа секции используются разные свойства сообщения:

**SessionId**: если для сообщения установлено свойство [BrokeredMessage.SessionId][BrokeredMessage.SessionId], то служебная шина использует это свойство в качестве ключа секции. Таким образом, все сообщения, которые относятся к одному сеансу, обрабатываются одним и тем же посредником сообщений. Это позволяет служебной шине гарантировать порядок сообщений и согласованность состояний сеансов.

**PartitionKey**. Если для сообщения установлено свойство [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey], но не установлено свойство [BrokeredMessage.SessionId][BrokeredMessage.SessionId], то служебная шина использует свойство [PartitionKey][PartitionKey] в качестве ключа секции. Если для сообщения установлены оба свойства [SessionId][SessionId] и [PartitionKey][PartitionKey], то их значения должны совпадать. Если значение свойства [PartitionKey][PartitionKey] отличается от значения свойства [SessionId][SessionId], служебная шина возвращает исключение недопустимой операции. Свойство [PartitionKey][PartitionKey] нужно использовать, если отправитель отправляет транзактные сообщения без учета сеансов. Ключ секции гарантирует, что все сообщения, отправленные в транзакции, будут обработаны одним и тем же посредником сообщений.

**MessageId**. Если для очереди или раздела для свойства [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] задано значение **true**, а свойства [BrokeredMessage.SessionId][BrokeredMessage.SessionId] или [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] не заданы, то в качестве ключа секции выступает свойство [BrokeredMessage.MessageId][BrokeredMessage.MessageId]. (Обратите внимание, что библиотеки Microsoft .NET и AMQP назначают идентификатор сообщения автоматически, если отправляющее приложение этого не сделало). В этом случае все копии одного сообщения обрабатываются одним и тем же посредником сообщений. Это позволяет служебной шине находить и исключать повторяющиеся сообщения. Если свойство [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] не установлено в значение **true**, служебная шина не рассматривает свойство [MessageId][MessageId] в качестве ключа секции.

### <a name="not-using-a-partition-key"></a>Неиспользование ключа секции
При отсутствии ключа секции служебная шина распределяет сообщение по принципу циклического перебора всем фрагментам секционированной очереди или раздела. Если выбранный фрагмент недоступен, служебная шина назначает сообщение другому фрагменту. Таким образом, операция отправки выполняется даже при временной недоступности хранилища сообщений. Тем не менее вы не получите гарантированного количества, обеспечиваемого ключом раздела.

Более подробно компромисс между доступностью (нет ключа раздела) и целостностью (с использованием ключа раздела) рассматривается [в этой статье](../event-hubs/event-hubs-availability-and-consistency.md). Эта информация применяется в равной степени в отношении сущностей служебной шины с разделами и разделов концентраторов событий.

Чтобы у служебной шины было достаточно времени для того, чтобы поместить сообщение в другой фрагмент, значение свойства [MessagingFactorySettings.OperationTimeout][MessagingFactorySettings.OperationTimeout], указываемое клиентом, который отправляет сообщение, должно быть больше 15 секунд. Рекомендуется установить свойство [OperationTimeout][OperationTimeout] в значение по умолчанию 60 секунд.

Обратите внимание, что ключ секции "прикрепляет" сообщение к определенному фрагменту. Если хранилище сообщений, содержащее этот фрагмент, недоступно, служебная шина возвращает ошибку. При отсутствии ключа секции служебная шина может выбрать другой фрагмент, и операция будет выполнена успешно. Поэтому рекомендуется не указывать ключ раздела, если он не является обязательным.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Дополнительные разделы: использование транзакций и секционированных сущностей
Сообщения, отправленные в рамках транзакции, должны указать ключ секции. Это может быть одно из следующих свойств: [BrokeredMessage.SessionId][BrokeredMessage.SessionId], [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] или [BrokeredMessage.MessageId][BrokeredMessage.MessageId]. Сообщения, отправляемые в рамках одной транзакции, должны иметь один и тот же ключ секции. При попытке отправить сообщение без ключа секции в рамках транзакции служебная шина возвращает исключение недопустимой операции. При попытке отправить несколько сообщений с различными ключами секции в рамках одной транзакции служебная шина возвращает исключение недопустимой операции. Например:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Если установлено любое из свойств, которые служат в качестве ключа секции, служебная шина прикрепляет сообщение к определенному фрагменту. Это происходит независимо от того, используется ли транзакция. Рекомендуется не указывать ключ секции, если в этом нет необходимости.

## <a name="using-sessions-with-partitioned-entities"></a>Использование сеансов и секционированных сущностей
Чтобы отправить транзакционное сообщение в раздел или очередь, учитывающие сеансы, для этого сообщения должно быть задано свойство [BrokeredMessage.SessionId][BrokeredMessage.SessionId]. Если также установлено свойство [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey], оно должно быть идентично свойству [SessionId][SessionId]. Если они различаются, служебная шина возвращает исключение недопустимой операции.

В отличие от обычных (несекционированных) очередей и разделов, использовать одну транзакцию для отправки нескольких сообщений в различные сеансы невозможно. Если же попытаться это сделать, служебная шина возвращает исключение недопустимой операции. Например:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Автоматическая переадресация сообщений для секционированных сущностей
Служебная шина поддерживает автоматическую переадресацию сообщений в секционированные сущности, из них или между ними. Чтобы включить автоматическую переадресацию сообщений, установите свойство [QueueDescription.ForwardTo][QueueDescription.ForwardTo] исходной очереди или подписки. Если в сообщении указан ключ секции ([SessionId][SessionId], [PartitionKey][PartitionKey] или [MessageId][MessageId]), то этот ключ секции используется для сущности назначения.

## <a name="considerations-and-guidelines"></a>Рекомендации и советы
* **Функции высокого уровня согласованности**. Если сущность использует такие функции, как сеансы, обнаружение дубликатов или явное управление ключом секционирования, то операции обмена сообщениями всегда направляются в определенные фрагменты. Если любой из фрагментов сталкивается с высоким трафиком или базовое хранилище неработоспособно, то эти операции завершаются сбоем и их доступность ограничивается. В целом уровень согласованности гораздо выше, чем при использовании несекционированных сущностей, поэтому проблемы возникают лишь в некоторой части трафика. Дополнительные сведения см. в этом [обсуждении доступности и целостности](../event-hubs/event-hubs-availability-and-consistency.md).
* **Управление**. Такие операции, как создание, обновление и удаление, должны выполняться во всех фрагментах сущности. Если какой-либо из фрагментов находится в неработоспособном состоянии, это может привести к сбою этих операций. Для операции GET требуется, чтобы сведения, например количество сообщений, были собраны для всех фрагментов. Если какой-либо из фрагментов находится в неработоспособном состоянии, состояние доступности сущности отображается как ограниченное.
* **Сообщения небольших объемов**. Чтобы получить все сообщения в таких сценариях, особенно при использовании протокола HTTP, может потребоваться выполнить несколько операций получения. Для запросов на получение внешний интерфейс выполняет операцию получения во всех фрагментах и кэширует все полученные ответы. Это кэширование повышает производительность последующего запроса на получение при том же подключении, уменьшая задержки получения. Тем не менее при наличии нескольких подключений или использовании протокола HTTP для каждого запроса устанавливается новое подключение. Таким образом, нет никакой гарантии, что запрос будет выполняться на том же узле. Если все имеющиеся сообщения блокируются и кэшируются в другом внешнем интерфейсе, операция получения возвращает значение **NULL**. Когда в конечном итоге срок действия сообщений истекает, их снова можно получать. Рекомендуется выполнить проверку активности HTTP.
* **Обзор и просмотр сообщений**. Метод [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) не всегда возвращает количество сообщений, указанное в свойстве [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_MessageCount). На это есть две причины. Первая причина — объединенный размер коллекции сообщений превышает максимальный размер в 256 КБ. Вторая причина заключается в том, что если для [свойства EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnablePartitioning) очереди или раздела задано значение **true**, то секция может содержать недостаточно сообщений для выполнения запроса. Как правило, если приложению требуется получить определенное количество сообщений, оно должно вызывать метод [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_), пока не получит желаемое количество сообщений или пока не закончатся сообщения, которые можно просмотреть. Дополнительные сведения, включая примеры кода, см. в разделах [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) или [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_PeekBatch_System_Int32_).

## <a name="latest-added-features"></a>Новые функции
* Теперь для секционированных сущностей поддерживаются операции добавления и удаления правила. В отличие от несекционированных сущностей эти операции не поддерживаются при выполнении транзакций. 
* Теперь AMQP может использоваться для обмена сообщениями (отправка и получение) с секционированными сущностями.
* Теперь протокол AMQP поддерживается для следующих операций: [пакетная отправка](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_BrokeredMessage__), [пакетное получение](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_ReceiveBatch_System_Int32_), [получение по порядковому номеру](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_Receive_System_Int64_), [обзор](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_Peek), [блокировка обновления](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_RenewMessageLock_System_Guid_), [планирование сообщений](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_ScheduleMessageAsync_Microsoft_ServiceBus_Messaging_BrokeredMessage_System_DateTimeOffset_), [отмена запланированных сообщений](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_CancelScheduledMessageAsync_System_Int64_), [добавление правила](/dotnet/api/microsoft.servicebus.messaging.ruledescription), [удаление правила](/dotnet/api/microsoft.servicebus.messaging.ruledescription), [блокировка обновления сеанса](/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_RenewLock), [установка состояния сеанса](/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_), [получение состояния сеанса](/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_GetState) и [перечисление сеансов](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessionsAsync).

## <a name="partitioned-entities-limitations"></a>Ограничения секционированных сущностей
В настоящее время служебная шина накладывает следующие ограничения на секционированные очереди и разделы:

* Секционированные очереди и разделы не поддерживают отправку сообщений, принадлежащих разным сеансам, в одной транзакции.
* В настоящее время служебная шина обеспечивает до 100 секционированных очередей или разделов на пространство имен. Каждая секционированная очередь или раздел учитывается в квоте в 10 000 сущностей на пространство имен (не относится к уровню "Премиум").

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о секционировании сущностей обмена сообщениями см. в статье [Руководство по использованию протокола AMQP 1.0 в служебной шине и концентраторах событий Azure][AMQP 1.0 support for Service Bus partitioned queues and topics]. 

[Service Bus architecture]: service-bus-architecture.md
[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnablePartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription#Microsoft_ServiceBus_Messaging_TopicDescription_EnablePartitioning
[BrokeredMessage.SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId
[BrokeredMessage.PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_PartitionKey
[SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_PartitionKey
[QueueDescription.RequiresDuplicateDetection]: /dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[MessagingFactorySettings.OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
