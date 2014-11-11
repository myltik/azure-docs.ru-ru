<properties linkid="dev-java-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Java) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic Java" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Java applications." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Topics/Subscriptions" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Использование разделов и подписок Service Bus

В этом руководстве показывается, как использовать разделы и
подписки Service Bus. Примеры написаны на Java и используют [Пакет Azure SDK для Java][Пакет Azure SDK для Java]. В этой статье описаны такие сценарии, как **создание разделов
и подписок**, **создание фильтров подписок**, **отправка
сообщений в раздел**, **получение сообщений из подписки** и
**удаление разделов и подписок**.

## Оглавление

-   [Что такое разделы и подписки Service Bus?][Что такое разделы и подписки Service Bus?]
-   [Создание пространства имен службы][Создание пространства имен службы]
-   [Получение учетных данных управления по умолчанию для пространства имен][Получение учетных данных управления по умолчанию для пространства имен]
-   [Настройка приложения для использования шины обслуживания][Настройка приложения для использования шины обслуживания]
-   [Практическое руководство. Создание раздела][Практическое руководство. Создание раздела]
-   [Практическое руководство. Создание подписок][Практическое руководство. Создание подписок]
-   [Практическое руководство. Отправка сообщений в раздел][Практическое руководство. Отправка сообщений в раздел]
-   [Практическое руководство. Получение сообщений из подписки][Практическое руководство. Получение сообщений из подписки]
-   [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений][Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]
-   [Практическое руководство. Удаление разделов и подписок][Практическое руководство. Удаление разделов и подписок]
-   [Дальнейшие действия][Дальнейшие действия]

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

Операции управления для очередей Service Bus можно выполнять с помощью класса
**ServiceBusRestProxy**. Объект **ServiceBusContract** создается
с соответствующей конфигурацией, которая инкапсулирует разрешения
маркера для управления им, а класс **ServiceBusContract** является
единственной точкой связи с Azure.

Класс **ServiceBusService** предоставляет методы для создания, перечисления
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

Существуют методы **TopicInfo**, позволяющие настраивать свойства раздела,
например задавать значение по умолчанию для "времени жизни", применяемое к
сообщениям, отправленным в раздел. Следующий пример показывает, как
создать раздел с именем "TestTopic" и максимальным размером 5 ГБ:

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Обратите внимание, метод **listQueues** можно использовать для объектов
**ServiceBusContract**, чтобы проверить, существует ли
уже очередь с указанным именем в пространстве имен служб.

## <a name="bkmk_HowToCreateSubscrip"> </a>Создание подписок

Подписки на разделы также создаются с помощью класса**ServiceBusService**
. Подписки имеют имена и могут использовать дополнительный фильтр,
 ограничивающий набор сообщений, доставляемых в виртуальную
очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию,
он используется в том случае, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**
, все сообщения, опубликованные в разделе, помещаются в
виртуальную очередь подписки. В следующем примере создается
подписка с именем "AllMessages" и используется фильтр по умолчанию **MatchAll**
.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Создание подписок с фильтрами

Вы также можете настроить фильтры, позволяющие определять, какие сообщения, отправленные
в раздел, будут отображаться в определенной подписке раздела.

Самый гибкий тип фильтра, который поддерживается подписками, — это
**SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают
со свойствами сообщений, которые опубликованы в разделе. Дополнительные
сведения о выражениях, которые можно использовать с SQL-фильтром,см. в описании синтаксиса
SqlFilter.SqlExpression.

В следующем примере создается подписка с именем "HighMessages" и фильтром
**SqlFilter**, который только выбирает сообщения, у которых значение пользовательского свойства
**MessageNumber** превышает 3:

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
SqlFilter, который выбирает только сообщения, у которых значение
свойства MessageNumber меньше
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
доставляться в приемники,
подписанные на подписку раздела "AllMessages", и в отдельные приемники, подписанные на подписки разделов
"HighMessages" и "LowMessages" (в зависимости от
содержимого сообщений).

## <a name="bkmk_HowToSendMsgs"> </a>Отправка сообщений в раздел

Чтобы отправить сообщение в раздел Service Bus, приложение получает объект
**ServiceBusContract**. В примере кода ниже показано, как отправить
сообщение в раздел "TestTopic", созданный ранее в
пространстве имен "HowToSample" службы:

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Сообщения, отправляемые в разделы Service Bus и получаемые из них, — это экземпляры класса
**BrokeredMessage**. Объекты **BrokeredMessage**, у которых есть набор
стандартных методов(например, **setLabel** и **TimeToLive**), словарь,
используемый для хранения настраиваемых свойств приложения, и текст
из произвольных данных приложения. Приложение может задать текст
сообщения, передав конструктору
**BrokeredMessage** любой сериализуемый объект, после чего для сериализации объекта будет использоваться соответствующий **DataContractSerializer**
. Кроме того, может быть предоставлен
**java.io.InputStream**.

