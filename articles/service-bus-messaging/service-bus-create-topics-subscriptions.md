---
title: "Создание приложений, использующих разделы и подписки служебной шины | Документация Майкрософт"
description: "Вводная информация о возможностях публикации и подписки, предлагаемых в разделах и подписках служебной шины."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a48fc9b0-b7b0-464e-8187-a517bf8b4eb4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 994a379129bffd7457912bc349f240a970aed253
ms.openlocfilehash: 799ef33c924a0067bb5e8da9d1b4e50091dbabf6


---
# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>Создание приложений, использующих разделы и подписки служебной шины
Служебная шина Azure поддерживает набор облачных технологий промежуточного уровня, ориентированных на обработку сообщений. Эти технологии представлены надежными очередями сообщений, а также возможностями публикации и подписки в рамках обмена сообщениями. Эта статья основана на сведениях, представленных в статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md), и содержит вводную информацию о возможностях публикации и подписки, предлагаемые разделами служебной шины.

## <a name="evolving-retail-scenario"></a>Развитие сценария розничной торговли
В этой статье продолжает использоваться сценарий розничной торговли, который описан в статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md). Напомним, что данные о продажах из отдельных точек продаж (POS) должны направляться в систему управления запасами, которая использует эти данные для определения момента пополнения запасов. Каждый терминал POS сообщает данные о продажах путем отправки сообщений в очередь **DataCollectionQueue**, в которой эти сообщения хранятся до их получения системой управления запасами, как показано ниже:

