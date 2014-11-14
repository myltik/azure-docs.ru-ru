<properties urlDisplayName="Table Service" pageTitle="Использование табличного хранилища (PHP) &mdash; Microsoft Azure" metaKeywords="Azure Table service PHP, Azure creating table, Azure deleting table, Azure insert table, Azure query table" description="Вы узнаете, как использовать службу таблиц в PHP для создания и удаления таблиц, вставки, удаления строк и создания запросов для таблиц." metaCanonical="" services="storage" documentationCenter="PHP" title="Использование службы таблиц в PHP" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Использование службы таблиц в PHP

В этом руководстве показано, как реализовать типичные сценарии с использованием службы таблиц Azure. Примеры написаны на PHP и используют [пакет Azure SDK для PHP][пакет Azure SDK для PHP]. Здесь описаны такие сценарии, как **создание и удаление таблицы, а также вставка, удаление и запрос сущностей в таблице**. Дополнительные сведения о службе таблиц Azure см. в разделе [Дальнейшие действия][Дальнейшие действия].

## Оглавление

-   [Что такое служба таблиц][Что такое служба таблиц]
-   [Основные понятия][Основные понятия]
-   [Создание учетной записи хранения Azure][Создание учетной записи хранения Azure]
-   [Создание приложения PHP][Создание приложения PHP]
-   [Настройка приложения для доступа к службе таблиц][Настройка приложения для доступа к службе таблиц]
-   [Настройка подключения к хранилищу Azure][Настройка подключения к хранилищу Azure]
-   [Практическое руководство. Создание таблицы][Практическое руководство. Создание таблицы]
-   [Практическое руководство. Добавление сущности в таблицу][Практическое руководство. Добавление сущности в таблицу]
-   [Практическое руководство. Извлечение одной сущности][Практическое руководство. Извлечение одной сущности]
-   [Практическое руководство. Получение всех сущностей в разделе][Практическое руководство. Получение всех сущностей в разделе]
-   [Практическое руководство. Получение диапазона сущностей в разделе][Практическое руководство. Получение диапазона сущностей в разделе]
-   [Практическое руководство. Запрос подмножества свойств сущности][Практическое руководство. Запрос подмножества свойств сущности]
-   [Практическое руководство. Обновление сущности][Практическое руководство. Обновление сущности]
-   [Практическое руководство. Пакетные операции с таблицами][Практическое руководство. Пакетные операции с таблицами]
-   [Практическое руководство. Удаление таблицы][Практическое руководство. Удаление таблицы]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [руководство-таблица-хранилище](../includes/howto-table-storage.md)]

## <span id="CreateAccount"></span></a>Создание учетной записи хранения Azure

