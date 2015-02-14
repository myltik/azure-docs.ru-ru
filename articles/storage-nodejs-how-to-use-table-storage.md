<properties 
	pageTitle="Как использовать табличное хранилище (Node.js) - Microsoft Azure" 
	description="Вы узнаете, как использовать службу табличного хранилища в Azure. Примеры написаны с помощью Node.js API." 
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
	ms.date="09/17/2014" 
	ms.author="mwasson"/>

# Использование службы таблиц из Node.js

В этом руководстве показано, как реализовать типичные сценарии с использованием службы таблиц Microsoft Azure. Примеры написаны с помощью API Node.js. Здесь описаны такие сценарии, как **создание и удаление таблицы, вставка и запрос сущностей в таблице**. Дополнительную информацию о таблицах см. в разделе [Дальнейшие действия][].

## Оглавление

* [Что такое служба таблиц][]
* [Основные понятия][]
* [Создание учетной записи хранения Azure](#create-account)
* [Создание приложения Node.js](#create-app)
* [Настройка приложения для доступа к хранилищу](#configure-access)
* [Настройка подключения к службе хранилища Azure](#setup-connection-string)  
* [Практическое руководство. Создание таблицы](#create-table)
* [Практическое руководство. Добавление сущности в таблицу](#add-entity)
* [Практическое руководство. Обновление сущности](#update-entity)
* [Практическое руководство. Работа с группами сущностей](#change-entities)
* [Практическое руководство. Извлечение сущности](#query-for-entity)
* [Практическое руководство. Запрос набора сущностей](#query-set-entities)
* [Практическое руководство. Удаление сущности](#delete-entity)
* [Практическое руководство. Удаление таблицы](#delete-table)   
* [Практическое руководство. Работа с подписанными URL-адресами](#sas)
* [Дальнейшие действия][]

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a>Создание учетной записи хранения Azure</h2>

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Создание приложения Node.js

Создайте пустое приложение Node.js. Указания по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure], [Облачная служба Node.js][Node.js Cloud Service] (с помощью Windows PowerShell) или [Веб-сайт с WebMatrix].

## <a name="configure-access"> </a>Настройка приложения для доступа к хранилищу

Для использования службы хранилища Azure необходим пакет SDK для службы хранилища Azure для Node.js, который содержит набор удобных библиотек, взаимодействующих со службами REST хранилища.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  Используя интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите к папке, в которой вы создали пример приложения.

2.  Введите в окне командной строки **npm install azure-storage**, что должно привести к следующему результату:

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Можно вручную выполнить команду **ls**, чтобы убедиться, что папка **node\_modules** создана. В этой папке находится пакет **azure-storage**, содержащий библиотеки, необходимые для доступа к хранилищу.

### Импорт пакета

Используйте Блокнот или другой текстовый редактор, чтобы добавить следующий код в начало файла **server.js** приложения, где планируется использовать хранилище.

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Настройка подключения к службе хранилища Azure

Модуль Azure считывает переменные среды AZURE\_STORAGE\_ACCOUNT и AZURE\_STORAGE\_ACCESS\_KEY или AZURE\_STORAGE\_CONNECTION\_STRING для получения сведений, необходимых для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, при вызове **TableService** необходимо указать сведения об учетной записи.

Пример настройки переменных среды на портале управления для веб-сайта Azure см. в статье [Веб-приложение Node.js с хранилищем]

## <a name="create-table"> </a>Как создать таблицу

Следующий код создает объект **TableService** и использует его для создания новой таблицы. Добавьте в начало **server.js**следующий код.

    var tableSvc = azure.createTableService();

Вызов **createTableIfNotExists** создает новую таблицу с определенным именем, если она уже не существует. В следующем примере создается новая таблица с именем "mytable", если она еще не создана:

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
		if(!error){
			// Table exists or created
		}
	});

Значение  `result` будет равно  `true`, если создана новая таблица, и  `false`, если таблица уже существует.  `response` будет содержать информацию о запросе.

###Фильтры

Дополнительные операции фильтрации можно применить к выполняемым операциям, используя **TableService**. К операциям фильтрации могут относиться ведение журнала, автоматически повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

		function handle (requestOptions, next)

Выполнив предварительную обработку параметров запроса, метод должен вызвать "next", передавая обратный вызов со следующей сигнатурой:

		function (returnObject, finalCallback, next)

В этой функции обратного вызова и после обработки returnObject (ответ на запрос к серверу) функция обратного вызова должна либо вызвать "next", если он существует, чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать "finalCallback" для завершения обращения к службе.

В пакет SDK для Azure для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter**. Следующий код создает объект **TableService**, использующий фильтр **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-entity"> </a>Как добавить сущность в таблицу

Чтобы добавить сущность, сначала создайте объект, который определяет свойства сущности. Все сущности должны содержать **PartitionKey** и **RowKey**, которые представляют собой ее уникальные идентификаторы.

* **PartitionKey** - - определяет раздел, в котором хранится сущность.

* **RowKey** - уникально определяет сущность в разделе.

**PartitionKey** и **RowKey** должны быть строковыми значениями. Дополнительную информацию см. в разделе [Основные сведения о модели данных службы таблиц](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Ниже приводится пример задания сущности. Обратите внимание, что **dueDate** задается как тип **Edm.DateTime**. Задание типа необязательно, типы будут определяться, если они не заданы.

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
	};

> [AZURE.NOTE] Для каждой записи есть поле **Timestamp**, значение которого задается Azure при вставке или обновлении сущности.

Чтобы создать сущность, можно также использовать **entityGenerator**. В следующем примере код создает сущность для той же задачи с использованием **entityGenerator**.

	var entGen = azure.TableUtilities.entityGenerator;
    var task = {
	  PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Чтобы добавить сущность в таблицу, передайте объект сущности в метод **insertEntity**.

	tableSvc.insertEntity('mytable',task, function (error, result, response) {
		if(!error){
			// Entity inserted
		}
	});

Если операция успешна,  `result` будет содержать [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) вставленной записи, а  `response` - информацию об операции.

> [AZURE.NOTE] По умолчанию метод **insertEntity** не возвращает вставленную сущность как часть информации, содержащейся в  `response`. Если вы хотите выполнить другие операции с этой сущностью или кэшировать информацию, необходимо, чтобы она была возвращена как часть  `result`. Это можно сделать, используя параметр **echoContent**, как показано ниже:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-entity"> </a>Как обновить сущность

Для обновления имеющейся сущности доступно несколько методов:

* **updateEntity** - обновляет существующую сущность с ее заменой.

* **mergeEntity** - обновляет сущность путем объединения новых значений свойств с имеющейся сущностью.

* **insertOrReplaceEntity** - обновляет существующую сущность с ее заменой. Если сущность не существует, будет вставлена новая сущность.

* **insertOrMergeEntity** - обновляет сущность путем объединения новых значений свойств с имеющейся сущностью. Если сущность не существует, будет вставлена новая сущность.

В следующем примере показано обновление сущности с помощью метода **updateEntity**:

	tableSvc.updateEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] По умолчанию обновление сущности не проверяет, были ли обновляемые данные ранее изменены другим процессом. Поддержка одновременных обновлений:
> 
> 1. Получите ETag обновляемого объекта. Он возвращается в составе  `response` для всех операций с сущностями и может быть извлечен с помощью  `response['.metadata'].etag`.
> 
> 2. При выполнении операции обновления с сущностью предварительно добавьте информацию ETag, извлеченную для новой сущности. Например:
> 
>     `entity2['.metadata'].etag = currentEtag;`
>    
> 3. Выполните операцию обновления. Если сущность была изменена с момента получения значения ETag, например, другим экземпляром вашего приложения, будет возвращена  `error`, указывающая, что определенное в запросе условие обновления не выполнено.
    
Если обновляемая сущность не существует, при использовании **updateEntity** и **mergeEntity** операция обновления завершается ошибкой. Поэтому, если нужно сохранить сущность независимо от того, существует ли она, следует использовать **insertOrReplaceEntity** или **insertOrMergeEntity**.

 `result` должен содержать значение **Etag** обновленной сущности в случае успешного выполнения операций.

## <a name="change-entities"></a>Работа с группами сущностей

Иногда имеет смысл отправлять совместно несколько операций в пакете для атомарной обработки сервером. Чтобы сделать это, используйте класс **TableBatch** для создания пакета, а затем метод **executeBatch** службы **TableService** для выполнения пакетных операций.

 В следующем примере показана отправка двух сущностей в пакете:

    var task1 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'Take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};
	var task2 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '2'},
	  description: {'_':'Wash the dishes'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};

	var batch = new azure.TableBatch();
	
	batch.insertEntity(task1, {echoContent: true});
	batch.insertEntity(task2, {echoContent: true});

	tableSvc.executeBatch('mytable', batch, function (error, result, response) {
	  if(!error) {
	    // Batch completed
	  }
	});

В успешных пакетных операциях  `result` содержит информацию о всех операциях в пакете.

###Работа с пакетными операциями

Операции, добавленные в пакет, можно проверить, просмотрев свойство  `operations`. Для работы с операциями можно также использовать следующие методы.

* **clear** - удаляет все операции из пакета.

* **getOperations** - получает операцию из пакета.

* **hasOperations** - возвращает значение true, если пакет содержит операции.

* **removeOperations** - удаляет операцию.

* **size** - возвращает количество операций в пакете.

## <a name="query-for-entity"></a>Практическое руководство. Извлечение сущности

Если необходимо возвратить определенную сущность на основе значений **PartitionKey** и **RowKey**, используйте метод **retrieveEntity**.

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
	  if(!error){
	    // result contains the entity
	  }
    });

После завершения этой операции  `result` будет содержать сущность.

## <a name="query-set-entities"> </a>Как запросить набор сущностей

Чтобы запросить таблицу, используйте объект **TableQuery** для создания выражения запроса с помощью следующих предложений:

* **select** - возвращаемые запросом поля;

* **where** - предложение where;

	* **and** - условие  `and`;

	* **or** - условие  `or`;

* **top** - количество извлекаемых элементов.


В следующем примере создается запрос который возвращает первые 5 элементов с использованием ключа PartitionKey со значением hometasks.

	var query = new azure.TableQuery()
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

Так как параметр **select** не используется, возвращаются все поля. Чтобы выполнить запрос сущности в таблице, используйте **queryEntities**. В следующем примере используется запрос для возврата сущностей из таблицы mytable.

	tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
	  if(!error) {
	    // query was successful
	  }
	});

В случае успешного выполнения  `result.entries` будет содержать массив сущностей, соответствующих запросу. Если запросу не удалось вернуть все сущности,  `result.continuationToken` можно использовать как третий параметр метода **queryEntities** для получения дополнительных результатов. В начальном запросе второму параметру должно быть присвоено значение  *NULL*.

###Практическое руководство. Запрос подмножества свойств сущности

Запрос к таблице может получить лишь несколько полей сущности.
Этот позволяет снизить потребление пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Используйте предложение **select**, чтобы передать имена возвращаемых полей. Например, следующий запрос возвратит только поля **description** и **dueDate** 

	var query = new azure.TableQuery()
	  .select(['description', 'dueDate'])
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-entity"> </a>Как удалить сущность

Сущность можно удалить с помощью ее ключей раздела и строки. В этом примере объект **task1** содержит значения **RowKey** и
**PartitionKey** сущности, которая будет удалена. Затем этот объект передается методу **deleteEntity**.

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'}
	};

    tableSvc.deleteEntity('mytable', task, function(error, response){
	  if(!error) {
		// Entity deleted
	  }
	});

> [AZURE.NOTE] Следует рассмотреть использование ETag при удалении элементов, чтобы гарантировать отсутствие в нем изменений сделанных другим процессом. См. [Практическое руководство. Обновление сущности][], чтобы получить информацию об использовании ETag.

## <a name="delete-table"> </a>Практическое руководство. Удаление таблицы

Следующий код удаляет таблицу из учетной записи хранения.

    tableSvc.deleteTable('mytable', function(error, response){
		if(!error){
			// Table deleted
		}
	});

Если неизвестно, существует ли таблица, используйте **deleteTableIfExists**.

## <a name="sas"></a>Практическое руководство. Работа с подписанными URL-адресами

Подписанный URL-адрес - безопасный способ предоставить детализированный доступ к таблицам без указания имени или ключей своей учетной записи хранения. SAS часто используется для предоставления ограниченного доступа к данным, например, позволяет мобильному приложению запрашивать записи.

Надежное приложение, например облачная служба, создает подписанный URL-адрес с помощью метода **generateSharedAccessSignature** службы **TableService**, и предоставляет ее ненадежному или частично надежному приложению. Например, мобильному приложению. Подпись SAS создается с использованием политики, которая описывает даты начала и окончания срока действия SAS, а также уровень доступа, который предоставляется держателю подписи SAS.

В следующем примере создается новая общая политика, которая позволяет держателю подписи SAS запрашивать ('r') в таблице в течение 100 минут с момента своего создания.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
		
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};

	var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
	var host = tableSvc.host;

Обратите внимание, что также должна быть предоставлена информация узла, поскольку она требуется держателю SAS для совершения попыток доступа к таблице.

Затем клиентское приложение использует подписанный URL-адрес с помощью **TableServiceWithSAS** для выполнения операций с таблицей. Следующий пример выполняет подключение к таблице и выполняет запрос.

	var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
	var query = azure.TableQuery()
	  .where('PartitionKey eq ?', 'hometasks');
		
	sharedTableService.queryEntities(query, null, function(error, result, response) {
	  if(!error) {
		// result contains the entities
	  }
	});

Поскольку подпись SAS была создана только для доступа с выполнение запроса, если выполняется попытка вставки, обновления или удаления сущностей, будет возвращена ошибка.

###Доступ к спискам управления

Можно также использовать список управления доступом (ACL) для задания политики доступа подписи SAS. Это может оказаться полезным, когда необходимо предоставить доступ к таблице нескольким клиентам, но с различной политикой доступа для каждого из них.

ACL реализуется с помощью массива политик доступа, каждая из которых связана со своим идентификатором. В следующем примере определяются две политики - для пользователей "user1" и "user2":

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

В этом примере код получает текущее значение ACL для таблицы **hometasks**, а затем добавляет новые политики с помощью **setTableAcl**. Такой подход допускает выполнение:

	tableSvc.getTableAcl('hometasks', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		tableSvc.setTableAcl('hometasks', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

После задания ACL можно создать подпись SAS на основе идентификатора политики. В следующем примере создается новая подпись SAS для пользователя 'user2':

	tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"> </a>Дальнейшие действия

Вы изучили основные сведения о табличном хранилище. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение и доступ к данным в Azure][].
-   [Посетите блог рабочей группы службы хранилища Azure][].
-   Посетите репозиторий [Пакет SDK для службы хранилища Azure для Node][] на веб-сайте GitHub.

  [Пакет SDK для службы хранилища Azure для Node]: https://github.com/Azure/azure-storage-node
  [Дальнейшие действия]: #next-steps
  [Что такое служба таблиц]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Создание приложения Node.js]: #create-app
  [Настройка приложения для доступа к хранилищу]: #configure-access
  [Настройка подключения к службе хранилища Azure]: #setup-connection-string
  [Практическое руководство. Создание таблицы]: #create-table
  [Практическое руководство. Добавление сущности в таблицу]: #add-entity
  [Практическое руководство. Обновление сущности]: #update-entity
  [Практическое руководство. Работа с группами сущностей]: #change-entities
  [Практическое руководство. Извлечение сущности]: #query-for-entity
  [Практическое руководство. Запрос набора сущностей]: #query-set-entities
  [Практическое руководство. Удаление сущности]: #query-entity-properties
  [Практическое руководство. Удаление таблицы]: #delete-entity
  [Практическое руководство. Работа с подписанными URL-адресами]: #delete-table

  [OData.org]: http://www.odata.org/
  [using the REST API]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh264518.aspx
  [Azure Management Portal]: http://manage.windowsazure.com

  [Node.js Cloud Service]: /ru-ru/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Хранение и доступ к данным в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Посетите блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Веб-сайт с WebMatrix]: /ru-ru/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Node.js Cloud Service with Storage]: /ru-ru/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [Веб-приложение Node.js с хранилищем]: /ru-ru/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: /ru-ru/documentation/articles/web-sites-nodejs-develop-deploy-mac/
<!--HONumber=42-->
