<properties 
	pageTitle="Использование очередей служебной шины (Java) - Azure" 
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
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Как использовать очереди служебной шины

В этом руководстве показано, как использовать очереди служебной шины. Примеры написаны на Java, и в них используется [пакет Azure SDK для Java][Azure SDK для Java]. Здесь описаны такие сценарии, как **создание очередей**, **отправка и получение сообщений**, а также **удаление очередей**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Настройка приложения для использования служебной шины

Добавьте в начало Java-файла следующие инструкции import:

	// Include the following imports to use service bus APIs
	import com.microsoft.windowsazure.services.serviceBus.*;
	import com.microsoft.windowsazure.services.serviceBus.models.*; 
	import com.microsoft.windowsazure.services.core.*; 
	import javax.xml.datatype.*;
	
## Как создать очередь

Операции управления для очередей служебной шины можно выполнять с помощью класса **ServiceBusContract**. Объект **ServiceBusContract** создается с соответствующей конфигурацией, которая инкапсулирует разрешения маркера для управления им, а класс **ServiceBusContract** является единственной точкой связи с Azure.

**ServiceBusService** предоставляет методы для создания, перечисления и удаления очередей. В следующем примере показано как **ServiceBusService** объектов можно использовать для создания очереди с именем "TestQueue" и имен "howtosample":

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

Существуют методы QueueInfo, позволяющие свойства настройки очереди (например: значение по умолчанию "время жизни" для применения к сообщениям, отправленным в очередь). Следующий пример показывает, как создать очередь с именем TestQueue и размером не более 5 ГБ.

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

Обратите внимание, что можно использовать **listQueues** метод **ServiceBusContract** объектов для проверки, если очередь с указанным именем уже существует в пространстве имен службы.

## Как отправлять сообщения в очередь

Чтобы отправить сообщение в очередь служебной шины, приложение получает объект**ServiceBusContract**. Ниже кода показано, как отправить сообщение в очередь "TestQueue", созданное ранее в нашем пространстве имен HowToSample службы:

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

Сообщения, отправляемые в очереди служебной шины и получаемые из них - это экземпляры класса **BrokeredMessage**. **Объекты BrokeredMessage** содержат набор стандартных методов (таких как **getLabel**, **getTimeToLive**, **setLabel** и **setTimeToLive**), словарь, который используется для хранения настраиваемых свойств приложения, и текст, состоящий из произвольных данных приложения. Приложение может задать текст сообщения, передав любой сериализуемый объект конструктору **BrokeredMessage**, после чего для сериализации объекта будет использоваться соответствующий сериализатор. Кроме того **java.ОПЕРАЦИИ ВВОДА-ВЫВОДА.InputStream** могут быть предоставлены.

В следующем примере показано, как отправить 5 тестовых сообщений в очередь TestQueue **MessageSender**, полученную в предыдущем фрагменте кода.

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i); 
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, - 64 КБ). Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ.

## Как получать сообщения из очереди

Самым простым способом получать сообщения из очереди является использование объекта **ServiceBusContract**. Полученные сообщения могут работать в двух различных режимах: **ReceiveAndDelete** и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда служебная шина получает запрос на чтение для сообщения в очереди, сообщение помечается как использованное и возвращается в приложение. **ReceiveAndDelete** режиме (который является режимом по умолчанию) представляет собой простейшую модель и лучше всего подходит для сценариев, в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки.
Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), оно завершает второй этап процесса получения, вызывая метод **Delete** для полученного сообщения. Когда служебная шина обнаруживает **удаление** вызова, он помечает сообщение как использованное и удаляет его из очереди.

В следующем примере показывается, как можно получать и обрабатывать сообщения с помощью режима **PeekLock** (не используемого по умолчанию). В примере ниже создается бесконечный цикл и сообщения обрабатываются по мере поступления в нашу "TestQueue":

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

служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого служебная шина разблокирует сообщение в очереди и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Service Bus разблокирует сообщение автоматически и снова сделает его доступным для получения.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

## Дальнейшие действия

Вы познакомились с основами использования очередей служебной шины. Дополнительную информацию см. в статье [Очереди, разделы и подписки][] на портале MSDN.

  [Azure SDK для Java]: http://azure.microsoft.com/develop/java/
  [Что такое очереди служебной шины?]: #what-are-service-bus-queues
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Настройка приложения для использования служебной шины]: #bkmk_ConfigApp
  [Практическое руководство. Создание поставщика маркеров безопасности]: #bkmk_HowToCreateQueue
  [Практическое руководство. Отправка сообщений в очередь]: #bkmk_HowToSendMsgs
  [Практическое руководство. Получение сообщений из очереди]: #bkmk_HowToReceiveMsgs
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #bkmk_HowToHandleAppCrashes
  [Дальнейшие действия]: #bkmk_NextSteps
  [Портал управления Azure]: http://manage.windowsazure.com/
  [Очереди, разделы и подписки]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx

<!--HONumber=47-->
 