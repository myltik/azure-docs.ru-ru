<properties
	pageTitle="Использование табличного хранилища Azure из Node.js | Microsoft Azure"
	description="Хранение структурированных данных в облаке в хранилище таблиц Azure (хранилище данных NoSQL)."
	services="storage"
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="micurd"/>


# Использование табличного хранилища Azure из Node.js

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)] .<br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## Обзор

В этом разделе рассматривается реализация типичных сценариев с помощью службы таблиц Azure в приложении Node.js.

В примерах кода в этом разделе предполагается, что приложение Node.js уже создано. Инструкции по созданию приложения Node.js в Azure см. в любой из следующих статей:

- [Создание веб-приложения Node.js в службе приложений Azure](../app-service-web/web-sites-nodejs-develop-deploy-mac.md)
- [Создание и развертывание веб-приложения Node.js в Azure с использованием WebMatrix](../app-service-web/web-sites-nodejs-use-webmatrix.md)
- [Создание и развертывание приложения Node.js в облачной службе Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (с помощью Windows PowerShell)


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## Настройка приложения для доступа к хранилищу Azure

Чтобы использовать службу хранилища Azure, вам понадобится пакет SDK службы хранилища Azure для Node.js, который содержит набор полезных библиотек, взаимодействующих со службами REST хранилища.

### Использование диспетчера пакета Node (NPM) для установки пакета

1.  Используя интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите к папке, в которой вы создали приложение.

2.  Введите в командной строке **npm install azure-storage**. Результат выполнения этой команды будет аналогичен следующему примеру:

		azure-storage@0.5.0 node_modules\azure-storage
		+-- extend@1.2.1
		+-- xmlbuilder@0.4.3
		+-- mime@1.2.11
		+-- node-uuid@1.4.3
		+-- validator@3.22.2
		+-- underscore@1.4.4
		+-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
		+-- xml2js@0.2.7 (sax@0.5.2)
		+-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Выполнив команду **ls**, можно убедиться, что каталог **node\_modules** создан. В этой папке находится пакет **azure-storage**, содержащий библиотеки, необходимые для доступа к хранилищу.

### Импорт пакета

Добавьте следующий код в начало файла **server.js** в приложении:

	var azure = require('azure-storage');

## Настройка подключения к службе хранилища Azure

Модуль Azure считывает переменные среды AZURE\\_STORAGE\\_ACCOUNT и AZURE\\_STORAGE\\_ACCESS\\_KEY или AZURE\_STORAGE\_CONNECTION\_STRING для получения сведений, необходимых для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, при вызове **TableService** необходимо указать сведения об учетной записи.

Пример настройки переменных среды для веб-сайта Azure на [портале Azure](https://portal.azure.com) см. в статье [Веб-приложение Node.js, использующее службу таблиц Azure].

## Создание таблицы

Следующий код создает объект **TableService** и использует его для создания новой таблицы. Добавьте в начало **server.js**следующий код.

	var tableSvc = azure.createTableService();

Вызов **createTableIfNotExists** создает новую таблицу с определенным именем, если она уже не существует. В следующем примере создается новая таблица с именем "mytable", если она еще не создана:

	tableSvc.createTableIfNotExists('mytable', function(error, result, response){
	  if(!error){
	    // Table exists or created
	  }
	});

`result.created` будет иметь значение `true`, если была создана новая таблица, и `false`, если таблица уже существует. `response` будет содержать информацию о запросе.

### Фильтры

Дополнительные операции фильтрации можно применить к выполняемым операциям, используя **TableService**. К операциям фильтрации могут относиться ведение журнала, автоматический повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

	function handle (requestOptions, next)

Выполнив предварительную обработку параметров запроса, метод должен вызвать next, передавая функцию обратного вызова со следующей сигнатурой:

	function (returnObject, finalCallback, next)

В этом примере, а также после обработки returnObject (ответа на запрос к серверу) функция обратного вызова должна вызвать функцию next (если она существует), чтобы продолжить обработку других фильтров. В противном случае она просто вызывает finalCallback, чтобы завершить вызов службы.

В Azure SDK для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter** Следующий код создает объект **TableService**, использующий фильтр **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableSvc = azure.createTableService().withFilter(retryOperations);

## Добавление сущности в таблицу

Чтобы добавить сущность, сначала создайте объект, который определяет свойства сущности. Все сущности должны содержать **PartitionKey** и **RowKey**, которые представляют собой ее уникальные идентификаторы.

* **PartitionKey** — определяет раздел, в котором хранится сущность.

* **RowKey** — уникально определяет сущность в разделе.

**PartitionKey** и **RowKey** должны быть строковыми значениями. Дополнительную информацию см. в статье [Общие сведения о модели данных службы таблиц](http://msdn.microsoft.com/library/azure/dd179338.aspx).

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

Если операция успешна, `result` будет содержать [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) вставленной записи, а `response` — информацию об операции.

Пример ответа:

	{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] По умолчанию метод **insertEntity** не возвращает вставленную сущность как часть информации, содержащейся в `response`. Если вы хотите выполнить другие операции с этой сущностью или кэшировать информацию, необходимо, чтобы она была возвращена как часть `result`. Это можно сделать следующим образом, включив **echoContent**:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## Обновление сущности

Для обновления имеющейся сущности доступно несколько методов:

* **replaceEntity** — обновляет имеющуюся сущность с ее заменой.

* **mergeEntity** — обновляет сущность посредством объединения новых значений свойств с имеющейся сущностью.

* **insertOrReplaceEntity** — обновляет имеющуюся сущность с ее заменой. Если сущность не существует, будет вставлена новая сущность.

* **insertOrMergeEntity** — обновляет сущность посредством объединения новых значений свойств с имеющейся сущностью. Если сущность не существует, будет вставлена новая сущность.

В следующем примере показано обновление сущности с помощью **replaceEntity**.

	tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
	  if(!error) {
	    // Entity updated
	  }
	});

> [AZURE.NOTE] По умолчанию обновление сущности не проверяет, были ли обновляемые данные ранее изменены другим процессом. Поддержка одновременных обновлений:
>
> 1. Получите ETag обновляемого объекта. Он возвращается в составе `response` для всех операций с сущностями и может быть извлечен с помощью `response['.metadata'].etag`.
>
> 2. При выполнении операции обновления с сущностью предварительно добавьте информацию ETag, извлеченную для новой сущности. Например:
>
>     .`entity2['.metadata'].etag = currentEtag;`
>
> 3. Выполните операцию обновления. Если сущность была изменена с момента получения значения ETag, например, другим экземпляром вашего приложения, будет возвращена ошибка `error`, указывающая, что определенное в запросе условие обновления не выполнено.

Если при использовании **replaceEntity** и **mergeEntity** обновляемая сущность не существует, то операция обновления завершается ошибкой. Поэтому, если нужно сохранить сущность независимо от того, существует ли она, следует использовать **insertOrReplaceEntity** или **insertOrMergeEntity**.

`result` должен содержать значение **Etag** обновленной сущности в случае успешного выполнения операций.

## Работа с группами сущностей

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

В успешных пакетных операциях `result` содержит информацию обо всех операциях в пакете.

### Работа с пакетными операциями

Операции, добавленные в пакет, можно проверить, просмотрев свойство `operations`. Для работы с операциями можно также использовать следующие методы:

* **clear** — удаляет все операции из пакета.

* **getOperations** — получает операцию из пакета.

* **hasOperations** — возвращает значение true, если пакет содержит операции.

* **removeOperations** — удаляет операцию.

* **size** — возвращает количество операций в пакете.

## Получение сущности по ключу

Чтобы возвратить определенную сущность на основе значений **PartitionKey** и **RowKey**, используйте метод **retrieveEntity**.

	tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
	  if(!error){
	    // result contains the entity
	  }
	});

