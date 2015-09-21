<properties
    pageTitle="Использование разделов служебной шины (.NET) | Microsoft Azure"
    description="Узнайте, как использовать разделы и подписки служебной шины в Azure. Примеры кода написаны для приложений .NET."
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

# Использование разделов и подписок служебной шины Azure

В этой статье описывается использование разделов и подписок служебной шины. Примеры написаны на языке C# и используют интерфейсы API .NET. В этой статье описаны такие сценарии, как создание разделов и подписок, создание фильтров подписок, отправка сообщений в раздел, получение сообщений из подписки и удаление разделов и подписок. Дополнительные сведения о разделах и подписках см. в разделе [Дальнейшие действия](#Next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Настройка приложения для использования служебной шины

При создании приложения, использующего служебную шину, необходимо добавить ссылку на сборку шины обслуживания и указать соответствующие пространства имен.

## Получение пакета NuGet для служебной шины

Пакет NuGet для служебной шины является самым простым способом получить интерфейс API служебной шины и настроить свое приложение с учетом всех зависимостей служебной шины. Расширение NuGet для Visual Studio упрощает установку и обновление библиотек и инструментов в Visual Studio и Visual Studio Express. Пакет NuGet для служебной шины является самым простым способом получить интерфейс API служебной шины и настроить свое приложение с учетом всех зависимостей служебной шины.

Для установки пакета NuGet в приложении выполните следующие действия:

1.  В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, затем выберите команду **Управление пакетами NuGet**.
2.  Выполните поиск по фразе "служебная шина" и выберите элемент **Служебная шина Microsoft Azure**. Щелкните **Установить**, чтобы выполнить установку, а затем закройте следующее диалоговое окно.

    ![][7]

Теперь все готово к написанию кода для служебной шины.

## Практическое руководство. Настройка строки подключения для служебной шины

Служебная шина использует строку подключения для хранения учетных данных и конечных точек. Строку подключения можно разместить в файле конфигурации, не задавая ее жестко в коде:

- При использовании облачных служб Azure рекомендуется хранить строки подключения с помощью системы конфигурации службы Azure (файлы CSDEF и CSCFG).
- При использовании веб-сайтов Azure или виртуальных машин Azure рекомендуется сохранять строки подключения с помощью системы конфигурации .NET (например, в файле Web.config).

В обоих случаях строку подключения можно получить с помощью метода `CloudConfigurationManager.GetSetting`, как показано далее в этой статье.

### Настройка строки подключения при использовании облачных служб

Механизм настройки службы является уникальным для проектов облачных служб Azure и позволяет динамически изменять параметры конфигурации на портале Azure без повторного развертывания приложения. Например, добавьте метку `Setting` в файл определения службы (****.csdef**), как показано в примере ниже:

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Затем задайте значения в файле конфигурации службы (CSCFG).

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

Используйте имя и значения ключа подписанного URL-адреса (SAS), полученные на портале Azure, как описано в предыдущем разделе.

### Настройка строки подключения при использовании веб-сайтов Azure или виртуальных машин Azure

При использовании веб-сайтов или виртуальных машин рекомендуется использовать систему конфигурации .NET (например, Web.config). Строка подключения сохраняется с помощью элемента `<appSettings>`.

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Используйте имя и значения ключа SAS, полученные на портале Azure, как описано в предыдущем разделе.

## Как создать раздел

Операции управления для разделов и подписок служебной шины можно выполнять с помощью класса [`NamespaceManager`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx). Этот класс предоставляет методы для создания, перечисления и удаления разделов.

В этом примере объект `NamespaceManager` создается с помощью класса Azure `CloudConfigurationManager` со строкой подключения, состоящей из базового адреса пространства имен службы служебной шины и учетных данных SAS с соответствующими правами на управление. Эта строка подключения имеет следующий вид:

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey

Используйте следующий пример с параметрами конфигурации из предыдущего раздела.

    // Create the topic if it does not exist already.
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

