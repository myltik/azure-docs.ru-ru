---
title: "Как использовать Azure Service Bus с пакетом SDK для WebJob"
description: "Информация об использовании очередей и разделов Azure Service Bus с пакетом SDK для WebJob"
services: app-service\web, service-bus
documentationcenter: .net
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: 2114a934-135b-42b8-871c-6cc040214e76
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/01/2016
ms.author: tdykstra
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4d5205b1c693b8c1f199235784263f3f7b6189ff
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-use-azure-service-bus-with-the-webjobs-sdk"></a>Как использовать Azure Service Bus с пакетом SDK для WebJob
## <a name="overview"></a>Обзор
В этом руководстве приведены примеры кода C#, демонстрирующие активацию процесса при получении сообщения служебной шины Azure. В примерах кода используется [пакет SDK для веб-заданий](websites-dotnet-webjobs-sdk.md) версии 1.x.

В этом руководстве предполагается, что вы уже знаете, [как создавать проект веб-заданий в Visual Studio со строками подключения, указывающими на учетную запись хранения](websites-dotnet-webjobs-sdk-get-started.md).

В фрагментах кода показаны только функции, а не код, создающий объект `JobHost` , как в следующем примере:

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

[Полный пример кода служебной шины](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs) можно найти в репозитории azure-webjobs-sdk-samples на сайте GitHub.com.

## <a name="a-idprerequisitesa-prerequisites"></a><a id="prerequisites"></a> Предварительные требования
Для работы со служебной шиной необходимо установить пакет NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) , а также другие пакеты SDK для веб-заданий. 

Необходимо также задать строку подключения AzureWebJobsServiceBus и строки подключения для хранилища.  Это можно сделать в разделе `connectionStrings` файла App.config, как показано в примере ниже.

        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
        </connectionStrings>

Пример проекта, который содержит строку подключения служебной шины в файле App.config, доступен в разделе [Пример служебной шины](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus). 

Строки подключения также можно задать в среде выполнения Azure. Эти строки переопределят параметры App.config при выполнении веб-задания в Azure. Дополнительные сведения см. в разделе [Начало работы с пакетом SDK для Azure для веб-заданий](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account).

## <a name="a-idtriggera-how-to-trigger-a-function-when-a-service-bus-queue-message-is-received"></a><a id="trigger"></a> Как вызывать функцию при получении сообщения очереди служебной шины
Чтобы написать функцию, которую пакет SDK для веб-заданий будет вызывать при получении сообщения очереди, используйте атрибут `ServiceBusTrigger` . Конструктор атрибута принимает параметр, который указывает имя очереди для опроса.

### <a name="how-servicebustrigger-works"></a>Как действует ServicebusTrigger
Пакет SDK получает сообщение в режиме `PeekLock` и вызывает метод `Complete` для сообщения, если функция выполнена успешно, или `Abandon` в случае сбоя. Если функция выполняется дольше времени ожидания `PeekLock` , блокировка возобновляется автоматически.

В служебной шине выполняется собственная обработка очереди подозрительных сообщений, которую нельзя контролировать или настраивать с помощью пакета SDK веб-заданий. 

### <a name="string-queue-message"></a>Строковое сообщение очереди
Следующий пример кода считывает сообщение очереди, содержащее строку, и записывает строку на панель мониторинга пакета SDK для заданий WebJob.

        public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
            TextWriter logger)
        {
            logger.WriteLine(message);
        }

**Примечание.** При создании сообщений очереди в приложении, которое не использует пакет SDK для веб-заданий, обязательно задайте для параметра [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) значение text/plain.

### <a name="poco-queue-message"></a>Сообщения очереди POCO
Пакет SDK автоматически десериализует сообщение очереди, содержащее тип JSON для объекта типа [POCO](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object). Следующий пример кода считывает сообщение очереди, которое содержит объект `BlobInformation` со свойством `BlobName`:

        public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
            TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

