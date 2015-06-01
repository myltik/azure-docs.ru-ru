<properties 
	pageTitle="Использование хранилища очередей из Node.js в Microsoft Azure" 
	description="Вы узнаете, как использовать службы очередей Azure для создания и удаления очередей, вставки, получения и удаления сообщений. Примеры кода написаны на Node.js." 
	services="storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="mwasson"/>


# Использование хранилища очередей из Node.js

[AZURE.INCLUDE [storage-selector-queue-include](../includes/storage-selector-queue-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием службы
очередей Microsoft Azure. Примеры написаны с помощью API-интерфейса Node.js
API. Здесь описаны такие сценарии, как **вставка**, **просмотр**,
**получение** и **удаление** сообщений очереди, а также **создание и
удаление очередей**.

[AZURE.INCLUDE [storage-queue-concepts-include](../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Создание приложения Node.js

Создайте пустое приложение Node.js. Инструкции по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure], [Облачная служба Node.js][Облачная служба Node.js] (с использованием Windows PowerShell) или [Веб-сайт с WebMatrix].

## Настройка приложения для доступа к хранилищу

Для использования службы хранилища Azure необходим пакет SDK для службы хранилища Azure для Node.js, который содержит набор удобных библиотек,
взаимодействующих со службами REST хранилища.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  Используя интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите к папке, в которой вы создали пример приложения.

2.  Введите в командной строке **npm install azure-storage**,
    должно получиться следующее:

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Можно вручную выполнить команду **ls**, чтобы убедиться в создании папки
    **node_modules**. В этой папке находится
    пакет **azure-storage**, содержащий библиотеки, необходимые для
    доступа к хранилищу.

### Импорт пакета

С помощью программы Блокнот или другого текстового редактора добавьте следующее в начало
файла **Server.js** приложения, где планируется использовать хранилище:

    var azure = require('azure-storage');

## Настройка подключения к хранилищу Azure

Модуль Azure считывает переменные среды AZURE_STORAGE_ACCOUNT и AZURE_STORAGE_ACCESS_KEY или AZURE_STORAGE_CONNECTION_STRING для получения сведений, необходимых для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, при вызове **createQueueService** необходимо указать сведения об учетной записи.

Пример настройки переменных среды на портале управления для веб-сайта Azure см. в статье [Веб-приложение Node.js с хранилищем]

## Практическое руководство. Создание очереди

Следующий пример кода создает объект **QueueService**, который позволяет
работать с очередями.

    var queueSvc = azure.createQueueService();

Используйте метод **createQueueIfNotExists**, который возвращает указанную
очередь, если она уже существует, или создает новую очередь с указанным
именем, если она отсутствует.

	queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
	  }
	});

Если очередь создана, значение  `result` истинно. Если очередь уже существует, значение  `result` ложно.

### Фильтры

Дополнительные операции фильтрации можно применить к выполняемым операциям, используя **QueueService**. К операциям фильтрации могут относиться ведение журнала, автоматически повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

		function handle (requestOptions, next)

Выполнив предварительную обработку параметров запроса, метод должен вызвать "next", передавая обратный вызов со следующей сигнатурой:

		function (returnObject, finalCallback, next)

В этой функции обратного вызова и после обработки returnObject (ответ на запрос к серверу) функция обратного вызова должна либо вызвать "next", если он существует, чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать "finalCallback" для завершения обращения к службе.

В Azure SDK для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter**. Следующий код создает объект **QueueService**, использующий **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var queueSvc = azure.createQueueService().withFilter(retryOperations);

## Практическое руководство. Вставка сообщения в очередь

Чтобы вставить сообщение в очередь, используйте метод **createMessage** для
создания нового сообщения и добавления его в очередь.

	queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
	  if(!error){
	    // Message inserted
	  }
	});

## Практическое руководство. Просмотр следующего сообщения

Вы можете просмотреть сообщение в начале очереди, не удаляя его
из очереди, вызвав метод **peekMessages**. По умолчанию
**peekMessages** просматривает одно сообщение.

	queueSvc.peekMessages('myqueue', function(error, result, response){
	  if(!error){
		// Messages peeked
	  }
	});

 `result` содержит сообщение.

> [AZURE.NOTE] Если использовать **peekMessages**, когда в очереди нет сообщений, сообщение об ошибке не отображается, однако сообщения не возвращаются.

## Практическое руководство. Удаление следующего сообщения из очереди

Обработка сообщения выполняется двухэтапным процессом:

1. удаление сообщения из очереди.

2. удаление сообщения.

Чтобы удалить сообщение из очереди, используйте **getMessage**. В результате сообщение становится невидимым в очереди, и другие клиенты не могут его обрабатывать. После того как приложение обработало сообщение, вызовите метод **deleteMessage**, чтобы удалить его из очереди. Следующий пример получает сообщение, а затем удаляет его:

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

> [AZURE.NOTE] По умолчанию сообщение остается скрытым в течение 30 секунд, а потом снова становится видимым для других клиентов. Можно задать другое значение, используя `options.visibilityTimeout` с методом **getMessages**.

