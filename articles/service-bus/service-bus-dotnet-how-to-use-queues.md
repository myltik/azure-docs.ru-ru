<properties
    pageTitle="Как использовать очереди служебной шины (.NET): Azure"
    description="Узнайте, как использовать очереди служебной шины в Azure. Примеры кода написаны на C# с использованием API .NET."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="sethm"/>

# Как использовать очереди служебной шины

В этом руководстве показано, как использовать очереди служебной шины. Примеры написаны на языке C# и используют интерфейс API .NET. Здесь описаны такие сценарии, как **создание очередей**, **отправка и получение сообщений**. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия](#Next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Настройка приложения для использования служебной шины

При создании приложения, использующего служебную шину, необходимо добавить ссылку на сборку шины обслуживания и указать соответствующие пространства имен.

## Получение пакета NuGet для служебной шины

Пакет **NuGet** для служебной шины является самым простым способом получить интерфейс API служебной шины и настроить свое приложение с учетом всех зависимостей служебной шины. Расширение NuGet для Visual Studio упрощает установку и обновление библиотек и инструментов в Visual Studio и Visual Studio Express. Пакет NuGet для служебной шины является самым простым способом получить интерфейс API служебной шины и настроить свое приложение с учетом всех зависимостей служебной шины.

Для установки пакета NuGet в приложении выполните следующие действия:

1.  В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, затем выберите команду **Управление пакетами NuGet**.
2.  Выполните поиск "служебная шина" и выберите пункт **Служебная шина Microsoft Azure**. Щелкните **Установить**, чтобы выполнить установку, а затем закройте это диалоговое окно.

    ![][7]

Теперь все готово к написанию кода для служебной шины.

## Практическое руководство. Настройка строки подключения для служебной шины

Служебная шина использует строку подключения для хранения учетных данных и конечных точек. Строку подключения можно разместить в файле конфигурации, не задавая ее жестко в коде:

- При использовании облачных служб Azure рекомендуется хранить строки подключения с помощью системы конфигурации службы Azure (файлы ***.csdef** и ***.cscfg**).
- При использовании веб-сайтов Azure или виртуальных машин Azure рекомендуется сохранять строки подключения с помощью системы конфигурации .NET (например, в файле **Web.config**).

В обоих случаях строку подключения можно извлечь с помощью метода `CloudConfigurationManager.GetSetting`, как показано далее в этом руководстве.

### Настройка строки подключения при использовании облачных служб

Механизм настройки службы является уникальным для проектов облачных служб Azure и позволяет динамически изменять параметры конфигурации на портале управления Azure без повторного развертывания приложения. Например, добавьте метку `Setting` в файл определения службы ( ***.csdef**), как показано ниже:

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Затем задайте значения в файле конфигурации службы (***.cscfg**):

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

Используйте имя и значения ключа подписанного URL-адреса (SAS), полученные на портале управления, как описано в предыдущем разделе.

### Настройка строки подключения при использовании веб-сайтов или виртуальных машин

