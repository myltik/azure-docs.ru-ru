<properties urlDisplayName="Service Bus Topics" pageTitle="Как использовать разделы шины обслуживания (Node.js) - Azure" metaKeywords= "Приступая к работе с разделами шины обслуживания Azure, начало работы с разделами шины обслуживания, обмен сообщениями при подписке на публикации обмена сообщениями Azure, разделы сообщений и подписки Azure, раздел шины обслуживания Node.js" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Node.js applications." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Topics/Subscriptions" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# Использование разделов и подписок Service Bus

В этом руководстве описано, как использовать разделы и подписки шины обслуживания из приложений Node.js. В этой статье описаны такие сценарии, как **создание разделов и подписок, создание фильтров подписок, отправка сообщений** в раздел, **получение сообщений от подписки** и **удаление разделов и подписок**. Дополнительные сведения о разделах и подписках см. в разделе [Дальнейшие действия][].

## Оглавление

-   [Что такое разделы и подписки шины обслуживания?][]
-   [Создание пространства имен службы][]
-   [Получение учетных данных для управления по умолчанию для пространства имен][]
-   [Создание приложения Node.js](#create-app)
-   [Настройка приложения для использования шины обслуживания](#configure-app)
-   [Практическое руководство. Создание раздела](#create-topic)
-   [Практическое руководство. Создание подписок](#create-subscription)
-   [Практическое руководство. Отправка сообщений в раздел](#send-messages)
-   [Практическое руководство. Получение сообщений от подписки](#receive-messages)
-   [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений](#handle-crashes)
-   [Практическое руководство. Удаление разделов и подписок](#delete)
-   [Дальнейшие действия](#next-steps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

##<a name="create-app"></a> Создание приложения Node.js

Создание пустого приложения Node.js. Инструкции по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure], [Облачная служба Node.js][Node.js Cloud Service] (с помощью Windows PowerShell) или [Веб-сайт с WebMatrix].

##<a name="configure-app"></a> Настройка приложения для использования шины обслуживания

Чтобы использовать шину обслуживания Azure, необходимо загрузить и использовать пакет Node.js azure. Он содержит набор удобных библиотек, взаимодействующих со службами REST шины обслуживания.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  В интерфейсе командной строки, например **PowerShell** (Windows) **Terminal** (Mac) или **Bash** (Unix), перейдите в папку, в которой создан пример приложения.

2.  В командном окне введите **npm install azure** в результате чего
    на выходе должно получиться следующее:

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

3.   Можно вручную выполнить команду **ls**, чтобы убедиться в создании папки **node\_modules**. В этой папке находится пакет **azure**, содержащий библиотеки, необходимые для доступа к разделам шины обслуживания.

### Импорт модуля

С помощью программы Блокнот или другого текстового редактора добавьте следующее в начало
файла приложения **server.js**:

    var azure = require('azure');

### Настройка подключения к шине обслуживания Azure

Модуль Azure будет считывать переменные среды AZURE_SERVICEBUS_NAMESPACE и AZURE_SERVICEBUS_ACCESS_KEY для получения сведений, необходимых для подключения к служебной шине в Azure. Если эти переменные среды не заданы, при вызове **createServiceBusService** необходимо указать сведения об учетной записи.

Пример настройки переменных среды в файле конфигурации для облачной службы Azure см. в статье [Облачная служба Node.js с хранилищем].

Пример настройки переменных среды на портале управления для веб-сайта Azure см. в статье [Веб-приложение Node.js с хранилищем]

##<a name="create-topic"></a> Создание раздела

Объект **ServiceBusService** позволяет работать с разделами. Следующий код создает объект **ServiceBusService**. Добавьте его в начало файла **server.js** после инструкции для импорта модуля azure:

    var serviceBusService = azure.createServiceBusService();

При вызове **createTopicIfNotExists** для объекта **ServiceBusService** возвращается указанный раздел (если он существует) или создается новый раздел с указанным именем. В следующем коде **createTopicIfNotExists** используется для создания раздела с именем MyTopic или подключения к нему:

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Topic was created or exists
            console.log('topic created or exists.');
        }
    });

**createServiceBusService** также поддерживает дополнительные параметры, позволяющие переопределить настройки раздела по умолчанию, такие как срок жизни сообщения или максимальный размер раздела. В следующем примере показано, как установить максимальный размер раздела 5 ГБ и срок жизни 1 минута.

    var topicOptions = {
            MaxSizeInMegabytes: '5120',
            DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
        if(!error){
            // topic was created or exists
        }
    });

###Фильтры

Дополнительные операции фильтрации можно применить к выполняемым операциям, используя **ServiceBusService**. К операциям фильтрации могут относиться ведение журнала, автоматически повтор и т. д. Фильтры являются объектами, реализующими метод с сигнатурой:

		function handle (requestOptions, next)

Выполнив предварительную обработку параметров запроса, метод должен вызвать "next", передавая обратный вызов со следующей сигнатурой:

		function (returnObject, finalCallback, next)

В этой функции обратного вызова и после обработки returnObject (ответ на запрос к серверу) функция обратного вызова должна либо вызвать "next", если он существует, чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать "finalCallback" для завершения обращения к службе.

В Azure SDK для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter**. Следующий код создает объект **ServiceBusService**, использующий **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

##<a name="create-subscription"></a> Создание подписок

Подписки разделов также создаются с помощью объекта **ServiceBusService**. Подписки имеют имена и могут использовать дополнительный фильтр, который ограничивает набор сообщений, доставляемых в виртуальную очередь подписки.

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Подписки являются постоянными и продолжают существовать либо до их удаления, либо до удаления раздела, с которым они связаны. Если приложение содержит логику для создания подписки, оно сначала должно проверить, существует ли подписка, используя метод <strong>getSubscription</strong>.</p>
</div>

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** используется по умолчанию, если при создании подписки не указать фильтр. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка с именем AllMessages и используется фильтр по умолчанию **MatchAll**. 
    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // subscription created
        }
    });

### Создание подписок с фильтрами

Вы также можете настроить фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в определенной подписке раздела.

Самый гибкий тип фильтра, который поддерживается подписками - это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые публикуются в разделе. Дополнительные сведения о выражениях, которые можно использовать с фильтром SQL, см. в описании синтаксиса [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Фильтры можно добавить в подписку с помощью метода **createrule** объекта **ServiceBusService**. Этот метод позволяет добавлять новые фильтры в существующую подписку.

> [WACOM.NOTE]

> Поскольку ко всем новым подпискам автоматически применяется фильтр по умолчанию, сначала необходимо удалить фильтр по умолчанию, иначе <strong>MatchAll</strong> переопределит все другие заданные фильтры. Вы можете удалить правило по умолчанию с помощью метода <strong>deleterule</strong> объекта <strong>ServiceBusService</strong>.

В следующем примере создается подписка с именем HighMessages и 
**SqlFilter**, который выбирает только сообщения со значением пользовательского свойства
**messagenumber** больше 3.

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

Аналогичным образом в следующем примере создается подписка с именем
LowMessages и **SqlFilter**, который выбирает только сообщения, имеющие
значение свойства **messagenumber**, меньшее или равное 3.

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

Если теперь сообщение будет отправлено в раздел "mytopic", оно всегда будет доставляться получателям, подписанным на подписку раздела "AllMessages", и выборочно доставляться получателям, подписанным на подписки разделов "HighMessages" и "LowMessages" (в зависимости от содержимого сообщений).

##<a name="send-messages"></a> Отправка сообщений в раздел

Чтобы отправить сообщение в раздел шины обслуживания, приложение должно использовать метод **sendTopicMessage** объекта **ServiceBusService**. Сообщения, отправленные в разделы шины обслуживания, являются объектами **BrokeredMessage**. У объектов **BrokeredMessage** есть набор стандартных свойств (например, **Label** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из строковых данных. Приложение может задать текст сообщения, передав строковое значение в метод **sendTopicMessage**, а все необходимые стандартные свойства будут заполнены значениями по умолчанию.

В следующем примере показано, как отправить пять тестовых сообщений в раздел MyTopic. Обратите внимание, что значение настраиваемого свойства **messagenumber** каждого сообщения зависит от итерации цикла (это определяет, получит ли подписка сообщение).

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
Разделы Service Bus поддерживают максимальный размер сообщения 256 МБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, - 64 МБ). Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер задается при создании с верхним пределом 5 ГБ.

##<a name="receive-messages"></a> Получение сообщений от подписки

Сообщения извлекаются из подписки с помощью метода **receiveSubscriptionMessage** на объекте **ServiceBusService**. По умолчанию прочитанные сообщения удаляются из подписки. Но можно прочитать (извлечь) сообщение и заблокировать его удаление из подписки, не удаляя его, задав для необязательного параметра**isPeekLock** значение **true**.

Поведение по умолчанию - чтение и удаление сообщения как часть операции получения - является простейшей моделью, оптимальной для сценариев, в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором клиент выдает запрос на получение и выходит из строя до его обработки. Поскольку шина обслуживания помечает сообщение как используемое, то после того, как приложение перезапускается и снова начинает обрабатывать сообщения, оно пропустит сообщение, которое использовалось до сбоя.

Если параметр **isPeekLock** имеет значение **true**, получение становится операцией из двух этапов, что позволяет поддерживать приложения, не допускающие пропуска сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), оно завершает второй этап процесса получения, вызывая метод **deleteMessage** и указывая сообщение для удаления в качестве параметра. Метод **deleteMessage** помечает сообщение как использованное и удаляет его из подписки.

В примере ниже показано, как можно получать и обрабатывать сообщения с помощью метода **receiveSubscriptionMessage**. В этом примере приложение сначала получает и удаляет сообщение из подписки LowMessages,а затем получает сообщение из подписки HighMessages, устанавливая для **isPeekLock** значение true. Затем это сообщение удаляется с помощью метода **deleteMessage**.

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

##<a name="handle-crashes"></a> Обработка сбоев приложения и нечитаемых сообщений

Служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать метод **unlockMessage** объекта **ServiceBusService**. После этого шина обслуживания разблокирует сообщение в подписке и сделает его доступным для приема тем же приложением или другим использующим приложением.

Кроме того, с сообщением, заблокированным в подписке, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), шина обслуживания разблокирует сообщение автоматически и сделает его доступным
для повторного получения.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода **deleteMessage**, сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

