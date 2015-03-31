<properties 
	pageTitle="Как использовать разделы служебной шины (Java) - Azure" 
	description="Узнайте, как использовать разделы и подписки служебной шины в Azure. Примеры кода написаны для приложений Java." 
	services="service-bus" 
	documentationCenter="java" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Использование разделов и подписок служебной шины

В этом руководстве описывается использование разделов служебной шины и подписки. Примеры написаны на Java, и в них используется [пакет Azure SDK для Java][Azure SDK для Java]. В этой статье описаны такие сценарии, как **создание разделов и подписок**, **создание фильтров подписок**, **отправка сообщений** в раздел, **получение сообщений из подписки** и **удаление разделов и подписок**.

[AZURE.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## Настройка приложения для использования служебной шины

Добавьте в начало Java-файла следующие инструкции import:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Добавьте библиотеки Azure для Java в путь построения и включите его в сборку развертывания проекта.

## Как создать раздел

Можно выполнять операции управления для разделов служебной шины с помощью **ServiceBusContract** класса. Объект **ServiceBusContract** создается с соответствующей конфигурацией, которая инкапсулирует разрешения маркера для управления им, а класс **ServiceBusContract** является единственной точкой связи с Azure.

**ServiceBusService** предоставляет методы для создания, перечисления и удаления разделов. В следующем примере показано как **ServiceBusService** объектов можно использовать для создания раздела с именем "TestTopic" и имен "howtosample":

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

	ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
	try  
	{
    	CreateTopicResult result = service.createTopic(topicInfo);
	}
	catch (ServiceException e) {
		System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
		System.exit(-1);
	}

Существуют методы **TopicInfo** , позволяющие свойства раздела для настройки (например: значение по умолчанию "время жизни" для применения к сообщениям, отправленным в раздел). В следующем примере показано, как создать раздел с именем "TestTopic" с максимальным размером 5 ГБ:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Обратите внимание, что можно использовать метод **listTopics** для объектов **ServiceBusContract** объектов, чтобы проверить, существует ли уже раздел с указанным именем в пространстве имен службы.

## Как создавать подписки

Подписки на разделы также создаются с помощью **ServiceBusService** класса. Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка с именем "AllMessages" и использует значение по умолчанию **MatchAll** фильтра.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Создание подписок с фильтрами

Вы также можете настроить фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в определенной подписке раздела.

Самый гибкий тип фильтра, который поддерживается подписками, - это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительную информацию о выражениях, которые можно использовать с фильтром SQL, см. в описании синтаксиса SqlFilter.SqlExpression.

В следующем примере создается подписка с именем HighMessages, содержащая фильтр **SqlFilter**, который выбирает только сообщения, значение настраиваемого свойства **MessageNumber** которых превышает 3:

    // Create a "HighMessages" filtered subscription  
	SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
    CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
	RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "HighMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "HighMessages", "$Default");

Аналогично, следующий пример создает подписку с именем "LowMessages" и фильтром SqlFilter, который выбирает только сообщения, у которых значение свойства MessageNumber меньше или равно 3:

    // Create a "LowMessages" filtered subscription
	SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
	CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "LowMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "LowMessages", "$Default");


Когда сообщение будет отправлено в раздел "testtopic", всегда будет доставляться в приемники, подписанные на подписку раздела "AllMessages" и выборочно доставляться получателям подписаться на "HighMessages" и "LowMessages" подписки раздела (в зависимости от содержимого сообщений).

## Как отправлять сообщения в раздел

Чтобы отправить сообщение в раздел служебной шины, приложение получает объект **ServiceBusContract**. Ниже кода показано, как отправить сообщение в разделе "TestTopic", созданное ранее в нашем пространстве имен HowToSample службы:

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Сообщения, отправленные в разделы служебной шины, являются экземплярами **BrokeredMessage** класса. **BrokeredMessage** объекты имеют набор стандартных методов (например, **setLabel** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения и текст из произвольных данных приложения. Приложение может задать текст сообщения, передав конструктору **BrokeredMessage** любой сериализуемый объект, после чего для сериализации объекта будет использоваться соответствующий **DataContractSerializer**. Либо
**java.io.InputStream** могут быть предоставлены.

В следующем примере показано, как отправить 5 тестовых сообщений в раздел TestTopic **MessageSender**, полученный в предыдущем фрагменте кода.
Обратите внимание как **MessageNumber** значение свойства каждого сообщения зависит от итерации цикла (определяет, какие подписки получают его):

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Разделы служебной шины поддерживают максимальный размер сообщения в 256 МБ (максимальный размер заголовка, содержащего стандартные и настраиваемые свойства приложения - 64 МБ). Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ.

## Как получать сообщения из подписки

Основным способом получения сообщений из подписки является использование объекта **ServiceBusContract**. Полученные сообщения могут работать в двух различных режимах: **ReceiveAndDelete** и **PeekLock**.

При использовании режима**ReceiveAndDelete** получение является одиночной операцией, т.е., когда служебная шина получает запрос на чтение для сообщения в подписке, он помечает сообщение как израсходованное и возвращает его приложению. **Режим ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), оно завершает второй этап процесса получения, вызывая метод **Delete** для полученного сообщения. Когда служебная шина обнаруживает **удаление** вызова, он помечает сообщение как использованное и удалить его из раздела.

В следующем примере показывается, как можно получать и обрабатывать сообщения с помощью режима **PeekLock** (не используемого по умолчанию). В следующем примере выполняется цикл и обрабатывает сообщения в подписке "HighMessages", а затем завершает работу, когда больше нет сообщений (Кроме того, можно настроить ожидание новых сообщений).

	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);
	
		while(true)  { 
		    ReceiveSubscriptionMessageResult  resultSubMsg = 
		        service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
		    BrokeredMessage message = resultSubMsg.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());    
			    // Display the topic message.
			    System.out.print("From topic: ");
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
			        message.getProperty("MessageNumber"));
			    // Delete message.
			    System.out.println("Deleting this message.");
			    service.deleteMessage(message);
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

Служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого служебная шина разблокирует сообщение в разделе и сделает его доступным для приема тем же или другим приложением-пользователем.

Кроме того, с сообщением, заблокированным в разделе, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), служебная шина разблокирует сообщение автоматически и сделает его доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

## Как удалять разделы и подписки

Основным способом удаления разделов и подписок является использование объекта **ServiceBusContract**. При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

## Дальнейшие действия

Теперь, когда вы ознакомились с основами использования очередей служебной шины, см. в разделе MSDN [очереди, разделы и подписки служебной шины][Очереди шины обслуживания, разделы и подписки] для получения дополнительных сведений.

  [Azure SDK для Java]: http://azure.microsoft.com/develop/java/
  [Общая информация о разделах и подписках служебной шины]: #what-are-service-bus-topics
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Настройка приложения для использования служебной шины]: #bkmk_ConfigYourApp
  [Практическое руководство. Создание раздела]: #bkmk_HowToCreateTopic
  [Практическое руководство. Создание подписок]: #bkmk_HowToCreateSubscrip
  [Практическое руководство. Отправка сообщений в раздел]: #bkmk_HowToSendMsgs
  [Практическое руководство. Получение сообщений от подписки]: #bkmk_HowToReceiveMsgs
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #bkmk_HowToHandleAppCrash
  [Практическое руководство. Удаление разделов и подписок]: #bkmk_HowToDeleteTopics
  [Дальнейшие действия]: #bkmk_NextSteps
  [Схема разделов служебной шины]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Портал управления Azure]: http://manage.windowsazure.com/
  [Снимок экрана узла служебной шины]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Создание нового пространства имен ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Снимок экрана списка пространств имен]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Снимок экрана панели свойств]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Снимок экрана ключа по умолчанию]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Очереди шины обслуживания, разделы и подписки]: http://msdn.microsoft.com/library/hh367516.aspx

<!--HONumber=47-->
