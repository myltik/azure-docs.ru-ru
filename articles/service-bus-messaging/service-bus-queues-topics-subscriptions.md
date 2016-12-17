---
title: "Очереди, разделы и подписки служебной шины | Документация Майкрософт"
description: "Общие сведения о сущностях обмена сообщениями в служебной шине."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: edee84938bddf28ac4dbf4152ccf9d7ab6afe6c3


---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Очереди, разделы и подписки служебной шины
Служебная шина Microsoft Azure поддерживает набор облачных технологий промежуточного уровня, ориентированных на обработку сообщений. Эти технологии представлены надежными очередями сообщений, а также возможностями публикации и подписки в рамках обмена сообщениями. Эти возможности обмена сообщениями через посредника могут рассматриваться как разделенные функции обмена сообщениями, поддерживающие публикацию и подписку, временное разделение, а также сценарии балансировки нагрузки с использованием фабрики обмена сообщениями служебной шины. Разделенный обмен данными имеет множество преимуществ. Например, клиенты и серверы могут подключаться по необходимости, выполняя свои операции в асинхронном режиме.

Сущности обмена сообщениями, образующие основные возможности обмена сообщениями через брокер в служебной шине, представлены очередями, разделами и подписками, а также правилами и действиями.

## <a name="queues"></a>Очереди
Очереди предлагают доставку сообщений конкурирующим потребителям FIFO. То есть обычно получатели принимают и обрабатывают сообщения в том порядке, в котором они были добавлены в очередь. При этом каждое сообщение принимается и обрабатывается только одним потребителем сообщений. Основное преимущество использования очередей — временное разделение компонентов приложений. Другими словами, производителям (отправителям) и потребителям (получателям) не приходится отправлять и получать сообщения в одно и то же время, поскольку сообщения надежно хранятся в очереди. Более того, производителю не нужно ждать ответ от потребителя, чтобы продолжить обработку и отправку дальнейших сообщений.

Сопутствующее преимущество заключается в выравнивании нагрузки — оно позволяет производителям и потребителям отправлять и получать сообщения с разной скоростью. Во многих приложениях уровень системной нагрузки со временем меняется, однако длительность обработки каждой единицы работы, как правило, остается постоянной. Обмен сообщениями между производителем и потребителем с использованием очереди предусматривает подготовку потребляющего приложения к обработке средней, а не пиковой нагрузки. При колебаниях входящей нагрузки просто изменяется глубина очереди. Это позволяет существенно сократить расходы на инфраструктуру, необходимую для обработки нагрузки приложения. По мере возрастания нагрузки могут потребоваться дополнительные рабочие процессы для чтения из очереди. Каждое сообщение обрабатывается одним рабочим процессом. Кроме того, балансировка нагрузки по запросу обеспечивает оптимальное использование рабочих компьютеров с разной вычислительной мощностью, позволяя извлекать сообщения с максимально доступной скоростью. Такой подход часто называют моделью "конкурирующих потребителей".

Использование очередей в качестве посредника между производителями и потребителями сообщений уменьшает зависимость между компонентами. Так как производители и потребители не зависят друг от друга, обновление потребителя не оказывает влияния на производителя.