![Служебная шина 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Для развития этого сценария к системе было добавлено новое требование: владелец магазина хочет иметь возможность отслеживать работу хранилища в режиме реального времени.

Для реализации этого требования система должна "разветвить" поток данных о продажах. Мы по-прежнему хотим, чтобы как и прежде каждое сообщение, отправляемое терминалами POS, направлялось в систему управления запасами. Но мы также хотим иметь еще одну копию каждого сообщения, чтобы представить ее владельцу магазина на панели мониторинга.

В любой ситуации такого рода, в которой требуется, чтобы каждое сообщение могло использоваться несколькими сторонами, можно воспользоваться *разделами* служебной шины. Разделы предоставляют модель публикации и подписки, благодаря которой каждое опубликованное сообщение становится доступным одной или нескольким подпискам, зарегистрированным в разделе. В отличие от этой схемы, в очереди каждое сообщение доступно только одному потребителю.

Сообщения отправляются в раздел точно так же, как и в очередь. Однако сообщения поступают не напрямую из раздела, а из подписок. Подписка на раздел напоминает виртуальную очередь, которая получает копии сообщений, отправленных в раздел. Сообщения поступают из подписки так же, как и из очереди.

Вернемся к сценарию розничной торговли. В этом сценарии очередь заменяется разделом и добавляется подписка, которую может использовать компонент системы управления запасами. Теперь система выглядит следующим образом:

![Служебная шина 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

Эта конфигурация работает аналогично предыдущей схеме на основе очереди. То есть сообщения, отправляемые в раздел, направляются в подписку **Запасы**, из которой их получает **система управления запасами**.

Для поддержки панели мониторинга управления мы создаем вторую подписку на раздел, как показано ниже.

![Служебная шина 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

В такой конфигурации каждое сообщение из терминалов POS доступно обеим подпискам — **Панель мониторинга** и **Запасы**.

## <a name="show-me-the-code"></a>Покажите мне код
В статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md) описаны регистрация учетной записи Azure и создание пространства имен службы. Чтобы использовать пространство имен служебной шины, приложение должно ссылаться на сборку служебной шины, а именно Microsoft.ServiceBus.dll. Самым простым способом сослаться на зависимости служебной шины является установка [пакета Nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) служебной шины. Эта сборка также доступна в составе пакета SDK Azure. Ее можно загрузить на [странице загрузки пакета SDK Azure](https://azure.microsoft.com/downloads/).

### <a name="create-the-topic-and-subscriptions"></a>Создание раздела и подписок
Операции управления для сущностей обмена сообщениями служебной шины (очередей и разделов публикации и подписки) выполняются с помощью класса [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager). Для создания экземпляра **NamespaceManager** для определенного пространства имен необходимы соответствующие учетные данные. Служебная шина использует модель безопасности на основе [подписанного URL-адреса](service-bus-sas-overview.md) (SAS). Класс [TokenProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) представляет поставщика маркеров безопасности со встроенными методами фабрики, которые возвращают несколько хорошо известных поставщиков маркеров. Для хранения учетных данных SAS мы будем использовать метод [CreateSharedAccessSignatureTokenProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_). Затем экземпляр [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) объединяется с базовым адресом пространства имен служебной шины и поставщиком маркеров.

Класс [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) предоставляет методы для создания, перечисления и удаления сущностей обмена сообщениями. Приведенный здесь код иллюстрирует создание и использование экземпляра **NamespaceManager** для создания раздела **DataCollectionTopic**.

```csharp
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";

TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);

namespaceManager.CreateTopic("DataCollectionTopic");
```

Обратите внимание, что существуют перегруженные версии метода [CreateTopic](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_), которые позволяют задавать свойства раздела. Например, можно указать значение "срока жизни" (TTL) по умолчанию, которое будет применяться к сообщениям, отправляемым в раздел. Затем добавьте подписки **Запасы** и **Панель мониторинга**.

```csharp
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>Отправка сообщений в раздел
Для выполнения операций над сущностями служебной шины, например для отправки и получения сообщений, приложение сначала должно создать объект [MessagingFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory#microsoft_servicebus_messaging_messagingfactory). По аналогии с классом [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) экземпляр **MessagingFactory** создается на основе базового адреса пространства имен службы и поставщика маркеров.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

Сообщения, отправляемые в разделы служебной шины и получаемые из них, представляют собой экземпляры класса [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Этот класс состоит из набора стандартных свойств (таких как [Label](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) и [TimeToLive](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), словаря, в котором хранятся свойства приложения, и набора произвольных данных приложения. Приложение может задать набор произвольных данных, передав любой сериализуемый объект, который будет использовать [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx) для сериализации объекта. (В следующем примере передается объект **SalesData**, представляющий данные о продажах от терминала POS.) Также можно предоставить объект [Stream](https://msdn.microsoft.com/library/system.io.stream.aspx).

```csharp
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

Самый простой способ отправки сообщений в раздел — с помощью метода [CreateMessageSender](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageSender_System_String_) создать объект [MessageSender](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagesender) прямо в экземпляре класса [MessagingFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory).

```csharp
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>Получение сообщений из подписки
По аналогии с использованием очередей для получения сообщений из подписки можно использовать объект [MessageReceiver](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagereceiver), который создается непосредственно в [MessagingFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory) с помощью [CreateMessageReceiver](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageReceiver_System_String_). Можно использовать один из двух различных режимов получения (**ReceiveAndDelete** и **PeekLock**), как описано в статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md).

Обратите внимание, что при создании **MessageReceiver** для подписок параметр *entityPath* имеет вид `topicPath/subscriptions/subscriptionName`. Поэтому, чтобы создать **MessageReceiver** для подписки **Запасы** раздела **DataCollectionTopic**, для *entityPath* необходимо задать значение `DataCollectionTopic/subscriptions/Inventory`. Код выглядит следующим образом.

```csharp
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

## <a name="subscription-filters"></a>Фильтры подписки
Пока в этом сценарии все сообщения, отправляемые в раздел, становились доступными для всех зарегистрированных подписок. Ключевая фраза здесь "становились доступными". Хотя подпискам служебной шины доступны все сообщения, отправленные в раздел, при желании в виртуальную очередь подписки можно скопировать только часть этих сообщений. Это возможно благодаря использованию *фильтров* подписок. При создании подписки можно указать критерий фильтра в форме предиката в стиле SQL92, который работает со свойствами сообщения: как с системными свойствами (например, [Label](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label)), так и со свойствами приложения, такими как **StoreName** в предыдущем примере.

Для развития сценария с целью это проиллюстрировать в наш сценарий розничной торговли добавляется второе хранилище. Данные о продажах со всех терминалов POS из обоих хранилищ по-прежнему должны направляться в централизованную систему управления запасами, но менеджера хранилища, использующего панель мониторинга, интересует только производительность этого хранилища. Для достижения этой цели можно использовать фильтрацию подписок. Обратите внимание, что при публикации сообщений терминалами POS они устанавливают свойство приложения **StoreName** для этих сообщений. Предположим, что у нас есть два хранилища — **Redmond** и **Seattle**. В этом случае терминалы POS в первом хранилище устанавливают значение параметра **StoreName** в своих сообщениях в **Redmond**, а терминалы во втором хранилище устанавливают значение параметра **StoreName** в **Seattle**. Диспетчеру хранилища Redmond нужны только данные от своих терминалов POS. Система выглядит следующим образом:

![Служебная шина&4;](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Чтобы настроить этот маршрут, создайте подписку **Панель мониторинга** следующим образом:

```csharp
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Благодаря этому [фильтру подписки](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter) в виртуальную очередь подписки **Панель мониторинга** копируются только сообщения со свойством **StoreName**, для которого задано значение **Redmond**. Но этим возможности фильтрации подписок не ограничиваются. Приложения могут иметь несколько правил фильтрации для каждой подписки, а также возможность изменять свойства сообщения при его попадании в виртуальную очередь подписки.

## <a name="summary"></a>Сводка
Все причины использования очередей, описанные в статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md), применимы и к разделам. К ним, в частности, относятся следующие:

* Временное разделение – производители (отправители) и потребители (получатели) не должны находиться в сети одновременно.
* Выравнивание нагрузки — пики нагрузки сглаживаются при помощи раздела, что позволяет подготавливать потребляющие приложения для средней, а не пиковой нагрузки.
* Балансировка нагрузки — по аналогии с очередями можно иметь несколько конкурирующих потребителей, прослушивающих одну и ту же подписку. При этом каждое сообщение передается только одному из потребителей, тем самым достигается балансировка нагрузки.
* Слабая взаимозависимость — сеть обмена сообщениями можно развивать, не затрагивая существующие конечные точки: например, добавляя подписки или изменяя фильтры для раздела для подключения новых потребителей.

## <a name="next-steps"></a>Дальнейшие действия
Сведения об использовании очередей в сценарии розничных точек продаж см. в статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md).




<!--HONumber=Jan17_HO2-->