Существуют перегрузки метода [`CreateTopic`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.createtopic.aspx), позволяющие настраивать свойства раздела, например задавать значение по умолчанию для "срока жизни", применяемое к сообщениям, отправленным в раздел. Эти параметры применяются с помощью класса [`TopicDescription`](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.topicdescription.aspx). В следующем примере демонстрируется создание раздела с именем "TestTopic" с максимальным размером в 5 ГБ и сроком жизни по умолчанию, равным 1 минуте.

    // Configure Topic Settings.
    TopicDescription td = new TopicDescription("TestTopic");
    td.MaxSizeInMegabytes = 5120;
    td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new Topic with custom settings.
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic(td);
    }

> [AZURE.NOTE]Можно использовать метод [`TopicExists`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.topicexists.aspx) для объектов [`NamespaceManager`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx), чтобы проверить, существует ли уже раздел с указанным именем в пространстве имен службы.

## Создание подписки

Можно также создать подписки на разделы, используя класс [`NamespaceManager`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx). Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка с именем AllMessages и фильтром по умолчанию **MatchAll**.

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Создание подписок с фильтрами

Можно также настраивать фильтры, позволяющие определять, какие посылаемые в раздел сообщения должны появляться в рамках конкретной подписки.

Самый гибкий тип фильтра, который поддерживается подписками, — это класс [SqlFilter], реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о выражениях, которые можно использовать с SQL-фильтром, см. в описании синтаксиса [SqlFilter.SqlExpression][].

В следующем примере создается подписка с именем **HighMessages**, содержащая объект [SqlFilter], который выбирает только сообщения, значение настраиваемого свойства **MessageNumber** которых превышает 3.

     // Create a "HighMessages" filtered subscription.
     SqlFilter highMessagesFilter =
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic",
        "HighMessages",
        highMessagesFilter);

Аналогичным образом в следующем примере создается подписка с именем **LowMessages** и фильтром [SqlFilter], который выбирает только сообщения, у которых значение свойства **MessageNumber** меньше или равно 3.

     // Create a "LowMessages" filtered subscription.
     SqlFilter lowMessagesFilter =
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic",
        "LowMessages",
        lowMessagesFilter);

Теперь, когда в раздел `TestTopic` отправляется сообщение, оно будет всегда доставляться получателям, подписанным на раздел **AllMessages**, и выборочно доставляться получателям, подписанным на разделы **HighMessages** и **LowMessages** в зависимости от содержания сообщения.

## Как отправлять сообщения в раздел

Чтобы отправить сообщение в раздел служебной шины, приложение создает объект [`TopicClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) с помощью строки подключения.

В примере кода ниже показано, как создать объект [`TopicClient`](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.topicclient.aspx) для раздела **TestTopic**, созданного с помощью вызова API [`CreateFromConnectionString`](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx).

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client =
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());


Сообщения, отправленные в разделы служебной шины, являются экземплярами класса [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). У объектов **BrokeredMessage** есть набор стандартных свойств (таких как [`Label`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) и [`TimeToLive`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), словарь, используемый для хранения настраиваемых свойств приложения, и текст из произвольных данных приложения. Приложение может задать текст сообщения, передав конструктору объекта [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) любой сериализуемый объект, после чего для сериализации объекта будет использоваться соответствующий **DataContractSerializer**. Кроме того, может быть предоставлен **System.IO.Stream**.

В следующем примере показано, как отправить пять тестовых сообщений в объект [`TopicClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) раздела **TestTopic**, полученный в предыдущем примере кода. Обратите внимание, что значение свойства [`MessageNumber`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) сообщений зависит от итерации цикла (это определяет, какие подписки их получают).

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body.
       BrokeredMessage message =
        new BrokeredMessage("Test message " + i);

       // Set additional custom app-specific property.
       message.Properties["MessageNumber"] = i;

       // Send message to the topic.
       Client.Send(message);
     }

