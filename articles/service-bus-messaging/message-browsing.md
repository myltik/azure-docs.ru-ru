---
title: Просмотр сообщений служебной шины Azure | Документация Майкрософт
description: Просмотр и извлечение сообщений служебной шины.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: 124c4592a41bf9f3e2a148ba5c3b928bb051d160
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2018
ms.locfileid: "28196626"
---
# <a name="message-browsing"></a>Просмотр сообщений

Обзор сообщений ("просмотр") позволяет клиенту служебной шины перечислить все сообщения, находящиеся в очереди или подписке, как правило, для диагностики и отладки.

Операции просмотра возвращают все сообщения, которые существуют в журнале сообщений очереди или подписки, а не только те, что доступны для немедленного получения посредством операции `Receive()` или цикла `OnMessage()`. Свойство `State` каждого сообщения указывает, что сообщение активно (доступно для получения), [отложено](message-deferral.md) или [запланировано](message-sequencing.md).

Использованные и просроченные сообщения очищаются при асинхронном выполнении"сборки мусора". Это не обязательно происходит сразу же после истечения срока действия сообщения, поэтому операция `Peek` действительно может возвращать сообщения, срок действия которых уже истек и которые будут удалены или отправлены в очередь недоставленных сообщений при последующем вызове операции получения для очереди или подписки.

Это особенно важно учитывать при попытке восстановления отложенных сообщений из очереди. Сообщение, для которого момент [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) прошел, больше не может быть получено обычным способом какими-либо другими способами, даже если оно возвращается при операции просмотра. Эти сообщения возвращаются намеренно, так как операция просмотра — это инструмент диагностики, отражающий текущее состояние журнала.

Эта операция также возвращает сообщения, которые были заблокированы и в настоящий момент обрабатываются другими получателями, но эта обработка еще не завершена. Тем не менее, так как операция просмотра возвращает отключенный моментальный снимок, состояние блокировки просматриваемого сообщения просмотреть невозможно, и при попытке приложения прочитать такое сообщение свойства [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) и [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) порождают исключение [InvalidOperationException](/dotnet/api/system.invalidoperationexception).

## <a name="peek-apis"></a>Интерфейсы API просмотра

Методы [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) и [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) имеются во всех клиентских библиотеках .NET и Java и для всех объектов получателей: **MessageReceiver**, **MessageSession**, **QueueClient** и **SubscriptionClient**. Операция просмотра работает для всех очередей, подписок и их соответствующих очередей недоставленных сообщений.

При повторяющихся вызовах метод Peek перечисляет все сообщения, которые существуют в журнале очереди или подписки, по возрастанию порядкового номера. Это порядок, в котором сообщения были поставлены в очередь, а не порядок, в котором сообщения со временем могут быть получены.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) получает несколько сообщений и возвращает их в виде перечисления. Если доступных сообщений нет, объект перечисления является пустым, а не содержащим значения NULL.

Можно также заполнить перегрузку метода значением [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber), с которого следует начать, а затем вызвать перегрузку метода без параметров для продолжения перечисления. **PeekBatch** работает аналогично, но получает набор сообщений за раз.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об обмене сообщениями через служебную шину см. в следующих статьях:

* [Базовая информация о служебной шине](service-bus-fundamentals-hybrid-solutions.md)
* [Очереди, разделы и подписки служебной шины](service-bus-queues-topics-subscriptions.md)
* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Как использовать разделы и подписки служебной шины](service-bus-dotnet-how-to-use-topics-subscriptions.md)