---
title: Оптимизация кода Azure в Visual Studio | Документация Майкрософт
description: Узнайте, каким образом средства оптимизации кода Azure в Visual Studio помогут сделать код более надежным и производительным.
services: visual-studio-online
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ed48ee06-e2d2-4322-af22-07200fb16987
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: cawa
ms.openlocfilehash: a606c8e7d8b730b67bd8481656e099900eb39fbc
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2017
ms.locfileid: "23452403"
---
# <a name="optimizing-your-azure-code"></a>Оптимизация кода Azure
Существуют определенные принципы программирования, позволяющие избежать проблем с масштабированием, поведением и производительностью приложений, использующих Microsoft Azure, в облачной среде. Майкрософт предлагает инструмент анализа кода Azure, который распознает и идентифицирует часто встречающиеся проблемы, а также помогает их решить. Его можно загрузить в Visual Studio на платформе NuGet.

## <a name="azure-code-analysis-rules"></a>Правила анализа кода Azure
Выявляя известные проблемы, влияющие на производительность, инструмент анализа кода Azure автоматически помечает соответствующий код Azure в соответствии с описанными ниже правилами. Обнаруженные проблемы отображаются в виде предупреждений или ошибок компилятора. Для отображения рекомендаций по устранению ошибок и предупреждений используется значок лампочки.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Старайтесь не использовать режим состояния сеанса по умолчанию (внутрипроцессорный)
### <a name="id"></a>ИД
AP0000

### <a name="description"></a>ОПИСАНИЕ
При использовании режима состояния сеанса по умолчанию (внутрипроцессный) для облачных приложений можно потерять состояние сеанса.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Причина
По умолчанию режимом состояния сеанса, указанным в файле web.config, является внутрипроцессный. Кроме того, если в файле конфигурации нет соответствующей записи, режимом состояния сеанса по умолчанию становится внутрипроцессный. Внутрипроцессный режим хранит состояние сеанса в памяти на веб-сервере. При перезапуске экземпляра или при использовании нового экземпляра для балансировки нагрузки и отработки отказа не сохраняется состояние сеанса, хранимое в памяти на веб-сервере. Такая ситуация не позволяет приложению быть масштабируемым в облаке.

