<properties 
    pageTitle="Создание приложений, использующих очереди служебной шины | Microsoft Azure"
    description="Как написать простое приложение на основе очереди, которое использует служебную шину."
    services="service-bus-messaging"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus-messaging"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/21/2016"
    ms.author="sethm" />

# Создание приложений, использующих очереди служебной шины

В этом разделе описаны очереди служебной шины и показаны способы написания простого приложения на основе очереди, которое использует служебную шину.

Рассмотрим сценарий из мира розничной продажи, в котором данные о продажах из отдельных точек продаж (POS) должны направляться в систему управления запасами, которая использует эти данные, чтобы определять необходимость пополнения запасов. В этом решении используется обмен сообщениями служебной шины для обмена данными между терминалами и системой управления запасами, как показано на следующем рисунке:

![Очереди служебной шины. Рис. 1](./media/service-bus-create-queues/IC657161.gif)

Каждый терминал POS передает данные о продажах путем отправки сообщений в очередь **DataCollectionQueue**. Эти сообщения остаются в этой очереди до тех пор, пока не извлекаются системой управления запасами. Эта схема часто называется *асинхронным обменом сообщениями*, так терминал POS терминал не должен ждать ответа от системы управления запасами для продолжения обработки.

## Зачем использовать очередь?

Прежде чем обратиться к коду данного приложения рассмотрим преимущества использования очереди по сравнению с прямым (синхронным) взаимодействием терминалов POS с системой управления запасами.

### Временное разделение

В асинхронной модели обмена отправители и получатели сообщений не обязательно должны находиться в сети одновременно. Инфраструктура обмена сообщениями надежно хранит сообщения до тех пор, пока принимающая сторона не будет готова к их приему. Это означает, что возможно отключение компонентов распределенного приложения как в плановых ситуациях, например для обслуживания, так и в экстренных случаях, чтобы исключить влияние сбоя одного компонента на систему в целом. Более того, потребляющему приложению достаточно находиться в сети несколько раз в день в определенное время. Например в данном сценарии розничной торговли, система управления запасами может подключиться к сети только после окончания рабочего дня.

### Выравнивание нагрузки

Во многих приложениях уровень нагрузки на систему меняется с течением времени, тогда как время, затрачиваемое на обработку каждой рабочей единицы, как правило, постоянно. Благодаря обмену сообщениями между поставщиками и потребителями через посредника с использованием очереди потребляющее приложение (рабочую роль) достаточно подготовить для обработки средней, а не пиковой нагрузки. В таких случаях при колебаниях входящей нагрузки просто изменяется глубина очереди. Это позволяет существенно сократить расходы на инфраструктуру, необходимую для обработки нагрузки приложения.

![Очереди служебной шины. Рис. 2](./media/service-bus-create-queues/IC657162.gif)

### Балансировка нагрузки.

По мере возрастания нагрузки могут потребоваться дополнительные рабочие роли для чтения из очереди рабочих ролей. Каждое сообщение обрабатывается одним рабочим процессом. Кроме того, балансировка нагрузки по запросу обеспечивает оптимальное использование компьютеров рабочих ролей с разной вычислительной мощностью, позволяя извлекать сообщения с максимально доступной скоростью. Такая концепция часто называется моделью конкурирующих потребителей.

![Очереди служебной шины. Рис. 3](./media/service-bus-create-queues/IC657163.gif)

### Слабые связи

Использование очередей сообщений в качестве посредника между производителями и потребителями сообщений уменьшает внутреннюю зависимость между компонентами. Так как производители и потребители не зависят друг от друга, обновление потребителя не оказывает влияния на производителя. Кроме того, топология обмена сообщениями может развиваться, не влияя на существующие конечные точки. Мы обсудим это подробнее, когда будем говорить о публикациях и подписках.

## Покажите мне код

Ниже показано, как использовать служебную шину для построения этого приложения.

### Регистрация учетной записи Azure

