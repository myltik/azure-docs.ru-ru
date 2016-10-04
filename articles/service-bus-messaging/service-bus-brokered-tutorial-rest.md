<properties 
    pageTitle="Руководство по обмену сообщениями через посредника служебной шины на основе REST | Microsoft Azure"
    description="Руководство по обмену сообщениями через посредника на основе REST"
    services="service-bus-messaging"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus-messaging"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# Руководство по обмену сообщениями через посредника служебной шины на основе REST

В этом руководстве показано, как создать базовую очередь служебной шины Azure и раздел или подписку на основе REST.

## Создание пространства имен

Сначала необходимо создать пространство имен службы и получить ключ [подписи общего доступа](../service-bus/service-bus-sas-overview.md) (SAS). Пространство имен определяет границы каждого приложения, предоставляемого через служебную шину. Ключ SAS автоматически создается системой при создании пространства имен службы. Сочетание пространства имен и ключа совместного доступа к подписи предоставляет учетные данные, на основе которых служба Service Bus осуществляет проверку подлинности и дает доступ к приложению.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## Создание клиента консоли

Очереди служебной шины позволяют хранить сообщения в очереди типа "первым поступил — первым обслужен". Разделы и подписки реализуют шаблон публикации/подписки. Сначала создается раздел, а затем одна или несколько подписок, связанных с этим разделом. При отправке сообщений в раздел они немедленно отправляются подписчикам этого раздела.

Код, приведенный в этом руководстве, выполняет следующие задачи.

- Использует пространство имен и ключ [подписанного URL-адреса](../service-bus/service-bus-sas-overview.md) (SAS) для получения доступа к ресурсам пространства имен служебной шины.

- Создает очередь, отправляет сообщение в очередь и читает сообщение из очереди.

- Создает раздел, подписку на этот раздел и отправляет и читает сообщение из подписки.

- Получает все сведения об очереди, разделе и подписке, включая правила подписки, от служебной шины.

- Удаляет ресурсы очереди, раздела и подписки.

Поскольку служба представляет собой веб-службу в стиле REST, то специальные типы не применяются, так как для всего обмена используются строки. Это означает, что проект Visual Studio не должен ссылаться на библиотеки служебной шины.

Получив пространство имен и учетные данные на первом шаге, создайте простое консольное приложение Visual Studio.

### Создание консольного приложение

1. Откройте Visual Studio с правами администратора, щелкнув программу правой кнопкой мыши в меню **Пуск** и выбрав **Запуск от имени администратора**.

1. Создайте новый проект консольного приложения. В меню **Файл** выберите **Создать**, а затем **Проект**. В диалоговом окне **Новый проект** выберите **Visual C#** (если **Visual C#** не отображается, откройте список **Другие языки**). Выберите шаблон **Консольное приложение** и назовите его **Microsoft.ServiceBus.Samples**. Используйте расположение по умолчанию. Нажмите кнопку **ОК**, чтобы создать проект.

1. Убедитесь, что в файле Program.cs операторы `using` отображаются следующим образом.

	```
	using System;
	using System.Globalization;
	using System.IO;
	using System.Net;
	using System.Security.Cryptography;
	using System.Text;
	using System.Xml;
	```

1. При необходимости переименуйте пространство имен для программы, заданное в Visual Studio по умолчанию, в `Microsoft.ServiceBus.Samples`.

1. В класс `Program` добавьте следующие глобальные переменные.
	
	```
	static string serviceNamespace;
	static string baseAddress;
	static string token;
	const string sbHostName = "servicebus.windows.net";
	```