Разделы служебной шины поддерживают [максимальный размер сообщения в 256 КБ](service-bus-quotas.md) (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ). Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер задается при создании с верхним пределом 5 ГБ. Если включено разделение, максимальный размер больше. Дополнительную информацию см. в разделе [Разделение сущностей обмена сообщениями](https://msdn.microsoft.com/library/azure/dn520246.aspx).

## Как получать сообщения из подписки

Рекомендуемым способом получения сообщений из подписки является использование объекта [`SubscriptionClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx). Объекты **SubscriptionClient** могут работать в двух режимах: [`ReceiveAndDelete` и `PeekLock`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда Service Bus получает запрос на чтение для сообщения в подписке, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus отметит сообщение как использованное, перезапущенное приложение, начав снова использовать сообщения, пропустит сообщение, использованное до аварийного завершения работы.

В режиме **PeekLock** (который является режимом по умолчанию) процесс получения проходит в два этапа, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), оно завершает второй этап процесса получения, вызывая метод [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) для полученного сообщения. Когда служебная шина обнаруживает вызов `Complete`, сообщение помечается как использованное и удаляется из подписки.

В следующем примере показано, как получать и обрабатывать сообщения с помощью режима по умолчанию **PeekLock**. Чтобы задать другое значение [`ReceiveMode`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), можно использовать другую перегрузку метода [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). В этом примере используется обратный вызов [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) для обработки сообщений по мере их поступления в подписку **HighMessages**.

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client =
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    // Configure the callback options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
        try
        {
            // Process message from subscription.
            Console.WriteLine("\n**High Messages**");
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Message Number: " +
                message.Properties["MessageNumber"]);

            // Remove message from subscription.
            message.Complete();
        }
        catch (Exception)
        {
            // Indicates a problem, unlock message in subscription.
            message.Abandon();
        }
    }, options);

В этом примере выполняется настройка обратного вызова [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) с помощью объекта [`OnMessageOptions`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). Для [`AutoComplete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) установлено значение **false**, что позволяет вручную управлять временем вызова [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) для полученного сообщения. Для [`AutoRenewTimeout`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) задается значение, равное 1 минуте, в результате чего клиент ожидает сообщение до одной минуты, после чего срок действия вызова истекает и клиент создает новый вызов для проверки сообщений. Значение этого свойства сокращает количество создаваемых клиентом оплачиваемых вызовов, не приводящих к получению сообщений.

## Как обрабатывать сбои приложения и нечитаемые сообщения

служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать метод [`Abandon`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) для полученного сообщения (вместо метода [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). После этого Service Bus разблокирует сообщение в подписке и снова делает его доступным для получения тем же или другим приложением.

Существует также определенное связанное с сообщением время ожидания в рамках подписки, и если приложение не может обработать сообщение до истечения этого времени (например, аварийно завершит работу), то служебная шина автоматически разблокирует сообщение и делает его доступным для повторного получения.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), это сообщение повторно доставляется в приложение после его перезапуска. Часто этот подход называют **Обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства [`MessageId`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) сообщения, которое остается постоянным для различных попыток доставки.

## Как удалять разделы и подписки

В следующем примере показано, как удалить раздел с именем **TestTopic** из пространства имен службы **HowToSample**.

     // Delete Topic.
     namespaceManager.DeleteTopic("TestTopic");

При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности. В следующем коде демонстрируется удаление подписки с именем **HighMessages** из раздела **TestTopic**.

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## Дальнейшие действия

Вы узнали основные сведения о разделах и подписках Service Bus. Для получения дополнительных сведений используйте следующие ссылки.

-   См. статью [Очереди, темы и подписки][].
-   Справочник API для [SqlFilter][].
-   Создание работающего приложения, отправляющего сообщения в очередь служебной шины и получающего их из нее: [Учебник по обмену сообщениями через посредника служебной шины в .NET][].
-   Примеры служебной шины: скачайте со страницы [примеров Azure][] или просмотрите [обзор](service-bus-samples.md).

  [Azure portal]: http://manage.windowsazure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Очереди, темы и подписки]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Учебник по обмену сообщениями через посредника служебной шины в .NET]: http://msdn.microsoft.com/library/azure/hh367512.aspx
  [примеров Azure]: https://code.msdn.microsoft.com/windowsazure/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2

<!---HONumber=Sept15_HO2-->