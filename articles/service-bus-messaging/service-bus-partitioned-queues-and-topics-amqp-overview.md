<properties 
    pageTitle="Поддержка AMQP 1.0 для секционированных очередей и разделов служебной шины | Microsoft Azure" 
    description="Узнайте об использовании расширенного протокола управления очередью сообщений (AMQP) версии 1.0 для секционированных очередей и разделов служебной шины." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="hillaryc" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="hillaryc;sethm"/>


# <a name="amqp-1.0-support-for-service-bus-partitioned-queues-and-topics"></a>Поддержка AMQP 1.0 для секционированных очередей и разделов служебной шины 

Служебная шина Azure теперь поддерживает расширенный протокол управления очередью сообщений (**AMQP**) версии 1.0 для **секционированных очередей и разделов** служебной шины.

**AMQP** — это открытый стандарт протокола очередей сообщений, который позволяет разрабатывать кроссплатформенные приложения с использованием различных языков программирования. Общие сведения о поддержке AMQP в служебной шине см. в статье [Поддержка AMQP 1.0 в служебной шине](service-bus-amqp-overview.md).

**Секционированные очереди и разделы**, которые также называют *секционированными сущностями*, обеспечивают более высокую доступность, надежность и пропускную способность по сравнению с обычными несекционированными очередями и разделами. Дополнительные сведения о секционированных сущностях см. в статье [Секционированные сущности обмена сообщениями](service-bus-partitioning.md).

Добавление AMQP 1.0 в качестве протокола для связи с секционированными очередями и разделами позволяет создавать совместно функционирующие приложения с более высокой доступностью, надежностью и пропускной способностью, которыми обладают секционированные сущности служебной шины.

Подробные инструкции по работе с протоколом AMQP 1.0 на уровне соединения, которые объясняют, как служебная шина реализует и использует техническую спецификацию OASIS AMQP, см. в статье [Руководство по использованию протокола AMQP 1.0 в служебной шине и концентраторах событий Azure](service-bus-amqp-protocol-guide.md).    

## <a name="use-amqp-with-partitioned-queues"></a>Использование AMQP с секционированными сущностями

Очереди удобны для сценариев, которым необходимы временная развязка, выравнивание нагрузки, балансировка нагрузки и слабые связи. Если сообщение может быть получено только один раз, издатели отправляют сообщения в очередь, а потребители получают сообщения из очереди. Хороший пример подобной схемы — система управления запасами, в которой терминалы точек продаж публикуют данные в очередь, а не в систему управления запасами напрямую. Система управления запасами сможет в любой момент использовать эти данные для пополнения запасов. У такого подхода есть несколько преимуществ: например, система управления запасами не должна находиться в сети все время. Дополнительные сведения об очередях служебной шины см. в статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md). 

Секционированная очередь может дополнительно повысить доступность, надежность и производительность для приложений, так как такая очередь секционируется между несколькими посредниками сообщений и хранилищами сообщений.     

### <a name="create-partitioned-queues"></a>Создание секционированных очередей

Секционированную очередь можно создать с помощью [классического портала Azure][] и пакета SDK служебной шины. Чтобы создать секционированную очередь, необходимо установить для свойства [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) экземпляра **QueueDescription** значение [true](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx). В следующем коде показано, как создать секционированную очередь с помощью пакета SDK служебной шины. 
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Отправка и получение сообщений с помощью AMQP

Для отправки и получения сообщений из секционированной очереди с помощью протокола AMQP необходимо установить для свойства [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) значение [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx), как показано в следующем фрагменте кода.  

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="use-amqp-with-partitioned-topics"></a>Использование AMQP с секционированными разделами

Разделы по сути похожи на очереди, но могут отправлять копию одного и того же сообщения в несколько *подписок*. В случае с разделом издатели отправляют сообщения в раздел, а потребители получают сообщения из подписок. В сценарии с системой управления запасами терминалы точек продаж будут публиковать данные в раздел. Затем система управления запасами получит сообщения из подписки. Кроме того, система мониторинга также может получить точно такое же сообщение из другой подписки. Дополнительные сведения о разделах и подписках служебной шины см. в статье [Создание приложений, использующих разделы и подписки служебной шины](service-bus-create-topics-subscriptions.md). 

Секционированный раздел (как и при использовании очередей) может дополнительно повысить доступность, надежность и пропускную способность для приложений, так как такие разделы и их подписки секционируются между несколькими брокерами сообщений и хранилищами сообщений. 

### <a name="create-partitioned-topics"></a>Создание секционированных разделов

Секционированный раздел можно создать с помощью [классического портала Azure][] и пакета SDK служебной шины. Чтобы создать секционированный раздел, необходимо задать для свойства [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) экземпляра **TopicDescription** значение [true](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx). В следующем коде показано, как создать секционированный раздел с помощью пакета SDK служебной шины.
    
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Отправка и получение сообщений с помощью AMQP

Вы можете отправлять сообщения в подписку секционированного раздела и получать сообщения от нее с помощью протокола AMQP. Для этого установите для свойства [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) значение [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx), как показано в следующем коде.  

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
    
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
    
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о протоколе AMQP и секционированных сущностях обмена сообщениями см. в следующих статьях.

*    [Секционированные сущности обмена сообщениями](service-bus-partitioning.md)
*    [Протокол OASIS AMQP, версия 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Поддержка AMQP 1.0 в служебной шине](service-bus-amqp-overview.md)
*    [Руководство по использованию протокола AMQP 1.0 в служебной шине и концентраторах событий Azure](service-bus-amqp-protocol-guide.md)
*    [Как использовать API службы сообщений Java (JMS) со служебной шиной и AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Как использовать AMQP 1.0 с API .NET служебной шины](service-bus-dotnet-advanced-message-queuing.md)

[классическом портале Azure]: http://manage.windowsazure.com



<!--HONumber=Oct16_HO2-->


