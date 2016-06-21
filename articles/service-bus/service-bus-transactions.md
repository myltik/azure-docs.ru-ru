<properties 
    pageTitle="Транзакции служебной шины | Microsoft Azure" 
    description="Общие сведения об атомарных транзакциях служебной шины Azure" 
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
    ms.date="05/16/2016"
    ms.author="clemensv;sethm"/>

# Обзор обработки транзакций в служебной шине

Эта статья описывает возможности служебной шины Azure по работе с транзакциями. Основную часть излагаемого материала иллюстрирует [пример выполнения атомарных транзакций с помощью служебной шины](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions). Эта статья рассматривает лишь обработку транзакций и функцию *отправить через* в служебной шине, но пример атомарных транзакций образец гораздо масштабнее и сложнее.

## Транзакции в служебной шине

[Транзакция](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions#what-are-transactions) объединяет две или более операций в *область выполнения*. По своей природе такая транзакция должна обеспечивать либо успешное, либо неудачное выполнение всех относящихся к данной группе операций. В этом отношении транзакция выступает в качестве единого целого, что часто называется *атомарностью*.

Служебная шина является брокером для транзакционных сообщений и гарантирует целостность всех внутренних операций в соответствии с хранилищами сообщений. Все передачи сообщений внутри служебной шины, например перемещение сообщений в [очередь недоставленных сообщений](service-bus-dead-letter-queues.md) или [автоматическая пересылка](service-bus-auto-forwarding.md) сообщений между сущностями, являются транзакционными. Таким образом, когда служебная шина принимает сообщение, оно уже сохранено и помечено порядковым номером. Начиная с этого момента любые передачи сообщений в служебной шине, являются скоординированными операциями между сущностями и не приведут ни к потере (успех исходной части, сбой целевой части), ни к дублированию (сбой исходной части, успех целевой части) сообщения.

Служебная шина поддерживает операции группирования относительно одной сущности обмена сообщениями (очереди, раздела, подписки) в области транзакции. Например, можно отправить несколько сообщений в одну очередь из области транзакций, и сообщение будет зафиксировано в журнале очереди только после успешного завершения транзакции.

## Операции в области транзакций 

Далее приведены операции, которые могут выполняться в области транзакций.

- **[QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx), [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)**: Send, SendAsync, SendBatch, SendBatchAsync 

- **[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)**: Complete, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync

Операции получения не указаны, так как предполагается, что приложение получает сообщения с помощью режима [ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), внутри некоторого цикла получения или с помощью с обратного вызова [OnMessage](https://msdn.microsoft.com/library/azure/dn369601.aspx) и только затем открывает область транзакций для обработки сообщения.

Обработка сообщения (выполнено, прервано, отложено, не доставлено) происходит в пределах области и зависит от общего результата транзакции.

## Передачи и функция "отправить через"

Чтобы обеспечить транзакционное перемещение в данных из очереди в обработчик, а затем в другую очередь, служебная шина поддерживает *передачи*. В операции передачи отправитель сначала отправляет сообщение в "очередь передачи", которая сразу же перемещает его в очередь пункта назначения, используя ту же надежную реализацию передачи, на которую полагается функция автоматической пересылки. Сообщение никогда не фиксируется в журнале очереди передачи так, чтобы стать видимым для получателей очереди передачи.

Эффективность этой возможности становится очевидной, когда сама очередь передачи является источником для входных сообщений отправителя. Другими словами, служебная шина может передавать сообщения в очередь назначения "через" очередь передачи, при этом делая входное сообщение завершенным (либо отложенным или недоставленным) в рамках одной атомарной операции.

### Пример кода

Для настройки таких передач вы создаете отправитель сообщений, сориентированный на очередь назначения через очередь передачи. Также имеется получатель, который извлекает сообщения из той же очереди. Например:

```
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Тогда простая транзакция использует эти элементы, как показано в следующем примере.

```
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## Дальнейшие действия

Дополнительные сведения об очередях служебной шины см. в следующих статьях:

- [Объединение в цепочки сущностей служебной шины с помощью автоматической пересылки](service-bus-auto-forwarding.md)
- [пример автоматической пересылки](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AutoForward)
- [Пример выполнения атомарных транзакций с помощью служебной шины](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)
- [Сравнение службы очередей Azure и службы очередей служебной шины](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Как использовать очереди служебной шины](service-bus-dotnet-get-started-with-queues.md)

<!---HONumber=AcomDC_0608_2016-->