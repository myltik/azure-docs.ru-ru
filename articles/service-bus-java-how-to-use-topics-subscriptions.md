<properties linkid="dev-java-how-to-service-bus-topics" urlDisplayName="Разделы Service Bus" pageTitle="Использование разделов Service Bus (Java) — Azure" metaKeywords="начало работы с разделами Azure Service Bus, начало работы с разделами Service Bus, обмен сообщениями публикаций и подписок Azure, разделы и подписки обмена сообщениями Azure, раздел Service Bus Java" description="Узнайте, как использовать разделы Service Bus и подписки в Azure. Примеры кода написаны для приложений Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="Использование разделов и подписок Service Bus" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />







# Использование разделов и подписок Service Bus

В этом руководстве показывается, как использовать разделы и
подписки Service Bus. Примеры написаны на Java и используют [Пакет Azure SDK для Java][]. В этой статье описаны такие сценарии, как **создание разделов
и подписок**, **создание фильтров подписок**, **отправка
сообщений в раздел**, **получение сообщений из подписки** и
**удаление разделов и подписок**.

## Оглавление

-   [Что такое разделы и подписки Service Bus?][]
-   [Создание пространства имен службы][]
-   [Получение учетных данных управления по умолчанию для пространства имен][]
-   [Настройка приложения для использования Service Bus][]
-   [Практическое руководство. Создание раздела][]
-   [Практическое руководство. Создание подписок][]
-   [Практическое руководство. Отправка сообщений в раздел][]
-   [Практическое руководство. Получение сообщений от подписки][]
-   [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений][]
-   [Практическое руководство. Удаление разделов и подписок][]
-   [Следующие шаги][]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="bkmk_ConfigYourApp"> </a>Настройка приложения для использования Service Bus

Добавьте в начало Java-файла следующие инструкции import:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Добавьте библиотеки Azure для Java в путь построения и включите его в сборку развертывания проекта.

## <a name="bkmk_HowToCreateTopic"> </a>Создание раздела

Операции управления для разделов Service Bus можно выполнять с помощью
класса **ServiceBusContract**. Объект **ServiceBusContract** создается с соответствующей конфигурацией, которая инкапсулирует разрешения маркера для управления им, а класс **ServiceBusContract** является единственной точкой связи с Azure.

Класс **ServiceBusService** предоставляет методы для создания, перечисления,
и удаления разделов. В следующем примере показано, как объект **ServiceBusService** можно
использовать для создания раздела с именем "TestTopic" и пространством имен "HowToSample":

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

Существуют методы **TopicInfo**, позволяющие настраивать свойства раздела, например
задавать значение по умолчанию для "времени жизни", применяемое к
сообщениям, отправленным в раздел. Следующий пример показывает, как
создать раздел с именем "TestTopic" и максимальным размером 5 ГБ:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Обратите внимание, что можно использовать метод **listTopics** для объектов
**ServiceBusContract**, чтобы проверить, существует ли уже раздел с указанным именем в
пространстве имен служб.

## <a name="bkmk_HowToCreateSubscrip"> </a>Создание подписок

Подписки на разделы также создаются с помощью класса
**ServiceBusService**. Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка
с именем "AllMessages", и используется фильтр по умолчанию **MatchAll**.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Создание подписок с фильтрами

Также можно настроить фильтры, позволяющие определять, какие сообщения, отправленные в раздел, должны появляться в конкретной подписке раздела.

Самым гибким типом фильтра, поддерживаемым подписками, является
**SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, опубликованных в разделе. Дополнительные
сведения о выражениях, которые можно использовать с SQL-фильтром,
см. в описании синтаксиса SqlFilter.SqlExpression.

В следующем примере создается подписка с именем "HighMessages" и фильтром
**SqlFilter**, которая выбирает только сообщения со значением пользовательского свойства
**MessageNumber** больше 3:

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

Аналогично, следующий пример создает подписку с именем
"LowMessages" и фильтром   
 SqlFilter, который выбирает только сообщения, у которых значение свойства MessageNumber
меньше   
 или равно 3:

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


Если сообщение будет отправлено в раздел "TestTopic", оно всегда будет
доставляться в приемники, подписанные на подписку раздела "AllMessages",
и в отдельные приемники, подписанные на подписки разделов
"HighMessages" и "LowMessages" topic subscriptions (в зависимости от
содержимого сообщений).

## <a name="bkmk_HowToSendMsgs"> </a>Отправка сообщений в раздел

