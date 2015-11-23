<properties
	pageTitle="Использование очередей служебной шины с Java | Microsoft Azure"
	description="Узнайте, как использовать очереди служебной шины в Azure. Примеры кода написаны на Java."
	services="service-bus"
	documentationCenter="java"
	authors="sethmanheim"
	manager="timlt"
	/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="10/07/2015"
	ms.author="sethm"/>

# Как использовать очереди служебной шины

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

В этой статье показано, как использовать очереди служебной шины. Примеры написаны на Java и используют [Пакет Azure SDK для Java][]. Здесь описаны такие сценарии, как **создание очередей**, **отправка и получение сообщений**, а также **удаление очередей**.

[AZURE.INCLUDE [service-bus-java-how-to-create-queue](../../includes/service-bus-java-how-to-create-queue.md)]

## Настройка приложения для использования служебной шины

Перед созданием этого образца убедитесь, что вы установили [пакет SDK Azure для Java][]. При использовании Eclipse можно установить [набор средств Azure для Eclipse][], включающий пакет SDK Azure для Java. Затем можно добавить **библиотеки Microsoft Azure для Java** в проект:

![](media/service-bus-java-how-to-use-queues/eclipselibs.png)

Добавьте в начало Java-файла следующие инструкции `import`:

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## Создание очереди

Операции управления для очередей служебной шины можно выполнять с помощью класса **ServiceBusContract**. Объект **ServiceBusContract** создается с соответствующей конфигурацией, которая инкапсулирует маркер SAS с разрешениями на управление им, а класс **ServiceBusContract** является единственной точкой связи с Azure.

Класс **ServiceBusService** предоставляет методы для создания, перечисления и удаления очередей. В следующем примере показано, каким образом можно использовать объект **ServiceBusService** для создания очереди с именем TestQueue и пространством имен HowToSample:

		Configuration config =
			ServiceBusConfiguration.configureWithSASAuthentication(
					"HowToSample",
					"RootManageSharedAccessKey",
					"SAS_key_value",
					".servicebus.windows.net"
					);

    ServiceBusContract service = ServiceBusService.create(config);
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    try
    {
		CreateQueueResult result = service.createQueue(queueInfo);
    }
	catch (ServiceException e)
	{
	    System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Существуют методы **QueueInfo**, позволяющие настраивать свойства очереди (например: значение «времени жизни» (Time to Live, TTL) по умолчанию для применения к сообщениям, отправленным в очередь). Следующий пример показывает, как создать очередь с именем `TestQueue` и размером не более 5 ГБ.

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateQueueResult result = service.createQueue(queueInfo);

Обратите внимание, что метод **listQueues** можно использовать для объектов **ServiceBusContract**, чтобы проверить, существует ли уже очередь с указанным именем в пространстве имен службы.

## Отправка сообщений в очередь

Чтобы отправить сообщение в очередь служебной шины, приложение получает объект **ServiceBusContract**. В следующем примере кода показано, как отправить сообщение в очередь `TestQueue`, созданную ранее в пространстве имен `HowToSample`.

    try
    {
        BrokeredMessage message = new BrokeredMessage("MyMessage");
        service.sendQueueMessage("TestQueue", message);
    }
    catch (ServiceException e)
    {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Сообщения, отправляемые в очереди служебной шины и получаемые из них, представляют собой экземпляры класса [BrokeredMessage][]. Объекты [BrokeredMessage][] обладают набором стандартных свойств, (таких как [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) и [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), словарем, хранящим разные пользовательские свойства, зависящие от приложения, и основным набором произвольных данных приложения. Приложение может задать текст сообщения, передав любой сериализуемый объект конструктору [BrokeredMessage][], после чего для сериализации объекта будет использоваться соответствующий сериализатор. Вы также можете указать объект **java.IO.InputStream**.

В следующем примере показано, как отправить пять тестовых сообщений в очередь `TestQueue` объекта **MessageSender**, полученного в предыдущем фрагменте кода:

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i);
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ). Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ.

## Получение сообщений из очереди

Самым простым способом получения сообщений из очереди является использование объекта **ServiceBusContract**. Полученные сообщения могут работать в двух различных режимах: **ReceiveAndDelete** и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда служебная шина получает запрос на чтение для сообщения в очереди, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** (который используется по умолчанию) представляет собой самую простую модель, которая лучше всего подходит для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), оно завершает второй этап процесса получения, вызывая метод **Delete** для полученного сообщения. Когда служебная шина обнаруживает вызов метода **Delete**, она помечает сообщение как использованное и удаляет его из очереди.

В следующем примере показано, как получать и обрабатывать сообщения с помощью режима **PeekLock** (не используется по умолчанию). В примере ниже создается бесконечный цикл и сообщения обрабатываются по мере поступления в нашу "TestQueue":

    	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

		while(true)  {
	         ReceiveQueueMessageResult resultQM =
	     			service.receiveQueueMessage("TestQueue", opts);
		    BrokeredMessage message = resultQM.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());
			    // Display the queue message.
			    System.out.print("From queue: ");
			    byte[] b = new byte[200];
			    String s = null;
			    int numRead = message.getBody().read(b);
			    while (-1 != numRead)
	            {
	                s = new String(b);
	                s = s.trim();
	                System.out.print(s);
	                numRead = message.getBody().read(b);
			    }
	            System.out.println();
			    System.out.println("Custom Property: " +
			        message.getProperty("MyProperty"));
			    // Remove message from queue.
			    System.out.println("Deleting this message.");
			    //service.deleteMessage(message);
		    }  
		    else  
		    {
		        System.out.println("Finishing up - no more messages.");
		        break;
		        // Added to handle no more messages.
		        // Could instead wait for more messages to be added.
		    }
	    }
	}
	catch (ServiceException e) {
	    System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	}
	catch (Exception e) {
	    System.out.print("Generic exception encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	}

## Как обрабатывать сбои приложения и нечитаемые сообщения

служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого Service Bus разблокирует сообщение в очереди и сделает его доступным для приема тем же или другим приложением-пользователем.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Service Bus разблокирует сообщение автоматически и сделает его доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

## Дальнейшие действия

Вы познакомились с основами использования очередей служебной шины. Дополнительные сведения см. в статье [Очереди, темы и подписки][].

Дополнительную информацию см. в [Центре разработчика Java](/develop/java/).


  [Пакет Azure SDK для Java]: http://azure.microsoft.com/develop/java/
  [пакет SDK Azure для Java]: http://azure.microsoft.com/develop/java/
  [набор средств Azure для Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Очереди, темы и подписки]: service-bus-queues-topics-subscriptions.md
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

<!---HONumber=Nov15_HO3-->