[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

## <span id="CreateApplication"></span></a>Создание приложения на PHP

Единственным требованием для создания приложения PHP, которое получает доступ к службе таблиц Azure, является ссылка на классы в пакете Azure SDK для PHP непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

В этом руководстве будут использоваться компоненты службы таблиц, которые могут быть вызваны локально из приложения PHP или в коде, работающем в веб-роли, рабочей роли или на веб-сайте Azure.

## <span id="GetClientLibrary"></span></a>Получение клиентских библиотек Azure

[WACOM.INCLUDE [получение-клиент-библиотеки](../includes/get-client-libraries.md)]

## <span id="ConfigureStorage"></span></a>Настройка приложения для доступа к службе таблиц

Чтобы использовать интерфейсы API службы таблиц Azure, необходимо следующее:

1.  Ссылка на файл автозагрузчика с использованием инструкции [require\_once][require\_once] и
2.  Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServicesBuilder**.

> [WACOM.NOTE]
> При работе с этим примером (и другими примерами в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure через Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо сослаться на файл автозагрузчика `WindowsAzure.php`.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

В приведенных ниже примерах инструкция `require_once` всегда будет отображаться, однако ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

## <span id="ConnectionString"></span></a>Настройка подключения к хранилищу Azure

Для создания клиента службы таблиц Azure необходимо сначала сформировать правильную строку подключения. Формат строки подключения к службе таблиц:

Для доступа к службе в режиме реального времени:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Для доступа к хранилищу эмулятора:

    UseDevelopmentStorage=true

Для создания клиента службы Azure необходимо использовать класс **ServicesBuilder**. Вы можете:

-   передать строку подключения напрямую или
-   использовать **CloudConfigurationManager (CCM)** для проверки нескольких внешних источников на наличие строки подключения:

    -   по умолчанию предоставляется поддержка одного внешнего источника – переменных среды
    -   можно добавить новые источники, расширив класс **ConnectionStringSource**

В приведенных здесь примерах строка подключения передается напрямую.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

## <span id="CreateTable"></span></a>Практическое руководство. Создание таблицы

Объект **TableRestProxy** позволяет создать таблицу с помощью метода **createTable**. При создании таблицы можно задать время ожидания службы таблиц. (Дополнительные сведения о времени ожидания службы таблиц см. в разделе [Настройка времени ожидания для операций службы таблиц][Настройка времени ожидания для операций службы таблиц].)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Handle exception based on error codes and messages.
        // Error codes and messages can be found here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx
    }

Дополнительные сведения об ограничениях на имена таблиц см. в разделе [Общие сведения о модели данных службы таблиц][Общие сведения о модели данных службы таблиц].

## <span id="AddEntity"></span></a>Практическое руководство. Добавление сущности в таблицу

Чтобы добавить сущность в таблицу, создайте новый объект **Entity** и передайте его в **TableRestProxy-\>insertEntity**. Обратите внимание, что при создании сущности необходимо задать свойства `PartitionKey` и `RowKey`. Это уникальные идентификаторы сущности и значения, которые можно запросить гораздо быстрее, чем другие свойства сущности. Система использует `PartitionKey`, чтобы автоматически распространять сущности таблицы на множество узлов хранилища. Сущности с одинаковым значением `PartitionKey` хранятся на одном узле. (Операции над несколькими сущностями, хранящимися на одном узле, будут выполняться лучше, чем для сущностей на разных узлах.) `RowKey` — это уникальный идентификатор сущности в разделе.

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
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

Дополнительные сведения о типах и свойствах таблиц см. в разделе [Общие сведения о модели данных службы таблиц][Общие сведения о модели данных службы таблиц].

Класс **TableRestProxy** предлагает два альтернативных способа вставки сущностей: **insertOrMergeEntity** и **insertOrReplaceEntity**. Чтобы использовать эти методы, создайте новый объект **Entity** и передайте его в качестве параметра в любой из этих методов. Каждый метод вставит эту сущность, если она не существует. Если сущность уже существует, **insertOrMergeEntity** будет обновлять значения свойств, если свойства уже существуют, и добавлять новые свойства, если они не существуют, в то время как **insertOrReplaceEntity** полностью заменяет имеющуюся сущность. Следующий пример показывает, как использовать **insertOrMergeEntity**. Если сущность с `PartitionKey` "tasksSeattle" и `RowKey` "1" еще не существует, она будет вставлена. Тем не менее если она была вставлена ранее (как показано в приведенном выше примере), будет обновлено свойство `DueDate` и добавлено свойство `Status`. Свойства `Description` и `Location` также будут обновлены, однако на такие значения, которые фактически равны предыдущим. Если эти два последних свойства не были добавлены, как показано в примере, но существовали в целевой сущности, их текущие значения остаются без изменений.

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

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }
       

## <span id="RetrieveEntity"></span></a>Практическое руководство. Извлечение одной сущности

Метод **TableRestProxy-\>getEntity** позволяет получить одну сущность, запрашивая ее значения `PartitionKey` и `RowKey`. В примере ниже ключ раздела `tasksSeattle` и ключ строки `1` передаются в метод **getEntity**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

## <span id="RetEntitiesInPartition"></span></a>Практическое руководство. Получение всех сущностей в разделе

Запросы сущностей создаются с помощью фильтров (дополнительные сведения см. в разделе [Запросы таблиц и сущностей][Запросы таблиц и сущностей]). Для получения всех сущностей в разделе используйте фильтр "PartitionKey eq *имя\_раздела*". Следующий пример показывает, как получить все сущности в разделе `tasksSeattle`, передав фильтр в метод **queryEntities**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <span id="RetrieveSubset"></span></a>Практическое руководство. Получение диапазона сущностей в разделе

Процедуру, аналогичную приведенной в предыдущем примере, можно использовать для получения любого подмножества сущностей в разделе. Получаемое подмножество сущностей определяется с помощью используемого фильтра (дополнительные сведения см. в разделе [Запросы таблиц и сущностей][Запросы таблиц и сущностей]). Следующий пример показывает, как использовать фильтр для получения всех сущностей с определенным значением `Location` и со значением `DueDate`, которое меньше указанной даты.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <span id="RetPropertiesSubset"></span></a>Практическое руководство. Запрос подмножества свойств сущности

