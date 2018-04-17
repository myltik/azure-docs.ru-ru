---
title: Как использовать разделы служебной шины Azure с помощью Java | Документация Майкрософт
description: Использование разделов и подписок служебной шины в Azure.
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 63d6c8bd-8a22-4292-befc-545ffb52e8eb
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/17/2017
ms.author: sethm
ms.openlocfilehash: 9c2501840b3c00a63b0344d48e3225fd2c9d1620
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-java"></a>Как использовать разделы и подписки служебной шины с Java

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

В этом руководстве описывается использование разделов и подписок служебной шины. Примеры написаны на Java и используют [пакет Azure SDK для Java][Azure SDK for Java]. В этой статье описаны такие сценарии, как **создание разделов и подписок**, **создание фильтров подписок**, **отправка сообщений в раздел**, **получение сообщений из подписки** и **удаление разделов и подписок**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Что такое разделы и подписки служебной шины?
Разделы и подписки служебной шины поддерживают модель обмена сообщениями " *публикация и подписка* ". При использовании разделов и подписок компоненты распределенного приложения не взаимодействуют между собой напрямую, а обмениваются сообщениями через раздел, который выступает в качестве посредника.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

В отличие от очередей служебной шины, где каждое сообщение обрабатывается одним потребителем, разделы и подписки предоставляют вид связи одного со многими с помощью шаблона публикации/подписки. Можно зарегистрировать несколько подписок на раздел. Когда сообщение отправляется в раздел, оно затем может обрабатываться независимо каждой подпиской.

Раздел подписки напоминает виртуальную очередь, которая получает копии сообщений, отправленных в раздел. Вы можете зарегистрировать правила фильтрации для раздела на основе подписки, которые позволят указать, какие сообщения и от каких подписок могут быть получены разделом.

Разделы и подписки служебной шины обеспечивают возможность масштабирования для обработки большого количества сообщений для большого количества пользователей и приложений.

## <a name="create-a-service-namespace"></a>Создание пространства имен службы
Чтобы начать использование разделов и подписок служебной шины в Azure, необходимо сначала создать *пространство имен*, которое предоставляет контейнер, предназначенный для ресурсов служебной шины в вашем приложении.

Создание пространства имен службы:

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Настройка приложения для использования служебной шины
Перед созданием этого образца убедитесь, что вы установили [пакет Azure SDK для Java][Azure SDK for Java]. При использовании Eclipse можно установить [набор средств Azure для Eclipse][Azure Toolkit for Eclipse], включающий в себя пакет Azure SDK для Java. Затем можно добавить **библиотеки Microsoft Azure для Java** в проект.

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Добавьте в начало Java-файла следующие инструкции `import`:

```java
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Добавьте библиотеки Azure для Java в путь построения и включите его в сборку развертывания проекта.

## <a name="create-a-topic"></a>Создание раздела
Операции управления для разделов служебной шины можно выполнять с помощью класса **ServiceBusContract**. Объект **ServiceBusContract** создается с соответствующей конфигурацией, которая инкапсулирует маркер SAS с разрешениями на управление им, а класс **ServiceBusContract** является единственной точкой связи с Azure.

Класс **ServiceBusService** предоставляет методы для создания, перечисления и удаления разделов. В следующем примере показано, как можно использовать объект **ServiceBusService** для создания раздела с именем `TestTopic` и пространством имен `HowToSample`.

```java
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
```

Методы **TopicInfo** позволяют настроить свойства раздела (например, задавать значение срока жизни (TTL) по умолчанию, применяемое к сообщениям, отправленным в раздел). Следующий пример показывает, как создать раздел с именем `TestTopic` и размером не более 5 ГБ.

```java
long maxSizeInMegabytes = 5120;  
TopicInfo topicInfo = new TopicInfo("TestTopic");  
topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateTopicResult result = service.createTopic(topicInfo);
```

Метод **listTopics** можно использовать для объектов **ServiceBusContract**, чтобы проверить, существует ли уже раздел с указанным именем в пространстве имен службы.

## <a name="create-subscriptions"></a>Создание подписок
Подписки на разделы также создаются с помощью класса **ServiceBusService**. Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Создание подписки с фильтром по умолчанию (MatchAll)
Если при создании новой подписки не указан фильтр, то по умолчанию используется фильтр **MatchAll**. Если используется фильтр **MatchAll**, то все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка AllMessages и используется фильтр по умолчанию **MatchAll**.

```java
SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
CreateSubscriptionResult result =
    service.createSubscription("TestTopic", subInfo);