1. В `Main()` вставьте следующий код.

	```
	Console.Write("Enter your service namespace: ");
	serviceNamespace = Console.ReadLine();
	
	Console.Write("Enter your SAS key: ");
	string SASKey = Console.ReadLine();
	
	baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
	try
	{
	    token = GetSASToken("RootManageSharedAccessKey", SASKey);
	
	    string queueName = "Queue" + Guid.NewGuid().ToString();
	
	    // Create and put a message in the queue
	    CreateQueue(queueName, token);
	    SendMessage(queueName, "msg1");
	    string msg = ReceiveAndDeleteMessage(queueName);
	
	    string topicName = "Topic" + Guid.NewGuid().ToString();
	    string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
	    CreateTopic(topicName);
	    CreateSubscription(topicName, subscriptionName);
	    SendMessage(topicName, "msg2");
	
	    Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));
	
	    // Get an Atom feed with all the queues in the namespace
	    Console.WriteLine(GetResources("$Resources/Queues"));
	
	    // Get an Atom feed with all the topics in the namespace
	    Console.WriteLine(GetResources("$Resources/Topics"));
	
	    // Get an Atom feed with all the subscriptions for the topic we just created
	    Console.WriteLine(GetResources(topicName + "/Subscriptions"));
	
	    // Get an Atom feed with all the rules for the topic and subscription we just created
	    Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));
	
	    // Delete the queue we created
	    DeleteResource(queueName);
	
	    // Delete the topic we created
	    DeleteResource(topicName);
	
	    // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
	    Console.WriteLine(GetResources("$Resources/Topics"));
	
	    // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
	    Console.WriteLine(GetResources("$Resources/Queues"));
	}
	catch (WebException we)
	{
	    using (HttpWebResponse response = we.Response as HttpWebResponse)
	    {
	        if (response != null)
	        {
	            Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
	        }
	        else
	        {
	            Console.WriteLine(we.ToString());
	        }
	    }
	}
	
	Console.WriteLine("\nPress ENTER to exit.");
	Console.ReadLine();
	```

## Создание учетных данных управления

Следующим шагом является написание метода, который обрабатывает пространство имен и ключ SAS, введенные на предыдущем шаге, и возвращает маркер SAS. В этом примере создается маркер SAS, который действителен в течение одного часа.

### Создание метода GetSASToken()

Вставьте следующий код в класс `Program` после метода `Main()`.

```
private static string GetSASToken(string SASKeyName, string SASKeyValue)
{
  TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
  string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
  string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
  HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

  string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
  string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
      WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
  return sasToken;
}
```
## Создание очереди

Следующим шагом является написание метода, который использует команду HTTP PUT в стиле REST для создания очереди.

Вставьте следующий код сразу после кода `GetSASToken()`, добавленного на предыдущем шаге.

