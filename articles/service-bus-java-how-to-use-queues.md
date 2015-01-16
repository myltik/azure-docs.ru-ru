<properties urlDisplayName="Service Bus Queues" pageTitle="Использование очередей Service Bus (Java) - Azure" metaKeywords="Очереди Azure Service Bus, очереди Azure, обмен сообщениями Azure, очереди Azure Java" description="Узнайте, как использовать очереди Service Bus в Azure. Примеры кода написаны на Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Queues" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Использование очередей Service Bus

В этом руководстве показано, как использовать очереди Service Bus. Примеры написаны на Java и используют [Пакет Azure SDK для Java][]. Здесь описаны такие сценарии, как **создание очередей**, **отправка и получение сообщений**, а также **удаление очередей**.

## Оглавление

-   [Что такое очереди Service Bus?][]
-   [Создание пространства имен службы][]
-   [Получение учетных данных управления по умолчанию для пространства имен][]
-   [Настройка приложения для использования шины обслуживания][]
-   [Практическое руководство. Создание поставщика токенов безопасности][]
-   [Практическое руководство. Создать очередь][How to: Create a Security Token Provider]
-   [Практическое руководство. Отправка сообщений в очередь][]
-   [Практическое руководство. Получение сообщений из очереди][]
-   [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений][]
-   [Дальнейшие действия][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="bkmk_ConfigApp"> </a>Настройка приложения для использования шины обслуживания

Добавьте в начало Java-файла следующие инструкции import:

	// Include the following imports to use service bus APIs
	import com.microsoft.windowsazure.services.serviceBus.*;
	import com.microsoft.windowsazure.services.serviceBus.models.*; 
	import com.microsoft.windowsazure.services.core.*; 
	import javax.xml.datatype.*;
	
## <a name="bkmk_HowToCreateQueue"> </a>Создание очереди

Операции управления для очередей служебной шины Service Bus можно выполнять с помощью класса **ServiceBusContract**. **Объект ServiceBusContract** создается в соответствующей конфигурации, которая инкапсулирует разрешения маркера для управления им, а класс **ServiceBusContract** является единственной точкой связи с Azure.

Класс **ServiceBusService** предоставляет методы создания, перечисления и удаления очередей. В следующем примере показано, каким образом можно использовать объект **ServiceBusService** для создания очереди с именем "TestQueue" и пространством имен "HowToSample":

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

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

В QueueInfo имеются методы, позволяющие настраивать свойства очередей (например, задавать значение "времени жизни" по умолчанию, применяемое к отправленным в очередь сообщениям). Следующий пример показывает, как создать очередь с именем TestQueue и размером не более 5 ГБ.

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

Обратите внимание, метод **listQueues** можно использовать для объектов **ServiceBusContract**, чтобы проверить, существует ли уже очередь с указанным именем в пространстве имен служб.

## <a name="bkmk_HowToSendMsgs"> </a>Отправка сообщений в очередь

Чтобы отправить сообщение в очередь Service Bus, приложение получает объект **ServiceBusContract**. В приведенном ниже образце кода показано, как отправить сообщение, созданное ранее в пространстве имен "HowToSample" службы, в очередь "TestQueue":

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

Сообщения, отправляемые в очереди Service Bus и получаемые из них - это экземпляры класса **BrokeredMessage**. Объекты **BrokeredMessage** обладают набором стандартных методов (таких как **getLabel**, **getTimeToLive**, **setLabel** и **setTimeToLive**), словарем, который используется для хранения заданных свойств пользовательского приложения, и телом произвольных данных приложения. Приложение может задать текст сообщения, передав любой сериализуемый объект конструктору **BrokeredMessage**, после чего для сериализации объекта используется соответствующий сериализатор. Кроме того, может быть предоставлен **java.IO.InputStream**.

В следующем примере показано, как отправить пять тестовых сообщений в
"TestQueue" **MessageSender**, который мы получили во фрагменте кода выше:

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i); 
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

очереди служебной шины поддерживают максимальный размер сообщения 256 КБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, - 64 КБ). Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ.

## <a name="bkmk_HowToReceiveMsgs"> </a>Получение сообщений из очереди

Самым простым способом получать сообщения из очереди является использование объекта **ServiceBusContract**. Полученные сообщения могут работать в двух различных режимах: **ReceiveAndDelete** и **PeekLock**.  В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда служебная шина получает запрос на чтение для сообщения в очереди, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** (режим по умолчанию) представляет собой самую простую модель, которая лучше всего работает для сценариев, в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку шина обслуживания помечает сообщение как используемое, то после того как приложение перезапускается и снова начинает обрабатывать сообщения, оно пропустит сообщение, которое использовалось до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), оно завершает второй этап процесса получения, вызывая метод **Delete** для полученного сообщения. Когда служебная шина Service Bus обнаруживает вызов **Delete**, она помечает сообщение как используемое и сообщение удаляется из очереди.

В следующем примере показывается, как можно получать и обрабатывать сообщения в режиме **PeekLock**, (не является режимом по умолчанию). В примере ниже создается бесконечный цикл и сообщения обрабатываются по мере поступления в нашу "TestQueue":

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

## <a name="bkmk_HowToHandleAppCrashes"> </a>Обработка сбоев приложения и нечитаемых сообщений

Служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого служебная шина разблокирует сообщение в очереди и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Service Bus разблокирует сообщение автоматически и сделает его доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage**, это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

## <a name="bkmk_NextSteps"> </a>Дальнейшие действия

Вы ознакомились с основами использования очередей служебной шины. Теперь вы можете изучить раздел MSDN
раздел [Очереди, разделы и подписки][] для получения дополнительной информации.

  [Пакет Azure SDK для Java]: http://azure.microsoft.com/ru-ru/develop/java/
  [Что такое очереди Service Bus?]: #what-are-service-bus-queues
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Настройка приложения для использования шины обслуживания]: #bkmk_ConfigApp
  [Практическое руководство. Создание поставщика токенов безопасности]: #bkmk_HowToCreateQueue
  [Практическое руководство. Отправка сообщений в очередь]: #bkmk_HowToSendMsgs
  [Практическое руководство. Получение сообщений из очереди]: #bkmk_HowToReceiveMsgs
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #bkmk_HowToHandleAppCrashes
  [Дальнейшие действия]: #bkmk_NextSteps
  [Портал управления Azure]: http://manage.windowsazure.com/
  [очереди, разделы и подписки]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh367516.aspx