При использовании веб-сайтов или виртуальных машин рекомендуется использовать систему конфигурации .NET (например, **Web.config**). Строка подключения сохраняется с помощью элемента `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Используйте имя и значения ключа SAS, полученные на портале управления, как описано в предыдущем разделе.

## Как создать очередь

Операции управления для очередей служебной шины можно выполнять с помощью класса [`NamespaceManager`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Этот класс предоставляет методы для создания, перечисления и удаления очередей.

В этом примере объект `NamespaceManager` создается с помощью класса Azure `CloudConfigurationManager` со строкой подключения, состоящей из базового адреса пространства имен службы служебной шины и учетных данных SAS с соответствующими правами на управление. Эта строка подключения имеет вид

    Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey

Например, для параметров конфигурации из предыдущего раздела:

    // Create the queue if it does not exist already
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Существуют перегрузки метода [`CreateQueue`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx), позволяющие настраивать свойства очереди, например задавать значение по умолчанию для "времени жизни", применяемое к сообщениям, отправленным в очередь. Эти параметры применяются с помощью класса [`QueueDescription`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx). В следующем примере показано, как создать очередь с именем TestQueue, максимальным размером 5 ГБ и временем жизни сообщения по умолчанию, равным 1 минуте:

    // Configure queue settings
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new queue with custom settings
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

> [AZURE.NOTE]Можно использовать метод [`QueueExists`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) для объектов [`NamespaceManager`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx), чтобы проверить, существует ли уже очередь с указанным именем в пространстве имен службы.

## Как отправлять сообщения в очередь

Чтобы отправить сообщение в очередь служебной шины, приложение создает объект [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) с помощью строки подключения.

В следующем примере кода показано, как создать объект [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) для очереди TestQueue, созданной с помощью вызова API [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx):

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client =
        QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    Client.Send(new BrokeredMessage());

Сообщения, отправляемые в очереди служебной шины и получаемые из них — это экземпляры класса [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Объекты `BrokeredMessage` имеют набор стандартных свойств (например, [`Label`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) и [`TimeToLive`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), словарь, используемый для хранения настраиваемых свойств приложения, и тело, состоящее из произвольных данных приложения. Приложение может задать текст сообщения, передав конструктору объекта [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) любой сериализуемый объект, после чего для сериализации объекта будет использоваться соответствующий **DataContractSerializer**. Кроме того, может быть предоставлен **System.IO.Stream**.

В следующем примере показано, как отправить пять тестовых сообщений в очередь TestQueue объекта [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), полученного в предыдущем фрагменте кода:

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some addtional custom app-specific properties
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;

       // Send message to the queue
       Client.Send(message);
     }

Очереди служебной шины поддерживают [максимальный размер сообщения 256 кБ](service-bus-quotas.md) (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 кБ). Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ. Если включено разделение, максимальный размер больше. Дополнительную информацию см. в разделе [Разделение сущностей обмена сообщениями](https://msdn.microsoft.com/library/azure/dn520246.aspx).

## Как получать сообщения из очереди

Рекомендуемым способом получения сообщения из очереди является использование объекта [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Объекты `QueueClient` могут работать в двух режимах: [`ReceiveAndDelete` и `PeekLock`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть когда служебная шина получает запрос на чтение для сообщения в очереди, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus отметит сообщение как использованное, перезапущенное приложение, начав снова использовать сообщения, пропустит сообщение, использованное до аварийного завершения работы.

В режиме **PeekLock** (который является режимом по умолчанию) процесс получения проходит в два этапа, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), оно завершает второй этап процесса получения, вызывая метод [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) для полученного сообщения. Когда служебная шина обнаруживает вызов `Complete`, сообщение помечается как используемое и удаляется из очереди.

В следующем примере показано, как получать и обрабатывать сообщения с помощью режима по умолчанию **PeekLock**. Чтобы задать другое значение [`ReceiveMode`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), можно использовать другую перегрузку [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx). В этом примере используется обратный вызов [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) для обработки сообщений по мере их поступления в очередь **TestQueue**.

    string connectionString =
      CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    QueueClient Client =
      QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    // Callback to handle received messages
    Client.OnMessage((message) =>
    {
        try
        {
            // Process message from queue
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Test Property: " +
            message.Properties["TestProperty"]);

            // Remove message from queue
            message.Complete();
        }
            catch (Exception)
        {
            // Indicates a problem, unlock message in queue
            message.Abandon();
        }
    }, options);

В этом примере выполняется настройка обратного вызова [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) с помощью объекта [`OnMessageOptions`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). Для [`AutoComplete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) установлено **false**, что позволяет вручную управлять временем вызова [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) для полученного сообщения. Для [`AutoRenewTimeout`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) задается значение, равное 1 минуте, в результате чего клиент ожидает сообщение до одной минуты, после чего срок действия вызова истекает и клиент создает новый вызов для проверки сообщений. Значение этого свойства сокращает количество создаваемых клиентом оплачиваемых вызовов, не приводящих к получению сообщений.

## Как обрабатывать сбои приложения и нечитаемые сообщения

Служебная шина предоставляет функции, помогающие правильно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать метод [`Abandon`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) для полученного сообщения (вместо метода [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). После этого служебная шина разблокирует сообщение в очереди и сделает его доступным для приема тем же или другим приложением-пользователем.

Кроме того, с сообщением, блокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), служебная шина автоматически разблокирует сообщение и снова сделает его доступным для получения.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), это сообщение повторно доставляется в приложение после его перезапуска. Часто этот подход называют **Обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства [`MessageId`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) сообщения, которое остается постоянным для различных попыток доставки.

## Дальнейшие действия

Вы узнали основные сведения об очередях служебной шины. Для получения дополнительных сведений используйте следующие ссылки.

-   См. обзор MSDN: [Очереди, разделы и подписки][].
-   Создание работающего приложения, отправляющего сообщения в очередь служебной шины и получающего их из нее: [Учебник по управляемому обмену сообщениями служебной шины в .NET].
-   Примеры служебной шины: загрузка со страницы [Примеры Azure][] или см. обзор на сайте [MSDN][].

  [What are Service Bus Queues]: #what-queues
  [Create a Service Namespace]: #create-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-creds
  [Configure Your Application to Use Service Bus]: #configure-app
  [How to: Set Up a Service Bus Connection String]: #set-up-connstring
  [How to: Configure your Connection String]: #config-connstring
  [How to: Create a Queue]: #create-queue
  [How to: Send Messages to a Queue]: #send-messages
  [How to: Receive Messages from a Queue]: #receive-messages
  [How to: Handle Application Crashes and Unreadable Messages]: #handle-crashes
  [Azure Management Portal]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Очереди, разделы и подписки]: http://msdn.microsoft.com/library/azure/hh367516.aspx
  [Учебник по управляемому обмену сообщениями служебной шины в .NET]: http://msdn.microsoft.com/library/azure/hh367512.aspx
  [Примеры Azure]: https://code.msdn.microsoft.com/windowsazure/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [MSDN]: https://msdn.microsoft.com/library/azure/dn194201.aspx
 

<!---HONumber=July15_HO4-->