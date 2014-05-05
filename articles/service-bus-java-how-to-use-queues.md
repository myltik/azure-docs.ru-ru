<properties linkid="dev-java-how-to-service-bus-queues" urlDisplayName="Очереди Service Bus" pageTitle="Использование очередей Service Bus (Java) — Azure" metaKeywords="Очереди Service Bus Azure, очереди Azure, обмен сообщениями Azure, очереди Azure Java" description="Сведения об использовании очередей Service Bus в Azure. Примеры кода написаны на Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="Использование очередей Service Bus" authors="waltpo" solutions="" manager="" editor="mollybos" />








# Использование очередей Service Bus

В этом руководстве показано, как использовать очереди Service Bus. Примеры написаны на Java и используют [Пакет Azure SDK для Java][]. Здесь описаны такие сценарии, как **создание очередей**, **отправка и получение сообщений**, а также **удаление очередей**.

## Оглавление

-   [Что такое очереди шины обслуживания?][]
-   [Создание пространства имен службы][]
-   [Получение учетных данных управления по умолчанию для пространства имен][]
-   [Настройка приложения для использования шины обслуживания][]
-   [Создание поставщика маркеров безопасности][]
-   [Практическое руководство. Создание очереди][How to: Create a Security Token Provider]
-   [Практическое руководство. Отправка сообщений в очередь][]
-   [Практическое руководство. Получение сообщений из очереди][]
-   [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений][]
-   [Следующие шаги][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="bkmk_ConfigApp"> </a>Настройка приложения для использования Service Bus

Добавьте в начало Java-файла следующие инструкции import:

	// Include the following imports to use service bus APIs
	import com.microsoft.windowsazure.services.serviceBus.*;
	import com.microsoft.windowsazure.services.serviceBus.models.*; 
	import com.microsoft.windowsazure.services.core.*; 
	import javax.xml.datatype.*;
	
## <a name="bkmk_HowToCreateQueue"> </a>Создание очереди

Операции управления для очередей Service Bus можно выполнять с помощью
класса **ServiceBusContract**. Объект **ServiceBusContract** создается с соответствующей конфигурацией, которая инкапсулирует разрешения маркера для управления им, а класс **ServiceBusContract** является единственной точкой связи с Azure.

Класс **ServiceBusService** предоставляет методы для создания, перечисления и удаления очередей. В следующем примере показано, как объект **ServiceBusService** можно использовать для создания очереди с именем "TestQueue" и пространством имен "HowToSample":

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

Существуют методы QueueInfo, позволяющие настраивать свойства очереди, например задавать значение по умолчанию для "времени жизни", применяемое к сообщениям, отправленным в очередь. Следующий пример показывает, как создать очередь с именем "TestQueue" с максимальным размером 5 ГБ:

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

Обратите внимание, метод **listQueues** можно использовать для объектов **ServiceBusContract**, чтобы проверить, существует ли уже очередь с указанным именем в пространстве имен служб.

## <a name="bkmk_HowToSendMsgs"> </a>Отправка сообщений в очередь

Чтобы отправить сообщение в очередь Service Bus, приложение получает
объект **ServiceBusContract**. В примере кода ниже показано, как отправить сообщение в очередь "TestQueue", созданное ранее в пространстве имен "HowToSample" службы:

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

Сообщения, отправляемые в очереди Service Bus и получаемые из них — это экземпляры класса **BrokeredMessage**. У объектов **BrokeredMessage** есть набор стандартных методов (например, **getLabel**, **getTimeToLive**,
**setLabel** и **setTimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из произвольных данных приложения. Приложение может определить текст сообщения, передав любой сериализуемый объект в конструктор
**BrokeredMessage**, а затем соответствующий сериализатор будет использоваться для сериализации объекта. Или же можно предоставить объект **java.IO.InputStream**.

В следующем примере показано, как отправить 5 тестовых сообщений в "TestQueue" **MessageSender**, полученный в предыдущем фрагменте кода.

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i); 
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

Очереди Service Bus поддерживают максимальный размер сообщения 256 КБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, составляет 64 КБ). Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ.

## <a name="bkmk_HowToReceiveMsgs"> </a>Получение сообщений из очереди

Основным способом получать сообщения из очереди является использование
объекта **ServiceBusContract**. Полученные сообщения могут работать в двух различных режимах: **ReceiveAndDelete** и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда Service Bus получает запрос на чтения для сообщения в очереди, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** (режим по умолчанию) представляет собой самую простую модель, которая лучше всего работает для сценариев, в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы разобраться, рассмотрим сценарий, в котором приложение-получатель выдает запрос на получение, после чего происходит сбой этого приложения еще до обработки сообщения.
Поскольку Service Bus помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), шина Service Bus завершает второй этап процесса получения, вызывая метод **Delete** для полученного сообщения. Когда Service Bus обнаруживает вызов **Delete**, сообщение помечается как используемое и удаляется из очереди.

В следующем примере показывается, как можно получать и обрабатывать сообщения с помощью режима **PeekLock** (не режим по умолчанию). В примере ниже создается бесконечный цикл и сообщения обрабатываются по мере поступления в "TestQueue":

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

Service Bus предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого Service Bus разблокирует сообщение в очереди и сделает его доступным для приема тем же или другим приложением-пользователем.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Service Bus разблокирует сообщение автоматически и сделает его доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны предусмотреть дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

## <a name="bkmk_NextSteps"> </a>Дальнейшие действия

Вы познакомились с основами использования очередей Service Bus. Дополнительные сведения см. в статье [Очереди, разделы и подписки][] на портале MSDN.

  [Пакет Azure SDK для Java]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh690953(v=vs.103).aspx
  [Что такое очереди шины обслуживания?]: #what-are-service-bus-queues
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Настройка приложения для использования шины обслуживания]: #bkmk_ConfigApp
  [Создание поставщика маркеров безопасности]: #bkmk_HowToCreateQueue
  [Практическое руководство. Отправка сообщений в очередь]: #bkmk_HowToSendMsgs
  [Практическое руководство. Получение сообщений из очереди]: #bkmk_HowToReceiveMsgs
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #bkmk_HowToHandleAppCrashes
  [Следующие шаги]: #bkmk_NextSteps
  [Схема очереди Service Bus]: ../../../DevCenter/Java/Media/SvcBusQueues_01_FlowDiagram.jpg
  [Портал управления Azure]: http://manage.windowsazure.com/
  [Снимок экрана узла Service Bus]: ../../../DevCenter/Java/Media/SvcBusQueues_02_SvcBusNode.jpg
  [Снимок экрана с новым пространством имен]: ../../../DevCenter/Java/Media/SvcBusQueues_03_CreateNewSvcNamespace.jpg
  [Снимок экрана доступных пространств имен]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
  [Снимок экрана списка пространств имен]: ../../../DevCenter/Java/Media/SvcBusQueues_05_NamespaceList.jpg
  [Снимок экрана панели свойств]: ../../../DevCenter/Java/Media/SvcBusQueues_06_PropertiesPane.jpg
  [Снимок экрана ключа по умолчанию]: ../../../DevCenter/Java/Media/SvcBusQueues_07_DefaultKey.jpg
  [Очереди, разделы и подписки]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh367516.aspx

