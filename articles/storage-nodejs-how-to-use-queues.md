<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Node.js) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Node.js" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Queue Service from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# Использование службы очередей в Node.js

В этом руководстве показано, как реализовать типичные сценарии с использованием службы очередей Windows Azure. Примеры написаны с помощью Node.jsAPI. Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание и удаление очередей**. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия][Дальнейшие действия].

## Оглавление

-   [Что такое служба очередей][Что такое служба очередей]
-   [Основные понятия][Основные понятия]
-   [Создание учетной записи хранения Azure][Создание учетной записи хранения Azure]
-   [Создание приложения Node.js][Создание приложения Node.js]
-   [Настройка приложения для доступа к хранилищу][Настройка приложения для доступа к хранилищу]
-   [Настройка строки подключения к хранилищу Azure][Настройка строки подключения к хранилищу Azure]
-   [Практическое руководство. Создать очередь][Практическое руководство. Создать очередь]
-   [Практическое руководство. Вставка сообщения в очередь][Практическое руководство. Вставка сообщения в очередь]
-   [Практическое руководство. Просмотр следующего сообщения][Практическое руководство. Просмотр следующего сообщения]
-   [Практическое руководство. Удаление следующего сообщения из очереди][Практическое руководство. Удаление следующего сообщения из очереди]
-   [Практическое руководство. Изменение содержимого сообщения в очереди][Практическое руководство. Изменение содержимого сообщения в очереди]
-   [Практическое руководство. Дополнительные параметры для удаления сообщений из очереди][Практическое руководство. Дополнительные параметры для удаления сообщений из очереди]
-   [Практическое руководство. Получение длины очереди][Практическое руководство. Получение длины очереди]
-   [Практическое руководство. Удаление очереди][Практическое руководство. Удаление очереди]
-   [Практическое руководство. Работа с подписями общего доступа][Практическое руководство. Работа с подписями общего доступа]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [руководство-очередь-хранилище](../includes/howto-queue-storage.md)]

## <a name="create-account"></a>Создайте учетную запись хранения Azure

[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Создание приложения Node.js

Создайте пустое приложение Node.js. Инструкции по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure][Создание и развертывание приложения Node.js на веб-сайте Azure], [Облачная служба Node.js][Облачная служба Node.js] (с помощью Windows PowerShell) или [Веб-сайт с WebMatrix][Веб-сайт с WebMatrix].

## <a name="configure-access"> </a>Настройка приложения для доступа к хранилищу

Для использования хранилища Azure необходимо загрузить и использовать пакет Azure Storage SDK для Node.js, который содержит набор удобных библиотек, взаимодействующих со службами REST хранилища.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  Используя интерфейс командной строки, такой как **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите в папку, в которой создан пример приложения.

2.  Введите в командной строке **npm install azure-storage**, что должно привести к следующему результату:

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Можно вручную выполнить команду **ls**, чтобы убедиться в создании папки **node\_modules**. В этой папке находится пакет **azure-storage**, содержащий библиотеки, необходимые для доступа к хранилищу.

### Импорт пакета

Используйте Блокнот или другой текстовый редактор, чтобы добавить следующий код в начало файла **server.js** приложения, где планируется использовать хранилище.

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Настройка подключения к хранилищу Azure

Модуль Azure считывает переменные среды AZURE\\\_STORAGE\\\_ACCOUNT и AZURE\\\_STORAGE\\\_ACCESS\\\_KEY или AZURE\_STORAGE\_CONNECTION\_STRING для получения сведений, необходимых для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, при вызове **createQueueService** необходимо указать сведения об учетной записи.

Пример настройки переменных среды на портале управления для веб-сайта Azure см. в статье [Веб-приложение Node.js с хранилищем][Веб-приложение Node.js с хранилищем]

## <a name="create-queue"> </a>Практическое руководство. Создание очереди

Следующий пример кода создает объект **QueueService**, который позволяет работать с очередями.

    var queueSvc = azure.createQueueService();

Используйте метод **createQueueIfNotExists**, который возвращает указанную очередь, если она уже существует, или создает новую очередь с указанным именем, если она отсутствует.

    queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
      }
    });

Если очередь создана, значение `result` истинно. Если очередь уже существует, значение `result` ложно.

### Фильтры

Дополнительные операции фильтрации можно применить к выполняемым операциям, используя **QueueService**. К операциям фильтрации могут относиться ведение журнала, автоматически повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

        function handle (requestOptions, next)

Выполнив предварительную обработку параметров запроса, метод должен вызвать "next", передавая обратный вызов со следующей сигнатурой:

        function (returnObject, finalCallback, next)