После завершения этой операции `result` будет содержать сущность.

## Запрос набора сущностей

Чтобы запросить таблицу, используйте объект **TableQuery** для создания выражения запроса с помощью следующих предложений:

* **select** — поля, возвращаемые из запроса.

* **where** — предложение where.

	* **and** — условие `and`.

	* **or** — условие `or`.

* **top** — количество извлекаемых элементов.


Следующий пример собирает запрос, который возвращает первые пять элементов с использованием ключа PartitionKey со значением hometasks.

	var query = new azure.TableQuery()
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

Так как параметр **select** не используется, возвращаются все поля. Чтобы выполнить запрос сущности в таблице, используйте **queryEntities**. В следующем примере используется запрос для возврата сущностей из таблицы 'mytable'.

	tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
	  if(!error) {
	    // query was successful
	  }
	});

В случае успешного выполнения `result.entries` будет содержать массив сущностей, соответствующих запросу. Если запросу не удалось вернуть все сущности, `result.continuationToken` не будет иметь значение *null* и его можно будет использовать в качестве третьего параметра **queryEntities** для получения других результатов. В начальном запросе третий параметр должен иметь значение *null*.

### Запрос подмножества свойств сущности

Запрос к таблице может получить лишь несколько полей сущности. Этот позволяет снизить потребление пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. С помощью предложения **select** передайте имена возвращаемых полей. Например, следующий запрос возвратит только поля **description** и **dueDate**.

	var query = new azure.TableQuery()
	  .select(['description', 'dueDate'])
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

