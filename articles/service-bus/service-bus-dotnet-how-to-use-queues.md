<properties
    pageTitle="Использование очередей служебной шины (.NET) | Microsoft Azure"
    description="Узнайте, как использовать очереди служебной шины в Azure. Примеры кода написаны на C# с использованием API .NET."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sethm"/>

# Как использовать очереди служебной шины

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

В этой статье показано, как использовать очереди служебной шины. Примеры написаны на языке C# и используют интерфейс API .NET. Здесь описаны такие сценарии, как создание очередей, отправка и получение сообщений. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия](#next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Получение пакета NuGet для служебной шины

[Пакет NuGet для служебной шины](https://www.nuget.org/packages/WindowsAzure.ServiceBus) — это самый простой способ получить интерфейс API служебной шины и настроить свое приложение с учетом всех зависимостей служебной шины. Для установки пакета NuGet в приложении выполните следующие действия:

1.  В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, затем выберите команду **Управление пакетами NuGet**.
2.  Выполните поиск по фразе «служебная шина» и выберите элемент **Служебная шина Microsoft Azure**. Щелкните **Установить**, чтобы выполнить установку, а затем закройте это диалоговое окно.

    ![][7]

Теперь все готово к написанию кода для служебной шины.

## Настройка строки подключения для служебной шины

Служебная шина использует строку подключения для хранения учетных данных и конечных точек. Строку подключения можно разместить в файле конфигурации, не задавая ее жестко в коде:

- При использовании облачных служб Azure рекомендуется хранить строки подключения с помощью системы конфигурации службы Azure (файлы CSDEF и CSCFG).
- При использовании веб-сайтов Azure или виртуальных машин Azure рекомендуется сохранять строки подключения с помощью системы конфигурации .NET (например, в файле Web.config).

В обоих случаях вы можете получить строку подключения с помощью метода [CloudConfigurationManager.GetSetting][GetSetting], как показано далее в этой статье.

### Настройка строки подключения

Механизм настройки службы позволяет динамически изменять параметры конфигурации на [классическом портале Azure][] без повторного развертывания приложения. Например, добавьте метку `Setting` в файл определения службы (CSDEF), как показано в примере ниже.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```
Затем задайте значения в файле конфигурации службы (CSCFG), как показано в примере ниже.

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Используйте имя и значения ключа подписанного URL-адреса (SAS), полученные на классическом портале Azure, как описано в предыдущем разделе.

### Настройка строки подключения при использовании веб-сайтов и виртуальных машин Azure

При использовании веб-сайтов или виртуальных машин рекомендуется использовать систему конфигурации .NET (например, **Web.config**). Строка подключения сохраняется с помощью элемента `<appSettings>`.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Используйте значения имени и ключа SAS, полученные на классическом портале Azure, как описано в предыдущем разделе.

## Создание очереди

Операции управления очередями служебной шины можно выполнять с помощью класса [NamespaceManager][]. Этот класс предоставляет методы для создания, перечисления и удаления очередей.

В этом примере объект [NamespaceManager][] создается с помощью класса Azure [CloudConfigurationManager][] со строкой подключения, состоящей из базового адреса пространства имен служб служебной шины и соответствующих учетных данных SAS с правами на управление. Эта строка выглядит так, как показано в следующем примере.

````
Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey
````

Используйте следующий пример с параметрами конфигурации из предыдущего раздела.

```
// Create the queue if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue("TestQueue");
}
```

Существуют перегруженные версии метода [CreateQueue](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx), позволяющие настраивать свойства очереди, например задавать стандартное значение для срока жизни (TimeToLive) сообщений, отправленных в очередь. Эти параметры применяются с помощью класса [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx). В следующем примере показано, как создать очередь `TestQueue` с максимальным размером 5 ГБ и стандартным сроком жизни сообщения, равным 1 минуте.

```
// Configure queue settings.
QueueDescription qd = new QueueDescription("TestQueue");
qd.MaxSizeInMegabytes = 5120;
qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new queue with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue(qd);
}
```

> [AZURE.NOTE] Применив метод [QueueExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) для объектов [NamespaceManager][], вы можете проверить, существует ли в пространстве имен службы очередь с указанным именем.

## Отправка сообщений в очередь

Чтобы отправить сообщение в очередь служебной шины, приложение создает объект [QueueClient][] с помощью строки подключения.

В следующем примере кода показано, как создать объект [QueueClient][] для очереди `TestQueue`, только что созданной с помощью вызова API [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx).

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

Client.Send(new BrokeredMessage());
```

