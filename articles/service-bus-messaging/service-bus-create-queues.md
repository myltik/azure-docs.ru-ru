---
title: "Создание приложений, использующих очереди служебной шины Azure | Документация Майкрософт"
description: "Как написать простое приложение на основе очереди, которое использует служебную шину Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 754d91b3-1426-405e-84b4-fd36d65b114a
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: d987aa22379ede44da1b791f034d713a49ad486a
ms.openlocfilehash: 40414edebcd76fc93136cbc14d7a6436fc7f6da5
ms.lasthandoff: 02/16/2017


---
# <a name="create-applications-that-use-service-bus-queues"></a>Создание приложений, использующих очереди служебной шины
В этом разделе описаны очереди служебной шины и показаны способы написания простого приложения на основе очереди, которое использует служебную шину.

Рассмотрим сценарий из мира розничной продажи, в котором данные о продажах из отдельных точек продаж (POS) должны направляться в систему управления запасами, которая использует эти данные, чтобы определять необходимость пополнения запасов. В этом решении используется обмен сообщениями служебной шины для обмена данными между терминалами и системой управления запасами, как показано на следующем рисунке:

![Очереди служебной шины, рисунок 1](./media/service-bus-create-queues/IC657161.gif)

Каждый терминал POS передает данные о продажах путем отправки сообщений в очередь **DataCollectionQueue**. Эти сообщения остаются в этой очереди до тех пор, пока не извлекаются системой управления запасами. Эта схема часто называется *асинхронным обменом сообщениями*, так терминал POS не должен ждать ответа от системы управления запасами для продолжения обработки.

## <a name="why-queuing"></a>Зачем использовать очередь?
Прежде чем обратиться к коду данного приложения рассмотрим преимущества использования очереди по сравнению с прямым (синхронным) взаимодействием терминалов POS с системой управления запасами.

### <a name="temporal-decoupling"></a>Временное разделение
В асинхронной модели обмена отправители и получатели сообщений не обязательно должны находиться в сети одновременно. Инфраструктура обмена сообщениями надежно хранит сообщения до тех пор, пока принимающая сторона не будет готова к их приему. Это означает, что возможно отключение компонентов распределенного приложения как в плановых ситуациях, например для обслуживания, так и в экстренных случаях, чтобы исключить влияние сбоя одного компонента на систему в целом. Более того, потребляющему приложению достаточно находиться в сети несколько раз в день в определенное время. Например в данном сценарии розничной торговли, система управления запасами может подключиться к сети только после окончания рабочего дня.

### <a name="load-leveling"></a>Выравнивание нагрузки
Во многих приложениях уровень нагрузки на систему меняется с течением времени, тогда как время, затрачиваемое на обработку каждой рабочей единицы, как правило, постоянно. Благодаря обмену сообщениями между поставщиками и потребителями через посредника с использованием очереди потребляющее приложение (рабочую роль) достаточно подготовить для обработки средней, а не пиковой нагрузки. В таких случаях при колебаниях входящей нагрузки просто изменяется глубина очереди. Это позволяет существенно сократить расходы на инфраструктуру, необходимую для обработки нагрузки приложения.