```
// Uses HTTP PUT to create the queue
private static string CreateQueue(string queueName, string token)
{
    // Create the URI of the new queue, note that this uses the HTTPS scheme
    string queueAddress = baseAddress + queueName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating queue {0}", queueAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                          <title type=""text"">" + queueName + @"</title>
                          <content type=""application/xml"">
                            <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                          </content>
                        </entry>";

    byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## Отправка сообщения в очередь

На этом шаге добавляется метод, использующий команду HTTP POST стиле REST для отправки сообщения в очередь, созданную на предыдущем шаге.

1. Вставьте следующий код сразу после кода `CreateQueue()`, добавленного на предыдущем шаге.

	```
	// Sends a message to the "queueName" queue, given the name and the value to enqueue
	// Uses an HTTP POST request.
	private static void SendMessage(string queueName, string body)
	{
	    string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
	    Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
	    WebClient webClient = new WebClient();
	    webClient.Headers[HttpRequestHeader.Authorization] = token;
	
	    webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));
	}
	```

1. Свойства стандартных сообщений, доставляемых через посредник, помещаются в заголовок HTTP `BrokerProperties`. Свойства посредника должен быть сериализованы в формате JSON. Чтобы указать значение **TimeToLive** в 30 секунд и добавить метку сообщения M1 в сообщение, добавьте следующий код непосредственно перед вызовом `webClient.UploadData()`, приведенным в предыдущем примере.

	```
	// Add brokered message properties "TimeToLive" and "Label"
	webClient.Headers.Add("BrokerProperties", "{ "TimeToLive":30, "Label":"M1"}");
	```

	Обратите внимание, что свойства сообщения посредника были добавлены и будут добавляться. Таким образом, запрос отправки должен содержать версию API, которая поддерживает все свойства сообщения посредника, которые являются частью запроса. Если указанная версия API не поддерживает свойство сообщения посредника, это свойство игнорируется.

1. Пользовательские свойства сообщения определяются в виде набора пар "ключ-значение". Каждое пользовательское свойство хранится в собственном заголовке TPPT. Чтобы добавить пользовательские свойства Priority и Customer, добавьте следующий код непосредственно перед вызовом `webClient.UploadData()`, показанным в предыдущем примере.

	```
	// Add custom properties "Priority" and "Customer".
	webClient.Headers.Add("Priority", "High");
	webClient.Headers.Add("Customer", "12345");
	```

## Получение и удаление сообщения из очереди

Следующим шагом является добавление метода, который использует команду HTTP DELETE в стиле REST для получения и удаления сообщения из очереди.

Вставьте следующий код сразу после кода `SendMessage()`, добавленного на предыдущем шаге.

```
// Receives and deletes the next message from the given resource (queue, topic, or subscription)
// using the resourceName and an HTTP DELETE request
private static string ReceiveAndDeleteMessage(string resourceName)
{
    string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
    Console.WriteLine("\nRetrieving message from {0}", fullAddress);
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
    string responseStr = Encoding.UTF8.GetString(response);

    Console.WriteLine(responseStr);
    return responseStr;
}
```

## Создание раздела и подписки

Следующим шагом является написание метода, который использует команду HTTP PUT в стиле REST для создания раздела. Затем можно написать метод, который создает подписку на этот раздел.

### Создание раздела

Вставьте следующий код сразу после кода `ReceiveAndDeleteMessage()`, добавленного на предыдущем шаге.

```
// Using an HTTP PUT request.
private static string CreateTopic(string topicName)
{
    var topicAddress = baseAddress + topicName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating topic {0}", topicAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

### Создание подписки

Следующий код создает подписку на раздел, созданный на предыдущем шаге. Добавьте следующий код сразу после определения `CreateTopic()`:

```
private static string CreateSubscription(string topicName, string subscriptionName)
{
    var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## Получение ресурсов сообщения

На этом шаге вы добавите код, который получает свойства сообщения, а затем удаляет ресурсы обмена сообщениями, созданные на предыдущих этапах.

### Получение подписки Atom с указанными ресурсами

Вставьте следующий код сразу после метода `CreateSubscription()`, добавленного на предыдущем шаге.

```
private static string GetResources(string resourceAddress)
{
    string fullAddress = baseAddress + resourceAddress;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;
    Console.WriteLine("\nGetting resources from {0}", fullAddress);
    return FormatXml(webClient.DownloadString(fullAddress));
}
```

### Удаление сущностей обмена сообщениями

Вставьте следующий код сразу после метода кода, добавленного на предыдущем шаге.

```
private static string DeleteResource(string resourceName)
{
    string fullAddress = baseAddress + resourceName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nDeleting resource at {0}", fullAddress);
    byte[] response = webClient.UploadData(fullAddress, "DELETE", newbyte[0]);
    return Encoding.UTF8.GetString(response);
}
```

### Изменение формата подписки Atom

Метод `GetResources()` содержит вызов метода `FormatXml()`, который изменяет формат полученной подписки Atom на более удобочитаемый. Далее определяется `FormatXml()`. Вставьте этот код сразу после кода `DeleteResource()`, добавленного на предыдущем шаге.

```
// Formats the XML string to be more human-readable; intended for display purposes
private static string FormatXml(string inputXml)
{
    XmlDocument document = new XmlDocument();
    document.Load(new StringReader(inputXml));

    StringBuilder builder = new StringBuilder();
    using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
    {
        writer.Formatting = Formatting.Indented;
        document.Save(writer);
    }

    return builder.ToString();
}
```

## Создание и запуск приложения

Теперь можно выполнить сборку и запуск приложения. В Visual Studio в меню **Сборка** выберите пункт **Построить решение** или нажмите сочетание клавиш **CTRL+SHIFT+B**.

### Выполнение приложения

При отсутствии ошибок нажмите клавишу F5 для запуска приложения. При появлении запроса введите пространство имен, имя ключа SAS и значение ключа SAS, полученные на первом шаге.

### Пример

Ниже приведен полный код после выполнения всех шагов в этом руководстве.

```
using System;
using System.Globalization;
using System.IO;
using System.Net;
using System.Security.Cryptography;
using System.Text;
using System.Xml;

namespace Microsoft.ServiceBus.Samples
{
    class Program
    {
        static string serviceNamespace;
        static string baseAddress;
        static string token;
        const string sbHostName = "servicebus.windows.net";

        static void Main(string[] args)
        {
            Console.Write("Enter your service namespace: ");
            serviceNamespace = Console.ReadLine();

            Console.Write("Enter your SAS key: ");
            string SASKey = Console.ReadLine();

            baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
            try
            {
                token = GetSASToken("RootManageSharedAccessKey", SASKey);

                string queueName = "Queue" + Guid.NewGuid().ToString();

                // Create and put a message in the queue
                CreateQueue(queueName, token);
                SendMessage(queueName, "msg1");
                string msg = ReceiveAndDeleteMessage(queueName);

                string topicName = "Topic" + Guid.NewGuid().ToString();
                string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
                CreateTopic(topicName);
                CreateSubscription(topicName, subscriptionName);
                SendMessage(topicName, "msg2");

                Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));

                // Get an Atom feed with all the queues in the namespace
                Console.WriteLine(GetResources("$Resources/Queues"));

                // Get an Atom feed with all the topics in the namespace
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the subscriptions for the topic we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions"));

                // Get an Atom feed with all the rules for the topic and subscription we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));

                // Delete the queue we created
                DeleteResource(queueName);

                // Delete the topic we created
                DeleteResource(topicName);

                // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Queues"));
            }
            catch (WebException we)
            {
                using (HttpWebResponse response = we.Response as HttpWebResponse)
                {
                    if (response != null)
                    {
                        Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
                    }
                    else
                    {
                        Console.WriteLine(we.ToString());
                    }
                }
            }

            Console.WriteLine("\nPress ENTER to exit.");
            Console.ReadLine();
        }

        private static string GetSASToken(string SASKeyName, string SASKeyValue)
        {
            TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
            string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
            string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
            HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

            string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
            string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
                WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
            return sasToken;
        }

        // Uses HTTP PUT to create the queue
        private static string CreateQueue(string queueName, string token)
        {
            // Create the URI of the new queue, note that this uses the HTTPS scheme
            string queueAddress = baseAddress + queueName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating queue {0}", queueAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + queueName + @"</title>
                                  <content type=""application/xml"">
                                    <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        // Sends a message to the "queueName" queue, given the name and the value to enqueue
        // Uses an HTTP POST request.
        private static void SendMessage(string queueName, string body)
        {
            string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
            Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            // Add brokered message properties “TimeToLive” and “Label”.
            webClient.Headers.Add("BrokerProperties", "{ "TimeToLive":30, "Label":"M1"}");
            // Add custom properties “Priority” and “Customer”.
            webClient.Headers.Add("Priority", "High");
            webClient.Headers.Add("Customer", "12345");
            webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));

        }

        // Receives and deletes the next message from the given resource (queue, topic, or subscription)
        // using the resourceName and an HTTP DELETE request.
        private static string ReceiveAndDeleteMessage(string resourceName)
        {
            string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
            Console.WriteLine("\nRetrieving message from {0}", fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            string responseStr = Encoding.UTF8.GetString(response);

            Console.WriteLine(responseStr);
            return responseStr;
        }

        // Using an HTTP PUT request.
        private static string CreateTopic(string topicName)
        {
            var topicAddress = baseAddress + topicName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating topic {0}", topicAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string CreateSubscription(string topicName, string subscriptionName)
        {
            var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string GetResources(string resourceAddress)
        {
            string fullAddress = baseAddress + resourceAddress;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            Console.WriteLine("\nGetting resources from {0}", fullAddress);
            return FormatXml(webClient.DownloadString(fullAddress));
        }

        private static string DeleteResource(string resourceName)
        {
            string fullAddress = baseAddress + resourceName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nDeleting resource at {0}", fullAddress);
            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            return Encoding.UTF8.GetString(response);
        }

        // Formats the XML string to be more human-readable; intended for display purposes
        private static string FormatXml(string inputXml)
        {
            XmlDocument document = new XmlDocument();
            document.Load(new StringReader(inputXml));

            StringBuilder builder = new StringBuilder();
            using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
            {
                writer.Formatting = Formatting.Indented;
                document.Save(writer);
            }

            return builder.ToString();
        }
    }
}
```

## Дальнейшие действия

Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Основные сведения об обмене сообщениями через служебную шину](service-bus-messaging-overview.md)
- [Базовая информация о служебной шине Azure](../service-bus/service-bus-fundamentals-hybrid-solutions.md)
- [Руководство по использованию ретранслятора служебной шины на основе REST](../service-bus-relay/service-bus-relay-rest-tutorial.md)

<!---HONumber=AcomDC_0928_2016-->