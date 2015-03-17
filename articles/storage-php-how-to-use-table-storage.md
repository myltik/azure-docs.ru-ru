<properties 
	pageTitle="Как использовать табличное хранилище (PHP) - Microsoft Azure" 
	description="Вы узнаете, как использовать службу таблиц в PHP для создания и удаления таблиц, вставки, удаления строк и создания запросов для таблиц." 
	services="storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>

# Использование службы таблиц в PHP

В этом руководстве показано, как реализовать типичные сценарии с использованием службы таблиц Azure. Примеры написаны на PHP и используют [пакет Azure SDK для PHP][download]. Здесь описаны такие сценарии, как **создание и удаление таблицы, а также вставка, удаление и запрос сущностей в таблице**. Дополнительную информацию о службе таблиц Azure см. в разделе [Дальнейшие действия](#NextSteps) .

##Оглавление

* [Что такое служба таблиц](#what-is)
* [Основные понятия](#concepts)
* [Создание учетной записи хранения Azure](#CreateAccount)
* [Создание приложения PHP](#CreateApplication)
* [Настройка приложения для доступа к службе таблиц](#ConfigureStorage)
* [Настройка подключения к службе хранилища Azure](#ConnectionString)
* [Практическое руководство. Создание таблицы](#CreateTable)
* [Практическое руководство. Добавление сущности в таблицу](#AddEntity)
* [Практическое руководство. Извлечение отдельной сущности](#RetrieveEntity)
* [Практическое руководство. Получение всех сущностей в разделе](#RetEntitiesInPartition)
* [Практическое руководство. Получение подмножества сущностей в разделе](#RetrieveSubset)
* [Практическое руководство. Запрос подмножества свойств сущности](#RetPropertiesSubset)
* [Практическое руководство. Обновление сущности](#UpdateEntity)
* [Практическое руководство. Пакетные операции с таблицами](#BatchOperations)
* [Практическое руководство. Удаление таблицы](#DeleteTable)
* [Дальнейшие действия](#NextSteps)

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

##<a id="CreateAccount"></a>Создание учетной записи хранения Azure

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

##<a id="CreateApplication"></a>Создание приложения PHP

Единственным требованием для создания приложения PHP, которое получает доступ к службе таблиц Azure, является ссылка на классы в пакете Azure SDK для PHP непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

В этом руководстве будут использоваться компоненты службы таблиц, которые могут быть вызваны локально из приложения PHP или в коде, работающем в веб-роли, рабочей роли или на веб-сайте Azure.

##<a id="GetClientLibrary"></a>Получение клиентских библиотек Azure

[AZURE.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureStorage"></a>Настройка приложения для доступа к службе таблиц

Чтобы использовать интерфейсы API службы таблиц Azure, необходимо следующее:

1. Ссылка на файл автозагрузчика с использованием оператора [require_once][require_once] и
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServicesBuilder**.

> [AZURE.NOTE]
> В этом примере (и других примерах в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure через Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо сослаться на файл автозагрузчика <code>WindowsAzure.php</code>.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


В приведенных ниже примерах всегда будет отображаться оператор `require_once`, однако ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

##<a id="ConnectionString"></a>Настройка подключения к службе хранилища Azure

Для создания клиента службы таблиц Azure необходимо сначала сформировать правильную строку подключения. Формат строки подключения к службе таблиц:

Для доступа к службе в режиме реального времени:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Для доступа к хранилищу эмулятора:

	UseDevelopmentStorage=true


Для создания клиента службы Azure необходимо использовать класс **ServicesBuilder**. Вы можете:

* передать строку подключения напрямую или
* использовать **CloudConfigurationManager (CCM)** для проверки нескольких внешних источников на наличие строки подключения:
	* по умолчанию предоставляется поддержка одного внешнего источника - переменных среды
	* можно добавить новые источники, расширив класс **ConnectionStringSource**.

В приведенных здесь примерах строка подключения передается напрямую.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


##<a id="CreateTable"></a>Практическое руководство. Создание таблицы

Объект **TableRestProxy** позволяет создать таблицу с помощью метода **createTable**. При создании таблицы можно задать время ожидания службы таблиц. (Дополнительные сведения о времени ожидания службы таблиц см. в разделе [Настройка времени ожидания для операций службы таблиц][table-service-timeouts].)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

	try	{
		// Create table.
		$tableRestProxy->createTable("mytable");
	}
	catch(ServiceException $e){
		$code = $e->getCode();
		$error_message = $e->getMessage();
		// Handle exception based on error codes and messages.
		// Error codes and messages can be found here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
	}

Дополнительные сведения об ограничениях на имена таблиц см. в разделе [Общие сведения о модели данных службы таблиц][table-data-model].

##<a id="AddEntity"></a>Практическое руководство. Добавление сущности в таблицу

Чтобы добавить сущность в таблицу, создайте новый объект **Entity** и передайте его в **TableRestProxy->insertEntity**. Обратите внимание, что при создании сущности необходимо задать свойства  `PartitionKey` и  `RowKey`. Это уникальные идентификаторы сущности и значения, которые можно запросить гораздо быстрее, чем другие свойства сущности. Система использует  `PartitionKey`, чтобы автоматически распространять сущности таблицы на множество узлов хранилища. Сущности с одинаковым значением  `PartitionKey` хранятся на одном узле. (Операции над несколькими сущностями, хранящимися на одном узле, будут выполняться лучше, чем для сущностей на разных узлах.)  `RowKey` - это уникальный идентификатор сущности в разделе.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$entity = new Entity();
	$entity->setPartitionKey("tasksSeattle");
	$entity->setRowKey("1");
	$entity->addProperty("Description", null, "Take out the trash.");
	$entity->addProperty("DueDate", 
						 EdmType::DATETIME, 
						 new DateTime("2012-11-05T08:15:00-08:00"));
	$entity->addProperty("Location", EdmType::STRING, "Home");
	
	try{
		$tableRestProxy->insertEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
	}

Дополнительные сведения о типах и свойствах таблиц см. в разделе [Общие сведения о модели данных службы таблиц][table-data-model].

Существует два альтернативных метода вставки сущностей для класса **TableRestProxy**: **insertOrMergeEntity** и **insertOrReplaceEntity**. Чтобы использовать эти методы, создайте новый объект **Entity** и передайте его в качестве параметра в любой из этих методов. Каждый метод вставит эту сущность, если она не существует. Если сущность уже существует, **insertOrMergeEntity** будет обновлять значения свойств, если свойства уже существуют, и добавлять новые свойства, если они не существуют, в то время как **insertOrReplaceEntity** полностью заменяет имеющуюся сущность. Следующий пример показывает, как использовать **insertOrMergeEntity**. Если сущность с  `PartitionKey` tasksSeattle и  `RowKey` 1 еще не существует, она будет вставлена. Тем не менее если она была вставлена ранее (как показано в приведенном выше примере), будет обновлено свойство  `DueDate` и добавлено свойство  `Status`. Свойства  `Description` и  `Location` также будут обновлены, однако до таких значений, которые фактически равны предыдущим. Если эти два последних свойства не были добавлены, как показано в примере, но существовали в целевой сущности, их текущие значения остаются без изменений.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	//Create new entity.
	$entity = new Entity();
	
	// PartitionKey and RowKey are required.
	$entity->setPartitionKey("tasksSeattle");
	$entity->setRowKey("1");
	
	// If entity exists, existing properties are updated with new values and
	// new properties are added. Missing properties are unchanged.
	$entity->addProperty("Description", null, "Take out the trash.");
	$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
	$entity->addProperty("Location", EdmType::STRING, "Home");
	$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.
	
	try	{
		// Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
		// would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
		$tableRestProxy->insertOrMergeEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	   

##<a id="RetrieveEntity"></a>Практическое руководство. Извлечение отдельной сущности

Метод **TableRestProxy->getEntity** позволяет получить одну сущность, запрашивая ее значения  `PartitionKey` и  `RowKey`. В примере ниже ключ раздела  `tasksSeattle` и ключ строки 1 передаются в метод **getEntity**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entity = $result->getEntity();

	echo $entity->getPartitionKey().":".$entity->getRowKey();

##<a id="RetEntitiesInPartition"></a>Практическое руководство. Получение всех сущностей в разделе

Запросы сущностей создаются с помощью фильтров (дополнительные сведения см. в разделе [Запросы таблиц и сущностей][filters]). Чтобы получить все сущности в разделе, используйте фильтр PartitionKey eq  *partition_name*. В следующем примере показано, как получить все сущности в разделе  `tasksSeattle`, передав фильтр в метод **queryEntities**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$filter = "PartitionKey eq 'tasksSeattle'";
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $filter);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

##<a id="RetrieveSubset"></a>Практическое руководство. Получение подмножества сущностей в разделе

Процедуру, аналогичную приведенной в предыдущем примере, можно использовать для получения любого подмножества сущностей в разделе. Получаемое подмножество сущностей определяется с помощью используемого фильтра (дополнительную информацию см. в разделе [Запросы к таблицам и сущностям][filters]). В следующем примере показано, как использовать фильтр для получения всех сущностей с определенным значением  `Location` и значением  `DueDate`, которое меньше указанной даты.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $filter);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

##<a id="RetPropertiesSubset"></a>Практическое руководство. Запрос подмножества свойств сущности

Запрос позволяет получить подмножество свойств сущности. Этот метод, который называется  *проекцией*, снижает потребление пропускной способности и может повысить производительность запросов, особенно для больших сущностей. Чтобы задать свойство для извлечения, передайте имя этого свойства в метод **Query->addSelectField**. Этот метод можно вызывать несколько раз для добавления дополнительных свойств. После выполнения **TableRestProxy->queryEntities** возвращаемые сущности будут иметь только выбранные свойства. (Если вы хотите вернуть подмножество сущностей таблицы, используйте фильтр из приведенных выше запросов.)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\QueryEntitiesOptions;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$options = new QueryEntitiesOptions();
	$options->addSelectField("Description");
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	// All entities in the table are returned, regardless of whether 
	// they have the Description field.
	// To limit the results returned, use a filter.
	$entities = $result->getEntities();

	foreach($entities as $entity){
		$description = $entity->getProperty("Description")->getValue();
		echo $description."<br />";
	}

##<a id="UpdateEntity"></a>Практическое руководство. Обновление сущности

Имеющиеся сущности можно обновить, воспользовавшись методами **Entity->setProperty** и **Entity->addProperty** для сущности, а затем вызвав **TableRestProxy->updateEntity**. Следующий пример извлекает сущность, изменяет одно свойство, удаляет другое свойство и добавляет новое свойство. Обратите внимание, что удаление свойства выполняется путем установки его в значение **null**. 

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
	
	$entity = $result->getEntity();
	
	$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
	
	$entity->setPropertyValue("Location", null); //Removed Location.
	
	$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

	try	{
		$tableRestProxy->updateEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="DeleteEntity"></a>Практическое руководство. Удаление сущности

Чтобы удалить сущность, передайте имя таблицы и значения  `PartitionKey` и  `RowKey` сущности в метод **TableRestProxy->deleteEntity**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		// Delete entity.
		$tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Обратите внимание, что для проверки на наличие конфликтов можно задать Etag для удаляемой сущности, воспользовавшись методом **DeleteEntityOptions->setEtag** и передав объект **DeleteEntityOptions** в **deleteEntity** в качестве четвертого параметра.

##<a id="BatchOperations"></a>Практическое руководство. Пакетные операции с таблицами

Метод **TableRestProxy->batch** позволяет выполнять несколько операций в одном запросе. Здесь шаблон включает в себя добавление операций в объект **BatchRequest** и последующую передачу объекта **BatchRequest** в метод **TableRestProxy->batch**. Чтобы добавить операцию в объект **BatchRequest**, можно вызвать любой из следующих методов несколько раз:

* **addInsertEntity** (добавляет операцию insertEntity)
* **addUpdateEntity** (добавляет операцию updateEntity)
* **addMergeEntity** (добавляет операцию mergeEntity)
* **addInsertOrReplaceEntity** (добавляет операцию insertOrReplaceEntity)
* **addInsertOrMergeEntity** (добавляет операцию insertOrMergeEntity)
* **addDeleteEntity** (добавляет операцию deleteEntity)

Следующий пример показывает, как выполнить операции **insertEntity** и **deleteEntity** в одном запросе:

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;
	use WindowsAzure\Table\Models\BatchOperations;

 	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	// Create list of batch operation.
	$operations = new BatchOperations();
	
	$entity1 = new Entity();
	$entity1->setPartitionKey("tasksSeattle");
	$entity1->setRowKey("2");
	$entity1->addProperty("Description", null, "Clean roof gutters.");
	$entity1->addProperty("DueDate", 
						  EdmType::DATETIME, 
						  new DateTime("2012-11-05T08:15:00-08:00"));
	$entity1->addProperty("Location", EdmType::STRING, "Home");
	
	// Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

	// Add operation to list of batch operations.
	$operations->addDeleteEntity("mytable", "tasksSeattle", "1");
	
	try	{
		$tableRestProxy->batch($operations);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Дополнительные сведения о пакетной обработке операций с таблицами см. в разделе [Выполнение групповых транзакций для сущности][entity-group-transactions].

##<a id="DeleteTable"></a>Практическое руководство. Удаление таблицы

Наконец, чтобы удалить таблицу, следует передать имя таблицы в метод **TableRestProxy->deleteTable**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		// Delete table.
		$tableRestProxy->deleteTable("mytable");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="NextSteps"></a>Дальнейшие действия

Вы изучили основные сведения о службе таблиц Azure. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- См. справочник MSDN: [Хранение и доступ к данным в Azure] []
- Посетите блог рабочей группы службы хранилища Azure: <http://blogs.msdn.com/b/windowsazurestorage/>

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Хранение и доступ к данным в Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/windowsazure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/windowsazure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/windowsazure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/windowsazure/dd894038.aspx
<!--HONumber=42-->
