<properties 
    pageTitle="Использование разделов служебной шины с Node.js | Microsoft Azure" 
    description="Узнайте, как использовать разделы и подписки служебной шины в Azure в приложении Node.js." 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>



# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Использование разделов и подписок служебной шины

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

В этом руководстве показано, как использовать разделы и подписки служебной шины в приложениях Node.js. В этой статье описаны такие сценарии, как **создание разделов и подписок**, **создание фильтров подписок**, **отправка сообщений в раздел**, **получение сообщений из подписки** и **удаление разделов и подписок**. Дополнительные сведения о разделах и подписках см. в разделе [Дальнейшие действия](#next-steps).

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-node.js-application"></a>Создание приложения Node.js

Создайте пустое приложение Node.js. Указания по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure], [Облачная служба Node.js][] (с помощью Windows PowerShell) или веб-сайт с WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Настройка приложения для использования служебной шины

Для использования служебной шины скачайте пакет Node.js для Azure. Пакет содержит набор библиотек, взаимодействующих со службами REST Service Bus.

### <a name="use-node-package-manager-(npm)-to-obtain-the-package"></a>Использование диспетчера пакета Node (NPM) для получения пакета

1.  Используя интерфейс командной строки, такой как **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите в папку, в которой создан пример приложения.

2.  В командной строке введите команду **npm install azure**. Должен получиться вот такой результат:

    ```
        azure@0.7.5 node_modules\azure
    ├── dateformat@1.0.2-1.2.3
    ├── xmlbuilder@0.4.2
    ├── node-uuid@1.2.0
    ├── mime@1.2.9
    ├── underscore@1.4.4
    ├── validator@1.1.1
    ├── tunnel@0.0.2
    ├── wns@0.5.3
    ├── xml2js@0.2.7 (sax@0.5.2)
    └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```

3.  Выполнив команду **ls** вручную, можно убедиться, что папка **node\_modules** создана. Найдите в папке пакет **azure**, который содержит библиотеки, необходимые для доступа к разделам служебной шины.

### <a name="import-the-module"></a>Импорт модуля

С помощью Блокнота или другого текстового редактора добавьте в начало файла **server.js** приложения следующее:

```
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Настройка подключения к Service Bus

Модуль Azure считывает переменные среды AZURE\_SERVICEBUS\_NAMESPACE и AZURE\_SERVICEBUS\_ACCESS\_KEY для получения сведений, необходимых для подключения к служебной шине. Если эти переменные среды не заданы, при вызове **createServiceBusService** необходимо указать сведения об учетной записи.

Пример настройки переменных среды в файле конфигурации для облачной службы Azure см. в статье [Облачная служба Node.js с хранилищем][].

Пример настройки переменных среды для веб-сайта Azure на [классическом портале Azure][] см. в статье [Веб-приложение Node.js с хранилищем][].

## <a name="create-a-topic"></a>Создание раздела

Объект **ServiceBusService** позволяет работать с разделами. Следующий код создает объект **ServiceBusService**. Добавьте его в начало файла **server.js** после оператора импорта модуля Аzure.

```
var serviceBusService = azure.createServiceBusService();
```

Вызов **createTopicIfNotExists** для объекта **ServiceBusService** возвратит указанный раздел (при его наличии) или создаст новый раздел с указанным именем. В приведенном ниже коде используется **createTopicIfNotExists** для создания раздела с именем MyTopic или подключения к нему.

```
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

Метод **createServiceBusService** также поддерживает дополнительные параметры, позволяющие переопределить настройки раздела, используемые по умолчанию, такие как срок жизни сообщения или максимальный размер раздела. В следующем примере показано, как установить максимальный размер раздела 5 ГБ и значение срока жизни в 1 минуту.

```
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Фильтры

Используя **ServiceBusService**, к выполняемым операциям можно применить дополнительные операции фильтрации. К операциям фильтрации могут относиться ведение журнала, автоматический повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

```
function handle (requestOptions, next)
```

Выполнив предварительную обработку параметров запроса, метод должен вызвать `next`, передавая обратный вызов со следующей сигнатурой:

```
function (returnObject, finalCallback, next)
```

В этой функции обратного вызова и после обработки **returnObject** (ответ на запрос к серверу) функция обратного вызова должна либо вызвать next (при его наличии), чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать **finalCallback** для завершения обращения к службе.

В пакет SDK Azure для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter**. Следующий код создает объект **ServiceBusService**, использующий **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="create-subscriptions"></a>Создание подписок

Подписки на разделы также создаются с помощью объекта **ServiceBusService**. Подписки имеют имена и могут использовать дополнительный фильтр, который ограничивает набор сообщений, доставляемых в виртуальную очередь подписки.

> [AZURE.NOTE] Подписки являются постоянными и продолжают существовать либо до их удаления, либо до удаления раздела, с которым они связаны. Если приложение содержит логику для создания подписки, оно сначала должно проверить, существует ли подписка, используя метод **getSubscription**.

### <a name="create-a-subscription-with-the-default-(matchall)-filter"></a>Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, то все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка AllMessages и используется фильтр по умолчанию **MatchAll**.

```
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Создание подписок с фильтрами

Вы также можете создать фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в той или иной подписке раздела.

