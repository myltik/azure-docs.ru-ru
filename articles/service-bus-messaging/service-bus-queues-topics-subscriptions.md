---
title: Обзор очередей сообщений, разделов и подписок служебной шины Azure | Документация Майкрософт
description: Общие сведения о сущностях обмена сообщениями в служебной шине.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: 5bea3b56cea81362b25e696a672bf2a00e26d3ef
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2017
ms.locfileid: "24029513"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Очереди, разделы и подписки служебной шины

Служебная шина Microsoft Azure поддерживает набор облачных технологий промежуточного уровня, ориентированных на обработку сообщений. Эти технологии представлены надежными очередями сообщений, а также возможностями публикации и подписки в рамках обмена сообщениями. Эти возможности обмена сообщениями через посредника могут рассматриваться как разделенные функции обмена сообщениями, поддерживающие публикацию и подписку, временное разделение, а также сценарии балансировки нагрузки с использованием рабочей нагрузки обмена сообщениями служебной шины. Разделенный обмен данными имеет множество преимуществ. Например, клиенты и серверы могут подключаться по необходимости, выполняя свои операции в асинхронном режиме.

Сущности обмена сообщениями, образующие основные возможности обмена сообщениями в служебной шине, представлены очередями, разделами и подписками, а также правилами и действиями.

## <a name="queues"></a>Очереди

Очереди предлагают доставку сообщений конкурирующим потребителям по типу *FIFO* (первым пришел, первым вышел). То есть обычно получатели принимают и обрабатывают сообщения в том порядке, в котором они были добавлены в очередь. При этом каждое сообщение принимается и обрабатывается только одним потребителем сообщений. Основное преимущество использования очередей — временное разделение компонентов приложений. Другими словами, производителям (отправителям) и потребителям (получателям) не приходится отправлять и получать сообщения в одно и то же время, поскольку сообщения надежно хранятся в очереди. Более того, производителю не нужно ждать ответ от потребителя, чтобы продолжить обработку и отправку дальнейших сообщений.

Сопутствующее преимущество заключается в выравнивании нагрузки — оно позволяет производителям и потребителям отправлять и получать сообщения с разной скоростью. Во многих приложениях уровень системной нагрузки со временем меняется, однако длительность обработки каждой единицы работы, как правило, остается постоянной. Обмен сообщениями между производителем и потребителем с использованием очереди предусматривает подготовку потребляющего приложения к обработке средней, а не пиковой нагрузки. При колебаниях входящей нагрузки просто изменяется глубина очереди. Это позволяет существенно сократить расходы на инфраструктуру, необходимую для обработки нагрузки приложения. По мере возрастания нагрузки могут потребоваться дополнительные рабочие процессы для чтения из очереди. Каждое сообщение обрабатывается одним рабочим процессом. Кроме того, балансировка нагрузки по запросу обеспечивает оптимальное использование рабочих компьютеров с разной вычислительной мощностью, позволяет извлекать сообщения с максимально доступной скоростью. Такой подход часто называют моделью "конкурирующих потребителей".

Использование очередей в качестве посредника между производителями и потребителями сообщений уменьшает зависимость между компонентами. Так как производители и потребители не зависят друг от друга, обновление потребителя не оказывает влияния на производителя.

Создание очереди является многоэтапным процессом. Выполнять операции управления для сущностей обмена сообщениями служебной шины (очередей и разделов) можно с использованием класса [Microsoft.ServiceBus.NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager). Этот класс создается путем предоставления базового адреса пространства имен служебной шины и учетных данных пользователя. Класс [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) предоставляет методы для создания, перечисления и удаления сущностей обмена сообщениями. Создав объект [Microsoft.ServiceBus.TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) на основе имени и ключа SAS, а также объект управления пространством имен службы, можно создать очередь, используя метод [Microsoft.ServiceBus.NamespaceManager.CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_). Например: 

```csharp
// Create management credentials
TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Затем можно создать объект очереди и фабрику обмена сообщениями с помощью URI служебной шины в качестве аргумента. Например: 

```csharp
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

После этого можно отправлять сообщения в очередь. Например, если имеется список сообщений, передаваемых через посредника, с именем `MessageList`, код будет выглядеть аналогично следующему примеру:

```csharp
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

Затем можно получить сообщения из очереди, как показано ниже:

```csharp
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