![Очереди служебной шины, рисунок 2](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>Балансировка нагрузки.
По мере возрастания нагрузки могут потребоваться дополнительные рабочие роли для чтения из очереди рабочих ролей. Каждое сообщение обрабатывается одним рабочим процессом. Кроме того, балансировка нагрузки по запросу обеспечивает оптимальное использование компьютеров рабочих ролей с разной вычислительной мощностью, позволяя извлекать сообщения с максимально доступной скоростью. Такая концепция часто называется моделью конкурирующих потребителей.

![Очереди служебной шины, рисунок 3](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>Слабые связи
Использование очередей сообщений в качестве посредника между производителями и потребителями сообщений уменьшает внутреннюю зависимость между компонентами. Так как производители и потребители не зависят друг от друга, обновление потребителя не оказывает влияния на производителя. Кроме того, топология обмена сообщениями может развиваться, не влияя на существующие конечные точки. Мы обсудим это подробнее, когда будем говорить о публикациях и подписках.

## <a name="show-me-the-code"></a>Покажите мне код
Ниже показано, как использовать служебную шину для построения этого приложения.

### <a name="sign-up-for-an-azure-account"></a>Регистрация учетной записи Azure
Чтобы начать работу со служебной шиной, вам потребуется учетная запись Azure. Если у вас ее нет, вы можете зарегистрироваться для получения бесплатной учетной записи [здесь](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="create-a-namespace"></a>Создание пространства имен
После получения подписки можно [создать пространство имен службы](service-bus-create-namespace-portal.md). Каждое пространство имен выступает в качестве определяющего область действия контейнера для набора сущностей служебной шины. Присвойте пространству имен имя, которое будет уникальным среди всех учетных записей служебной шины. 

### <a name="install-the-nuget-package"></a>Установка пакета NuGet
Чтобы использовать пространство имен служебной шины, приложение должно ссылаться на сборку служебной шины, а именно Microsoft.ServiceBus.dll. Эта сборка является частью пакета SDK Microsoft Azure, который можно загрузить на [странице загрузки пакета SDK Azure](https://azure.microsoft.com/downloads/). Однако проще всего получить API служебной шины и настроить свое приложение с учетом всех зависимостей служебной шины с помощью [пакета NuGet для служебной шины](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

### <a name="create-the-queue"></a>Создание очереди
Операции управления для сущностей обмена сообщениями служебной шины (очередей и разделов публикации и подписки) выполняются с помощью класса [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager). Служебная шина использует модель безопасности на основе [подписанного URL-адреса](service-bus-sas.md) (SAS). Класс [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) представляет поставщика маркеров безопасности со встроенными методами фабрики, которые возвращают несколько хорошо известных поставщиков маркеров. Для хранения учетных данных SAS мы будем использовать метод [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_). Затем экземпляр [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) объединяется с базовым адресом пространства имен служебной шины и поставщиком маркеров.

Класс [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) предоставляет методы для создания, перечисления и удаления сущностей обмена сообщениями. Приведенный здесь код иллюстрирует создание и использование экземпляра класса [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) для создания очереди **DataCollectionQueue**.

```csharp
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

Обратите внимание, что существуют перегруженные версии метода [CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_), которые позволяют задавать свойства очереди. Например, можно указать значение "срока жизни" (TTL) по умолчанию, которое будет применяться к сообщениям, отправляемым в очередь.

### <a name="send-messages-to-the-queue"></a>Отправка сообщений в очередь
Для выполнения операций над сущностями служебной шины, например для отправки и получения сообщений, приложение сначала должно создать объект [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory). По аналогии с классом [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) экземпляр [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) создается на основе базового адреса пространства имен службы и поставщика маркеров.

```csharp
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

Сообщения, отправляемые в очереди служебной шины и получаемые из них, представляют собой экземпляры класса [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Этот класс состоит из набора стандартных свойств (таких как [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) и [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), словаря, в котором хранятся свойства приложения, и набора произвольных данных приложения. Приложение может задать набор произвольных данных, передав любой сериализуемый объект, который будет использовать [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx) для сериализации объекта. (В следующем примере передается объект **SalesData**, представляющий данные о продажах от терминала POS.) Также можно предоставить объект [Stream](https://msdn.microsoft.com/library/system.io.stream.aspx).

Самый простой способ отправки сообщений в очередь, в данном случае **DataCollectionQueue**, — воспользоваться методом [CreateMessageSender](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageSender_System_String_) для создания объекта [MessageSender](/dotnet/api/microsoft.servicebus.messaging.messagesender) прямо из экземпляра класса [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory).

```csharp
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>Получение сообщений из очереди
Для получения сообщений из очереди можно воспользоваться объектом [MessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), который создается непосредственно в [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) с помощью [CreateMessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageReceiver_System_String_). Получатели сообщений могут работать в двух различных режимах: **ReceiveAndDelete** и **PeekLock**. Режим приема [ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode) устанавливается при создании получателя сообщения и задается в качестве параметра вызова [CreateMessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagingfactory?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageReceiver_System_String_Microsoft_ServiceBus_Messaging_ReceiveMode_).

В режиме **ReceiveAndDelete** получение является одиночной операцией. Это значит, что, когда служебная шина получает запрос, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель. Он наиболее эффективен в сценариях, когда приложение допускает отсутствие обработки сообщения в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Так как служебная шина помечает сообщение как использованное, то после перезапуска и начала приема сообщений приложение пропустит сообщение, полученной перед возникновением сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, неустойчивые к потере сообщений. Получив запрос, служебная шина находит следующее сообщение, которое должно быть получено, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или надежно сохраняет его для последующей обработки), оно завершает второй этап процесса получения, вызывая метод [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) для полученного сообщения. Когда служебная шина фиксирует вызов [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete), сообщение помечается как использованное.

Также возможны два других результата. Во-первых, если приложение по каким-либо причинам не может обработать сообщение, оно может вызвать для полученного сообщения метод [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) (вместо метода [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). После этого служебная шина разблокирует сообщение в очереди, сделав его доступным для приема тому же потребителю или другому конкурирующему потребителю. Во-вторых, с блокировкой связано определенное время ожидания. Если приложение не сможет обработать сообщение до истечения времени ожидания (например, при сбое приложения), служебная шина автоматически разблокирует сообщение, сделав его снова доступным для получения (фактически выполняя операцию [прерывания](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) по умолчанию).

Обратите внимание, что, если сбой приложения происходит после обработки сообщения, но до отправки запроса [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete), это сообщение будет повторно доставлено в приложение после его перезапуска. Такой подход часто называется обработкой *по крайней мере один раз*. Это означает, что каждое приложение будет обработано по крайней мере один раз, но в некоторых случаях одно и то же сообщение может быть доставлено повторно. Если сценарий не допускает обработки дубликатов сообщений, в приложении необходимо реализовать дополнительную логику для обнаружения дубликатов. Обнаружить дубликаты можно с помощью свойства [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) сообщения. Значение данного свойства остается неизменным при всех попытках доставки. Это называется обработкой *только один раз*.

Приведенный здесь код получает и обрабатывает сообщения в режиме **PeekLock**, который используется по умолчанию, если значение параметра [ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode) не указано явно.

```csharp
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

### <a name="use-the-queue-client"></a>Использование клиента очереди
В примерах, приведенных ранее в этом разделе, создаются объекты [MessageSender](/dotnet/api/microsoft.servicebus.messaging.messagesender) и [MessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) непосредственно из экземпляра класса [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory). Эти методы используются для отправки и получения сообщений из очереди соответственно. Альтернативным подходом является использование объекта [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient), который поддерживает операции отправки и получения сообщений, а также более сложные функции, такие как сеансы.

```csharp
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

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы ознакомились с основами использования очередей, обратитесь к статье [Создание приложений, использующих разделы и подписки служебной шины](service-bus-create-topics-subscriptions.md). В этой статье объясняется, как использовать возможности публикации и подписки разделов и подписок служебной шины.