Самый гибкий тип фильтра, который поддерживается подписками, — это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительную информацию о выражениях, которые можно использовать с SQL-фильтром, см. в описании синтаксиса [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Добавить фильтры в подписку можно с помощью метода **createRule** объекта **ServiceBusService**. Этот метод позволяет добавлять новые фильтры в существующую подписку.

> [AZURE.NOTE] Так как ко всем новым подпискам автоматически применяется фильтр по умолчанию, сначала необходимо удалить фильтр по умолчанию, иначе фильтр **MatchAll** переопределит поведение всех остальных заданных фильтров. Вы можете удалить правило по умолчанию с помощью метода **deleteRule** объекта **ServiceBusService**.

В следующем примере создается подписка с именем `HighMessages`, содержащая объект **SqlFilter**, который выбирает только те сообщения, значение настраиваемого свойства **messagenumber** которых больше 3.

```
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'HighMessages', 
            'HighMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Аналогичным образом в следующем примере создается подписка с именем `LowMessages` и фильтром **SqlFilter**. Он выбирает только те сообщения, у которых значение свойства **messagenumber** меньше или равно 3.

```
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'LowMessages', 
            'LowMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Если сообщение отправляется в раздел `MyTopic`, оно всегда будет доставляться в приемники, подписанные на подписку раздела `AllMessages`, и в отдельные приемники, подписанные на подписки разделов `HighMessages` и `LowMessages` (в зависимости от содержимого сообщения).

## <a name="how-to-send-messages-to-a-topic"></a>Как отправлять сообщения в раздел

Чтобы отправить сообщение в раздел служебной шины, ваше приложение должно использовать метод **sendTopicMessage** объекта **ServiceBusService**.
Сообщения, отправленные в разделы служебной шины, являются объектами **BrokeredMessage**.
Объекты **BrokeredMessage** содержат набор стандартных свойств (например, **Label** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из строковых данных. Приложение может задать текст сообщения, передав строковое значение в метод **sendTopicMessage**, и все обязательные стандартные свойства будут заполнены значениями по умолчанию.

На приведенном ниже примере продемонстрирована отправка тестового сообщения в раздел MyTopic. Обратите внимание, что значение свойства **messagenumber** сообщений зависит от итерации цикла (определяет, какие подписки получают его).

```
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Разделы служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ.

## <a name="receive-messages-from-a-subscription"></a>Получение сообщений из подписки

Сообщения извлекаются из подписки с помощью метода **receiveSubscriptionMessage** в объекте **ServiceBusService**. По умолчанию прочитанные сообщения удаляются из подписки. Но сообщение можно прочитать (просмотреть) и заблокировать, не удаляя его из подписки. Для этого следует задать для необязательного параметра **isPeekLock** значение **true**.

Поведение по умолчанию — чтение и удаление сообщения как часть операции получения — это простейшая модель, оптимальная для сценариев, в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

Если для параметра **isPeekLock** установлено значение **true**, получение становится двухэтапной операцией, что позволяет поддерживать приложения, которые не могут пропускать сообщения. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению.
Обработав сообщение (или сохранив его в надежном месте для последующей обработки), приложение вызывает метод **deleteMessage** и указывает сообщение, которое будет удалено как параметр, таким образом завершая второй этап процесса получения. Метод **deleteMessage** помечает сообщение как использованное и удаляет его из подписки.

В следующем примере показано получение и обработка сообщений с помощью метода **receiveSubscriptionMessage**. В этом примере сообщение сначала поступает из подписки LowMessages и удаляется, затем сообщение поступает из подписки HighMessages при значении true, заданном для параметра **isPeekLock**. Затем сообщение удаляется с помощью метода **deleteMessage**:

```
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Как обрабатывать сбои приложения и нечитаемые сообщения

служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если по какой-либо причине приложению-получателю не удается обработать сообщение, оно вызывает метод **unlockMessage** в объекте **ServiceBusService**. После этого служебная шина разблокирует сообщение в подписке и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, при блокировке сообщения в подписке предусмотрено определенное время ожидания. Если приложение не может обработать сообщение до истечения времени блокировки (например, аварийно завершит работу), то служебная шина автоматически разблокирует сообщение и оно станет доступным для повторного получения.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода **deleteMessage**, сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

## <a name="delete-topics-and-subscriptions"></a>Удаление разделов и подписок

Разделы и подписки хранятся постоянно, и их нужно удалять явным образом на [классическом портале Azure][] или с помощью программных средств.
В следующем примере показано, как удалить раздел с именем `MyTopic`.

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности. В следующем примере показано, как удалить подписку с именем `HighMessages` из раздела `MyTopic`.

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## <a name="next-steps"></a>Дальнейшие действия

Вы узнали основные сведения о разделах служебной шины. Для получения дополнительных сведений используйте следующие ссылки.

-   См. статью [Очереди, разделы и подписки][].
-   Справочник API для [SqlFilter][].
-   Посетите репозиторий [Azure SDK for Node][] на веб-сайте GitHub.

  [Пакет SDK Azure для Node]: https://github.com/Azure/azure-sdk-for-node
  [классический портал Azure]: https://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Очереди, разделы и подписки]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Облачная служба Node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Облачная служба Node.js с хранилищем]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Веб-приложение Node.js с хранилищем]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
 



<!--HONumber=Oct16_HO2-->