##<a name="delete"></a> Удаление разделов и подписок

Разделы и подписки хранятся постоянно, и их нужно удалять явным образом на портале управления Azure или программными средствами. В приведенном ниже примере показано, как удалить раздел с именем MyTopic.

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

При удалении раздела также удаляются все подписки, которые зарегистрированы в разделе. Подписки также можно удалять по отдельности. В следующем примере кода показано, как удалить подписку с именем "HighMessages" из раздела "MyTopic".

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

##<a name="next-steps"></a> Дальнейшие действия

Вы узнали основные сведения о разделах Service Bus. Для получения дополнительных сведений используйте следующие ссылки.

-   Справочник MSDN: [Очереди, разделы и подписки][].
-   Справочник по API для [SqlFilter][].
-   Посетите репозиторий [Azure SDK for Node] на веб-сайте GitHub.

  [Azure SDK для Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Дальнейшие действия]: #nextsteps
  [Что такое разделы и подписки шины обслуживания?]: #what-are-service-bus-topics
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Создание приложения Node.js]: #Create_a_Nodejs_Application
  [Настройка приложения для использования шины обслуживания]: #Configure_Your_Application_to_Use_Service_Bus
  [Практическое руководство. Создание раздела]: #How_to_Create_a_Topic
  [Практическое руководство. Создание подписок]: #How_to_Create_Subscriptions
  [Практическое руководство. Отправка сообщений в раздел]: #How_to_Send_Messages_to_a_Topic
  [Практическое руководство. Получение сообщений от подписки]: #How_to_Receive_Messages_from_a_Subscription
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Практическое руководство. Удаление разделов и подписок]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Портал управления Azure]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [очереди, разделы и подписки]: http://msdn.microsoft.com/ru-ru/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Веб-сайт с WebMatrix]: /ru-ru/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Облачная служба Node.js]: /ru-ru/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
  [Облачная служба Node.js с хранилищем]: /ru-ru/develop/nodejs/tutorials/web-app-with-storage/
  [Веб-приложение Node.js с хранилищем]: /ru-ru/develop/nodejs/tutorials/web-site-with-storage/
