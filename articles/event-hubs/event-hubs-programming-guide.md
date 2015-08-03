<properties 
   pageTitle="Руководство по программированию концентраторов событий"
   description="Сведения о программировании с использованием концентраторов событий Azure с помощью пакета SDK Azure .NET."
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/10/2015"
   ms.author="sethm" />

# Руководство по программированию концентраторов событий

В этом разделе представлены сведения о программировании с использованием концентраторов событий Azure с помощью пакета SDK Azure .NET. Предполагается, что вы уже имеете представление о концентраторах событий. Общие сведения о концентраторах событий см. в статье [Общие сведения о концентраторах событий](event-hubs-overview.md).

## Публикация событий: издатели событий

Отправка событий в концентратор событий осуществляется с использованием HTTP POST или через подключение AMQP 1.0. Выбор способа зависит от определенного сценария, к которому выполняется обращение. Подключения AMQP 1.0 измеряются как подключения через посредника по служебной шине. Они больше всего подходят для сценариев с большими объемами сообщений и менее строгими требованиями к задержке, поскольку такие подключения обеспечивают постоянный канал обмена сообщениями.

Концентраторы событий создаются и обрабатываются с помощью класса [NamespaceManager](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx). При использовании управляемых API .NET основными конструктивными элементами для публикации данных в концентраторах событий являются классы [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) и [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx). Класс [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) предоставляет коммуникационный канал AMQP, по которому события отправляются в концентратор событий. Класс [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) представляет собой событие и используется для публикации сообщений в концентратор событий. Этот класс содержит текст, некоторые метаданные и данные заголовка о событии. По мере передачи объекта [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) объекта в концентратор событий к этому объекту добавляются другие свойства.

## Приступая к работе