Запрос позволяет получить подмножество свойств сущности. Этот метод, который называется *проекцией*, снижает потребление полосы пропускания и может повысить производительность запросов, особенно для крупных сущностей. Чтобы задать свойство для извлечения, передайте имя этого свойства в метод **Query-\>addSelectField**. Этот метод можно вызывать несколько раз для добавления дополнительных свойств. После выполнения **TableRestProxy-\>queryEntities** возвращаемые сущности будут иметь только выбранные свойства. (Если вы хотите вернуть подмножество сущностей таблицы, используйте фильтр из приведенных выше запросов.)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx
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

## <span id="UpdateEntity"></span></a>Практическое руководство. Обновление сущности

Существующие сущности можно обновить, воспользовавшись методами **Entity-\>setProperty** и **Entity-\>addProperty** для сущности, а затем вызвав **TableRestProxy-\>updateEntity**.. Следующий пример извлекает сущность, изменяет одно свойство, удаляет другое свойство и добавляет новое свойство. Обратите внимание, что удаление свойства выполняется путем установки его в значение **null**.

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

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DeleteEntity"></span></a>Практическое руководство. Удаление сущности

Чтобы удалить сущность, передайте имя таблицы и значения `PartitionKey` и `RowKey` сущности в метод **TableRestProxy-\>deleteEntity**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Обратите внимание, что для проверки на наличие конфликтов можно задать Etag для удаляемой сущности, воспользовавшись методом **DeleteEntityOptions-\>setEtag** и передав объект **DeleteEntityOptions** в **deleteEntity** в качестве четвертого параметра.

## <span id="BatchOperations"></span></a>Практическое руководство. Пакетные операции с таблицами

Метод **TableRestProxy-\>batch** позволяет выполнять несколько операций в одном запросе. Здесь шаблон включает в себя добавление операций в объект **BatchRequest** и последующую передачу объекта **BatchRequest** в метод **TableRestProxy-\>batch**. Чтобы добавить операцию в объект **BatchRequest**, можно вызвать любой из следующих методов несколько раз:

-   **addInsertEntity** (добавляет операцию insertEntity)
-   **addUpdateEntity** (добавляет операцию updateEntity)
-   **addMergeEntity** (добавляет операцию mergeEntity)
-   **addInsertOrReplaceEntity** (добавляет операцию insertOrReplaceEntity)
-   **addInsertOrMergeEntity** (добавляет операцию insertOrMergeEntity)
-   **addDeleteEntity** (добавляет операцию deleteEntity)

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

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Дополнительные сведения о пакетной обработке операций с таблицами см. в разделе [Выполнение групповых транзакций для сущности][Выполнение групповых транзакций для сущности].

## <span id="DeleteTable"></span></a>Практическое руководство. Удаление таблицы

Наконец, чтобы удалить таблицу, следует передать имя таблицы в метод **TableRestProxy-\>deleteTable**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="NextSteps"></span></a>Дальнейшие действия

Вы изучили основные сведения о службе таблиц Azure. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure][Хранение данных и доступ к ним в Azure]
-   Посетите блог команды разработчиков хранилища Azure <http://blogs.msdn.com/b/windowsazurestorage/>

  [пакет Azure SDK для PHP]: http://go.microsoft.com/fwlink/?LinkID=252473
  [Дальнейшие действия]: #NextSteps
  [Что такое служба таблиц]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #CreateAccount
  [Создание приложения PHP]: #CreateApplication
  [Настройка приложения для доступа к службе таблиц]: #ConfigureStorage
  [Настройка подключения к хранилищу Azure]: #ConnectionString
  [Практическое руководство. Создание таблицы]: #CreateTable
  [Практическое руководство. Добавление сущности в таблицу]: #AddEntity
  [Практическое руководство. Извлечение одной сущности]: #RetrieveEntity
  [Практическое руководство. Получение всех сущностей в разделе]: #RetEntitiesInPartition
  [Практическое руководство. Получение диапазона сущностей в разделе]: #RetrieveSubset
  [Практическое руководство. Запрос подмножества свойств сущности]: #RetPropertiesSubset
  [Практическое руководство. Обновление сущности]: #UpdateEntity
  [Практическое руководство. Пакетные операции с таблицами]: #BatchOperations
  [Практическое руководство. Удаление таблицы]: #DeleteTable
  [require\_once]: http://php.net/require_once
  [Настройка времени ожидания для операций службы таблиц]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd894042.aspx
  [Общие сведения о модели данных службы таблиц]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179338.aspx
  [Запросы таблиц и сущностей]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd894031.aspx
  [Выполнение групповых транзакций для сущности]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd894038.aspx
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
