<properties 
	pageTitle="Как использовать Azure Service Bus с пакетом SDK для WebJob" 
	description="Информация об использовании очередей и разделов Azure Service Bus с пакетом SDK для WebJob" 
	services="app-service\web, service-bus" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="tdykstra"/>

# Как использовать Azure Service Bus с пакетом SDK для WebJob

## Обзор

В этом руководстве приведены примеры кода на C# для запуска процессов при создании или обновлении большого двоичного объекта Azure. В примерах кода используется [пакет SDK для веб-заданий](websites-dotnet-webjobs-sdk.md) версии 1.x.

В этом руководстве предполагается, что вы уже знаете, [как создавать проект веб-заданий в Visual Studio со строками подключения, указывающими на учетную запись хранения](websites-dotnet-webjobs-sdk-get-started.md).

В фрагментах кода показаны только функции, а не код, создающий объект `JobHost`, как в следующем примере:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## <a id="prerequisites"></a> Предварительные требования

Для работы со служебной шиной необходимо установить пакет NuGet [ Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/), а также другие пакеты SDK для веб-заданий.

Необходимо также задать строку подключения AzureWebJobsServiceBus и строки подключения для хранилища. Это можно сделать в разделе `connectionStrings` файла Web.config, как показано в примере ниже:

		<connectionStrings>
		    <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
		    <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
		    <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
		</connectionStrings>

Пример проекта см. в [примере служебной шины](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus). Дополнительную информацию см. в статье [Приступая к работе с пакетом SDK для веб-заданий](websites-dotnet-webjobs-sdk-get-started.md).

## <a id="trigger"></a> Как вызывать функцию при получении сообщения очереди служебной шины

Чтобы написать функцию, которую пакет SDK для веб-заданий будет вызывать при получении сообщения очереди, используйте атрибут `ServiceBusTrigger`. Конструктор атрибута принимает параметр, который указывает имя очереди для опроса.

### Принцип действия ServicebusTrigger

Пакет SDK получает сообщение в режиме `PeekLock` и вызывает метод `Complete` для сообщения, если функция выполнена успешно, или `Abandon` в случае сбоя. Если функция выполняется дольше времени ожидания `PeekLock`, блокировка возобновляется автоматически.

Service Bus выполняет собственную обработку очереди подозрительных сообщений, так что пакет SDK для заданий WebJob не может управлять ею, и ее нельзя настроить с его помощью.

### Строковое сообщение очереди

Следующий пример кода считывает сообщение очереди, содержащее строку, и записывает строку на панель мониторинга пакета SDK для заданий WebJob.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Примечание.** При создании сообщений очереди в приложении, которое не использует пакет SDK для веб-заданий, обязательно задайте для параметра [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) значение text/plain.

### Сообщения очереди POCO

Пакет SDK автоматически десериализует сообщение очереди, содержащее тип JSON для объекта типа [POCO](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object). Следующий пример кода считывает сообщение очереди, которое содержит объект `BlobInformation` со свойством `BlobName`:

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Примеры кода, в которых показано, как использовать свойства POCO для работы с большими двоичными объектами и таблицами в одной функции, см. в [версии этой статьи для очередей хранилища](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

### Типы, с которыми работает атрибут ServiceBusTrigger

Помимо `string` и типов POCO атрибут `ServiceBusTrigger` можно использовать с массивом байтов или объектом `BrokeredMessage`.

## <a id="create"></a> Как создавать сообщения очереди служебной шины

Чтобы написать функцию, которая создает новое сообщение очереди, используйте атрибут `ServiceBus` и передайте имя очереди конструктору атрибута.


### Создание одного сообщения очереди в неасинхронной функции

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

### Создание нескольких сообщений очереди или сообщений в асинхронных функциях

Чтобы создать несколько сообщений, используйте атрибут `ServiceBus` с `ICollector<T>` или `IAsyncCollector<T>`, как показано в следующем примере кода:

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Каждое сообщение очереди создается сразу после вызова метода `Add`.

## <a id="topics"></a>Как работать с разделами служебной шины

Чтобы написать функцию, которую пакет SDK будет вызывать при получении сообщения в раздел служебной шины, используйте атрибут `ServiceBusTrigger` с конструктором, принимающим имя раздела и подписки, как показано в следующем примере кода:

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Чтобы создать сообщение для раздела, используйте атрибут `ServiceBus` с именем раздела также, как при использовании с именем очереди.

## <a id="queues"></a>Связанные разделы, которые описаны в практическом руководстве по работе с очередями хранилища

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

## <a id="nextsteps"></a>Дальнейшие действия

В этом руководстве предоставлены примеры кода обработки обычных сценариев для работы со служебной шиной Azure. Дополнительную информацию об использовании веб-заданий Azure и пакета SDK для веб-заданий см. в статье [Рекомендуемые ресурсы по веб-заданиям Azure](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=July15_HO3-->