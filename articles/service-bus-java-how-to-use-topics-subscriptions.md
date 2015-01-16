<properties urlDisplayName="Service Bus Topics" pageTitle="Как использовать разделы Service Bus (Java) - Azure" metaKeywords="Приступая к работе с разделами Azure Service Bus, начало работы с разделами Service Bus, публикации подписки сообщения Azure, обмен сообщениями разделы и подписки Azure, раздел Service Bus Java" description="Узнайте, как использовать разделы и подписки Service Bus в Azure. Примеры кода написаны для приложений Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Topics/Subscriptions" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Использование разделов и подписок Service Bus

В этом руководстве показано как использовать разделы и
подписки Service Bus. Примеры написаны на Java и используют [Пакет Azure SDK для Java][]. Описаны скрипты: **создание разделов
и подписок**, **создание фильтров подписок**, **отправка
сообщений в раздел**, **получение сообщений из подписки**, и
**удаление разделов и подписок**.

## Оглавление

-   [Что такое разделы и подписки Service Bus?][]
-   [Создание пространства имен службы][]
-   [Получение учетных данных управления по умолчанию для пространства имен][]
-   [Настройка приложения для использования шины обслуживания][]
-   [Практическое руководство. Создание раздела][]
-   [Практическое руководство. Создание подписок][]
-   [Практическое руководство. Отправка сообщений в раздел][]
-   [Практическое руководство. Получение сообщений от подписки][]
-   [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений][]
-   [Практическое руководство. Удаление разделов и подписок][]
-   [Дальнейшие действия][]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="bkmk_ConfigYourApp"> </a>Настройка приложения для использования шины обслуживания

Добавьте в начало Java-файла следующие инструкции import:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Добавьте библиотеки Azure для Java в путь построения и включите его в сборку развертывания проекта.

## <a name="bkmk_HowToCreateTopic"> </a>Создание раздела

Операции управления для разделов Service Bus можно выполнять с помощью
класса **ServiceBusContract**. Объект **ServiceBusContract** 
создается с соответствующей конфигурацией, которая инкапсулирует
разрешения токенов для управления им и класс **ServiceBusContract ** 
является единственной точкой связи с Azure.

Класс **ServiceBusService** предоставляет методы для создания, перечисления,
и удаления разделов. В приведенном ниже примере показано, как объект **ServiceBusService** 
можно использовать для создания раздела с именем TestTopic и с пространством имен HowToSample:

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

В **TopicInfo** есть методы для настройки свойств раздела
(например: установить значение по умолчанию "времени жизни" для
сообщений, отправляемых в раздел). В следующем примере показан способ
создания раздела с именем TestTopic с максимальным размером 5 ГБ:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Обратите внимание, что можно использовать метод **listTopics** на объектах
**ServiceBusContract** для проверки наличия раздела с определенным названием
в пространстве имен службы.

## <a name="bkmk_HowToCreateSubscrip"> </a>Создание подписок

Подписки на разделы также создаются с помощью класса **ServiceBusService**
. У подписок есть названия, также они могут иметь дополнительный фильтр
, который ограничивает набор сообщений, передаваемых в виртуальную
очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, который применяется, в случае
при создании новой подписки не задается какой-либо фильтр. Когда используется фильтр **MatchAll**
все сообщения, публикуемые в разделе, помещаются в
в виртуальную очередь подписки. В следующем примере создается
подписка с именем AllMessages и используется фильтр умолчанию **MatchAll**
.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Создание подписок с фильтрами

Можно также настроить фильтры, позволяющие определять, какие сообщения, отправленные
в раздел, должны отображаться в определенной подписке раздела.

Самым гибким типом фильтра, поддерживаемым подписками, является
**SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают
со свойствами сообщений, публикуемых в разделе. Для
получения дополнительных сведений о выражениях, которые можно использовать с фильтрами SQL
изучите синтаксис SqlFilter.SqlExpression.

В следующем примере создается подписка с именем "HighMessages" и фильтром
**SqlFilter**, которая выбирает только сообщения со значением пользовательского свойства
**MessageNumber** больше 3.

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

Аналогичным образом в следующем примере создается подписка с именем
"LowMessages" с   
 SqlFilter, который выбирает только сообщения со значением свойства MessageNumber
меньшим   
 или равным 3:

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


Если сообщение отправить в TestTopic, оно всегда будет
доставлено получателям, подписанным на подписку AllMessages,
и выборочно доставлено получателям, подписанным на
HighMessages и LowMessages (в зависимости от
содержимого сообщения).

## <a name="bkmk_HowToSendMsgs"> </a>Отправка сообщений в раздел

Чтобы отправить сообщение в раздел Service Bus, приложение получает
объекта **ServiceBusContract**. Код ниже показывает способ отправки
сообщения для раздела TestTopic, созданного ранее в нашем
пространстве имен HowToSample службы:

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Сообщения, отправленные в разделы Service Bus, являются экземплярами
класса **BrokeredMessage**. Объекты **BrokeredMessage** имеют набор
стандартных методов (например, **setLabel** и **TimeToLive**), словарь
, используемый для хранения настраиваемых свойств приложения и набора
произвольных данных приложения. Приложение может задать текст
сообщения, передав любой сериализуемый объект в конструктор
**BrokeredMessage**, а подходящий  **DataContractSerializer** будет
использован для сериализации объекта. Либо
можно предоставить **java.io.InputStream**.