В режиме [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) получение является одиночной операцией. Это значит, что, когда служебная шина получает запрос, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель. Наиболее эффективен он в сценариях, когда приложение допускает отсутствие обработки сообщения в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Служебная шина помечает сообщение как использованное. Следовательно, когда после перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) процесс получения становится двухэтапной операцией. Это позволяет поддерживать приложения, которые не допускают пропуск сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы другие потребители не могли его принять, а затем возвращает его приложению. Когда приложение завершает обработку сообщения (или надежно сохраняет его для последующей обработки), оно завершает второй этап процесса получения, вызывая метод [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) для полученного сообщения. Когда служебная шина фиксирует вызов **Complete**, сообщение помечается как использованное.

Если приложение по каким-либо причинам не может обработать сообщение, оно может вызвать для полученного сообщения метод [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) (вместо метода [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). После этого служебная шина разблокирует сообщение в очереди, сделав его доступным для приема тем же или другим конкурирующим потребителем. Кроме того, блокирование связано с определенным временем ожидания. Если приложение не сможет обработать сообщение до истечения времени ожидания (например, при сбое приложения), служебная шина автоматически разблокирует сообщение, сделав его снова доступным для получения (фактически выполняя операцию [прерывания](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) по умолчанию).

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **Complete**, такое сообщение будет повторно доставлено в приложение после перезапуска. Такой подход предполагает принцип обработки сообщения *хотя бы один раз*. Тем не менее в некоторых случаях это же сообщение может быть доставлено повторно. Если сценарий не допускает повторную обработку, для обнаружения дубликатов требуется дополнительная логика в приложении. Это реализуется с помощью свойства сообщения **MessageId**, которое остается постоянным в ходе разных попыток доставки. Такой подход предполагает концепцию обработки *только один раз*.

## <a name="topics-and-subscriptions"></a>Разделы и подписки
В отличие от очередей, в которых каждое сообщение обрабатывается одним потребителем, *разделы* и *подписки* предоставляют возможность взаимодействия типа "один ко многим" в рамках шаблона *публикации или подписки*. Каждое опубликованное сообщение становится доступным в рамках каждой подписки, зарегистрированной в разделе. Это особенно удобно при масштабировании с учетом большого количества получателей. Сообщения отправляются в раздел и доставляются в одну или несколько связанных подписок в зависимости от правил фильтрации, которые могут быть заданы для каждой подписки. Подписки могут использовать дополнительные фильтры для ограничения получаемых сообщений. Сообщения отправляются в раздел так же, как и в очередь; при этом непосредственно из раздела получить их нельзя. Зато их можно получить из подписок. Подписка раздела напоминает виртуальную очередь, которая получает копии сообщений, отправленных в раздел. Сообщения передаются из подписки так же, как и из очереди.

Продолжая сравнение, следует отметить, что при отправке из очереди сообщения распределяются непосредственно в раздел, а при извлечении — в подписку. Помимо прочего, это означает, что подписки также поддерживают схемы для очередей, описанные ранее в этом разделе, в том числе конкуренцию потребителей, временное разделение, а также выравнивание и балансировку нагрузки.

Создание раздела аналогично созданию очереди, как показано в примере, приведенном в предыдущем разделе. Создайте URI службы, а затем с помощью класса [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) создайте клиент пространства имен. Затем с помощью метода [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_) можно создать раздел. Например: 

```csharp
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Затем добавьте необходимые подписки:

```csharp
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

После этого можно создать клиент раздела. Например: 

```csharp
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

С помощью отправителя сообщений можно отправлять сообщения в раздел и извлекать их из раздела, как описано выше. Например: 

```csharp
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Как и в случае с очередью, сообщения извлекаются из подписки с помощью объекта [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient), используемого вместо объекта [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient). Создайте клиент подписки, передав в качестве параметров имя раздела, имя подписки и (необязательно) режим получения. Пример с подпиской **Inventory**:

```csharp
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

```csharp
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Благодаря этому фильтру подписки в виртуальную очередь для подписки `Dashboard` копируются только сообщения со свойством `StoreName`, которому задано значение `Store1`.

Дополнительные сведения о возможных значениях фильтров см. в документации по классам [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) и [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction). Ознакомьтесь также с примерами [Brokered Messaging: Advanced Filters](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) (Обмен сообщениями через брокер: расширенные фильтры) и [Topic Filters](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/TopicFilters) (Фильтры разделов).

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения и примеры использования обмена сообщениями в служебной шине см. в следующих дополнительных статьях.

* [Основные сведения об обмене сообщениями через служебную шину](service-bus-messaging-overview.md)
* [Учебное пособие по обмену сообщениями .NET через посредника в служебной шине](service-bus-brokered-tutorial-dotnet.md)
* [Руководство по обмену сообщениями через посредника служебной шины на основе REST](service-bus-brokered-tutorial-rest.md)
* [Brokered Messaging: Advanced Filters (Обмен сообщениями через брокер: расширенные фильтры)](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