Чтобы отправить сообщение в раздел Service Bus, приложение получает
объект **ServiceBusContract**. В примере кода ниже показано, как отправить
сообщение в раздел "TestTopic", созданный ранее в
пространстве имен "HowToSample" службы:

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Сообщения, отправленные в разделы Service Bus, являются экземплярами
класса **BrokeredMessage**. Объекты **BrokeredMessage**, у которых есть набор стандартных методов
(например, **setLabel** и **TimeToLive**), словарь,
используемый для хранения настраиваемых свойств приложения, и текст
из произвольных данных приложения. Приложение может определить текст сообщения,
передав любой сериализуемый объект в конструктор
**BrokeredMessage**, а затем соответствующий сериализатор **DataContractSerializer** будет
использоваться для сериализации объекта. Либо
**java.io.InputStream**.

В следующем примере показано, как отправить пять тестовых сообщений в
"TestTopic" **MessageSender**, полученный в предыдущем фрагменте кода.
Обратите внимание, что значение свойства **MessageNumber** каждого сообщения зависит
от итерации цикла (определяет, какие подписки
получат это сообщение):

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Разделы Service Bus поддерживают максимальный размер сообщения 256 МБ (максимальный размер заголовка, содержащего стандартные и настраиваемые свойства приложения — 64 МБ). Ограничения на количество сообщений в разделе нет, но есть ограничение на максимальный общий размер сообщений в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ.

## <a name="bkmk_HowToReceiveMsgs"> </a>Получение сообщений от подписки

Основным способом получать сообщения из подписки является использование
объекта **ServiceBusContract**. Полученные сообщения могут работать в двух различных режимах: **ReceiveAndDelete** и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией,
то есть, - когда Service Bus получает запрос на чтение для сообщения,
сообщение помечается как использованное и возвращается в
приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая
лучше всего работает для сценариев, где приложение может не обрабатывать
сообщение в случае сбоя. Чтобы разобраться, рассмотрим
сценарий, в котором приложение-получатель выдает запрос на получение, после чего
происходит сбой этого приложения еще до обработки сообщения. Поскольку Service Bus помечает сообщение
как использованное, то когда после своего перезапуска приложение снова начнет
обрабатывать сообщения, оно пропустит сообщение,
использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения
(или сохраняет его для будущей обработки), шина Service Bus
завершает второй этап процесса получения, вызывая метод **Delete**
для полученного сообщения. Когда Service Bus обнаруживает вызов **Delete**,
сообщение помечается как используемое и удаляется из раздела.

В следующем примере показывается, как можно получать и обрабатывать сообщения с помощью режима **PeekLock** (не режим по умолчанию). В следующем
примере выполняется цикл обработки сообщений в подписке "HighMessages", после чего при отсутствии дополнительных сообщений осуществляется выход (при необходимости можно настроить ожидание новых сообщений).

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

## <a name="bkmk_HowToHandleAppCrash"> </a>Обработка сбоев приложения и нечитаемых сообщений

Service Bus предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого Service Bus
разблокирует сообщение в разделе и сделает его доступным для
приема тем же или другим
приложением-пользователем.

Кроме того, с сообщением, заблокированным в разделе, связано время ожидания.
Если приложение не сможет обработать сообщение в течение времени ожидания
(например, при сбое приложения), Service Bus
разблокирует сообщение автоматически и сделает его
доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны предусмотреть дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

## <a name="bkmk_HowToDeleteTopics"> </a>Удаление разделов и подписок

Основным способом удаления разделов и подписок является использование
объекта **ServiceBusContract**. При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>Дальнейшие действия

Вы познакомились с основами использования очередей Service Bus.
Дополнительные сведения см. в статье [Очереди, разделы и подписки Service Bus][] на портале MSDN.

  [Пакет Azure SDK для Java]: http://www.windowsazure.com/ru-ru/develop/java/
  [Что такое разделы и подписки Service Bus?]: #what-are-service-bus-topics
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Настройка приложения для использования Service Bus]: #bkmk_ConfigYourApp
  [Практическое руководство. Создание раздела]: #bkmk_HowToCreateTopic
  [Практическое руководство. Создание подписок]: #bkmk_HowToCreateSubscrip
  [Практическое руководство. Отправка сообщений в раздел]: #bkmk_HowToSendMsgs
  [Практическое руководство. Получение сообщений от подписки]: #bkmk_HowToReceiveMsgs
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #bkmk_HowToHandleAppCrash
  [Практическое руководство. Удаление разделов и подписок]: #bkmk_HowToDeleteTopics
  [Следующие шаги]: #bkmk_NextSteps
  [Схема разделов Service Bus]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Портал управления Azure]: http://manage.windowsazure.com/
  [Снимок экрана узла Service Bus]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Создание нового пространства имен ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Снимок экрана списка пространств имен]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Снимок экрана панели свойств]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Снимок экрана ключа по умолчанию]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Очереди, разделы и подписки Service Bus]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx

