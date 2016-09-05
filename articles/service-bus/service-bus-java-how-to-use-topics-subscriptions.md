.<properties
	pageTitle="Использование разделов служебной шины с Java | Microsoft Azure"
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
	ms.date="08/23/2016"
	ms.author="sethm"/>

# Как использовать разделы и подписки служебной шины

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

В этом руководстве описывается использование разделов и подписок служебной шины. Примеры написаны на Java и используют [Пакет Azure SDK для Java][]. В этой статье описаны такие сценарии, как **создание разделов и подписок**, **создание фильтров подписок**, **отправка сообщений** в раздел, **получение сообщений от подписки** и **удаление разделов и подписок**.

## Что такое разделы и подписки служебной шины?

Разделы и подписки служебной шины поддерживают модель обмена сообщениями "*публикация и подписка*". При использовании разделов и подписок компоненты распределенного приложения не взаимодействуют между собой напрямую, а обмениваются сообщениями через раздел, который выступает в качестве посредника.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

В отличие от очередей служебной шины, где каждое сообщение обрабатывается одним потребителем, разделы и подписки предоставляют вид связи одного со многими с помощью шаблона публикации и подписки. Можно зарегистрировать несколько подписок на раздел. Когда сообщение отправляется в раздел, оно затем может обрабатываться независимо каждой подпиской.

Раздел подписки напоминает виртуальную очередь, которая получает копии сообщений, отправленных в раздел. Вы можете дополнительно зарегистрировать правила фильтрации для раздела на основе подписки, которые позволят указать, какие сообщения и от каких подписок могут быть получены разделом.

Разделы и подписки служебной шины дают возможность масштабирования для обработки очень большого количества сообщений от очень большого количества пользователей и приложений.

## Создание пространства имен службы

Чтобы начать использование разделов и подписок служебной шины в Azure, необходимо сначала создать пространство имен службы. Пространство имен предоставляет контейнер для адресации ресурсов служебной шины в вашем приложении.

Создание пространства имен службы:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## Настройка приложения для использования служебной шины

Перед созданием этого образца убедитесь, что вы установили [пакет SDK Azure для Java][]. При использовании Eclipse можно установить [набор средств Azure для Eclipse][], включающий пакет SDK Azure для Java. Затем можно добавить **библиотеки Microsoft Azure для Java** в проект:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Добавьте в начало Java-файла следующие инструкции import:

```
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Добавьте библиотеки Azure для Java в путь построения и включите его в сборку развертывания проекта.

## Создание раздела

Операции управления для разделов служебной шины можно выполнять с помощью класса **ServiceBusContract**. Объект **ServiceBusContract** создается с соответствующей конфигурацией, которая инкапсулирует маркер SAS с разрешениями на управление им, а класс **ServiceBusContract** является единственной точкой связи с Azure.

Класс **ServiceBusService** предоставляет методы для создания, перечисления и удаления разделов. В следующем примере показано, как можно использовать объект **ServiceBusService** для создания раздела с именем `TestTopic` и пространством имен `HowToSample`.

    Configuration config =
    	ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

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

Методы **TopicInfo** позволяют настроить свойства раздела (например, задавать значение срока жизни (TTL) по умолчанию, применяемое к сообщениям, отправленным в раздел). Следующий пример показывает, как создать раздел с именем `TestTopic` и размером не более 5 ГБ.

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

Обратите внимание, что метод **listTopics** можно использовать для объектов **ServiceBusContract**, чтобы проверить, существует ли уже раздел с указанным именем в пространстве имен службы.

## Создание подписок

Подписки на разделы также создаются с помощью класса **ServiceBusService**. Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, то все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка AllMessages и используется фильтр по умолчанию **MatchAll**.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### Создание подписок с фильтрами

Вы также можете создать фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в определенной подписке раздела.

Самый гибкий тип фильтра, который поддерживается подписками — это [SqlFilter][], реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о выражениях, которые можно использовать с SQL-фильтром, см. в описании синтаксиса [SqlFilter.SqlExpression][].

В следующем примере создается подписка `HighMessages`, содержащая объект [SqlFilter][], который выбирает только сообщения, значение настраиваемого свойства **MessageNumber** которых превышает 3.

```
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