В этой функции обратного вызова и после обработки returnObject (ответ на запрос к серверу) функция обратного вызова должна либо вызвать "next", если он существует, чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать "finalCallback" для завершения обращения к службе.

В Azure SDK для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter** Следующий код создает объект **QueueService**, использующий **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueSvc = azure.createQueueService().withFilter(retryOperations);

## <a name="insert-message"> </a>Практическое руководство. Вставка сообщения в очередь

Чтобы вставить сообщение в очередь, используйте метод **createMessage** для создания нового сообщения и добавления его в очередь.

    queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
      if(!error){
        // Message inserted
      }
    });

## <a name="peek-message"> </a>Практическое руководство. Просмотр следующего сообщения

Вы можете просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод **peekMessages**. По умолчанию **peekMessages** просматривает одно сообщение.

    queueSvc.peekMessages('myqueue', function(error, result, response){
      if(!error){
        // Messages peeked
      }
    });

`result` содержит сообщение.

> [WACOM.NOTE] Если использовать **peekMessage**, когда в очереди нет сообщений, сообщение об ошибке не отображается, однако сообщения не возвращаются.

## <a name="get-message"> </a>Практическое руководство. Удаление следующего сообщения из очереди

Обработка сообщения выполняется двухэтапным процессом:

1.  удаление сообщения из очереди.

2.  удаление сообщения.

Чтобы удалить сообщение из очереди, используйте **getMessage**. В результате сообщение становится невидимым в очереди, и другие клиенты не могут его обрабатывать. После того, как приложение обработало сообщение, вызовите **deleteMessage** для его удаления из очереди. Следующий пример получает сообщение, а затем удаляет его:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // message dequed
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageid, message.popreceipt, function(error, response){
          if(!error){
            //message deleted
          }
        });
      }
    });

> [WACOM.NOTE] По умолчанию сообщение остается скрытым в течение 30 секунд, а потом снова становится видимым для других клиентов. Можно задать другое значение, используя `options.visibilityTimeout` с **getMessages**.

> [WACOM.NOTE]
>  Если использовать **getMessages**, когда в очереди нет сообщений, сообщение об ошибке не отображается, однако сообщения не возвращаются.

## <a name="change-contents"> </a>Практическое руководство. Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди с помощью **updateMessage**. Следующий пример обновляет текст сообщения:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Got the message
        var message = result[0];
        queueSvc.updateMessage('myqueue', message.messageid, message.popreceipt, 10, {messageText: 'new text'}, function(error, result, response){
          if(!error){
            // Message updated successfully
          }
        });
      }
    });

## <a name="advanced-get"> </a>Практическое руководство. Дополнительные параметры для удаления сообщений из очереди

Существует два способа настройки извлечения сообщения из очереди:

-   `options.numOfMessages` — извлекает пакет сообщений (до 32.)
-   `options.visibilityTimeout` — задает более длительное или короткое время ожидания невидимости.

В следующем примере кода метод **getMessages** используется для получения 15 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла for. Он также задает время ожидания невидимости пять минут для всех сообщений, возвращенных методом.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
      if(!error){
        // Messages retreived
        for(var index in result){
          // text is available in result[index].messageText
          var message = result[index];
          queueSvc.deleteMessage(queueName, message.messageid, message.popreceipt, function(error, response){
            if(!error){
              // Message deleted
            }
          });
        }
      }
    });

## <a name="get-queue-length"> </a>Практическое руководство. Получение длины очереди

Метод **getQueueMetadata** возвращает метаданные очереди, в том числе приблизительное количество сообщений, ожидающих в очереди.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
      if(!error){
        // Queue length is available in result.approximatemessagecount
      }
    });

## <a name="list-queue"> </a>Практическое руководство. Перечисление очередей

Чтобы извлечь список очередей, используйте **listQueuesSegmented**. Чтобы извлечь список, отфильтрованный по определенному префиксу, используйте **listQueuesSegmentedWithPrefix**.

    queueSvc.listQueuesSegmented(null, function(error, result, response){
      if(!error){
        // result.entries contains the list of queues
      }
    });

Если не удается вернуть все очереди, `result.continuationToken` может использоваться в качестве первого параметра **listQueuesSegmented** или второго параметра **listQueuesSegmentedWithPrefix** для получения дополнительных результатов.

## <a name="delete-queue"> </a>Практическое руководство. Удаление очереди

Для удаления очереди и всех сообщений в ней вызовите метод **deleteQueue** в объекте очереди.

    queueSvc.deleteQueue(queueName, function(error, response){
        if(!error){
            // Queue has been deleted
        }
    });

