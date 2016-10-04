<properties
    pageTitle="Использование разделов служебной шины с .NET | Microsoft Azure"
    description="Узнайте, как использовать разделы и подписки служебной шины с .NET в Azure. Примеры кода написаны для приложений .NET."
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# Как использовать разделы и подписки служебной шины

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

В этой статье описывается использование разделов и подписок служебной шины. Примеры написаны на языке C# и используют интерфейсы API .NET. В этой статье описаны такие сценарии, как создание разделов и подписок, создание фильтров подписок, отправка сообщений в раздел, получение сообщений из подписки и удаление разделов и подписок. Дополнительные сведения о разделах и подписках см. в разделе [Дальнейшие действия](#next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Настройка приложения для использования служебной шины

При создании приложения, использующего служебную шину, необходимо добавить ссылку на сборку шины обслуживания и указать соответствующие пространства имен. Самый простой способ это сделать — скачать соответствующий пакет [NuGet](https://www.nuget.org).

## Получение пакета NuGet для служебной шины

[Пакет NuGet для служебной шины](https://www.nuget.org/packages/WindowsAzure.ServiceBus) — это самый простой способ получить API служебной шины и настроить свое приложение с учетом всех необходимых зависимостей служебной шины. Для установки пакета NuGet служебной шины в проекте выполните следующие действия:

1.  В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, затем выберите команду **Управление пакетами NuGet**.
2.  Выполните поиск по фразе "служебная шина" и выберите элемент **Служебная шина Microsoft Azure**. Нажмите кнопку **Установить**, чтобы выполнить установку, а затем закройте следующее диалоговое окно.

    ![][7]

Теперь все готово к написанию кода для служебной шины.

## Создание строки подключения для служебной шины

Служебная шина использует строку подключения для хранения учетных данных и конечных точек. Строку подключения можно разместить в файле конфигурации, не задавая ее жестко в коде:

- При использовании служб Azure рекомендуется хранить строку подключения с помощью системы конфигурации службы Azure (файлы CSDEF и CSCFG).
- При использовании веб-сайтов Azure или виртуальных машин Azure рекомендуется сохранять строки подключения с помощью системы конфигурации .NET (например, в файле Web.config).

В обоих случаях строку подключения можно получить с помощью метода `CloudConfigurationManager.GetSetting`, как показано далее в этой статье.

### Настройка строки подключения

Механизм настройки службы позволяет динамически изменять параметры конфигурации на [портале Azure][] без повторного развертывания приложения. Например, добавьте метку `Setting` в файл определения службы (**CSDEF-файл**), как показано в примере ниже.

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

Затем задайте значения в файле конфигурации службы (CSCFG).

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

Используйте имя и значения ключа подписанного URL-адреса (SAS), полученные на портале Azure, как описано ранее.

### Настройка строки подключения при использовании веб-сайтов Azure или виртуальных машин Azure

При использовании веб-сайтов или виртуальных машин рекомендуется использовать систему конфигурации .NET (например, Web.config). Строка подключения сохраняется с помощью элемента `<appSettings>`.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Используйте имя и значения ключа SAS, полученные на [портале Azure][], как описано ранее.

## Создание раздела

Операции управления для разделов и подписок служебной шины можно выполнять с использованием класса [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Этот класс предоставляет методы для создания, перечисления и удаления разделов.

В этом примере объект `NamespaceManager` создается с помощью класса Azure `CloudConfigurationManager` со строкой подключения, состоящей из базового адреса пространства имен служебной шины и учетных данных SAS с соответствующими правами на управление. Эта строка подключения имеет следующий вид:

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Используйте следующий пример с параметрами конфигурации из предыдущего раздела.

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Существуют перегруженные версии метода [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx), позволяющие задавать свойства этого раздела, например стандартное значение срока жизни (TimeToLive) сообщений, отправляемых в раздел. Эти параметры применяются с помощью класса [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx). В следующем примере демонстрируется создание раздела **TestTopic** с максимальным размером 5 ГБ и сроком жизни по умолчанию, равным 1 минуте.

```
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
```

> [AZURE.NOTE] Примечание. Вы можете использовать метод [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) для объектов [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx), чтобы проверить, существует ли раздел с указанным именем в пространстве имен.

## Создание подписки

Подписки раздела можно также создавать с помощью класса [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Если при создании новой подписки не указан фильтр, то по умолчанию используется фильтр **MatchAll**. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка AllMessages и используется фильтр по умолчанию **MatchAll**.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### Создание подписок с фильтрами

Кроме того, можно настраивать фильтры, позволяющие определять, какие посылаемые в раздел сообщения должны появляться в рамках конкретной подписки.

Самый гибкий тип фильтра, который поддерживается подписками, — это класс [SqlFilter][], реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о выражениях, которые можно использовать с SQL-фильтром, см. в описании синтаксиса [SqlFilter.SqlExpression][].

В следующем примере создается подписка с именем **HighMessages**, содержащая объект [SqlFilter][]. Этот объект выбирает только сообщения со значением настраиваемого свойства **MessageNumber** больше 3.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

Аналогично в следующем примере создается подписка с именем **LowMessages** и фильтром [SqlFilter][], который выбирает только сообщения со значением свойства **MessageNumber** меньше или равно 3.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Теперь, когда в раздел `TestTopic` отправляется сообщение, оно будет всегда доставляться получателям, подписанным на раздел **AllMessages**, и выборочно доставляться получателям, подписанным на разделы **HighMessages** и **LowMessages** в зависимости от содержания сообщения.

## Отправка сообщений в раздел

Чтобы отправить сообщение в раздел служебной шины, приложение создает объект [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) с помощью строки подключения.

В примере кода ниже показано, как создать объект [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) для раздела **TestTopic**, созданного ранее с помощью вызова API [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx).

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Сообщения, отправляемые в разделы служебной шины и получаемые из них, — это экземпляры класса [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Объекты [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) обладают набором стандартных свойств (таких как [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) и [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), словарем, в котором хранятся зависящие от приложения пользовательские свойства, и основным набором произвольных данных приложения. Приложение может задать текст сообщения, передав конструктору объекта [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) любой сериализуемый объект, после чего для сериализации объекта будет использоваться соответствующий класс **DataContractSerializer**. Или можно указать класс **System.IO.Stream**.

В следующем примере показано, как отправить пять тестовых сообщений в объект [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) раздела **TestTopic**, полученный в предыдущем примере кода. Обратите внимание, что значение свойства [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) каждого из сообщений зависит от итерации цикла (определяет, какие подписки их получают).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Разделы служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня "Премиум"](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ. Если включено разделение, максимальный размер больше. Дополнительные сведения см. в статье [Секционированные очереди и разделы](service-bus-partitioning.md).

## Как получать сообщения из подписки

Чтобы получить сообщения из подписки, вы можете использовать объект [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx). Объекты [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) могут работать в двух режимах: [*ReceiveAndDelete* и *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

В режиме **ReceiveAndDelete** получение осуществляется в рамках одиночной операции. То есть когда служебная шина получает запрос на чтение для сообщения в подписке, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает в ситуациях, когда приложение может не обрабатывать сообщение при сбое. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Так как служебная шина отметит сообщение как использованное, перезапущенное приложение, начав снова использовать сообщения, пропустит сообщение, использованное до аварийного завершения работы.

В режиме **PeekLock** (режим по умолчанию) процесс получения проходит в два этапа. Это позволяет поддерживать приложения, в которых не допускается пропуск сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или надежно сохраняет его для последующей обработки), оно завершает второй этап процесса получения, вызывая метод [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) для полученного сообщения. Когда служебная шина обнаруживает вызов **Complete**, сообщение помечается как использованное и удаляется из подписки.

В следующем примере показано, как получать и обрабатывать сообщения с помощью стандартного режима **PeekLock**. Чтобы задать другое значение [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), можно использовать другую перегрузку метода [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). В этом примере используется обратный вызов [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) для обработки сообщений по мере их поступления в подписку **HighMessages**.

```
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
```

В этом примере обратный вызов [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) настраивается с помощью объекта [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). Для [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) устанавливается значение **False**. Это позволяет включить ручное управление вызовом метода [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) для полученного сообщения. Для параметра [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) устанавливается значение 1 минута. Клиент ожидает сообщение в течение одной минуты, после чего действие функции автоматического продления завершается и клиент создает новый вызов для проверки сообщений. Значение этого свойства сокращает количество создаваемых клиентом оплачиваемых вызовов, не приводящих к получению сообщений.

## Как обрабатывать сбои приложения и нечитаемые сообщения

служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если принимающее приложение по каким-то причинам не может обработать полученное сообщение, оно вызывает метод [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) (а не метод [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). После этого служебная шина разблокирует сообщение в подписке и снова сделает его доступным для получения тем же или другим приложением.

Кроме того, с сообщением, заблокированным в подписке, связано время ожидания. Если приложение не может обработать сообщение в течение времени ожидания с блокировкой (например, при сбое приложения), служебная шина разблокирует сообщение автоматически и сделает его доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), это сообщение будет повторно доставлено в приложение после его перезапуска. Часто такой подход называют *Обработать хотя бы один раз*, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) сообщения, которое остается постоянным в ходе разных попыток доставки.

## Удаление разделов и подписок

В следующем примере показано, как удалить раздел с именем **TestTopic** из пространства имен службы **HowToSample**.

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности. В следующем коде показано, как удалить подписку с именем **HighMessages** из раздела **TestTopic**.

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## Дальнейшие действия

Вы узнали основные сведения о разделах и подписках служебной шины. Дополнительные сведения см. в следующих источниках.

-   [Очереди, разделы и подписки служебной шины][].
-   [Пример фильтров раздела][]
-   Справочник API для [SqlFilter][].
-   Дополнительные сведения о создании работающего приложения, отправляющего сообщения в очередь служебной шины и получающего их из нее, см. в статье [Учебное пособие по обмену сообщениями .NET через посредника в служебной шине][].
-   Примеры служебной шины см. на странице [примеров Azure][] или в статье [Служебная шина: примеры](../service-bus/service-bus-samples.md).

  [портале Azure]: https://portal.azure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Очереди, разделы и подписки служебной шины]: service-bus-queues-topics-subscriptions.md
  [Пример фильтров раздела]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Учебное пособие по обмену сообщениями .NET через посредника в служебной шине]: service-bus-brokered-tutorial-dotnet.md
  [примеров Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2

<!---HONumber=AcomDC_0928_2016-->