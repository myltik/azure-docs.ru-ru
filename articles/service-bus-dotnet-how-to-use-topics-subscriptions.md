<properties linkid="dev-net-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (.NET) - Azure" metaKeywords="Get started Azure Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions C# " description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for .NET applications. " metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Использование разделов и подписок Service Bus

<span>В этом руководстве показано, как использовать разделы и подписки Service Bus. Примеры написаны на языке C# и используют интерфейс API .NET. В этом разделе описаны такие сценарии, как **создание разделов и подписок, создание фильтров подписок, отправка сообщений** в раздел, **получение сообщений из подписки** и **удаление разделов и подписок**. Дополнительную информацию о разделах и подписках см. в разделе [Дальнейшие действия][Дальнейшие действия]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <span class="short-header">Настройка приложения</span>Настройка приложения для использования Service Bus

При создании приложения, использующего Service Bus, потребуется добавить ссылку на сборку Service Bus и указать соответствующие пространства имен.

## <span class="short-header">Получение пакета NuGet</span>Получение пакета NuGet для Service Bus

Пакет **NuGet** для Service Bus является самым простым способом получить API Service Bus и настроить для приложения все зависимости Service Bus. Расширение NuGet для Visual Studio упрощает установку и обновление библиотек и инструментов в Visual Studio и Visual Studio Express 2012 для Web. Пакет NuGet для Service Bus является самым простым способом получить API Service Bus и настроить для приложения все зависимости Service Bus.

Для установки пакета NuGet в приложении выполните следующие действия:

1.  В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, затем выберите команду
    **Управление пакетами NuGet**.
2.  Выполните поиск «MicrosoftAzure» и выберите элемент **Azure Service Bus**. Щелкните **Установить**, чтобы выполнить установку, а затем закройте это диалоговое окно.

    ![][0]

Теперь все готово, чтобы написать код для Service Bus.

## <span class="short-header">Как настроить строку подключения</span> Как настроить строку подключения Service Bus

Service Bus использует строку подключения для хранения учетных данных и конечных точек. Строку подключения можно разместить в файле конфигурации, не задавая ее жестко в коде:

-   При использовании облачных служб Azure рекомендуется сохранять строки подключения с помощью системы конфигурации службы Azure (файлы `*.csdef` и `*.cscfg`).
-   При использовании веб-сайтов Azure или виртуальных машин Azure рекомендуется сохранять строки подключения с помощью системы конфигурации .NET (например, в файле `web.config`).

В обоих случаях можно извлечь строку подключения с помощью метода `CloudConfigurationManager.GetSetting`, как показано далее в этом руководстве.

### <a name="config-connstring"> </a> Настройка строки подключения при использовании облачных служб

Механизм настройки службы является уникальным для проектов облачных служб Azure и позволяет динамически изменять параметры конфигурации на портале управления Azure без повторного развертывания приложения. Например, добавьте параметр в файл определения службы (`*.csdef`), как показано ниже:

    <ServiceDefinition name="WindowsAzure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Затем задайте нужные значения в файле конфигурации службы (`*.cscfg`):

    <ServiceConfiguration serviceName="WindowsAzure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" 
                         value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

Используйте значения издателя и ключа, полученные на портале управления, как описано в предыдущем разделе.

### Настройка строки подключения при использовании веб-сайтов или виртуальных машин