В следующем примере показано, как отправить пять тестовых сообщений в
"TestTopic" **MessageSender** полученную ранее во фрагменте кода.
Обратите внимание, как изменяется значение свойства **MessageNumber** каждого сообщения
при итерации цикла (это определяет, какие подписки
получат сообщение):

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Разделы Service Bus поддерживают максимальный размер сообщения в 256 МБ (заголовок,
, которая содержит стандартные и настраиваемые свойства приложения и размер которой
не может превышать 64 МБ). Ограничения на количество сообщений нет
, но есть ограничение на общий размер сообщений
в разделе. Этот размер раздела определяется во время создания:
верхнее ограничение - 5 ГБ.

## <a name="bkmk_HowToReceiveMsgs"> </a>Получение сообщений от подписки

Основным способом получать сообщения из подписки является использование
объекта **ServiceBusContract**. Полученные сообщения могут работать в двух
различных режимах: **ReceiveAndDelete** и **PeekLock**.

В режиме **ReceiveAndDelete** получение является однократной
операцией - когда Service Bus получает запрос на чтение
сообщения, она помечает сообщение как использованное и возвращает его в
приложение. Режим **ReceiveAndDelete** - самая простая модель чтения и
лучше всего подходит для скриптов, в которых допускается, что приложение может пропустить
сообщение в случае сбоя. Чтобы понять этот процесс, рассмотрим
скрипт, в котором приложение пользователя выдает запрос на получение сообщения, а затем
завершается с критической ошибкой до его обработки. Поскольку Service Bus пометит
сообщение как использованное, то когда приложение перезапустится и снова начнет
потреблять сообщения, оно пропустит то сообщение, которое было
потреблено до сбоя.

В режиме **PeekLock** операция получения становится двухэтапной операцией, что делает
возможным писать приложения, для которых недопустимо пропускать
сообщения. Получив запрос, Service Bus находит следующее сообщение
для потребления, блокирует его, чтобы предотвратить его получение другими получателями и
возвращает его приложению. Как только приложение
завершит обработку сообщения (или надежно сохранит его для будущей обработки), оно
завершает второй этап процесса получения, вызывая метод **Delete**
для полученного сообщения. Когда Service Bus обнаруживает вызов **Delete**, то
сообщение помечается как использованное и удаляется из раздела.

В примере ниже показано, как можно получать сообщения, и
обрабатывать их в режиме **PeekLock** (а не в режиме по умолчанию). В следующем примере
выполняется цикл обработки сообщений в подписке "HighMessages", после чего при отсутствии дополнительных сообщений осуществляется выход (при необходимости цикл можно настроить на ожидание новых сообщений).

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

Service Bus предоставляет функции, помогающие надлежащим образом исправить ошибки
в приложении или восстановиться после трудностей, возникших при обработке сообщения. Если
приложение-получатель не может обработать сообщение по какой-то причине,
оно может вызвать метод **unlockMessage** для принятого сообщения
(вместо **deleteMessage**). После Service Bus
разблокирует сообщение в разделе и снова сделает его доступным для получения
, либо тем же приложением либо
другим.

Существует также время ожидания, связанное с сообщением, заблокированным в
@@@разделе, и если приложение не сможет обработать сообщение пока
время ожидания блокировки истекает (например, при сбое приложения), то Service Bus
разблокирует сообщение автоматически и сделает его доступным для
повторного получения.

Если сбой приложения происходит после обработки сообщения
,но до выдачи запроса **deleteMessage**, то сообщение
будет повторно доставлено в приложение после его перезапуска. Такой подход часто
называется **Обработать хотя бы один раз**, то есть, каждое сообщение будет
обработано хотя бы один раз, но в некоторых ситуациях это же сообщение может
быть доставлено повторно. Если в скрипте не допускается повторная обработка,
то разработчики приложения должны добавить дополнительную логику для
для обработки случаев повторной доставки сообщений. Зачастую это решается
использованием метода **getMessageId** сообщения, этот идентификатор остается
неизменным при всех попытках доставки.

## <a name="bkmk_HowToDeleteTopics"> </a>Удаление разделов и подписок

Основным способом удаления разделов и подписок является использование
объекта **ServiceBusContract**. При удалении раздела также удаляются все подписки, которые зарегистрированы
в этом разделе. Подписки также можно удалять по отдельности.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>Дальнейшие действия

Вы ознакомились с основами использования очередей Service Bus. Теперь вы можете изучить раздел MSDN
раздел [Очереди, разделы и подписки Service Bus][] для получения дополнительных сведений.

  [Пакет Azure SDK для Java]: http://www.windowsazure.com/ru-ru/develop/java/
  [Что такое разделы и подписки Service Bus?]: #what-are-service-bus-topics
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Настройка приложения для использования шины обслуживания]: #bkmk_ConfigYourApp
  [Практическое руководство. Создание раздела]: #bkmk_HowToCreateTopic
  [Практическое руководство. Создание подписок]: #bkmk_HowToCreateSubscrip
  [Практическое руководство. Отправка сообщений в раздел]: #bkmk_HowToSendMsgs
  [Практическое руководство. Получение сообщений от подписки]: #bkmk_HowToReceiveMsgs
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #bkmk_HowToHandleAppCrash
  [Практическое руководство. Удаление разделов и подписок]: #bkmk_HowToDeleteTopics
  [Дальнейшие действия]: #bkmk_NextSteps
  [Схема разделов Service Bus]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Портал управления Azure]: http://manage.windowsazure.com/
  [Снимок экрана узла Service Bus]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Создание нового пространства имен ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Снимок экрана списка пространств имен]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Снимок экрана панели свойств]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Снимок экрана ключа по умолчанию]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Очереди, разделы и подписки Service Bus]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
