<properties
   pageTitle="Оптимизация кода Azure в Visual Studio | Microsoft Azure"
   description="Узнайте, каким образом средства оптимизации кода Azure в Visual Studio помогут сделать код более надежным и производительным."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/08/2015"
   ms.author="tarcher" />

# Оптимизация кода Azure

Существуют определенные принципы программирования, позволяющие избежать проблем с масштабированием, поведением и производительностью приложений, использующих Microsoft Azure, в облачной среде. Майкрософт предлагает инструмент анализа кода Azure, который распознает и идентифицирует часто встречающиеся проблемы, а также помогает их решить. Его можно загрузить в Visual Studio на платформе NuGet.

## Правила анализа кода Azure

Выявляя известные проблемы, влияющие на производительность, инструмент анализа кода Azure автоматически помечает соответствующий код Azure в соответствии с описанными ниже правилами. Обнаруженные проблемы отображаются в виде предупреждений или ошибок компилятора. Для отображения рекомендаций по устранению ошибок и предупреждений используется значок лампочки.

## Старайтесь не использовать режим состояния сеанса по умолчанию (внутрипроцессорный)

### ИД

AP0000

### Описание

При использовании режима состояния сеанса по умолчанию (внутрипроцессный) для облачных приложений можно потерять состояние сеанса.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Причина

По умолчанию режимом состояния сеанса, указанным в файле web.config, является внутрипроцессный. Кроме того, если в файле конфигурации нет соответствующей записи, режимом состояния сеанса по умолчанию становится внутрипроцессный. Внутрипроцессный режим хранит состояние сеанса в памяти на веб-сервере. При перезапуске экземпляра или при использовании нового экземпляра для балансировки нагрузки и отработки отказа не сохраняется состояние сеанса, хранимое в памяти на веб-сервере. Такая ситуация не позволяет приложению быть масштабируемым в облаке.

Состояние сеанса ASP.NET поддерживает несколько различных параметров хранения данных о состоянии сеанса: InProc, StateServer, SQLServer, Custom и Off. Рекомендуется использовать пользовательский режим для размещения данных на внешнем хранилище состояния сеанса, таком как [поставщик состояния сеанса Azure для Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### Решение

Одно из рекомендуемых решений — хранение состояния сеанса в управляемой службе кэша. Узнайте, как сохранять состояния сеанса, используя [поставщик состояния сеанса Azure для Redis](http://go.microsoft.com/fwlink/?LinkId=401521). Чтобы обеспечить масштабируемость приложения в облаке, состояния сеансов можно хранить и в других местах. Подробнее об альтернативных решениях см. в статье [Режимы состояния сеанса](https://msdn.microsoft.com/library/ms178586).

## Метод Run не должен быть асинхронным

### ИД

AP1000

### Описание

Создайте асинхронные методы (такие как [await](https://msdn.microsoft.com/library/hh156528.aspx)) вне метода [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx), а затем вызовите асинхронные методы из [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Объявление метода [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) асинхронным приводит к тому, что рабочая роль входит в цикл перезагрузки.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Причина

Вызов асинхронных методов в методе [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) заставляет среду выполнения облачной службы обновить рабочую роль. При запуске рабочей роли все выполнение программы происходит в методе [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Выход из метода [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) приводит к перезапуску рабочей роли. В случаях, когда среда выполнения рабочей роли сталкивается с асинхронным методом, она производит диспетчеризацию всех операций после асинхронного метода, а затем возвращается. Это приводит к тому, что рабочая роль выходит из метода [[[[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) и перезапускается. В следующей итерации выполнения рабочая роль снова попадает на асинхронный метод и перезапускается, что опять приводит к ее обновлению.

### Решение

Разместите все асинхронные операции вне метода [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Затем вызовите рефакторизованный асинхронный метод из метода [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx), например RunAsync().wait. Инструмент анализа кода Azure может помочь решить эту проблему.

В следующем фрагменте кода продемонстрировано исправление кода для устранения этой проблемы:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## Использование проверки подлинности подписанного URL-адреса с помощью служебной шины

### ИД

AP2000

### Описание

Используйте для проверки подлинности подписанный URL-адрес (SAS). Служба управления доступом (ACS) больше не используется для проверки подлинности служебной шины.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Причина

Для повышения уровня безопасности Azure Active Directory заменяет проверку подлинности с помощью ACS на проверку подлинности с использованием SAS. Информацию о плане перехода см. в статье [Azure Active Directory — будущее для ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx).

### Решение

В приложениях используйте проверку подлинности SAS. В приведенном ниже примере показано использование существующего токена SAS для доступа к пространству имен или сущности служебной шины.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Дополнительные сведения см. в таких статьях:

- Общие сведения см. в статье [Проверка подлинности подписанного URL-адреса с помощью служебной шины](https://msdn.microsoft.com/library/dn170477.aspx)

- [Использование проверки подлинности подписанного URL-адреса с помощью служебной шины](https://msdn.microsoft.com/library/dn205161.aspx)

- Пример проекта см. в разделе [Использование проверки подлинности подписанного URL-адреса (SAS) для доступа к подпискам служебной шины](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## Рекомендуется использовать метод OnMessage, чтобы избежать "цикла получения"

### ИД

AP2002

### Описание

Чтобы избежать вхождения в "цикл получения", для получения сообщений лучше использовать вызов метода **OnMessage**, чем вызов метода **Receive**. Но если необходимо использовать метод **Receive** и задать время ожидания сервера, отличное от значения по умолчанию, убедитесь, что время ожидания сервера превышает одну минуту.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Причина

При вызове метода **OnMessage** клиент начинает процесс обработки внутренних сообщений, который постоянно опрашивает очередь или подписку. Этот процесс обработки сообщений содержит бесконечный цикл, который отправляет вызов для получения сообщений. Если время ожидания вызова истекает, выдается новый вызов. Интервал времени ожидания определяется по значению свойства [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) используемого [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx).

Преимущество метода **OnMessage** по сравнению с методом **Receive** заключается в том, что пользователям не требуется вручную извлекать сообщения, обрабатывать исключения, параллельно обрабатывать несколько сообщений и завершать сообщения.

При вызове метода **Receive** без использования значения по умолчанию убедитесь, что значение *ServerWaitTime* превышает одну минуту. Установка для *ServerWaitTime* значения больше одной минуты позволяет предотвратить истечение времени ожидания сервера до полного получения сообщения.

### Решение

См. примеры рекомендованного использования кода ниже. Дополнительные сведения см. в статьях [Метод QueueClient.OnMessage (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx) и [Метод QueueClient.Receive (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Для повышения производительности инфраструктуры обмена сообщениями Azure см. шаблон разработки [Начало асинхронного обмена сообщениями](https://msdn.microsoft.com/library/dn589781.aspx).

Ниже приведен пример использования метода **OnMessage** для получения сообщений.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Ниже приведен пример использования метода **Receive** с временем ожидания сервера по умолчанию.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

Ниже приведен пример использования метода **Receive** с временем ожидания сервера не по умолчанию.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## Рассмотрите возможность использования асинхронных методов служебной шины

### ИД

AP2003

### Описание

Используйте асинхронные методы служебной шины для повышения производительности обмена сообщениями через посредника.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Причина

Использование асинхронных методов обеспечивает программный параллелизм приложений, так как выполнение каждого вызова не блокирует основной поток. При использовании методов обмена сообщениями служебной шины требуется время на выполнение операции (отправки, получения, удаления и т. д.). В это время входит обработка операции службой Service Bus, а также задержка запроса и ответа. Чтобы увеличить количество операций в единицу времени, необходимо выполнять их параллельно. Дополнительные сведения см. в статье [Советы и рекомендации по повышению производительности с помощью обмена сообщениями через посредника служебной шины](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### Решение

Сведения о способах использования рекомендованного асинхронного метода см. в статье [Класс QueueClient (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx).

Для повышения производительности инфраструктуры обмена сообщениями Azure см. шаблон разработки [Начало асинхронного обмена сообщениями](https://msdn.microsoft.com/library/dn589781.aspx).

## Рассмотрите возможность секционирования очередей и разделов служебной шины

### ИД

AP2004

### Описание

Выполните секционирование очередей и разделов служебной шины, чтобы повысить производительность обмена сообщениями служебной шины.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Причина

Секционирование очередей и разделов служебной шины повышает производительность, пропускную способность и доступность службы, поскольку общая пропускная способность секционированной очереди или раздела не ограничивается производительностью одного брокера сообщений или хранилища сообщений. Кроме того, из-за временного сбоя хранилища сообщений секционированная очередь или раздел не становятся недоступными. Дополнительную информацию см. в разделе [Разделение сущностей обмена сообщениями](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### Решение

В следующем фрагменте кода показано, как выполнить секционирование сущностей обмена сообщениями.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Дополнительную информацию см. в статье [Секционированные очереди и разделы служебной шины | Блог Microsoft Azure](http://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) и в примере [секционированной очереди служебной шины Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f).

## Не задавайте значение SharedAccessStartTime

### ИД

AP3001

### Описание

Не задавайте значение текущего времени для параметра SharedAccessStartTime, чтобы немедленно запустить политику общего доступа. Это свойство следует задавать только в тех случаях, если политику общего доступа планируется запустить позднее.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Причина

Синхронизация часов вызывает небольшую разницу во времени между центрами обработки данных. Например, может показаться логичным, что если указать время запуска политики SAS хранилища на текущее время с помощью метода DateTime.Now или аналогичного метода, то это вызовет немедленное применение политики SAS. Однако небольшая разница во времени между центрами обработки данных может вызывать проблемы, так как одни центры обработки данных будут немного отставать от времени запуска, а другие — опережать. В результате срок действия политики SAS может быстро (или даже немедленно) закончиться, если установлено слишком короткое время жизни политики.

Дополнительные рекомендации по использованию подписанного URL-адреса в службе хранилища Azure см. в разделе [Знакомство с SAS (Подписанный URL-адрес) таблиц, SAS очередей и обновления в SAS больших двоичных объектов — блог группы службы хранилища Microsoft Azure — Главная — Блоги MSDN](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### Решение

Удалите инструкцию, которая задает время запуска политики общего доступа. Средство анализа кода Azure может помочь решить эту проблему. Подробнее об управлении безопасностью см. в шаблоне разработки [Шаблон ключа Valet](https://msdn.microsoft.com/library/dn568102.aspx).

В следующем фрагменте кода продемонстрировано исправление кода для устранения этой проблемы:

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## Время окончания срока действия общей политики доступа должно быть больше пяти минут

### ИД

AP3002

### Описание

Разница во времени между центрами обработки данных в разных расположениях может составлять до пяти минут в силу условия, известного как расфазировка синхронизирующих импульсов. Чтобы маркер политики SAS не устарел раньше, чем планировалось, установите срок действия больше пяти минут.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Причина

Центры обработки данных в разных местах по всему миру синхронизируются по сигналу часов. Поскольку передача сигнала в разные расположения требует времени, возможна разница во времени между центрами обработки в разных географических местоположениях, хотя теоретически все синхронизировано. Разница во времени может повлиять на время запуска и интервал истечения срока действия политики общего доступа. Таким образом, чтобы убедиться, что политика общего доступа вступает в силу немедленно, не указывайте время начала. Кроме того, убедитесь, что срок действия составляет более 5 минут, чтобы предотвратить раннее истечение времени ожидания.

Дополнительную информацию по использованию подписанного URL-адреса в службе хранилища Azure см. в разделе [Знакомство с SAS (Подписанный URL-адрес) таблиц, SAS очередей и обновления в SAS BLOB-объектов — блог группы службы хранилища Microsoft Azure — Главная — Блоги MSDN](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### Решение

Дополнительную информацию об управлении безопасностью см. в шаблоне разработки [Шаблон ключа Valet](https://msdn.microsoft.com/library/dn568102.aspx).

Ниже приведен пример, в котором не указано время запуска политики общего доступа.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Ниже приведен пример, в котором указано время запуска политики общего доступа со сроком действия больше пяти минут.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Дополнительную информацию см. в статье [Создание и использование подписанного URL-адреса](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## Используйте CloudConfigurationManager

### ИД

AP4000

### Описание

Использование класса [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) для таких проектов, как веб-сайт Azure и мобильные службы Azure, не вызовет проблем времени выполнения. Рекомендуется использовать Cloud[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) как единый способ управления конфигурациями всех приложений облака Azure.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Причина

CloudConfigurationManager выполняет считывание файла конфигурации, который подходит для среды приложения.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### Решение

Переработайте свой код для использования [класса CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). Инструмент анализа кода Azure позволяет исправить код и устранить эту проблему.

В следующем фрагменте кода продемонстрировано исправление кода для устранения этой проблемы: Замените

`var settings = ConfigurationManager.AppSettings["mySettings"];`

на

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Ниже приведен пример того, как сохранить параметры конфигурации в файл App.config или Web.config. Добавьте параметры в раздел appSettings файла конфигурации. Ниже приведен файл Web.config для предыдущего примера кода.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## Избегайте использования жестко запрограммированных строк подключения

### ИД

AP4001

### Описание

Если используются жестко запрограммированные строки подключения, которые вам потребуется обновить позднее, необходимо внести изменения в исходный код и перекомпилировать приложение. Но если хранить строки подключения в файле конфигурации, вы сможете изменить их позже, просто обновив файл конфигурации.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Причина

Не рекомендуется использовать жестко запрограммированные строки подключения, так как из-за этого возникают проблемы при необходимости быстро изменить строки подключения. Кроме того, если проект должен быть помещен в систему управления версиями, жестко запрограммированные строки подключения делают систему безопасности уязвимой, так как строки можно увидеть в исходном коде.

### Решение

Храните строки подключения в файлах конфигурации или в средах Azure.

- Для автономных приложений используйте для хранения параметров строки подключения файл app.config.

- Для веб-приложений, размещенных в IIS, используйте для хранения параметров строки подключения файл web.config.

- Для приложений ASP.NET vNext используйте для хранения параметров строки подключения файл configuration.json.

Сведения об использовании файлов конфигурации, таких как web.config или app.config, см. в разделе [Правила веб-конфигурации ASP.NET](https://msdn.microsoft.com/library/vstudio/ff400235(v=vs.100).aspx). Сведения о принципах работы переменных среды Azure см. в справочной системе [Веб-сайты Microsoft Azure: как работают строки приложения и подключения](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Сведения о хранении строки подключения в системе управления версиями см. в разделе [Не размещайте конфиденциальные сведения, например строки подключения, в файлах, которые хранятся в репозитории исходного кода](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## Использование файла конфигурации диагностики

### ИД

AP5000

### Описание

Вместо настройки параметров диагностики в коде, например с помощью API программирования Microsoft.WindowsAzure.Diagnostics, следует настроить параметры диагностики в файле diagnostics.wadcfg (или в файле diagnostics.wadcfgx, если используется пакет Azure SDK 2.5). Это позволит изменить параметры диагностики без повторной компиляции кода.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Причина

До появления пакета Azure SDK 2.5 (с использованием диагностики Azure 1.3) диагностику Microsoft Azure (WAD) можно было настраивать с помощью нескольких различных методов: путем ее добавления в BLOB-объект конфигурации в хранилище, с использованием императивного кода, декларативной конфигурации или конфигурации по умолчанию. Однако предпочтительным способом настройки диагностики является использование XML-файла конфигурации (diagnostics.wadcfg или diagnositcs.wadcfgx для пакета SDK 2.5 и более поздних версий) в проекте приложения. При таком подходе файл diagnostics.wadcfg полностью определяет конфигурацию, может обновляться и повторно развертываться по желанию. Использование файла конфигурации diagnostics.wadcfg наряду с программными методами настройки конфигураций при применении классов [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx) или [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx) может вас запутать. Дополнительную информацию см. в статье [Инициализация или изменение конфигурации службы диагностики Azure](https://msdn.microsoft.com/library/azure/hh411537.aspx).

Начиная с версии 1.3 службы WAD (входит в состав пакета SDK для Azure 2.5) нельзя использовать код для настройки диагностики. В результате вы можете предоставить конфигурацию только при применении или обновлении расширения диагностики.

### Решение

Используйте конструктор конфигурации диагностики для перемещения диагностических параметров в файл конфигурации диагностики (diagnositcs.wadcfg или diagnositcs.wadcfgx для пакета SDK 2.5 и более поздних версий). Кроме того, рекомендуется установить пакет [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) и использовать последние функции диагностики.

1. В контекстном меню интересующей вас роли выберите пункт "Свойства", а затем перейдите на вкладку "Конфигурация".

1. В разделе **Диагностика** установите флажок **Включить диагностику**.

1. Нажмите кнопку **Настроить**.

  ![Доступ к параметру "Включить диагностику"](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

  Дополнительную информацию см. в статье [Настройка системы диагностики для облачных служб и виртуальных машин Azure](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).


## Не объявляйте объекты DbContext статическими

### ИД

AP6000

### Описание

Для экономии памяти не объявляйте объекты DBContext статическими.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Причина

Объекты DBContext содержат результаты запроса из каждого вызова. Статические объекты DBContext не удаляются, пока не будет выгружен домен приложения. В связи с этим статический объект DBContext может использовать большой объем памяти.

### Решение

Объявите DBContext как локальную переменную или поле нестатического экземпляра, используйте его для задачи, а после использования он будет удален.

Приведенный пример класса контроллера MVC показывает, как использовать объект DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## Дальнейшие действия

Дополнительную информацию об оптимизации и устранении неполадок приложений Azure см. в статье [Устранение неполадок веб-приложения в службе приложений Azure с помощью Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

<!----HONumber=AcomDC_1217_2015-->