## Удаление сущности

Сущность можно удалить с помощью ее ключей раздела и строки. В этом примере объект **task1** содержит значения **RowKey** и **PartitionKey** удаляемой сущности. Затем этот объект передается в метод **deleteEntity**.

	var task = {
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'}
	};

	tableSvc.deleteEntity('mytable', task, function(error, response){
	  if(!error) {
	    // Entity deleted
	  }
	});

> [AZURE.NOTE] Следует рассмотреть использование тегов ETag при удалении элементов, чтобы гарантировать отсутствие в них изменений, внесенных другим процессом. Сведения об использовании тегов ETag см. в разделе [Обновление сущности](#update-an-entity).

## Удаление таблицы

Следующий код удаляет таблицу из учетной записи хранения.

	tableSvc.deleteTable('mytable', function(error, response){
		if(!error){
			// Table deleted
		}
	});

Если неизвестно, существует ли таблица, используйте **deleteTableIfExists**.

## Использование маркеров продолжения

При выполнении запросов к таблицам для получения больших объемов результатов следует искать маркеры продолжения. По вашему запросу может быть найден большой объем данных, который, возможно, не удастся реализовать, если не создать метод определения наличия маркера продолжения.

При наличии такого маркера объект результатов, возвращаемый при запросе сущностей, задает свойство `continuationToken`. В последствии его можно использовать при выполнении запроса для продолжения и перемещения между разделами и сущностями таблицы.

При выполнении запросов для экземпляра объекта запроса и функции обратного вызова можно указать параметр continuationToken:

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Если обратиться к объекту `continuationToken`, то вы обнаружите, что он имеет такие свойства, как `nextPartitionKey`, `nextRowKey` и `targetLocation`, которые можно использовать для итерации по всем результатам.

Кроме того, на сайте GitHub в репозитории Node.js для службы хранилища Azure есть пример использования маркеров продолжения. Поищите `examples/samples/continuationsample.js`.

## Работа с подписями общего доступа

Подписанные URL-адреса (SAS) — безопасный способ предоставить детальный доступ к таблицам без указания имени или ключей своей учетной записи хранения. SAS часто используется для предоставления ограниченного доступа к данным, например, позволяет мобильному приложению запрашивать записи.

Доверенное приложение, например облачная служба, создает подписанный URL-адрес с помощью **generateSharedAccessSignature** службы **TableService** и предоставляет его недоверенному или полудоверенному приложению, например предназначенному для мобильных устройств. Подпись SAS создается с использованием политики, которая описывает даты начала и окончания срока действия SAS, а также уровень доступа, который предоставляется держателю подписи SAS.

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

Клиентское приложение далее использует подпись SAS с помощью **TableServiceWithSAS** для выполнения операций с таблицей. Следующий пример выполняет подключение к таблице и выполняет запрос.

	var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
	var query = azure.TableQuery()
	  .where('PartitionKey eq ?', 'hometasks');

	sharedTableService.queryEntities(query, null, function(error, result, response) {
	  if(!error) {
	    // result contains the entities
	  }
	});

Поскольку подпись SAS была создана только для доступа с выполнение запроса, если выполняется попытка вставки, обновления или удаления сущностей, будет возвращена ошибка.

### Списки управления доступом

Можно также использовать список управления доступом (ACL) для задания политики доступа подписи SAS. Это может оказаться полезным, когда необходимо предоставить доступ к таблице нескольким клиентам, но с различной политикой доступа для каждого из них.

ACL реализуется с помощью массива политик доступа, каждая из которых связана со своим идентификатором. В следующем примере определяются две политики, по одной для пользователей user1 и user2:

	var sharedAccessPolicy = {
	  user1: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	  user2: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

В этом примере код получает текущее значение ACL для таблицы **hometasks**, а затем добавляет новые политики с помощью **setTableAcl**. Такой подход допускает выполнение:

	var extend = require('extend');
	tableSvc.getTableAcl('hometasks', function(error, result, response) {
    if(!error){
	    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
	    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
	      if(!error){
	        // ACL set
	      }
	    });
	  }
	});

После задания ACL можно создать подпись SAS на основе идентификатора политики. В следующем примере создается новая подпись SAS для пользователя 'user2':

	tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы.

-   [Блог рабочей группы службы хранилища Azure][].
-   Репозиторий [Пакет SDK службы хранилища Azure для Node][] на веб-сайте GitHub.
-   [Центр разработчиков Node.js.](/develop/nodejs/)

  [Пакет SDK службы хранилища Azure для Node]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [Веб-приложение Node.js, использующее службу таблиц Azure]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md

<!---HONumber=AcomDC_0817_2016-->