Создание очереди является многоэтапным процессом. Выполнять операции управления для сущностей обмена сообщениями служебной шины (очередей и разделов) можно с использованием класса [Microsoft.ServiceBus.NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Этот класс создается путем предоставления базового адреса пространства имен служебной шины и учетных данных пользователя. Класс [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) предоставляет методы для создания, перечисления и удаления сущностей обмена сообщениями. Создав объект [Microsoft.ServiceBus.TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) на основе имени и ключа SAS, а также объект управления пространством имен службы, можно создать очередь, используя метод [Microsoft.ServiceBus.NamespaceManager.CreateQueue](https://msdn.microsoft.com/library/azure/hh293157.aspx). Например:

```
// Create management credentials
TokenProvider credentials = TokenProvider. CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Затем можно создать объект очереди и фабрику обмена сообщениями с помощью URI служебной шины в качестве аргумента. Например:

```
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

После этого можно отправлять сообщения в очередь. Например, если имеется список сообщений, передаваемых через посредника, с именем `MessageList`, код будет выглядеть приблизительно так:

```
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

Можно затем получить сообщения из очереди, как показано ниже:

```
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

В режиме [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) получение является одиночной операцией. Это значит, что, когда служебная шина получает запрос, сообщение помечается как использованное и возвращается в приложение. Режим [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) представляет собой самую простую модель. Наиболее эффективен он в сценариях, когда приложение допускает отсутствие обработки сообщения в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Служебная шина помечает сообщение как использованное. Следовательно, когда после перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме [PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) процесс получения становится двухэтапной операцией. Это позволяет поддерживать приложения, которые не допускают пропуск сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы другие потребители не могли его принять, а затем возвращает его приложению. Когда приложение завершает обработку сообщения (или надежно сохраняет его для последующей обработки), оно завершает второй этап процесса получения, вызывая метод [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) для полученного сообщения. Когда служебная шина фиксирует вызов [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), сообщение помечается как использованное.

Если приложение по каким-либо причинам не может обработать сообщение, оно может вызвать для полученного сообщения метод [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) (вместо метода [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). После этого служебная шина разблокирует сообщение в очереди, сделав его доступным для приема тем же или другим конкурирующим потребителем. Кроме того, блокирование связано с определенным временем ожидания. Если приложение не сможет обработать сообщение до истечения времени ожидания (например, при сбое приложения), служебная шина автоматически разблокирует сообщение, сделав его снова доступным для получения (фактически выполняя операцию [прерывания](https://msdn.microsoft.com/library/azure/hh181837.aspx) по умолчанию).

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), такое сообщение будет повторно доставлено в приложение после перезапуска. Такой подход предполагает принцип обработки сообщения *хотя бы один раз*. Тем не менее в некоторых случаях это же сообщение может быть доставлено повторно. Если сценарий не допускает повторную обработку, для обнаружения дубликатов требуется дополнительная логика в приложении. Это реализуется с помощью свойства сообщения **MessageId**, которое остается постоянным в ходе разных попыток доставки. Такой подход предполагает концепцию обработки *только один раз*.

Дополнительные сведения и рабочий пример создания и отправки сообщений в очередь и из нее см. в статье [Учебное пособие по обмену сообщениями .NET через посредника в служебной шине](service-bus-brokered-tutorial-dotnet.md).

## <a name="topics-and-subscriptions"></a>Разделы и подписки
В отличие от очередей, в которых каждое сообщение обрабатывается одним потребителем, *разделы* и *подписки* предоставляют возможность взаимодействия типа "один ко многим" в рамках шаблона *публикации или подписки*. Каждое опубликованное сообщение становится доступным в рамках каждой подписки, зарегистрированной в разделе. Это особенно удобно при масштабировании с учетом большого количества получателей. Сообщения отправляются в раздел и доставляются в одну или несколько связанных подписок в зависимости от правил фильтрации, которые могут быть заданы для каждой подписки. Подписки могут использовать дополнительные фильтры для ограничения получаемых сообщений. Сообщения отправляются в раздел так же, как и в очередь; при этом непосредственно из раздела получить их нельзя. Зато их можно получить из подписок. Подписка раздела напоминает виртуальную очередь, которая получает копии сообщений, отправленных в раздел. Сообщения передаются из подписки так же, как и из очереди.

Продолжая сравнение, следует отметить, что при отправке из очереди сообщения распределяются непосредственно в раздел, а при извлечении — в подписку. Помимо прочего, это означает, что подписки также поддерживают схемы для очередей, описанные ранее в этом разделе, в том числе конкуренцию потребителей, временное разделение, а также выравнивание и балансировку нагрузки.

Создание раздела аналогично созданию очереди, как показано в примере, приведенном в предыдущем разделе. Создайте URI службы, а затем с помощью класса [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) создайте клиент пространства имен. Затем с помощью метода [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) можно создать раздел. Например:

```
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Затем добавьте необходимые подписки:

```
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

После этого можно создать клиент раздела. Например:

```
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

С помощью отправителя сообщений можно отправлять сообщения в раздел и извлекать их из раздела, как описано выше. Например:

```
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Как и в случае с очередью, сообщения извлекаются из подписки с помощью объекта [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx), используемого вместо объекта [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Создайте клиент подписки, передав в качестве параметров имя раздела, имя подписки и (необязательно) режим получения. Пример с подпиской **Inventory**:

```
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Правила и действия
Во многих ситуациях сообщения с определенными характеристиками должны обрабатываться разными способами. Для этого можно настроить подписки, обеспечивающие поиск сообщений с нужными свойствами, после чего можно определенным образом изменить эти свойства. Подписки служебной шины регистрируют все сообщения, отправленные в раздел, однако в виртуальную очередь подписки можно скопировать только подмножество этих сообщений. Это возможно благодаря использованию фильтров подписок. Такие изменения называются *действиями фильтров*. При создании подписки можно указать выражение фильтра, которое работает со свойствами сообщения, включая системные свойства (например, **Label**) и свойства пользовательского приложения (например, **StoreName**). В этом случае SQL-выражение фильтра является необязательным. Без него все определенные в подписке действия фильтра будут выполняться по отношению ко всем сообщениям в рамках этой подписки.

Используя предыдущий пример, вы можете создать подписку Dashboard для фильтрации сообщений, приходящих только из хранилища **Store1**, как показано ниже.

```
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Благодаря этому фильтру подписки в виртуальную очередь для подписки `Dashboard` копируются только сообщения со свойством `StoreName`, которому задано значение `Store1`.

Дополнительные сведения о возможных значениях фильтров см. в документации по классам [SqlFilter](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx) и [SqlRuleAction](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlruleaction.aspx). Ознакомьтесь также с примерами [Brokered Messaging: Advanced Filters](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) (Обмен сообщениями через брокер: расширенные фильтры) и [Topic Filters](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters) (Фильтры разделов).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения и примеры использования сущностей обмена сообщениями через брокер в служебной шине см. в следующих дополнительных статьях.

* [Основные сведения об обмене сообщениями через служебную шину](service-bus-messaging-overview.md)
* [Учебное пособие по обмену сообщениями .NET через посредника в служебной шине](service-bus-brokered-tutorial-dotnet.md)
* [Руководство по обмену сообщениями через посредника служебной шины на основе REST](service-bus-brokered-tutorial-rest.md)
* [Пример фильтров разделов](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters)
* [Brokered Messaging: Advanced Filters (Обмен сообщениями через брокер: расширенные фильтры)](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)




<!--HONumber=Nov16_HO3-->