При использовании веб-сайтов или виртуальных машин рекомендуется использовать систему конфигурации .NET (например, файл `web.config`). Строка подключения хранится с помощью элемента `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
        </appSettings>
    </configuration>

Используйте значения издателя и ключа, полученные на портале управления, как описано в предыдущем разделе.

## <span class="short-header">Как создать раздел</span>Как создать раздел

Для выполнения операций управления для разделов и подписок Service Bus можно использовать класс **NamespaceManager**. Класс **NamespaceManager** предоставляет методы для создания, перечисления и удаления очередей.

В этом примере объект **NamespaceManager** создается с помощью класса Azure **CloudConfigurationManager** со строкой подключения, состоящей из базового адреса пространства имен служб Service Bus и учетных данных с соответствующими правами на управление. Эта строка подключения имеет вид

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedSecretIssuer=<issuerName>;SharedSecretValue=<yourDefaultKey>

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

Существуют перегрузки метода **CreateTopic**, позволяющие настраивать свойства раздела, например задавать значение по умолчанию для «времени жизни», применяемое к сообщениям, отправленным в раздел. Эти параметры применяются с помощью класса **TopicDescription**. В следующем примере показано, как создать раздел с именем TestTopic, максимальным размером 5 ГБ и временем жизни сообщения по умолчанию, равным 1 минуте.

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

## <span class="short-header">Как создавать подписки</span>Как создавать подписки

Можно также создать подписки раздела, используя класс **NamespaceManager**. Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, который используется, если при создании новой подписки не были указаны какие-либо другие фильтры. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка с именем AllMessages и используется фильтр по умолчанию **MatchAll**.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);
    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Создание подписок с фильтрами

Можно также настроить фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в конкретной подписке раздела.

Самый гибкий тип фильтра, который поддерживается подписками, — это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительную информацию о выражениях, которые можно использовать с фильтром SQL, см. в описании синтаксиса SqlFilter.SqlExpression.

В следующем примере создается подписка с именем HighMessages и фильтром **SqlFilter**, который отбирает только те сообщения, у которых значение пользовательского свойства **MessageNumber** превышает 3:

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");
     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

Аналогично, в следующем примере создается подписка с именем LowMessages и фильтром **SqlFilter**, который отбирает только те сообщения, у которых значение свойства **MessageNumber** меньше или равно 3:

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");
     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

Теперь в случае отправки сообщения в раздел TestTopic оно будет всегда доставляться получателям, которые оформили подписку на раздел AllMessages, и выборочно — получателям, которые оформили подписку на разделы HighMessages и LowMessages (в зависимости от содержимого сообщений).

## <span class="short-header">Отправка сообщений в раздел</span>Как отправлять сообщения в раздел

Чтобы отправить сообщение в раздел Service Bus, приложение создает объект **TopicClient** с помощью строки подключения.

В следующем примере кода показано, как создать объект **TopicClient** для раздела TestTopic, созданного с помощью вызова API **CreateFromConnectionString**:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");
    Client.Send(new BrokeredMessage());

Сообщения, отправляемые в разделы Service Bus и получаемые из них, — это экземпляры класса **BrokeredMessage**. У объектов **BrokeredMessage** есть набор стандартных свойств (например, **Label** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из произвольных данных приложения. Приложение может задать текст сообщения, передав конструктору **BrokeredMessage** любой сериализуемый объект, после чего для сериализации объекта будет использоваться соответствующий **DataContractSerializer**. Кроме того, может быть предоставлен **System.IO.Stream**.

В следующем примере показано, как отправить пять тестовых сообщений клиенту **TopicClient** раздела TestTopic, полученному в предыдущем фрагменте кода. Обратите внимание, как изменяется значение свойства **MessageNumber** для каждого сообщения в зависимости от итерации цикла (таким образом определяются подписки, которым будет доставлено это сообщение).

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

Разделы Service Bus поддерживают максимальный размер сообщения 256 КБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, составляет 64 КБ). Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ.

## <span class="short-header">Получение сообщений из подписки</span>Как получать сообщения из подписки

Самым простым способом получения сообщений из подписки является использование объекта **SubscriptionClient**. Объекты **SubscriptionClient** могут работать в двух режимах: **ReceiveAndDelete** и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда Service Bus получает запрос на чтение для сообщения в подписке, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего подходит для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы разобраться в этом, рассмотрим сценарий, в котором получатель выдает запрос на получение и вызывает сбой до его обработки. Так как Service Bus помечает сообщение как использованное, то после того как приложение перезапускается и снова начинает обрабатывать сообщения, оно пропускает сообщение, которое использовалось до сбоя.

В режиме **PeekLock** (который является режимом по умолчанию) процесс получения проходит в два этапа, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, которое должно быть использовано, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), Service Bus завершает второй этап процесса получения, вызывая метод **Complete** для полученного сообщения. Когда Service Bus обнаруживает вызов **Complete**, сообщение помечается как использованное и удаляется из подписки.

В следующем примере показано, как получать и обрабатывать сообщения с помощью режима по умолчанию **PeekLock**. Чтобы задать другое значение **ReceiveMode**, можно использовать другую перегрузку метода **CreateFromConnectionString**. В этом примере создается бесконечный цикл и сообщения обрабатываются, как только они поступают в подписку "HighMessages". Обратите внимание, что пусть к подписке HighMessages отображается в следующем формате: "\<*путь раздела*\>/subscriptions/\<*имя подписки*\>"\.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");
    Client.Receive();
     
    // Continuously process messages received from the HighMessages subscription 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();
       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("MessageNumber: " + 
                message.Properties["MessageNumber"]);
             // Remove message from subscription
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in subscription
             message.Abandon();
          }
       }
    } 

## <span class="short-header">Сбои приложения и недоступные для прочтения сообщения</span>Как обрабатывать сбои приложения и недоступные для прочтения сообщения

Service Bus поддерживает функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщений. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **Abandon** (вместо метода **Complete**). После этого Service Bus разблокирует сообщение в подписке и сделает его доступным для приема тем же или другим приложением-пользователем.

Кроме того, с сообщением, блокированным в подписке, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Service Bus автоматически разблокирует сообщение и снова сделает его доступным для получения.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **Complete**, это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, то есть каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить в него дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается неизменным для различных попыток доставки.

## <span class="short-header">Удаление разделов и подписок</span>Как удалять разделы и подписки

В следующем примере показано, как удалить раздел с именем **TestTopic** из пространства имен службы **HowToSample**:

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности. В следующем примере кода показано, как удалить подписку с именем **HighMessages** из раздела **TestTopic**:

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## <span class="short-header">Дальнейшие действия</span>Дальнейшие действия

Теперь, когда вы изучили основы использования разделов и подписок Service Bus, воспользуйтесь следующими ссылками, чтобы получить дополнительную информацию.

-   См. справочник MSDN: [Очереди, разделы и подписки][Очереди, разделы и подписки].
-   Справочник по API для [SqlFilter][SqlFilter].
-   Сборка рабочего приложения, способного отправлять сообщения в очередь Service Bus и получать их из нее: [Учебник по обмену сообщениями .NET через посредника в Service Bus][Учебник по обмену сообщениями .NET через посредника в Service Bus].

  [Дальнейшие действия]: #nextsteps
  [0]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  [Очереди, разделы и подписки]: http://msdn.microsoft.com/ru-ru/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/ru-ru/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [Учебник по обмену сообщениями .NET через посредника в Service Bus]: http://msdn.microsoft.com/ru-ru/library/hh367512.aspx