Чтобы удалить все сообщения очереди без удаления самой очереди, используйте метод **clearMessages**.

## <a name="sas"></a>Практическое руководство. Работа с подписями общего доступа

Подписи общего доступа (SAS) — безопасный способ предоставить детальный доступ к очередям без указания имени или ключей своей учетной записи хранения. SAS часто используется для предоставления ограниченного доступа к очередям, например, позволяет мобильному приложению отправлять сообщения.

Надежное приложение, например, облачная служба, создает подпись SAS с помощью **generateSharedAccessSignature** службы **QueueService** и предоставляет ее ненадежному или полунадежному приложению. Например, мобильному приложению. Подпись SAS создается с использованием политики, которая описывает даты начала и окончания срока действия SAS, а также уровень доступа, который предоставляется держателю подписи SAS.

В следующем примере создается новая общая политика, которая позволяет держателю подписи SAS добавлять сообщения в очередь в течение 100 минут с момента своего создания.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

    var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
    var host = queueSvc.host;

Обратите внимание, что также должна быть предоставлена информация узла, поскольку она требуется держателю SAS для совершения попыток доступа к очереди.

Клиентское приложение далее использует подпись SAS с помощью **QueueServiceWithSAS** для выполнения операций с очередью. Следующий пример выполняет подключение к очереди и создает сообщение.

    var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
    sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
      if(!error){
        //message added
      }
    });

Поскольку подпись SAS была создана только для доступа добавления, если выполняется попытка чтения, обновления или удаления сообщений, будет возвращена ошибка.

### Доступ к спискам управления

Можно также использовать список управления доступом (ACL) для задания политики доступа подписи SAS. Это может оказаться полезным, когда необходимо предоставить доступ к очереди нескольким клиентам, но с различной политикой доступа для каждого из них.

ACL реализуется с помощью массива политик доступа, каждая из которых связана со своим идентификатором. В следующем примере определяются две политики — для пользователей 'user1' и 'user2':

    var sharedAccessPolicy = [
      {
        AccessPolicy: {
          Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user1'
      },
      {
        AccessPolicy: {
          Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user2'
      }
    ];

В этом примере код получает текущее значение ACL для **myqueue**, а затем добавляет новые политики с помощью **setQueueAcl**. Такой подход допускает выполнение:

    queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
        //push the new policy into signedIdentifiers
        result.signedIdentifiers.push(sharedAccessPolicy);
        queueSvc.setQueueAcl('myqueue', result, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

После задания ACL можно создать подпись SAS на основе идентификатора политики. В следующем примере создается новая подпись SAS для пользователя 'user2':

    queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## <a name="next-steps"> </a> Дальнейшие действия

Вы изучили основные сведения о хранилище очередей. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure][Хранение данных и доступ к ним в Azure].
-   Посетите [блог команды разработчиков хранилища Azure][блог команды разработчиков хранилища Azure].
-   Посетите репозиторий [Пакет SDK хранилища Azure для Node][Пакет SDK хранилища Azure для Node] на веб-сайте GitHub.

  [Дальнейшие действия]: #next-steps
  [Что такое служба очередей]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Создание приложения Node.js]: #create-app
  [Настройка приложения для доступа к хранилищу]: #configure-access
  [Настройка строки подключения к хранилищу Azure]: #setup-connection-string
  [Практическое руководство. Создать очередь]: #create-queue
  [Практическое руководство. Вставка сообщения в очередь]: #insert-message
  [Практическое руководство. Просмотр следующего сообщения]: #peek-message
  [Практическое руководство. Удаление следующего сообщения из очереди]: #get-message
  [Практическое руководство. Изменение содержимого сообщения в очереди]: #change-contents
  [Практическое руководство. Дополнительные параметры для удаления сообщений из очереди]: #advanced-get
  [Практическое руководство. Получение длины очереди]: #get-queue-length
  [Практическое руководство. Удаление очереди]: #delete-queue
  [Практическое руководство. Работа с подписями общего доступа]: #sas
  [руководство-очередь-хранилище]: ../includes/howto-queue-storage.md
  [создание-хранилище-учетная запись]: ../includes/create-storage-account.md
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: /ru-ru/documentation/articles/web-sites-nodejs-develop-deploy-mac/
  [Облачная служба Node.js]: /ru-ru/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Веб-сайт с WebMatrix]: /ru-ru/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Веб-приложение Node.js с хранилищем]: /ru-ru/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Пакет SDK хранилища Azure для Node]: https://github.com/Azure/azure-storage-node