> [AZURE.NOTE]
> Если использовать метод <b>getMessages</b>, когда в очереди нет сообщений, сообщение об ошибке не отображается, однако сообщения не возвращаются.

## Практическое руководство. Изменение содержимого сообщения в очереди

Можно изменить содержимое сообщения непосредственно в очереди с помощью метода **updateMessage**. Следующий пример обновляет текст сообщения:

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

## Практическое руководство. Дополнительные параметры для удаления сообщений из очереди

Существует два способа настройки извлечения сообщения из очереди:

* options.numOfMessages - извлекает пакет сообщений (до 32).
* options.visibilityTimeout - задает более длительное или короткое время ожидания невидимости.

В следующем примере кода метод **getMessages** используется, чтобы получить 15 сообщений в одном вызове. Затем он обрабатывает
каждое сообщение с помощью цикла for. Он также задает время ожидания невидимости пять минут для всех сообщений, возвращенных методом.

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

## Практическое руководство. Получение длины очереди

Метод **getQueueMetadata** возвращает метаданные очереди, в том числе приблизительное количество сообщений, ожидающих в очереди.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
	  if(!error){
		// Queue length is available in result.approximatemessagecount
	  }
	});

## Практическое руководство. Перечисление очередей

Чтобы получить список очередей, используйте метод **listQueuesSegmented**. Чтобы извлечь список, отфильтрованный по определенному префиксу, используйте метод **listQueuesSegmentedWithPrefix**.

	queueSvc.listQueuesSegmented(null, function(error, result, response){
	  if(!error){
	    // result.entries contains the list of queues
	  }
	});

Если не удается вернуть все очереди, `result.continuationToken` можно использовать в качестве первого параметра **listQueuesSegmented** или второго параметра **listQueuesSegmentedWithPrefix** для получения дополнительных результатов.

## Практическое руководство. Удаление очереди

Для удаления очереди и всех сообщений в ней вызовите метод
**deleteQueue** в объекте очереди.

    queueSvc.deleteQueue(queueName, function(error, response){
		if(!error){
			// Queue has been deleted
		}
	});

Чтобы удалить все сообщения очереди, не удаляя саму очередь, используйте метод **clearMessages**.

## Практическое руководство. Работа с подписанными URL-адресами

Подписанный URL-адрес - безопасный способ предоставить детализированный доступ к очередям без указания имени или ключей своей учетной записи хранения. SAS часто используется для предоставления ограниченного доступа к очередям, например, позволяет мобильному приложению отправлять сообщения.

Надежное приложение, например облачная служба, создает подписанный URL-адрес с помощью метода **generateSharedAccessSignature** службы **QueueService** и предоставляет его ненадежному или частично надежному приложению. Например, мобильному приложению. Подпись SAS создается с использованием политики, которая описывает даты начала и окончания срока действия SAS, а также уровень доступа, который предоставляется держателю подписи SAS.

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

Затем клиентское приложение использует подписанный URL-адрес с помощью **QueueServiceWithSAS** для выполнения операций с очередью. Следующий пример выполняет подключение к очереди и создает сообщение.

	var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
	sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
	  if(!error){
	    //message added
	  }
	});

Поскольку подпись SAS была создана только для доступа добавления, если выполняется попытка чтения, обновления или удаления сообщений, будет возвращена ошибка.

### Доступ к спискам управления

Можно также использовать список управления доступом (ACL) для задания политики доступа подписи SAS. Это может оказаться полезным, когда необходимо предоставить доступ к очереди нескольким клиентам, но с различной политикой доступа для каждого из них.

ACL реализуется с помощью массива политик доступа, каждая из которых связана со своим идентификатором. В следующем примере определяются две политики - для пользователей user1 и user2:

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

## Дальнейшие действия

Вы получили основную информацию об управлении очередью хранилища. Перейдите по следующим ссылкам,
чтобы получить дополнительные сведения о более сложных задачах по использованию хранилища.

-   См. справочник MSDN: [Хранение и доступ к данным в Azure][].
-   Посетите [блог рабочей группы службы хранилища Azure][].
-   Посетите репозиторий [Пакет SDK для службы хранилища Azure для Node][] на веб-сайте GitHub.

  [Пакет SDK для службы хранилища Azure для Node]: https://github.com/Azure/azure-storage-node
  [Использование REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Портал управления Azure]: http://manage.windowsazure.com
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: web-sites-nodejs-develop-deploy-mac.md
  [Облачная служба Node.js с хранилищем]: storage-nodejs-use-table-storage-cloud-service-app.md
  [Веб-приложение Node.js с хранилищем]: storage-nodejs-use-table-storage-web-site.md

  
  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png
  
  
  
  [Облачная служба Node.js]: cloud-services-nodejs-develop-deploy-app.md
  [Хранение и доступ к данным в Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
 [Веб-сайт с WebMatrix]: web-sites-nodejs-use-webmatrix.md

<!--HONumber=49-->