Сообщения, отправляемые в очереди служебной шины и получаемые из них, представляют собой экземпляры класса [BrokeredMessage][]. Объекты [BrokeredMessage][] обладают набором стандартных свойств (таких как [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) и [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), словарем, в котором хранятся зависящие от приложения пользовательские свойства, и основным набором произвольных данных приложения. Приложение может задать текст сообщения, передав конструктору объекта [BrokeredMessage][] любой сериализуемый объект, после чего для сериализации объекта будет использоваться соответствующий класс **DataContractSerializer**. Вы также можете указать объект **System.IO.Stream**.

В следующем примере показано, как отправить пять тестовых сообщений в объект [QueueClient][] очереди `TestQueue`, полученный в предыдущем примере кода.

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set some addtional custom app-specific properties.
  message.Properties["TestProperty"] = "TestValue";
  message.Properties["Message number"] = i;

  // Send message to the queue.
  Client.Send(message);
}
```

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня "Премиум"](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ. Если включено разделение, максимальный размер больше. Дополнительные сведения см. в статье [Секционированные сущности обмена сообщениями](service-bus-partitioning.md).

## Как получать сообщения из очереди

Для получения сообщений из очереди мы рекомендуем использовать объект [QueueClient][]. Объекты [QueueClient][] поддерживают два разных режима: [ReceiveAndDelete и PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

В режиме **ReceiveAndDelete** получение является одноэтапной операцией. Поэтому, когда служебная шина получает запрос на чтение сообщения в очереди, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего подходит для ситуаций, когда приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus отметит сообщение как использованное, перезапущенное приложение, начав снова использовать сообщения, пропустит сообщение, использованное до аварийного завершения работы.

В режиме **PeekLock** (режим по умолчанию) процесс получения проходит в два этапа. Это позволяет поддерживать приложения, в которых не допускается пропуск сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или надежно сохраняет его для последующей обработки), оно завершает второй этап процесса получения, вызывая метод [Complete][] для полученного сообщения. Когда служебная шина обнаруживает вызов [Complete][], сообщение помечается как использованное и удаляется из очереди.

В следующем примере показано, как получать и обрабатывать сообщения с помощью режима по умолчанию **PeekLock**. Чтобы задать другое значение [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), можно использовать другую перегрузку метода [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx). В этом примере используется обратный вызов [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) для обработки сообщений по мере их поступления в `TestQueue`.

```
string connectionString =
  CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
QueueClient Client =
  QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

// Callback to handle received messages.
Client.OnMessage((message) =>
{
    try
    {
        // Process message from queue.
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Test Property: " +
        message.Properties["TestProperty"]);

        // Remove message from queue.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in queue.
        message.Abandon();
    }
}, options);
```

В этом примере обратный вызов [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) настраивается с помощью объекта [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). Для [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) устанавливается значение **False**. Это позволяет вручную определять время вызова метода [Complete][] для полученного сообщения. Для [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) устанавливается значение 1 минута. Клиент ожидает сообщение в течение одной минуты, после чего срок действия вызова истекает и клиент создает новый вызов для проверки сообщений. Значение этого свойства сокращает количество создаваемых клиентом оплачиваемых вызовов, не приводящих к получению сообщений.

## Как обрабатывать сбои приложения и нечитаемые сообщения

Служебная шина предоставляет функции, помогающие правильно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-то причинам не может обработать сообщение, приложение может вызвать для полученного сообщения метод [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) (вместо метода [Complete][]). После этого служебная шина разблокирует сообщение в очереди и сделает его доступным для приема тем же или другим приложением-пользователем.

Кроме того, с сообщением, блокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), служебная шина автоматически разблокирует сообщение и снова сделает его доступным для получения.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса [Complete][], такое сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **Обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться минимум один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) сообщения, которое остается постоянным в ходе разных попыток доставки.

## Дальнейшие действия

Вы узнали основные сведения об очередях служебной шины. Для получения дополнительных сведений используйте следующие ссылки.

-   Сведения о сущностях обмена сообщениями служебной шины см. в статье [Очереди, разделы и подписки служебной шины][].
-   Создание работающего приложения, отправляющего сообщения в очередь служебной шины и получающего их из нее: [Учебное пособие по обмену сообщениями .NET через посредника в служебной шине][].
-   Скачайте примеры служебной шины со страницы [Примеры Azure][] или просмотрите [обзор примеров служебной шины][].

  [классическом портале Azure]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Очереди, разделы и подписки служебной шины]: service-bus-queues-topics-subscriptions.md
  [Учебное пособие по обмену сообщениями .NET через посредника в служебной шине]: service-bus-brokered-tutorial-dotnet.md
  [Примеры Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [обзор примеров служебной шины]: service-bus-samples.md
  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [Complete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx

<!---HONumber=AcomDC_0525_2016-->