Аналогичным образом в следующем примере создается подписка `LowMessages` с фильтром [SqlFilter][], который выбирает только те сообщения, у которых значение свойства **messagenumber** меньше или равно 3.

```
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Если сообщение отправляется в раздел `TestTopic`, то оно всегда будет доставляться в приемники, подписанные на `AllMessages`, и в отдельные приемники, подписанные на `HighMessages` и `LowMessages` (в зависимости от содержимого сообщений).

## Отправка сообщений в раздел

Чтобы отправить сообщение в раздел служебной шины, приложение получает объект **ServiceBusContract**. В следующем примере кода показано, как отправить сообщение в раздел `TestTopic`, созданный ранее в пространстве имен `HowToSample`.

```
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

Сообщения, отправляемые в разделы Service Bus и получаемые из них, — это экземпляры класса [BrokeredMessage][]. Объекты [BrokeredMessage][]* имеют набор стандартных методов (например, **setLabel** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из произвольных данных приложения. Приложение может задать текст сообщения, передав конструктору [BrokeredMessage][] любой сериализуемый объект, после чего для сериализации объекта будет использоваться соответствующий **DataContractSerializer**. Кроме того, может быть предоставлен объект **java.io.InputStream**.

В следующем примере показано, как отправить пять тестовых сообщений в очередь `TestTopic` объекта **MessageSender**, полученного в предыдущем фрагменте кода: Обратите внимание, что значение свойства **MessageNumber** всех сообщений зависит от итерации цикла (определяет, какие подписки получают их).

```
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Разделы служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня "Премиум"](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ.

## Как получать сообщения из подписки

Чтобы получить сообщения из подписки, используйте объект **ServiceBusContract**. Полученные сообщения могут работать в двух различных режимах: **ReceiveAndDelete** и **PeekLock**.

При использовании режима **ReceiveAndDelete** получение является одиночной операцией, т. е. когда служебная шина получает запрос на чтение для сообщения в подписке, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает в ситуациях, когда приложение может не обрабатывать сообщение при сбое. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), оно завершает второй этап процесса получения, вызывая метод **Delete** для полученного сообщения. Когда служебная шина обнаруживает вызов **Delete**, она помечает сообщение как использованное и удаляет его из раздела.

В следующем примере показано, как получать и обрабатывать сообщения с помощью режима **PeekLock** (не используется по умолчанию): В следующем примере выполняется цикл и обрабатывает сообщения в подписке «HighMessages», а затем завершает работу, когда больше нет сообщений (Кроме того, можно настроить ожидание новых сообщений).

```
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
```

## Как обрабатывать сбои приложения и нечитаемые сообщения

служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого служебная шина разблокирует сообщение в разделе и сделает его доступным для приема тем же или другим приложением-пользователем.

Кроме того, с сообщением, заблокированным в разделе, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), то служебная шина разблокирует сообщение автоматически и сделает его доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

## Удаление разделов и подписок

Основным способом удаления разделов и подписок является использование объекта **ServiceBusContract**. При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности.

```
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## Дальнейшие действия

Вы познакомились с основами использования очередей служебной шины. Дополнительные сведения см. в статье [Очереди, темы и подписки][].

  [Пакет Azure SDK для Java]: http://azure.microsoft.com/develop/java/
  [пакет SDK Azure для Java]: http://azure.microsoft.com/develop/java/
  [набор средств Azure для Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Azure classic portal]: http://manage.windowsazure.com/
  [Очереди, темы и подписки]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  
  [0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
  [2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
  [3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png

<!---HONumber=AcomDC_0824_2016-->