Примеры кода, в которых показано, как использовать POCO для работы с большими двоичными объектами и таблицами в одной функции, см. в [версии этой статьи для очередей хранилища](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

Если ваш код, создающий сообщения очереди, основан не на пакете SDK для веб-заданий, используйте код, аналогичный приведенному ниже:

        var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
        BlobInformation blobInformation = new BlobInformation () ;
        var message = new BrokeredMessage(blobInformation);
        client.Send(message);

### <a name="types-servicebustrigger-works-with"></a>Типы, с которыми работает атрибут ServiceBusTrigger
Помимо `string` и типов POCO атрибут `ServiceBusTrigger` можно использовать с массивом байтов или объектом `BrokeredMessage`.

## <a name="a-idcreatea-how-to-create-service-bus-queue-messages"></a><a id="create"></a> Как создавать сообщения очереди служебной шины
Чтобы написать функцию, которая создает новое сообщение очереди, используйте атрибут `ServiceBus` и передайте имя очереди конструктору атрибута. 

### <a name="create-a-single-queue-message-in-a-non-async-function"></a>Создание одного сообщения очереди в неасинхронной функции
В следующем примере кода используется выходной параметр для создания нового сообщения в очереди с именем outputqueue с тем же содержимым, что и сообщение очереди, поступившее в очередь с именем inputqueue.

        public static void CreateQueueMessage(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] out string outputQueueMessage)
        {
            outputQueueMessage = queueMessage;
        }

Выходной параметр для создания одного сообщения очереди может принадлежать к любому из следующих типов:

* `string`
* `byte[]`
* `BrokeredMessage`
* Сериализуемый тип POCO, заданный вами Сериализируется как JSON автоматически.

Для параметров типа POCO сообщение очереди всегда создается при завершении функции. Если значение параметра — NULL, пакет SDK создает сообщение очереди, которое вернет значение NULL при получении и десериализации сообщения. Если значение параметра другого типа — NULL, сообщение очереди не создается.

### <a name="create-multiple-queue-messages-or-in-async-functions"></a>Создание нескольких сообщений очереди или сообщений в асинхронных функциях
Чтобы создать несколько сообщений, используйте атрибут `ServiceBus` с `ICollector<T>` или `IAsyncCollector<T>`, как показано в следующем примере кода.

        public static void CreateQueueMessages(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Каждое сообщение очереди создается сразу после вызова метода `Add` .

## <a name="a-idtopicsahow-to-work-with-service-bus-topics"></a><a id="topics"></a>Как работать с разделами служебной шины
Чтобы написать функцию, которую пакет SDK будет вызывать при получении сообщения в раздел служебной шины, используйте атрибут `ServiceBusTrigger` с конструктором, принимающим имя раздела и подписки, как показано в следующем примере кода:

        public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
            TextWriter logger)
        {
            logger.WriteLine("Topic message: " + message);
        }

Чтобы создать сообщение для раздела, используйте атрибут `ServiceBus` с именем раздела также, как при использовании с именем очереди.

## <a name="features-added-in-release-11"></a>Функции, добавленные в версии 1.1
В версии 1.1 были добавлены следующие возможности:

* Добавлена возможность обширной настройки обработки сообщений с помощью `ServiceBusConfiguration.MessagingProvider`.
* `MessagingProvider` поддерживает настройку `MessagingFactory` и `NamespaceManager` служебной шины.
* Шаблон стратегии `MessageProcessor` позволяет указать процессор для каждой очереди или раздела.
* По умолчанию поддерживается параллелизм обработки сообщений. 
* Упрощена настройка `OnMessageOptions` через `ServiceBusConfiguration.MessageOptions`.
* Для `ServiceBusTriggerAttribute`/`ServiceBusAttribute` необходимо предоставить разрешение [AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71) (для сценариев, в которых возможно управление правами). 

## <a name="a-idqueuesarelated-topics-covered-by-the-storage-queues-how-to-article"></a><a id="queues"></a>Связанные разделы, которые описаны в практическом руководстве по работе с очередями хранилища
Информацию о сценариях SDK для веб-заданий, которые не относятся к служебной шине, см. в статье [Использование пакета SDK веб-заданий для работы с хранилищем очередей Azure](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

В этой статье рассматриваются следующие вопросы:

* Асинхронные функции
* Выполнение на нескольких экземплярах
* Корректное завершение работы
* Использование атрибутов пакета SDK для заданий WebJob очереди в теле функции
* Установка строк подключения пакета SDK в коде.
* Установка значений параметров конструктора пакета SDK для заданий WebJob в коде
* Вызов функции вручную
* Запись журналов

## <a name="a-idnextstepsa-next-steps"></a><a id="nextsteps"></a>Дальнейшие действия
В этом руководстве предоставлены примеры кода обработки обычных сценариев для работы со служебной шиной Azure. Дополнительную информацию об использовании веб-заданий Azure и пакета SDK для веб-заданий см. в [рекомендуемых ресурсах для веб-заданий Azure](http://go.microsoft.com/fwlink/?linkid=390226).