В следующем примере показано, как отправить пять тестовых сообщений в
"TestTopic" **MessageSender**, полученный в предыдущем фрагменте кода.
Обратите внимание, что значение свойства **MessageNumber** каждого сообщения зависит от
итерации цикла (это определяет, получит ли подписка
сообщение).

    for (int i=0; i<5; i++)  {
        // Create message, passing a string message for the body
        BrokeredMessage message = new BrokeredMessage("Test message " + i);
        // Set some additional custom app-specific property
        message.setProperty("MessageNumber", i);
        // Send message to the topic
        service.sendTopicMessage("TestTopic", message);
    }

Разделы Service Bus поддерживают максимальный размер сообщения 256 МБ (максимальный размер заголовка,
который содержит стандартные и настраиваемые
свойства приложения, — 64 МБ). Ограничения на количество сообщений
в разделе нет, но есть максимальный общий размер сообщений,
содержащихся в разделе. Этот размер задается при создании с верхним пределом 5 ГБ.

## <a name="bkmk_HowToReceiveMsgs"> </a>Получение сообщений из подписки

Основным способом получать сообщения из подписки является использование объекта
**ServiceBusContract**. Полученные сообщения могут работать в двух
различных режимах: **ReceiveAndDelete** и **PeekLock**.

В режиме **ReceiveAndDelete** получение является
одиночной операцией, то есть, когда Service Bus получает запрос на чтение для
сообщения, оно помечается как использованное и
возвращается в приложение. **Режим ReceiveAndDelete** представляет собой самую простую модель, которая лучше
всего работает для сценариев, где приложение может не обрабатывать
сообщение в случае сбоя. Чтобы это понять, рассмотрим
сценарий, в котором объект-получатель выдает запрос на получение и
выходит из строя до его обработки. Поскольку Service Bus помечает
сообщение как используемое, то после того как приложение перезапускается и
снова начинает обрабатывать сообщения, оно пропустит сообщение, которое использовалось до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что
позволяет поддерживать приложения, не устойчивые к пропуску
сообщений. Получив запрос, Service Bus находит следующее сообщение,
блокирует его, чтобы предотвратить его получение другими получателями, и
возвращает его приложению. Когда приложение завершает
обработку сообщения (или сохраняет его для будущей обработки), шина Service Bus
завершает второй этап процесса получения, вызывая метод **Delete**
для полученного сообщения. Когда Service Bus обнаруживает вызов **Delete**,сообщение
помечается как используемое и удаляется из раздела.

В следующем примере показывается, как можно получать и
обрабатывать сообщения с помощью режима **PeekLock** (не режим по умолчанию). В следующем
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

Служебная шина предоставляет функции, помогающие корректно исправить
ошибки в приложении или устранить проблемы с обработкой сообщения. Если
по каким-либо причинам не может обработать сообщение,
то оно может вызвать для полученного сообщения метод **unlockMessage**
(вместо метода **deleteMessage**). После этого Service Bus
разблокирует сообщение в очереди и сделает его доступным для
приема тем же или другим
приложением-пользователем.

Кроме того, с сообщением, заблокированным в
разделе, связано время ожидания. Если приложение не сможет обработать сообщение
в течение времени ожидания (например, при сбое приложения), Service
Bus разблокирует сообщение автоматически и сделает его
доступным для приема.

Если сбой приложения происходит после обработки сообщения,
но перед отправкой запроса **deleteMessage** это сообщение
будет повторно доставлено в приложение после его перезапуска. Часто этот подход
называют **обработать хотя бы один раз**, т. е. каждое сообщение будет
обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть
доставлено повторно. Если повторная обработка недопустима,
разработчики приложения должны добавить дополнительную логику для
обработки повторной доставки сообщений. Часто это достигается
с помощью метода **getMessageId** сообщения, которое остается
постоянным для различных попыток доставки.

## <a name="bkmk_HowToDeleteTopics"> </a>Удаление разделов и подписок

Основным способом удаления разделов и подписок является использование объекта
**ServiceBusContract**.
При удалении раздела также удаляются все подписки, которые зарегистрированы в разделе. Подписки также можно удалять по отдельности.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
    service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a> Дальнейшие действия

Вы познакомились с основами использования очередей Service Bus.
Дополнительные сведения см. в статье [Очереди, разделы и подписки Service Bus][Очереди, разделы и подписки Service Bus] на портале MSDN.

  [Пакет Azure SDK для Java]: http://www.windowsazure.com/ru-ru/develop/java/
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Настройка приложения для использования шины обслуживания]: #bkmk_ConfigYourApp
  [Практическое руководство. Создание раздела]: #bkmk_HowToCreateTopic
  [Практическое руководство. Создание подписок]: #bkmk_HowToCreateSubscrip
  [Практическое руководство. Отправка сообщений в раздел]: #bkmk_HowToSendMsgs
  [Практическое руководство. Получение сообщений из подписки]: #bkmk_HowToReceiveMsgs
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #bkmk_HowToHandleAppCrash
  [Практическое руководство. Удаление разделов и подписок]: #bkmk_HowToDeleteTopics
  [Дальнейшие действия]: #bkmk_NextSteps
  [howto-service-bus-topics]: ../includes/howto-service-bus-topics.md
  [Очереди, разделы и подписки Service Bus]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
