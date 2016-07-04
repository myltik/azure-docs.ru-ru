<properties 
    pageTitle="Создание приложений, использующих разделы и подписки служебной шины | Microsoft Azure"
    description="Введение в возможности публикации и подписки, предлагаемые разделами и подписками служебной шины."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/21/2016"
    ms.author="sethm" />

# Создание приложений, использующих разделы и подписки служебной шины

Служебная шина Azure поддерживает набор облачных технологий промежуточного уровня, ориентированных на обработку сообщений. Эти технологии представлены надежными очередями сообщений, а также возможностями публикации и подписки в рамках обмена сообщениями. Эта статья основана на сведениях, представленных в статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md), и предлагает введение в возможности публикации и подписки, предлагаемые разделами служебной шины.

## Развитие сценария розничной торговли

В этой статье продолжает использоваться сценарий розничной торговли, который описан в статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md). Напомним, что данные о продажах из отдельных точек продаж (POS) должны направляться в систему управления запасами, которая использует эти данные для определения момента пополнения запасов. Каждый терминал POS сообщает данные о продажах путем отправки сообщений в очередь **DataCollectionQueue**, в которой эти сообщения хранятся до их получения системой управления запасами, как показано ниже:

![Служебная шина 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Для развития этого сценария к системе было добавлено новое требование: владелец магазина хочет иметь возможность отслеживать работу хранилища в режиме реального времени.

Для реализации этого требования система должна "разветвить" поток данных о продажах. Мы по-прежнему хотим, чтобы как и прежде каждое сообщение, отправляемое терминалами POS, направлялось в систему управления запасами. Но мы также хотим иметь еще одну копию каждого сообщения, чтобы представить ее владельцу магазина на панели мониторинга.

В любой ситуации такого рода, в которой требуется, чтобы каждое сообщение могло использоваться несколькими сторонами, можно воспользоваться *разделами* служебной шины. Разделы предоставляют модель публикации и подписки, благодаря которой каждое опубликованное сообщение становится доступным одной или нескольким подпискам, зарегистрированным в разделе. В отличие от этой схемы, в очереди каждое сообщение доступно только одному потребителю.

Сообщения отправляются в раздел точно так же, как в очередь. Однако сообщения не получаются из раздела напрямую; они получаются из подписок. Подписка на раздел напоминает виртуальную очередь, которая получает копии сообщений, отправленных в раздел. Сообщения извлекаются из подписки так же, как и из очереди.

Вернемся к сценарию розничной торговли. В этом сценарии очередь заменяется разделом и добавляется подписка, которую может использовать компонент системы управления запасами. Теперь система выглядит следующим образом:

![Служебная шина 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

Эта конфигурация работает аналогично предыдущей схеме на основе очереди. То есть, сообщения, отправляемые в раздел, направляются в подписку **Запасы**, из которой их получает **Система управления запасами**.

Для поддержки панели мониторинга мы создаем вторую подписку на раздел, как показано ниже.

![Служебная шина 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

В такой конфигурации каждое сообщение из терминалов POS доступно обеим подпискам **Панель мониторинга** и **Запасы**.

## Покажите мне код

В статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md) описаны регистрация учетной записи Azure и создание пространства имен службы. Чтобы использовать пространство имен служебной шины, приложение должно ссылаться на сборку служебной шины, а именно Microsoft.ServiceBus.dll. Самым простым способом сослаться на зависимости служебной шины является установка [пакета Nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) служебной шины. Эта сборка также доступна в составе пакета SDK Azure. Ее можно загрузить на [странице загрузки пакета SDK Azure](https://azure.microsoft.com/downloads/).

### Создание раздела и подписок

Операции управления для сущностей обмена сообщениями служебной шины (очередей и разделов публикации и подписки) выполняются с помощью класса [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Для создания экземпляра [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) для определенного пространства имен необходимы соответствующие учетные данные. Служебная шина использует модель безопасности на основе [подписи общего доступа (SAS)](service-bus-sas-overview.md). Класс [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) представляет поставщика маркеров безопасности со встроенными методами фабрики, которые возвращают несколько хорошо известных поставщиков маркеров. Для хранения учетных данных SAS мы будем использовать метод [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx). Затем экземпляр [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) объединяется с базовым адресом пространства имен служебной шины и поставщиком маркеров.

Класс [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) предоставляет методы для создания, перечисления и удаления сущностей обмена сообщениями. Приведенный здесь код иллюстрирует создание и использование экземпляра [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) для создания раздела **DataCollectionTopic**.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

Обратите внимание, что существуют перегруженные версии метода [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx), которые позволяют задавать свойства раздела. Например, можно указать значение "срока жизни" (TTL) по умолчанию, которое будет применяться к сообщениям, отправляемым в раздел. Затем добавьте подписки **Запасы** и **Панель мониторинга**.

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### Отправка сообщений в раздел

Для выполнения операций над сущностями служебной шины, например для отправки и получения сообщений, приложение сначала должно создать объект [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx). Аналогично классу [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) экземпляр [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) создается на основе базового адреса пространства имен службы и поставщика маркеров.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

Сообщения, отправляемые в разделы служебной шины и получаемые из них, представляют собой экземпляры класса [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Этот класс состоит из набора стандартных свойств (таких как [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) и [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), словаря, в котором хранятся свойства приложения, и набора произвольных данных приложения. Приложение может задать набор произвольных данных, передав любой сериализуемый объект, который будет использовать [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) для сериализации объекта. (В следующем примере передается объект **SalesData**, представляющий данные о продажах от терминала POS). Также можно предоставить объект [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx).

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

Самый простой способ отправки сообщений в раздел — воспользоваться [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) для создания объекта [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) прямо из экземпляра [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx).

```
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### Получение сообщений от подписки

По аналогии с использованием очередей для получения сообщений из подписки можно использовать объект [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx), который создается непосредственно в [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) с помощью [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Можно использовать один из двух различных режимов получения (**ReceiveAndDelete** и **PeekLock**), как описано в статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md).

Обратите внимание, что при создании [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) для подписок параметр *entityPath* имеет вид `topicPath/subscriptions/subscriptionName`. Поэтому, чтобы создать [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) для подписки **Запасы** раздела **DataCollectionTopic**, *entityPath* необходимо установить в `DataCollectionTopic/subscriptions/Inventory`. Код выглядит следующим образом.

```
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

## Фильтры подписки

Пока в этом сценарии все сообщения, отправляемые в раздел, становились доступными для всех зарегистрированных подписок. Ключевая фраза здесь "становились доступными". Хотя подпискам служебной шины доступны все сообщения, отправленные в раздел, при желании в виртуальную очередь подписки можно скопировать только часть этих сообщений. Это возможно благодаря использованию *фильтров* подписок. При создании подписки можно указать критерий фильтра в форме предиката в стиле SQL92, который работает со свойствами сообщения и как с системными свойствами (например, [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)), и как со свойствами приложения, такими как **StoreName** в предыдущем примере.

Для развития сценария с целью это проиллюстрировать в наш сценарий розничной торговли добавляется второе хранилище. Данные о продажах со всех терминалов POS из обоих хранилищ по-прежнему должны направляться в централизованную систему управления запасами, но менеджера хранилища, использующего панель мониторинга, интересует только производительность этого хранилища. Для достижения этой цели можно использовать фильтрацию подписок. Обратите внимание, что при публикации сообщений терминалами POS они устанавливают свойство приложения **StoreName** для этих сообщений. Предположим, что у нас есть два хранилища **Redmond** и **Seattle**. В этом случае терминалы POS в первом хранилище устанавливают значение **StoreName** в своих сообщениях в **Redmond**, а терминалы во втором хранилище устанавливают значение свойства **StoreName** в **Seattle**. Диспетчеру хранилища Redmond нужны только данные от своих терминалов POS. Система выглядит следующим образом:

![Служебная шина 4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Чтобы настроить этот маршрут, создайте подписку **Панель мониторинга** следующим образом.

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Благодаря этому фильтру подписки в виртуальную очередь подписки **Панель мониторинга** копируются только сообщения со свойством **StoreName**, установленным в **Redmond**. Но этим возможности фильтрации подписок не ограничиваются. Приложения могут иметь несколько правил фильтрации для каждой подписки, а также возможность изменять свойства сообщения при его попадании в виртуальную очередь подписки.

## Сводка

Все причины использования очередей, описанные в статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md), также справедливы и для разделов. К ним, в частности, относятся следующие:

- Временное разделение – производители (отправители) и потребители (получатели) не должны находиться в сети одновременно.

- Выравнивание нагрузки — пики нагрузки сглаживаются разделом, что позволяет подготавливать приложения потребителей для средней нагрузки вместо пиковой нагрузки.

- Балансировка нагрузки — по аналогии с очередями можно иметь несколько конкурирующих клиентов, прослушивающих одну и ту же подписку. При этом каждое сообщение передается только одному из потребителей, тем самым достигается балансировка нагрузки.

- Слабое связывание — сеть обмена сообщениями можно развивать, не затрагивая существующие конечные точки: например, добавляя подписки или изменяя фильтры для раздела для подключения новых потребителей.

## Дальнейшие действия

Сведения об использовании очередей в сценарии розничных точек продаж см. в статье [Создание приложений, использующих очереди служебной шины](service-bus-create-queues.md).

<!---HONumber=AcomDC_0622_2016-->