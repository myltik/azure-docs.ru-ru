<properties 
    pageTitle="Очереди недоставленных сообщений служебной шины | Microsoft Azure" 
    description="Обзор очередей недоставленных сообщений служебной шины Azure" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="10/03/2016"
    ms.author="clemensv;sethm"/>


# <a name="overview-of-service-bus-dead-letter-queues"></a>Обзор очередей недоставленных сообщений служебной шины

Очереди служебной шины и подписки на разделы формируют дополнительную вложенную очередь, которая называется *очередью недоставленных сообщений* (DLQ). Очередь недоставленных сообщений не нужно создавать явно, также ее нельзя удалить или управлять ею другим образом, независимо от основной сущности.

Очередь недоставленных сообщений предназначена для хранения сообщений, которые невозможно доставить ни одному адресату, либо сообщений, которые не удалось обработать. Сообщение из очереди DLQ можно извлечь и изучить. Приложение с помощью оператора может исправить проблемы и повторно отправить это сообщение, зарегистрировать ошибку и предпринять действия по исправлению. 

С точки зрения интерфейса API и протокола очередь DLQ очень похожа на другие очереди за исключением того, что сообщения могут отправляться только через жест недоставленных сообщений родительской сущности. Кроме того, в ней не отслеживается время жизни, поэтому удалить сообщение из очереди DLQ невозможно. Очередь недоставленных сообщений полностью поддерживает доставку с блокировкой для просмотра и транзакционные операции.

Обратите внимание, что автоматическая очистка очереди DLQ не предусмотрена. Сообщения остаются в очереди DLQ, пока вы явно не извлечете их оттуда и не вызовете метод [Complete()](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.completeasync.aspx) для недоставленного сообщения.

## <a name="moving-messages-to-the-dlq"></a>Перемещение сообщений в очередь DLQ

В служебной шине есть несколько действий, которые приводят к отправке сообщений в очередь DLQ из самой системы обработки сообщений. Сообщения также могут явно отправляться в очередь DLQ самим приложением. 

Когда брокер перемещает сообщение, к нему добавляется два свойства в момент вызова брокером своей внутренней версии метода [DeadLetter](https://msdn.microsoft.com/library/azure/hh291941.aspx) для сообщения: `DeadLetterReason` и `DeadLetterErrorDescription`.

Приложения могут определять свои собственные коды для свойства `DeadLetterReason`, однако система задает следующие значения.

| Условие                                                                                                                             | DeadLetterReason            | DeadLetterErrorDescription                                                       |
|---------------------------------------------------------------------------------------------------------------------------------------|-----------------------------|----------------------------------------------------------------------------------|
| Всегда                                                                                                                                | HeaderSizeExceeded          | Превышен максимальный размер для этого потока.                                |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing and SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions | exception.GetType().Name    | exception.Message                                                                |
| EnableDeadLetteringOnMessageExpiration                                                                                                | TTLExpiredException         | Время жизни сообщения истекло, и оно было перемещено в очередь недоставленных.                                       |
| SubscriptionDescription.RequiresSession                                                                                               | Идентификатор сеанса имеет значение null.         | Сущность, поддерживающая сеансы, не допускает использование сообщений, идентификатор сеанса которых имеет значение null. |
| !очередь недоставленных сообщений                                                                                                                    | MaxTransferHopCountExceeded | Null                                                                             |
| Явное перемещение приложением в очередь недоставленных сообщений                                                                                                   | Задается приложением    | Задается приложением                                                         |

## <a name="exceeding-maxdeliverycount"></a>Превышено значение MaxDeliveryCount

Очереди и подписки имеют свойства [QueueDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) и [SubscriptionDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount.aspx) соответственно. Значение по умолчанию — 10. Всякий раз, когда сообщение доставляется с блокировкой ([ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)), но затем от него явно отказываются или срок действия блокировки истекает, значение счетчика [BrokeredMessage.DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx) увеличивается. Когда значение [DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx) превышает [MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx), сообщение перемещается в очередь DLQ с указанием кода причины `MaxDeliveryCountExceeded`.

Это правило нельзя отключить, однако можно задать для параметра [MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) очень большое значение.

## <a name="exceeding-timetolive"></a>Превышение значения TimeToLive

Когда свойство [QueueDescription.EnableDeadLetteringOnMessageExpiration](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration.aspx) или [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.enabledeadletteringonmessageexpiration.aspx) имеет значение **true** (по умолчанию — **false**), все просроченные сообщения перемещаются в очередь DLQ с указанием кода причины `TTLExpiredException`.

Обратите внимание, что просроченные сообщения удаляются и, следовательно, перемещаются в очередь DLQ только при наличии по крайней мере одного активного получателя, который получает сообщения из основной очереди или подписки. Это поведение по умолчанию.

## <a name="errors-while-processing-subscription-rules"></a>Ошибки при обработке правил подписки

Когда для подписки активировано свойство [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.enabledeadletteringonfilterevaluationexceptions.aspx), любые ошибки, возникающие при исполнении правила SQL-фильтрации подписки, фиксируются в очереди DLQ вместе с сообщением, вызвавшим ошибку.

## <a name="application-level-dead-lettering"></a>Недоставленные сообщения на уровне приложения

Помимо системных функций работы с недоставленными сообщениями, приложения могут использовать очередь DLQ для явного отклонения недопустимых сообщений. К ним могут относиться сообщения, которые невозможно должным образом обработать из-за каких-либо системных проблем, сообщения, содержащие полезные данные неправильного формата, или сообщения, не прошедшие проверку подлинности при использовании какой-либо схемы безопасности на уровне сообщений.

## <a name="example"></a>Пример

Следующий фрагмент кода создает получатель сообщений. В цикле получения для основной очереди код получает сообщение с помощью метода [Receive(TimeSpan.Zero)](https://msdn.microsoft.com/library/azure/dn130350.aspx), который запрашивает у брокера мгновенный возврат любых доступных сообщений или возврат без результата. Если код получает сообщение, он немедленно отказывается от него, что ведет к увеличению `DeliveryCount`. Когда система перемещает сообщение в очередь DLQ, основная очередь оказывается пуста и осуществляется выход из цикла, так как [ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130350.aspx) возвращает значение **null**.

```
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об очередях служебной шины см. в следующих статьях:

- [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
- [Сравнение службы очередей Azure и службы очередей служебной шины](service-bus-azure-and-service-bus-queues-compared-contrasted.md)



<!--HONumber=Oct16_HO2-->


