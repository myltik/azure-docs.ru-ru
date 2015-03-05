<properties 
	pageTitle="Как использовать Azure Service Bus с пакетом SDK для WebJob" 
	description="Информация об использовании очередей и разделов Azure Service Bus с пакетом SDK для WebJob" 
	services="web-sites, service-bus" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Как использовать Azure Service Bus с пакетом SDK для WebJob

## Обзор

В этом руководстве приведены примеры кода на C# для запуска процессов при создании или обновлении большого двоичного объекта Azure. В примерах кода используется [Пакет SDK для заданий WebJobs](../websites-dotnet-webjobs-sdk/) версии 1.x.

В этом руководстве предполагается, что вы уже знаете, [как создавать проект WebJob в Visual Studio со строками подключения, указывающими на учетную запись хранения](../websites-dotnet-webjobs-sdk-get-started/).

В фрагментах кода показаны только функции, а не код, создающий объект `JobHost`, как в следующем примере:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## <a id="prerequisites"></a> Предварительные требования

Для работы с шиной обслуживания Service Bus необходимо установить пакет [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) NuGet в дополнение к другим пакетам SDK для заданий WebJobs. 

Необходимо также задать строку подключения AzureWebJobsServiceBus и строки подключения для хранилища.

## <a id="trigger"></a> Вызов функции при получении сообщения очереди Service Bus

Чтобы написать функцию, которую пакет SDK для заданий WebJob будет вызывать при получении сообщения очереди, используйте атрибут `ServiceBusTrigger`. Конструктор атрибута принимает параметр, который указывает имя очереди для опроса.

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

**Примечание:** при создании сообщений очереди в приложении, которое не использует пакет SDK WebJobs, не забудьте задать параметру [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) значение "text/plain".

### Сообщения очереди POCO

Пакет SDK автоматически десериализует сообщение очереди, содержащее тип JSON для типа POCO [(](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)). Следующий пример кода считывает сообщение очереди, которое содержит объект `BlobInformation` со свойством `BlobName`:

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Примеры кода, в которых показано, как использовать свойства POCO для работы с большими двоичными объектами и таблицами в одной функции, см. в [версии этой статьи для очередей хранилища](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#pocoblobs).

### Типы, с которыми работает атрибут ServiceBusTrigger

Помимо типов `string` и POCO  можно использовать атрибут `ServiceBusTrigger` с массивом байтов или объект `BrokeredMessage`.

## <a id="create"></a> Создание сообщений очереди Service Bus

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

Для параметров типа POCO сообщение очереди всегда создается при завершении функции. Если значение параметра - NULL, пакет SDK создает сообщение очереди, которое вернет значение NULL при получении и десериализации сообщения. Если значение параметра другого типа - NULL, сообщение очереди не создается.

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

## <a id="topics"></a>Работа с разделами Service Bus

Чтобы написать функцию, которую пакет SDK будет вызывать при получении сообщения в раздел Service Bus, используйте атрибут `ServiceBusTrigger` с конструктором, принимающим имя раздела и подписки, как показано в следующем примере кода:

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Чтобы создать сообщение для раздела, используйте атрибут `ServiceBus` с именем раздела также, как при использовании с именем очереди.

## <a id="queues"></a>Связанные разделы, которые описаны в практическом руководстве по работе с очередями хранилища

Сведения о сценариях SDK WebJobs, которые не относятся к Service Bus, см. в разделе [Использование хранилища очередей Azure с помощью пакета SDK WebJobs](../websites-dotnet-webjobs-sdk-storage-queues-how-to/). 

В этой статье рассматриваются следующие вопросы:

* Асинхронные функции
* Выполнение на нескольких экземплярах
* Корректное завершение работы
* Использование атрибутов пакета SDK для заданий WebJob очереди в теле функции
* Установка строк подключения пакета SDK в коде.
* Установка значений параметров конструктора пакета SDK для заданий WebJob в коде
* Вызов функции вручную
* Запись журналов

## <a id="nextsteps"></a> Дальнейшие действия

В этом руководстве приведены примеры кода для распространенных сценариев использования Azure Service Bus. Дополнительную информацию об использовании заданий Azure WebJob и пакета SDK для заданий WebJob см. в разделе [Рекомендуемые ресурсы Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

<!--HONumber=45--> 