```

### <a name="create-subscriptions-with-filters"></a>Создание подписок с фильтрами
Вы также можете создать фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в определенной подписке раздела.

Самый гибкий тип фильтра, который поддерживается подписками, — это [SqlFilter][SqlFilter], реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о выражениях, которые можно использовать с фильтром SQL, см. в описании синтаксиса [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

В следующем примере создается подписка `HighMessages`, содержащая объект [SqlFilter][SqlFilter], который выбирает только сообщения, значение настраиваемого свойства **MessageNumber** которых превышает 3.

```java
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

Аналогично в следующем примере создается подписка `LowMessages` с фильтром [SqlFilter][SqlFilter], который выбирает только те сообщения, у которых значение свойства **MessageNumber** меньше или равно 3.

```java
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Если сообщение отправляется в раздел `TestTopic`, оно всегда будет доставляться в приемники, подписанные на `AllMessages`, и в отдельные приемники, подписанные на `HighMessages` и `LowMessages` (в зависимости от содержимого сообщений).

## <a name="send-messages-to-a-topic"></a>Отправка сообщений в раздел
Чтобы отправить сообщение в раздел служебной шины, приложение получает объект **ServiceBusContract**. В следующем примере кода показано, как отправить сообщение в раздел `TestTopic`, созданный ранее в пространстве имен `HowToSample`.

```java
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

Сообщения, отправляемые в разделы служебной шины, — это экземпляры класса [BrokeredMessage][BrokeredMessage]. Объекты [BrokeredMessage][BrokeredMessage]* имеют набор стандартных методов (например, **setLabel** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из произвольных данных приложения. Приложение может задать текст сообщения, передав конструктору объекта [BrokeredMessage][BrokeredMessage] любой сериализуемый объект, после чего для сериализации объекта будет использоваться соответствующий класс **DataContractSerializer**. Кроме того, может быть предоставлен объект **java.io.InputStream**.

В следующем примере показано, как отправить пять тестовых сообщений в очередь `TestTopic` объекта **MessageSender**, полученного в предыдущем фрагменте кода.
Обратите внимание, что значение свойства **MessageNumber** сообщения зависит от итерации цикла (это значение определяет, какие подписки его получают).

```java
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Разделы служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения количества сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ.

## <a name="how-to-receive-messages-from-a-subscription"></a>Как получать сообщения из подписки
Чтобы получить сообщения из подписки, используйте объект **ServiceBusContract**. Полученные сообщения могут работать в двух режимах: **ReceiveAndDelete** и **PeekLock** (режим по умолчанию).

При использовании режима **ReceiveAndDelete** получение является одиночной операцией, т. е. когда служебная шина получает запрос на чтение для сообщения в подписке, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает в ситуациях, когда приложение может не обрабатывать сообщение при сбое. Для примера рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и аварийно завершается до его обработки. Так как служебная шина отмечает сообщение как использованное, перезапущенное приложение, начав снова использовать сообщения, пропустит сообщение, использованное до аварийного завершения работы.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), оно завершает второй этап процесса получения, вызывая метод **Delete** для полученного сообщения. Когда служебная шина обнаруживает вызов **Delete**, она отмечает сообщение как использованное и удаляет его из раздела.

В следующем примере показано, как получать и обрабатывать сообщения с помощью режима **PeekLock** (режим по умолчанию). Следующий пример выполняет цикл и обрабатывает сообщения в подписке `HighMessages`, а затем завершает работу, когда больше нет сообщений (кроме того, можно настроить ожидание новых сообщений).

```java
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Как обрабатывать сбои приложения и нечитаемые сообщения
служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого служебная шина разблокирует сообщение в разделе и сделает его доступным для приема тем же или другим приложением-пользователем.

Кроме того, с сообщением, блокированным в разделе, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), служебная шина автоматически разблокирует сообщение и снова сделает его доступным для получения.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage**, это сообщение повторно доставляется в приложение после его перезапуска. Часто такой процесс называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

## <a name="delete-topics-and-subscriptions"></a>Удаление разделов и подписок
Основным способом удаления разделов и подписок является использование объекта **ServiceBusContract**. При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности.

```java
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Дальнейшие действия
Вы познакомились с основами использования очередей служебной шины. Дополнительные сведения см. в статье [Очереди, разделы и подписки служебной шины][Service Bus queues, topics, and subscriptions].

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

[0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
[2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
[3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