Классы .NET, которые поддерживают концентраторы событий, входят в сборку Microsoft.ServiceBus.dll. Самый простой способ получить API служебной шины и настроить свое приложение с учетом всех зависимостей служебной шины — это скачать пакет NuGet для служебной шины. Дополнительные сведения см. в разделе[Использование пакета NuGet для служебной шины](https://msdn.microsoft.com/library/dn741354.aspx). Кроме того, можно воспользоваться [консолью диспетчера пакетов](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) в Visual Studio. Для этого выполните следующую команду в окне [консоли диспетчера пакетов](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```powershell
Install-Package WindowsAzure.ServiceBus
```

## Создание концентратора событий

Для создания концентраторов событий можно использовать класс [NamespaceManager](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx). Например:

```c
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

В большинстве случаев рекомендуется использовать методы [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx), чтобы избежать возникновения исключений в случае перезапуска службы. Например:

```
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Для выполнения всех операции по созданию концентратора событий, включая [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx) требуется разрешение на **управление** необходимым пространством имен. Если вы хотите ограничить разрешения приложения издателя или потребителя, эти вызовы операций создания можно исключить из рабочего кода при использовании учетных данных с ограниченными разрешениями.

Класс [EventHubDescription](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubdescription.aspx) содержит сведения о концентраторе событий, включая правила авторизации, интервал хранения сообщений, идентификаторы секций, состояние и путь. Этот класс можно использовать для обновления метаданных в концентраторе событий.

## Создание клиента концентратора событий

Основным классом для взаимодействия с концентраторами событий является класс [Microsoft.ServiceBus.Messaging.EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx). Этот класс предоставляет возможности отправителя и получателя. Можно создать экземпляр этого класса с помощью метода [Create](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.create.aspx), как показано в следующем примере.

```
var client = EventHubClient.Create(description.Path);
```

Этот метод использует сведения о подключении к служебной шине в файле App.config в разделе `appSettings`. Пример раздела XML `appSettings`, в котором хранятся данные подключения к служебной шине, см. в документации для метода [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.create.aspx).

Кроме того, можно создать клиент из строки подключения. Это удобно в случаях, когда используются рабочие роли Azure, поскольку строку можно хранить в свойствах конфигурации для исполнителя. Например:

```
EventHubClient.CreateFromConnectionString("your_connection_string");
```

Строка подключения будет иметь тот же формат, в котором она представлена в файле App.config для предыдущих методов:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=Manage;SharedAccessKey=[key]
```

Наконец, можно также создать объект [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) из экземпляра [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx), как показано в следующем примере.

```
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

Важно отметить, что дополнительные объекты [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx), созданные из экземпляра фабрики обмена сообщениями, будут повторно использовать одно и то же базовое подключение TCP. Таким образом эти объекты имеют ограничение на пропускную способность на стороне клиента. Метод [Create](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.create.aspx) использует одну фабрику обмена сообщениями. Если требуется достаточно высокая пропускная способность от одного отправителя, то можно создать несколько фабрик обмена сообщениями и один объект [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) из каждой фабрики обмена сообщениями.

## Отправка событий в концентратор событий

Отправка событий в концентратор событий выполняется путем создания экземпляра [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) и его отправки посредством метода [Send](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.send.aspx). Этот метод принимает один параметр экземпляра [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) и синхронно отправляет его в концентратор событий.

## Сериализация событий

Класс [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) имеет [четыре перегруженных конструктора](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx), которые принимают различные параметры, такие как объект и сериализатор, массив байтов или поток. Кроме того, можно создать экземпляр класса [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) и затем задать поток текста. При использовании JSON совместно с [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) можно применить метод **Encoding.UTF8.GetBytes()** для получения массива байтов для строки в кодировке JSON.

## Ключ секции

Класс [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) имеет свойство [PartitionKey](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.partitionkey.aspx), которое позволяет отправителю указывать значение, хэшируемое для создания назначения секции. Использование ключа секции гарантирует, что все события с одинаковым ключом отправляются в одну и ту же секцию концентратора событий. Общие ключи секции включают идентификаторы сеансов пользователей и уникальные идентификаторы отправителей. Свойство [PartitionKey](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) является необязательным и может быть указано при использовании метода [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) или [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx). Если не указать значение для свойства [PartitionKey](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.partitionkey.aspx), отправленные события будут распространены в секции по принципу циклического перебора.

## Пакетные операции отправки событий

Пакетная отправка событий позволяет значительно повысить пропускную способность. Метод [SendBatch](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) принимает параметр **IEnumerable** типа [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) и отправляется весь пакет в концентратор событий в виде атомарной операции.

```
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Важно отметить, что размер одного пакета не должен превышать 256 КБ для события. Кроме того, каждое сообщение в пакете использует один и тот же идентификатор издателя. Отправитель должен убедиться, что размер пакета не превышает максимальный размер события. Если же размер больше, генерируется ошибка **Send** клиента.

## Асинхронная отправка и отправка в нужном масштабе

События можно отправлять в концентратор событий асинхронно. Асинхронная отправка позволяет увеличить скорость, с которой клиент способен отправлять события. В асинхронной версии доступны оба метода [Send](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.send.aspx) и [SendBatch](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx), которые возвращают объект [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx). Несмотря на то что этот способ позволяет увеличить пропускную способность, он также может привести к возникновению ситуации, когда клиент продолжает отправлять события, даже когда отправка регулируется службой концентраторов событий. В случае неправильной реализации это может привести к возникновению ошибок на стороне клиента или к потере сообщений. Кроме того, на стороне клиента можно использовать свойство [RetryPolicy](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.cliententity.retrypolicy.aspx) для управления параметрами повторного выполнения попыток клиентом.

## Создание отправителя секции

Несмотря на то что наиболее распространена отправка событий в концентратор событий с ключом секции, в некоторых случаях может потребоваться отправить события напрямую в указанную секцию. Например:

```
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

Метод [CreatePartitionedSender](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.createpartitionedsender.aspx) возвращает объект [EventHubSender](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubsender.aspx), который можно использовать для публикации событий в определенной секции концентратора событий.

## Прием событий: потребители событий

Концентраторы событий имеют две основных модели потребления событий: прямые получатели и абстракции более высокого уровня, такие как [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx). Прямые получатели сами отвечают за собственную координацию доступа к секциям в группе потребителей.

### Прямой потребитель

Самый простой способ чтения из секции в группе потребителей — это воспользоваться классом [EventHubReceiver](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubreceiver.aspx). Чтобы создать экземпляр этого класса, необходимо использовать экземпляр класса [EventHubConsumerGroup](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubconsumergroup.aspx). В следующем примере кода при создании получателя для группы потребителей необходимо указать идентификатор секции.

```
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

Метод [CreateReceiver](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubconsumergroup.createreceiver.aspx) имеет несколько перегрузок, которые упрощают контроль над созданием модуля чтения. Эти методы включают указание смещения в качестве строки или метки времени, а также возможность указать, следует ли включать это указанное смещение в возвращаемый поток или запустить после него. После создания получателя можно начать прием событий в возвращаемом объекте. Метод [Receive](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubreceiver.receive.aspx) имеет четыре перегрузки, которые управляют параметрами операции получения, такими как размер пакета и время ожидания. Для увеличения пропускной способности потребителя можно использовать асинхронные версии этих методов. Например:

```
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

В отношении к определенной секции сообщения получаются в том порядке, в котором они были отправлены в концентратор событий. Смещение представляет собой маркер строки, используемый для идентификации сообщения в секции.

Важно отметить, что одновременно к одной секции в группе потребителей можно подключить не более 5 параллельных модулей чтения. При подключении или отключении модулей чтения их сеансы могут оставаться активным на несколько минут, прежде чем служба определит, что они были отключены. В течение этого времени повторное подключение к секции может завершиться сбоем. Завершенный пример написания прямого получателя для концентраторов событий см. в примере [прямых приемников концентраторов событий служебной шины](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6).

### Узел обработчика событий

Класс [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) обрабатывает данные из концентраторов событий. Эту реализацию следует использовать при создании модулей чтения событий на платформе .NET. Класс [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) предоставляет потокобезопасную, многопроцессную и безопасную среду выполнения для реализации обработчика событий, которая также предоставляет возможности управления созданием контрольных точек и арендой секций.

Чтобы воспользоваться классом [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx), можно реализовать интерфейс [IEventProcessor](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.aspx). Этот интерфейс содержит три метода:

- [OpenAsync](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.openasync.aspx)

- [CloseAsync](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.closeasync.aspx)

- [ProcessEventsAsync](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.processeventsasync.aspx)

Чтобы начать обработку событий, создайте экземпляр [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx), указав соответствующие параметры для концентратора событий. Затем вызовите метод [RegisterEventProcessorAsync](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.registereventprocessorasync.aspx) для регистрации вашей реализации интерфейса [IEventProcessor](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.aspx) в среде выполнения. На этом этапе узел попытается получить аренду для каждой секции в концентраторе событий, используя "жадный" алгоритм. Эти аренды будут продолжаться в течение заданного промежутка времени, после чего их необходимо продлить. По мере перехода новых узлов (в нашем случае это рабочие экземпляры) в оперативный режим они размещают резервирования аренды и со временем нагрузка смещается между узлами при каждой попытке получения дополнительной аренды.

![Узел обработчика событий](./media/event-hubs-programming-guide/IC759863.png)

Со временем устанавливается равновесие. Такая динамическая возможность позволяет применить к потребителям автоматическое масштабирование как вверх, так и вниз на основе использования ресурсов ЦП. Поскольку концентраторы событий не имеют прямой концепции подсчета сообщений, среднее использование ресурсов ЦП зачастую является оптимальным механизмом для измерения масштабирования внутреннего сервера или потребителя. Если издатели начинают публиковать больше событий, чем могут обработать потребители, увеличение использования ресурсов ЦП потребителями можно использовать для автоматического масштабирования на основе числа экземпляров исполнителей.

Класс [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) также реализует механизм создания контрольных точек на основе службы хранилища Azure. Этот механизм хранит смещение для каждой секции, чтобы каждый потребитель мог определить последнюю контрольную точку от предыдущего потребителя. По мере перехода секций между узлами перенос нагрузки осуществляется за счет механизма синхронизации.

## Отзыв издателя

Наряду с дополнительными возможностями среды выполнения [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx), концентраторы событий позволяют отзывать издателя для блокировки для некоторых из них возможности отправлять события в концентратор событий. Эти функции особенно полезны, если маркер издателя был взломан или после обновления программного обеспечения эти функции перестали работать должным образом. В этих случаях для идентификатора издателя, который является частью маркера SAS, можно заблокировать возможность публикации событий.

Дополнительные сведения об отзыве издателя и отправке в концентраторы событий в качестве издателя см. в примере [Крупномасштабная безопасная публикация концентраторов событий служебной шины](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## Дальнейшие действия

Дополнительные сведения о сценариях концентраторов событий см. в разделах, ссылки на которые указаны ниже.

- [Общие сведения об API концентраторов событий](event-hubs-api-overview.md)
- [Обзор концентраторов событий](event-hubs-overview.md)
- [Примеры кода концентраторов событий](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hub&f[0].Type=SearchText&ac=5)
- [Справочник по API узла обработчика событий](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)

<!---HONumber=July15_HO4-->