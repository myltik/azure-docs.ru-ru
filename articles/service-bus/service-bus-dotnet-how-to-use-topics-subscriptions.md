<properties 
    pageTitle="Использование разделов служебной шины (.NET) - Azure" 
    description="Узнайте, как использовать разделы и подписки Service Bus в Azure. Примеры кода написаны для приложений .NET." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor="mattshel"/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="02/26/2015" 
    ms.author="sethm"/>





# Использование разделов и подписок Service Bus

Из этого руководства вы узнаете, как использовать разделы и подписки Service Bus. Примеры написаны на языке C# и используют интерфейс API .NET. В этой статье описаны такие сценарии, как **создание разделов и подписок, создание фильтров подписок, отправка сообщений** в раздел, **получение сообщений от подписки** и **удаление разделов и подписок**. Дополнительные сведения о разделах и подписках см. в разделе [Дальнейшие действия][].

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Настройка приложения для использования Service Bus

При создании приложения, использующего служебную шину, вам понадобится добавить ссылку на сборку Service Bus и включить туда соответствующие пространства имен.

## Получение пакета NuGet для служебной шины

Пакет **NuGet** для Service Bus - самый простой способ получить Service Bus API и сконфигурировать свое приложение со всеми зависимостями Service Bus. Расширение NuGet для Visual Studio упрощает установку и обновления библиотек и инструментов в Visual Studio и Visual Studio Express. Пакет NuGet для служебной шины является самым простым способом получить интерфейс API служебной шины и настроить свое приложение с учетом всех зависимостей служебной шины.

Для установки пакета NuGet в приложении выполните следующие действия:

1.  Щелкните в обозревателе решений правой кнопкой мыши пункт **Ссылки**, затем щелкните **Управление пакетами NuGet**.
2.  Выполните поиск по строке "служебная шина" и выберите элемент **Microsoft Azure Service Bus**. Нажмите кнопку **Установить** для завершения установки, а затем закройте это диалоговое окно.

    ![][7]

Теперь все готово к написанию кода для служебной шины.

## Практическое руководство. Настройка строки подключения для служебной шины

Служебная шина использует строку подключения для хранения учетных данных и конечных точек. Строку подключения можно разместить в файле конфигурации, не задавая ее жестко в коде:

- При использовании облачных служб Azure рекомендуется сохранять строки подключения с помощью системы конфигурации службы Azure (файлы `*.csdef` и `*.cscfg`).
- При использовании веб-сайтов Azure или виртуальных машин Azure рекомендуется сохранять строки подключения с помощью системы конфигурации .NET (например, в файле  `web.config`).

В обоих случаях можно извлечь строку подключения с помощью метода  `CloudConfigurationManager.GetSetting`, как показано далее в этом руководстве.

### Настройка строки подключения при использовании облачных служб

Механизм настройки службы является уникальным для проектов облачных служб Azure и позволяет динамически изменять параметры конфигурации на портале управления Azure без повторного развертывания приложения.  Например, добавьте параметр в файл определения службы (`*.csdef`), как показано ниже:

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Затем задайте нужные значения в файле конфигурации службы (`*.cscfg`):

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

Используйте значения ключей, полученные на портале управления, как описано в предыдущем разделе.

### Настройка строки подключения при использовании веб-сайтов или виртуальных машин