Чтобы начать работу со служебной шиной, вам потребуется учетная запись Azure. Если у вас ее нет, вы можете зарегистрироваться для получения бесплатной учетной записи [здесь](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### Создание пространства имен службы

После получения подписки можно [создать новое пространство имен](../service-bus/service-bus-create-namespace-portal.md). Каждое пространство имен выступает в качестве определяющего область действия контейнера для набора сущностей служебной шины. Присвойте пространству имен имя, которое будет уникальным среди всех учетных записей служебной шины.

### Установка пакета NuGet

Чтобы использовать пространство имен служебной шины, приложение должно ссылаться на сборку служебной шины, а именно Microsoft.ServiceBus.dll. Эту сборка является частью пакета SDK Microsoft Azure, который можно загрузить на [странице загрузки пакета SDK Azure](https://azure.microsoft.com/downloads/). Однако проще всего получить API служебной шины и настроить свое приложение с учетом всех зависимостей служебной шины с помощью [пакета NuGet для служебной шины](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

### Создание очереди

Операции управления для сущностей обмена сообщениями служебной шины (очередей и разделов публикации и подписки) выполняются с помощью класса [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Служебная шина использует модель безопасности на основе [подписи общего доступа (SAS)](../service-bus/service-bus-sas-overview.md). Класс [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) представляет поставщика маркеров безопасности со встроенными методами фабрики, которые возвращают несколько хорошо известных поставщиков маркеров. Для хранения учетных данных SAS мы будем использовать метод [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx). Затем экземпляр [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) объединяется с базовым адресом пространства имен служебной шины и поставщиком маркеров.

Класс [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) предоставляет методы для создания, перечисления и удаления сущностей обмена сообщениями. Приведенный здесь код иллюстрирует создание и использование экземпляра [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) для создания очереди **DataCollectionQueue**.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Обратите внимание, что существуют перегруженные версии метода [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx), которые позволяют задавать свойства очереди. Например, можно указать значение "срока жизни" (TTL) по умолчанию, которое будет применяться к сообщениям, отправляемым в очередь.

### Отправка сообщений в очередь

Для выполнения операций над сущностями служебной шины, например для отправки и получения сообщений, приложение сначала должно создать объект [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx). Аналогично классу [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) экземпляр [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) создается на основе базового адреса пространства имен службы и поставщика маркеров.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

Сообщения, отправляемые в очереди служебной шины и получаемые из них, представляют собой экземпляры класса [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Этот класс состоит из набора стандартных свойств (таких как [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) и [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), словаря, в котором хранятся свойства приложения, и набора произвольных данных приложения. Приложение может задать набор произвольных данных, передав любой сериализуемый объект, который будет использовать [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) для сериализации объекта. (В следующем примере передается объект **SalesData**, представляющий данные о продажах от терминала POS). Также можно предоставить объект [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx).

Самый простой способ отправки сообщений в очередь, в данном случае **DataCollectionQueue** — воспользоваться [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) для создания объекта [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) прямо из экземпляра [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx).

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### Получение сообщений из очереди

Для получения сообщений из очереди можно воспользоваться объектом [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx), который создается непосредственно в [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) с помощью [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Получатели сообщений могут работать в двух различных режимах: **ReceiveAndDelete** и **PeekLock**. Режим приема [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) устанавливается при создании получателя сообщения и задается в качестве параметра вызова [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx).


В режиме **ReceiveAndDelete** получение является одиночной операцией. Это означает, что когда служебная шина получает запрос, она помечает сообщение как использованное и возвращает его в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель. Он наиболее эффективен в сценариях, когда приложение допускает отсутствие обработки сообщения в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Так как служебная шина помечает сообщение как использованное, то после перезапуска и начала приема сообщений приложение пропустит сообщение, полученной перед возникновением сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, неустойчивые к потере сообщений. Получив запрос, служебная шина находит следующее сообщение, которое должно быть получено, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или надежно сохраняет его для последующей обработки), оно завершает второй этап процесса получения, вызывая метод [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) для полученного сообщения. Когда служебная шина фиксирует вызов [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), сообщение помечается как прочитанное.

Также возможны два других результата. Во-первых, если приложение по каким-либо причинам не может обработать сообщение, оно может вызвать для полученного сообщения метод [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) (вместо метода [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). После этого служебная шина разблокирует сообщение в очереди, сделав его доступным для приема тому же потребителю или другому конкурирующему потребителю. Во-вторых, с блокировкой связано определенное время ожидания. Если приложение не сможет обработать сообщение до истечения времени ожидания (например, при сбое приложения), служебная шина автоматически разблокирует сообщение, сделав его снова доступным для получения (фактически выполняя операцию [прерывания](https://msdn.microsoft.com/library/azure/hh181837.aspx) по умолчанию).

Обратите внимание, что если сбой приложения происходит после обработки сообщения, но до отправки запроса [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), это сообщение будет повторно доставлено в приложение после его перезапуска. Такой подход часто называется обработкой *по крайней мере один раз*. Это означает, что каждое приложение будет обработано по крайней мере один раз, но в некоторых случаях одно и то же сообщение может быть доставлено повторно. Если сценарий не допускает обработки дубликатов сообщений, в приложении необходимо реализовать дополнительную логику для обнаружения дубликатов. Обнаружить дубликаты можно с помощью свойства [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) сообщения. Значение данного свойства остается неизменным при всех попытках доставки. Это называется обработкой *только один раз*.

Приведенный здесь код получает и обрабатывает сообщения в режиме **PeekLock**, который используется по умолчанию, если значение [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) не указано явно.

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
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

### Использование клиента очереди

В примерах, приведенных ранее в этом разделе, создаются объекты [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) и [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) непосредственно из [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx). Эти методы используются для отправки и получения сообщений из очереди соответственно. Альтернативным подходом является использование класса [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), который поддерживает операции отправки и получения сообщений, а также более сложные функции, такие как сеансы.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## Дальнейшие действия

Теперь, когда вы ознакомились с основами использования очередей, см. статью [Создание приложений, использующих разделы и подписки служебной шины](service-bus-create-topics-subscriptions.md). В этой статье объясняется, как использовать возможности публикации и подписки разделов и подписок служебной шины.

<!---HONumber=AcomDC_0928_2016-->