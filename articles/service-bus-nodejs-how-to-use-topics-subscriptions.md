<properties urlDisplayName="Service Bus Topics" pageTitle="Использование разделов Service Bus (Node.js) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic Node.js" description="Узнайте, как использовать разделы и подписки Service Bus в Azure. Примеры кода написаны для приложений Node.js." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Topics/Subscriptions" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# Использование разделов и подписок Service Bus

В этом руководстве показано, как использовать разделы и подписки Service Bus в приложениях Node.js В этой статье описаны такие сценарии, как **создание разделов и подписок, создание фильтров подписок, отправка сообщений** в раздел, **получение сообщений от подписки** и **удаление разделов и подписок**. Дополнительные сведения о разделах и подписках см. в разделе [Дальнейшие действия][].

## Оглавление

-   [Общие сведения о разделах и подписках Service Bus][]
-   [Создание пространства имен службы][]
-   [Получение учетных данных управления по умолчанию для пространства имен][]
-   [Создание приложения Node.js](#create-app)
-   [Настройка приложения для использования служебной шины](#configure-app)
-   [Практическое руководство. Создание раздела](#create-topic)
-   [Практическое руководство. Создание подписок](#create-subscription)
-   [Практическое руководство. Отправка сообщений в раздел](#send-messages)
-   [Практическое руководство. Получение сообщений от подписки](#receive-messages)
-   [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений](#handle-crashes)
-   [Практическое руководство. Удаление разделов и подписок](#delete)
-   [Дальнейшие действия](#next-steps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

##<a name="create-app"></a> Создание приложения Node.js

Создайте пустое приложение Node.js. Инструкции по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure], [Облачная служба Node.js][Node.js Cloud Service] (с помощью Windows PowerShell) или [Веб-сайт с WebMatrix].

##<a name="configure-app"></a> Настройка приложения для использования служебной шины

Чтобы использовать служебную шину Azure, необходимо скачать и запустить пакет Azure Node.js. Он содержит набор удобных библиотек, взаимодействующих со службами REST служебной шины.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  Используя интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите к папке, в которой вы создали пример приложения.

2.  Введите **npm install azure** в командной строке, чтобы получить такой результат:

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

3.  Можно вручную выполнить команду **ls**, чтобы убедиться, что папка **node\_modules** создана. Найдите в папке пакет **azure**, содержащий библиотеки, необходимые для доступа к разделам Service Bus.

### Импорт модуля

С помощью Блокнота или другого текстового редактора добавьте следующее в начало файла **server.js** приложения:

    var azure = require('azure');

### Настройка подключения к Service Bus Azure

Модуль azure будет считывать переменные среды AZURE\_SERVICEBUS\_NAMESPACE и AZURE\_SERVICEBUS\_ACCESS\_KEY для получения сведений, необходимых для подключения к служебной шине Azure. Если эти переменные среды не заданы, при вызове **createServiceBusService** необходимо указать сведения об учетной записи.

Пример настройки переменных среды в файле конфигурации для облачной службы Azure см. в статье [Облачная служба Node.js с хранилищем].

Пример настройки переменных среды на портале управления для веб-сайта Azure см. в статье [Веб-приложение Node.js с хранилищем]

##<a name="create-topic"></a> Создание раздела

Работать с разделами позволяет объект **ServiceBusService**. Следующий код создает объект **ServiceBusService**. Добавьте его в начало файла **server.js** после инструкции импорта модуля azure.

    var serviceBusService = azure.createServiceBusService();

Путем вызова **createTopicIfNotExists** в объекте **ServiceBusService**
будет возвращен указанный раздел(если он существует) или создан новый раздел с указанным именем. В приведенном ниже коде используется **createTopicIfNotExists** для создания раздела с именем MyTopic или подключения к нему.

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Topic was created or exists
            console.log('topic created or exists.');
        }
    });

Кроме того, **createServiceBusService** поддерживает дополнительные параметры, которые
позволяют переопределить параметры раздела по умолчанию, например срок жизни
сообщения или максимальный размер раздела. На следующем примере продемонстрирована
установка максимальных значений размера раздела (5 ГБ) и срока жизни (1 минута):

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

Дополнительные операции фильтрации можно применить к выполняемым операциям, используя **ServiceBusService** К операциям фильтрации могут относиться ведение журнала, автоматически повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

		function handle (requestOptions, next)

Выполнив предварительную обработку параметров запроса, метод должен вызвать "next", передавая обратный вызов со следующей сигнатурой:

		function (returnObject, finalCallback, next)

В этой функции обратного вызова и после обработки returnObject (ответ на запрос к серверу) функция обратного вызова должна либо вызвать "next", если он существует, чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать "finalCallback" для завершения обращения к службе.

В Azure SDK для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter** Следующий код создает объект **ServiceBusService**, использующий **ExponentialRetryPolicyFilter**

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

##<a name="create-subscription"></a> Создание подписок

Подписки раздела также создаются с помощью объекта **ServiceBusService**. У подписок есть имена, и они могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Подписки являются постоянными и продолжают существовать либо до их удаления, либо до удаления раздела, с которым они связаны. Если приложение содержит логику для создания подписки, оно сначала должно проверить, существует ли подписка, используя метод <strong>getSubscription</strong> .</p>
</div>

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В приведенном ниже примере создается подписка с именем AllMessages и используется фильтр по умолчанию **MatchAll**:

    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // subscription created
        }
    });

### Создание подписок с фильтрами

Вы также можете настроить фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в определенной подписке раздела.

Самый гибкий тип фильтра, который поддерживается подписками, - это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о выражениях, которые можно использовать с SQL-фильтром, см. в описании синтаксиса [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Добавить фильтры в подписку можно с помощью метода **createRule** объекта **ServiceBusService**. Этот метод позволяет добавлять новые фильтры в существующую подписку.

> [WACOM.NOTE]

> Так как фильтр по умолчанию автоматически применяется ко всем новым подпискам, необходимо сначала удалить фильтр по умолчанию. Иначе <strong>MatchAll</strong> сможет переопределить любой указанный фильтр. Правило по умолчанию можно удалить с помощью метода <strong>deleteRule</strong> объекта <strong>ServiceBusService</strong> .

В следующем примере создается подписка с именем HighMessages, содержащая фильтр **SqlFilter**, который выбирает только сообщения, значение настраиваемого свойства **messagenumber** которых превышает 3:

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

Аналогично, в следующем примере создается подписка с именем
LowMessages, содержащая фильтр **SqlFilter**, который выбирает только сообщения,
значение свойства **messagenumber** которых меньше или равно 3:

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

Теперь, если отправить сообщение в раздел MyTopic, оно будет доставлено всем получателям, владеющим подпиской раздела AllMessages, а также некоторым получателям с подписками разделов HighMessages и LowMessages (в зависимости от содержимого сообщения).

##<a name="send-messages"></a> Отправка сообщений в раздел

Чтобы отправить сообщение в раздел Service Bus, ваше приложение должно использовать метод **sendTopicMessage** объекта **ServiceBusService**. Сообщения, отправленные в разделы Service Bus, являются объектами **BrokeredMessage**. **Объекты BrokeredMessage** содержат набор стандартных свойств (например, **Label** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из строки данных. Приложение может задать текст сообщения, передав текстовую строку в метод **sendTopicMessage**, и все обязательные стандартные свойства будут заполнены значениями по умолчанию.

На приведенном ниже примере продемонстрирована отправка тестового сообщения в раздел MyTopic. Обратите внимание: значения свойств **messagenumber** сообщений зависят от итерации цикла (это определяет получающую подписку).

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

Разделы Service Bus поддерживают максимальный размер сообщения в 256 МБ (максимальный размер заголовка, содержащего стандартные и настраиваемые свойства приложения - 64 МБ). Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ.

##<a name="receive-messages"></a> Получение сообщений из подписки

Сообщения извлекаются из подписки с помощью метода **receiveSubscriptionMessage** в объекте **ServiceBusService**. По умолчанию прочитанные сообщения удаляются из подписки, однако сообщение можно просмотреть (с помощью функции peek) и заблокировать, не удаляя его из подписки, установив для необязательного параметра **isPeekLock** значение **true**.

Поведение по умолчанию - чтение и удаление сообщения как часть операции получения - это простейшая модель, оптимальная для сценариев,в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

Если для параметра **isPeekLock** установлено значение **true**, получение становится двухэтапной операцией, что позволяет поддерживать приложения, которые не могут пропускать сообщения. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Обработав сообщение (или сохранив его в надежном месте для последующей обработки), приложение вызывает метод **deleteMessage** и указывает сообщение, которое будет удалено, как параметр, таким образом завершая второй этап процесса получения. Метод **deleteMessage** помечает сообщение как использованное и удаляет его из подписки.

На приведенном ниже примере продемонстрировано получение сообщений и их обработка с помощью метода **receiveSubscriptionMessage**. Сначала сообщение поступает из подписки LowMessages и удаляется, а затем - из подписки HighMessages, если для параметра **isPeekLock** установлено значение true. Затем сообщение удаляется с помощью метода **deleteMessage**:

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

Service Bus предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если по какой-либо причине приложению-получателю не удается обработать сообщение, оно вызывает метод **unlockMessage** в объекте **ServiceBusService**. Таким образом, служебная шина разблокирует сообщение в подписке и сделает его доступным для приема тем же или другим приложением.

Кроме того, с сообщением, заблокированным в подписке, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), служебная шина автоматически разблокирует сообщение и сделает его доступным для приема.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода **deleteMessage**, сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

##<a name="delete"></a> Удаление разделов и подписок

Разделы и подписки - это устойчивые объекты, которые необходимо явно удалять
на портале управления Azure или программным методом.
В приведенном ниже примере показано, как удалить раздел с именем "MyTopic".

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности. На следующем примере продемонстрировано удаление подписки HighMessages из раздела MyTopic:

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

##<a name="next-steps"></a> Дальнейшие действия

Вы узнали основные сведения о разделах Service Bus. Для получения дополнительных сведений используйте следующие ссылки.

-   См. справочник MSDN: [Очереди, разделы и подписки][].
-   Справочник по API для [SqlFilter][].
-   Посетите репозиторий [пакета SDK Azure для Node] на портале GitHub.

  [SDK Azure для Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Дальнейшие действия]: #nextsteps
  [Общие сведения о разделах и подписках Service Bus]: #what-are-service-bus-topics
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Создание приложения Node.js]: #Create_a_Nodejs_Application
  [Настройка приложения для использования служебной шины]: #Configure_Your_Application_to_Use_Service_Bus
  [Практическое руководство. Создание раздела]: #How_to_Create_a_Topic
  [Практическое руководство. Создание подписок]: #How_to_Create_Subscriptions
  [Практическое руководство. Отправка сообщений в раздел]: #How_to_Send_Messages_to_a_Topic
  [Практическое руководство. Получение сообщений от подписки]: #How_to_Receive_Messages_from_a_Subscription
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Практическое руководство. Удаление разделов и подписок]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Портал управления Azure]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Очереди, разделы и подписки]: http://msdn.microsoft.com/ru-ru/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Веб-сайт с WebMatrix]: /ru-ru/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Облачная служба Node.js]: /ru-ru/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
  [Облачная служба Node.js с хранилищем]: /ru-ru/develop/nodejs/tutorials/web-app-with-storage/
  [Веб-приложение Node.js с хранилищем]: /ru-ru/develop/nodejs/tutorials/web-site-with-storage/

<!--HONumber=35.2-->