Состояние сеанса ASP.NET поддерживает несколько различных параметров хранения данных о состоянии сеанса: InProc, StateServer, SQLServer, Custom и Off. Рекомендуется использовать пользовательский режим для размещения данных на внешнем хранилище состояния сеанса, таком как [поставщик состояния сеанса Azure для Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Решение
Одно из рекомендуемых решений — хранение состояния сеанса в управляемой службе кэша. Узнайте, как сохранять состояния сеанса, используя [поставщик состояний сеанса Azure для Redis](http://go.microsoft.com/fwlink/?LinkId=401521). Чтобы обеспечить масштабируемость приложения в облаке, состояния сеансов можно хранить и в других местах. Чтобы больше узнать об альтернативных решениях, ознакомьтесь со статьей [Режимы состояний сеанса](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Метод Run не должен быть асинхронным
### <a name="id"></a>ИД
AP1000

### <a name="description"></a>ОПИСАНИЕ
Создайте асинхронные методы (такие как [await](https://msdn.microsoft.com/library/hh156528.aspx)) вне метода [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx), а затем вызовите эти методы из [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Объявление метода [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) асинхронным приводит к тому, что рабочая роль входит в цикл перезагрузки.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Причина
Вызов асинхронных методов в методе [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) заставляет среду выполнения облачной службы обновить рабочую роль. При запуске рабочей роли все выполнение программы происходит в методе [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Выход из метода [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) приводит к перезапуску рабочей роли. В случаях, когда среда выполнения рабочей роли сталкивается с асинхронным методом, она производит диспетчеризацию всех операций после асинхронного метода, а затем возвращается. Это приводит к тому, что рабочая роль выходит из метода [[[[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) и перезапускается. В следующей итерации выполнения рабочая роль снова попадает на асинхронный метод и перезапускается, что опять приводит к ее обновлению.

### <a name="solution"></a>Решение
Разместите все асинхронные операции вне метода [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Затем вызовите рефакторизованный асинхронный метод из метода [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , например RunAsync().wait. Инструмент анализа кода Azure может помочь решить эту проблему.

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

## <a name="use-service-bus-shared-access-signature-authentication"></a>Использование проверки подлинности подписанного URL-адреса с помощью служебной шины
### <a name="id"></a>ИД
AP2000

### <a name="description"></a>ОПИСАНИЕ
Используйте для проверки подлинности подписанный URL-адрес (SAS). Служба управления доступом (ACS) больше не используется для проверки подлинности служебной шины.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Причина
Для повышения уровня безопасности Azure Active Directory заменяет проверку подлинности с помощью ACS на проверку подлинности с использованием SAS. Информацию о плане перехода см. в статье [Azure Active Directory is the future of ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) (Azure Active Directory — будущее для ACS).

### <a name="solution"></a>Решение
В приложениях используйте проверку подлинности SAS. В приведенном ниже примере показано использование существующего токена SAS для доступа к пространству имен или сущности служебной шины.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Дополнительные сведения см. в следующих статьях.

* Общие сведения см. в статье [Shared Access Signature Authentication with Service Bus](https://msdn.microsoft.com/library/dn170477.aspx) (Аутентификация на основе подписанного URL-адреса с помощью служебной шины).
* [Использование проверки подлинности подписанного URL-адреса с помощью служебной шины](https://msdn.microsoft.com/library/dn205161.aspx)
* Пример проекта см. на странице [Примеры кода Azure](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c).

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Рекомендуется использовать метод OnMessage, чтобы избежать "цикла получения"
### <a name="id"></a>ИД
AP2002

### <a name="description"></a>ОПИСАНИЕ
Чтобы избежать вхождения в "цикл получения", для получения сообщений лучше использовать вызов метода **OnMessage**, чем вызов метода **Receive**. Но если необходимо использовать метод **Receive** и задать время ожидания сервера, отличное от значения по умолчанию, убедитесь, что время ожидания сервера превышает одну минуту.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Причина
При вызове метода **OnMessage**клиент начинает процесс обработки внутренних сообщений, который постоянно опрашивает очередь или подписку. Этот процесс обработки сообщений содержит бесконечный цикл, который отправляет вызов для получения сообщений. Если время ожидания вызова истекает, выдается новый вызов. Интервал времени ожидания определяется по значению свойства [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) используемого [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx).

Преимущество метода **OnMessage** по сравнению с методом **Receive** заключается в том, что пользователям не требуется вручную извлекать сообщения, обрабатывать исключения, параллельно обрабатывать несколько сообщений и завершать сообщения.

При вызове метода **Receive** без использования значения по умолчанию убедитесь, что значение *ServerWaitTime* превышает одну минуту. Установка для *ServerWaitTime* значения больше одной минуты позволяет предотвратить истечение времени ожидания сервера до полного получения сообщения.

### <a name="solution"></a>Решение
См. примеры рекомендованного использования кода ниже. Дополнительные сведения см. в статьях [Метод QueueClient.OnMessage (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx) и [Метод QueueClient.Receive (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Для повышения производительности инфраструктуры обмена сообщениями Azure ознакомьтесь с шаблоном проекта в разделе [Учебник по асинхронному обмену сообщениями](https://msdn.microsoft.com/library/dn589781.aspx).

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
## <a name="consider-using-asynchronous-service-bus-methods"></a>Рассмотрите возможность использования асинхронных методов служебной шины
### <a name="id"></a>ИД
AP2003

### <a name="description"></a>ОПИСАНИЕ
Используйте асинхронные методы служебной шины для повышения производительности обмена сообщениями через посредника.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Причина
Использование асинхронных методов обеспечивает программный параллелизм приложений, так как выполнение каждого вызова не блокирует основной поток. При использовании методов обмена сообщениями служебной шины требуется время на выполнение операции (отправки, получения, удаления и т. д.). В это время входит обработка операции службой Service Bus, а также задержка запроса и ответа. Чтобы увеличить количество операций в единицу времени, необходимо выполнять их параллельно. Дополнительные сведения см. в статье [Советы и рекомендации по повышению производительности с помощью обмена сообщениями через посредника служебной шины](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Решение
Сведения о способах использования рекомендованного асинхронного метода см. в статье [Класс QueueClient (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx).

Для повышения производительности инфраструктуры обмена сообщениями Azure ознакомьтесь с шаблоном проекта в разделе [Учебник по асинхронному обмену сообщениями](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Попробуйте секционировать очереди и разделы служебной шины
### <a name="id"></a>ИД
AP2004

### <a name="description"></a>ОПИСАНИЕ
Секционирование очередей и разделов служебной шины для повышения производительности обмена сообщениями служебной шины.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Причина
Секционирование очередей и разделов служебной шины повышает производительность, пропускную способность и доступность службы, поскольку общая пропускная способность секционированной очереди или раздела не ограничивается производительностью одного брокера сообщений или хранилища сообщений. Кроме того, из-за временного сбоя хранилища сообщений секционированная очередь или раздел не становятся недоступными. Дополнительную информацию см. в разделе [Разделение сущностей обмена сообщениями](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Решение
В следующем фрагменте кода показано, как выполнить секционирование сущностей обмена сообщениями.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Чтобы узнать больше, ознакомьтесь с записью [блога Microsoft Azure, посвященной секционированным очередям и разделам служебной шины](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/), и примером [секционированной очереди служебной шины Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f).

## <a name="do-not-set-sharedaccessstarttime"></a>Не задавайте значение SharedAccessStartTime
### <a name="id"></a>ИД
AP3001

### <a name="description"></a>ОПИСАНИЕ
Не задавайте значение текущего времени для параметра SharedAccessStartTime, чтобы немедленно запустить политику общего доступа. Это свойство следует задавать только в тех случаях, если политику общего доступа планируется запустить позднее.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Причина
Синхронизация часов вызывает небольшую разницу во времени между центрами обработки данных. Например, может показаться логичным, что если указать время запуска политики SAS хранилища на текущее время с помощью метода DateTime.Now или аналогичного метода, то это вызовет немедленное применение политики SAS. Однако небольшая разница во времени между центрами обработки данных может вызывать проблемы, так как одни центры обработки данных будут немного отставать от времени запуска, а другие — опережать. В результате срок действия политики SAS может быстро (или даже немедленно) закончиться, если установлено слишком короткое время жизни политики.

Дополнительные рекомендации по использованию подписанного URL-адреса в службе хранилища Azure см. в записи блога MSDN [Introducing Table SAS (Shared Access Signature), Queue SAS and update to Blob SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx) (Знакомство с SAS (подписанный URL-адрес) таблиц, SAS очередей и изменениями SAS больших двоичных объектов).

### <a name="solution"></a>Решение
Удалите инструкцию, которая задает время запуска политики общего доступа. Средство анализа кода Azure может помочь решить эту проблему. Чтобы узнать больше об управлении безопасностью, ознакомьтесь с конструктивным шаблоном [Ключ камердинера](https://msdn.microsoft.com/library/dn568102.aspx).

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

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Время окончания срока действия общей политики доступа должно быть больше пяти минут
### <a name="id"></a>ИД
AP3002

### <a name="description"></a>ОПИСАНИЕ
Разница во времени между центрами обработки данных в разных расположениях может составлять до пяти минут в силу условия, известного как расфазировка синхронизирующих импульсов. Чтобы маркер политики SAS не устарел раньше, чем планировалось, установите срок действия больше пяти минут.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Причина
Центры обработки данных в разных местах по всему миру синхронизируются по сигналу часов. Поскольку передача сигнала в разные расположения требует времени, возможна разница во времени между центрами обработки в разных географических местоположениях, хотя теоретически все синхронизировано. Разница во времени может повлиять на время запуска и интервал истечения срока действия политики общего доступа. Таким образом, чтобы убедиться, что политика общего доступа вступает в силу немедленно, не указывайте время начала. Кроме того, убедитесь, что срок действия составляет более 5 минут, чтобы предотвратить раннее истечение времени ожидания.

Дополнительную информацию по использованию подписанного URL-адреса в службе хранилища Azure см. в записи блога MSDN [Introducing Table SAS (Shared Access Signature), Queue SAS and update to Blob SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx) (Знакомство с SAS (подписанный URL-адрес) таблиц, SAS очередей и изменениями SAS больших двоичных объектов).

### <a name="solution"></a>Решение
Чтобы узнать больше об управлении безопасностью, ознакомьтесь с конструктивным шаблоном [Ключ камердинера](https://msdn.microsoft.com/library/dn568102.aspx).

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

## <a name="use-cloudconfigurationmanager"></a>Используйте CloudConfigurationManager
### <a name="id"></a>ИД
AP4000

### <a name="description"></a>ОПИСАНИЕ
Использование класса [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) для таких проектов, как веб-сайт Azure и мобильные службы Azure, не вызовет проблем времени выполнения. Рекомендуется использовать Cloud[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) как единый способ управления конфигурациями всех облачных приложений Azure.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Причина
CloudConfigurationManager выполняет считывание файла конфигурации, который подходит для среды приложения.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Решение
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

## <a name="avoid-using-hard-coded-connection-strings"></a>Избегайте использования жестко запрограммированных строк подключения
### <a name="id"></a>ИД
AP4001

### <a name="description"></a>ОПИСАНИЕ
Если используются жестко запрограммированные строки подключения, которые вам потребуется обновить позднее, необходимо внести изменения в исходный код и перекомпилировать приложение. Но если хранить строки подключения в файле конфигурации, вы сможете изменить их позже, просто обновив файл конфигурации.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Причина
Не рекомендуется использовать жестко запрограммированные строки подключения, так как из-за этого возникают проблемы при необходимости быстро изменить строки подключения. Кроме того, если проект должен быть помещен в систему управления версиями, жестко запрограммированные строки подключения делают систему безопасности уязвимой, так как строки можно увидеть в исходном коде.

### <a name="solution"></a>Решение
Храните строки подключения в файлах конфигурации или в средах Azure.

* Для автономных приложений используйте для хранения параметров строки подключения файл app.config.
* Для веб-приложений, размещенных в IIS, используйте для хранения параметров строки подключения файл web.config.
* Для приложений ASP.NET vNext используйте для хранения параметров строки подключения файл configuration.json.

Сведения об использовании файлов конфигурации, таких как web.config или app.config, см. в разделе [Правила веб-конфигурации ASP.NET](https://msdn.microsoft.com/library/vstudio/ff400235\(v=vs.100\).aspx). Сведения о принципах работы переменных среды Azure см. в записи блога [Windows Azure Web Sites: How Application Strings and Connection Strings Work](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) (Веб-сайты Microsoft Azure: как работают строки приложения и строки подключения). Чтобы узнать о хранении строки подключения в системе управления версиями см. в разделе [Source Control (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) (Система управления версиями (создание реальных облачных приложений в Azure)).

## <a name="use-diagnostics-configuration-file"></a>Использование файла конфигурации диагностики
### <a name="id"></a>ИД
AP5000

### <a name="description"></a>ОПИСАНИЕ
Вместо настройки параметров диагностики в коде, например с помощью API программирования Microsoft.WindowsAzure.Diagnostics, следует настроить параметры диагностики в файле diagnostics.wadcfg (или в файле diagnostics.wadcfgx, если используется пакет Azure SDK 2.5). Это позволит изменить параметры диагностики без повторной компиляции кода.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Причина
До появления пакета Azure SDK 2.5 (с использованием диагностики Azure 1.3) диагностику Microsoft Azure (WAD) можно было настраивать с помощью нескольких различных методов: путем ее добавления в BLOB-объект конфигурации в хранилище, с использованием императивного кода, декларативной конфигурации или конфигурации по умолчанию. Однако предпочтительным способом настройки диагностики является использование XML-файла конфигурации (diagnostics.wadcfg или diagnositcs.wadcfgx для пакета SDK 2.5 и более поздних версий) в проекте приложения. При таком подходе файл diagnostics.wadcfg полностью определяет конфигурацию, может обновляться и повторно развертываться по желанию. Использование файла конфигурации diagnostics.wadcfg наряду с программными методами настройки конфигураций при применении классов [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx) или [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx) может вас запутать. Дополнительную информацию см. в статье [Инициализация или изменение конфигурации службы диагностики Azure](https://msdn.microsoft.com/library/azure/hh411537.aspx).

Начиная с версии 1.3 службы WAD (входит в состав пакета SDK для Azure 2.5) нельзя использовать код для настройки диагностики. В результате вы можете предоставить конфигурацию только при применении или обновлении расширения диагностики.

### <a name="solution"></a>Решение
Используйте конструктор конфигурации диагностики для перемещения диагностических параметров в файл конфигурации диагностики (diagnositcs.wadcfg или diagnositcs.wadcfgx для пакета SDK 2.5 и более поздних версий). Кроме того, рекомендуется установить пакет [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) и использовать последние функции диагностики.

1. В контекстном меню интересующей вас роли выберите пункт "Свойства", а затем перейдите на вкладку "Конфигурация".
2. В разделе **Диагностика** установите флажок **Включить диагностику**.
3. Нажмите кнопку **Настроить** .

   ![Доступ к параметру "Включить диагностику"](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

   Дополнительную информацию см. в статье [Настройка системы диагностики для облачных служб и виртуальных машин Azure](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Не объявляйте объекты DbContext статическими
### <a name="id"></a>ИД
AP6000

### <a name="description"></a>ОПИСАНИЕ
Для экономии памяти не объявляйте объекты DBContext статическими.

Делитесь своими идеями и предложениями на [странице отзывов об анализе кода Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Причина
Объекты DBContext содержат результаты запроса из каждого вызова. Статические объекты DBContext не удаляются, пока не будет выгружен домен приложения. В связи с этим статический объект DBContext может использовать большой объем памяти.

### <a name="solution"></a>Решение
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

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об оптимизации и устранении неполадок приложений Azure см. в статье [Устранение неполадок веб-приложения в службе приложений Azure с помощью Visual Studio](app-service/web-sites-dotnet-troubleshoot-visual-studio.md).