При использовании веб-сайтов или виртуальных машин рекомендуется использовать систему конфигурации .NET (например, файл  `web.config`).  Строка подключения сохраняется с помощью элемента `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Используйте значения издателя и ключа, полученные на портале управления, как описано в предыдущем разделе.

## Как создать раздел

Для выполнения операций управления для разделов и подписок Service Bus можно использовать класс **NamespaceManager**. Класс **NamespaceManager** предоставляет методы для создания, перечисления и удаления очередей. 

В этом примере объект **NamespaceManager** создается с помощью класса Azure **CloudConfigurationManager** со строкой подключения, состоящей из базового адреса пространства имен служб Service Bus и учетных данных с соответствующими правами на управление. Эта строка подключения имеет вид

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey

Например, для параметров конфигурации из предыдущего раздела:

    // Create the topic if it does not exist already
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

Существуют перегрузки метода **CreateTopic**, позволяющие настраивать свойства раздела, например задавать значение по умолчанию для "времени жизни", применяемое к сообщениям, отправленным в раздел. Эти параметры применяются с помощью класса **TopicDescription**. В следующем примере демонстрируется создание раздела по имени TestTopic с максимальным размером в 5 Гб и сроком жизни по умолчанию, равным 1 минуте.

    // Configure Topic Settings
    TopicDescription td = new TopicDescription("TestTopic");
    td.MaxSizeInMegabytes = 5120;
    td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new Topic with custom settings
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic(td);
    }

**Примечание.** Вы можете использовать метод **TopicExists** для объектов **NamespaceManager**, чтобы проверить, существует ли уже раздел с указанным именем в пространстве имен служб.

<h2>Создание подписок</h2>

Можно также создать подписки раздела, используя класс **NamespaceManager**. Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка с именем "AllMessages" и использует значение по умолчанию **MatchAll** фильтра.

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

Самым гибким типом фильтра, поддерживаемым подписками, является **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительную информацию о выражениях, которые можно использовать с фильтром SQL, см. в описании синтаксиса [SqlFilter.SqlExpression][SqlFilter].

В следующем примере создается подписка с именем "HighMessages" и фильтром **SqlFilter**, который выбирает только сообщения, у которых значение пользовательского свойства
**MessageNumber** больше 3:

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

Аналогично, в следующем примере создается подписка с именем LowMessages с фильтром **SqlFilter**, который выбирает только сообщения, значение свойства **MessageNumber** у которых меньше или равно 3:

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

Теперь, если сообщение отправляется в раздел TestTopic, оно доставляется получателям, имеющим подписку AllMessages, и выборочно доставляется получателям, имеющим подписки HighMessages и LowMessages на разделы (в зависимости от содержимого сообщения).

## Как отправлять сообщения в раздел

Чтобы отправить сообщение в разделе Service Bus, приложение создает объект **TopicClient**, используя строку подключения.

Приведенный ниже код демонстрирует создание объекта **TopicClient** для раздела TestTopic, созданного выше с помощью API-вызова **CreateFromConnectionString**:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());
 

Сообщения, отправленные в разделы Service Bus, являются экземплярами класса **BrokeredMessage**. Объекты **BrokeredMessage** обладают набором стандартных свойств (таких как **Label** и **TimeToLive**), словарем, хранящим разные пользовательские свойства, зависящие от приложения, и основным набором произвольных данных приложения. Приложение может определить текст сообщения, передав любой сериализуемый объект в конструктор **BrokeredMessage**, а затем соответствующий **DataContractSerializer** будет использоваться для сериализации объекта. Как вариант, может предоставляться **System.IO.Stream**.

В следующем примере показано, как отправить пять тестовых сообщений в раздел TestTopic **TopicClient**, полученный в предыдущем фрагменте кода.
Обратите внимание как **MessageNumber** значение свойства каждого сообщения зависит от итерации цикла (определяет, какие подписки получают его):

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = 
        new BrokeredMessage("Test message " + i);

       // Set additional custom app-specific property
       message.Properties["MessageNumber"] = i;

       // Send message to the topic
       Client.Send(message);
     }

Разделы Service Bus поддерживают максимальный размер сообщения в 256 Кб (размер заголовка, включающего в себя стандартные и пользовательские свойства приложения, может быть не более 64 Кб). Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ.

## Как получать сообщения из подписки

Рекомендуемым способом получать сообщения из подписки является использование объекта **SubscriptionClient**. Объекты **SubscriptionClient** могут работать в двух режимах: **ReceiveAndDelete** и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда Service Bus получает запрос на чтение для сообщения в подписке, сообщение помечается как использованное и возвращается в приложение. **Режим ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus отметит сообщение как использованное, перезапущенное приложение, начав снова использовать сообщения, пропустит сообщение, использованное до аварийного завершения работы.

В режиме **PeekLock** (который является режимом по умолчанию) процесс получения проходит в два этапа, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), служебная шина завершает второй этап процесса получения, вызывая метод **Complete** для полученного сообщения. Когда служебная шина обнаруживает вызов **Complete**, сообщение помечается как использованное и удаляется из очереди.

В примере ниже показано, как можно получать и обрабатывать сообщения с использованием режима по умолчанию **PeekLock**. Чтобы задать другое значение режима **ReceiveMode**, можно использовать другую перегрузку метода **CreateFromConnectionString**. В этом примере используется обратный вызов **OnMessage** для обработки сообщений по мере их поступления в подписке HighMessages.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    subscriptionClientHigh.OnMessage((message) =>
    {
        try
        {
            // Process message from subscription
            Console.WriteLine("\n**High Messages**");
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Message Number: " +
                message.Properties["MessageNumber"]);

            // Remove message from subscription
            message.Complete();
        }
        catch (Exception)
        {
            // Indicates a problem, unlock message in subscription
            message.Abandon();
        }
    }, options);

В этом примере выполняется настройка обратного вызова **OnMessage** с помощью объекта **OnMessageOptions**. Для **AutoComplete** задается значение **false** для включения  ручного управления вызовом **Complete** для полученного сообщения.
Для **AutoRenewTimeout** задается значение, равное одной минуте, в результате чего клиент ожидает сообщение в течение минуты, после чего истекает срок действия вызова и клиент создает новый вызов для проверки сообщений. Это сокращает количество создаваемых клиентом оплачиваемых вызовов, не приводящих к получению сообщений.

## Как обрабатывать сбои приложения и нечитаемые сообщения

Service Bus предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **Abandon** (вместо метода **Complete**). После этого Service Bus разблокирует сообщение в подписке и снова делает его доступным для получения тем же или другим приложением.

Существует также определенное связанное с сообщением время ожидания в рамках подписки, и если приложение не может обработать сообщение до истечения этого времени (например, аварийно завершит работу), то Service Bus автоматически разблокирует сообщение и делает его доступным для повторного получения.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **Complete**, это сообщение повторно доставляется в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

## Как удалять разделы и подписки

В следующем примере показано, как удалить раздел с именем **TestTopic** из пространства имен службы **HowToSample**:

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности. В следующем примере кода показано, как удалить подписку с именем **HighMessages** из раздела **TestTopic**:

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## Дальнейшие действия

Вы узнали основные сведения о разделах и подписках Service Bus. Для получения дополнительных сведений используйте следующие ссылки.

-   См. справочник MSDN: [Очереди, разделы и подписки][].
-   Справочник по API для [SqlFilter][].
-   Сборка рабочего приложения, способного отправлять сообщения в очередь служебной шины и получать их из нее: [Учебник по управляемому обмену сообщениями служебной шины в .NET][].

  [Дальнейшие действия]: #nextsteps
  [Общая информация о разделах и подписках служебной шины]: #what-is
  [Создание пространства имен службы]: #create-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-creds
  [Настройка приложения для использования служебной шины]: #configure-app
  [Практическое руководство. Настройка строки подключения для служебной шины]: #set-up-connstring
  [Практическое руководство. Настройка строки подключения]: #config-connstring
  [Практическое руководство. Создание раздела]: #create-topic
  [Практическое руководство. Создание подписок]: #create-subscriptions
  [Практическое руководство. Отправка сообщений в раздел]: #send-messages
  [Практическое руководство. Получение сообщений от подписки]: #receive-messages
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #handle-crashes
  [Практическое руководство. Удаление разделов и подписок]: #delete-topics
  
  [Основные понятия раздела]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/sb-topics-01.png
  [Портал управления Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  
  [Очереди, разделы и подписки]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [Учебник по управляемому обмену сообщениями служебной шины в .NET]: http://msdn.microsoft.com/library/hh367512.aspx

<!--HONumber=47-->
 