<properties urlDisplayName="Table Service" pageTitle="Как использовать табличное хранилище (Ruby) - Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Ruby, table storage Ruby" description="Learn how to use the table storage service in Azure. Code samples are written using the Ruby API." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Table Service from Ruby" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="11/24/2014" ms.author="tomfitz" />





# Использование службы таблиц в Ruby

В этом руководстве показано, как реализовать типичные сценарии с использованием службы таблиц Microsoft Azure. Примеры написаны с помощью Ruby API. Здесь описаны такие сценарии, как **создание и удаление таблицы, а также вставка и запрос сущностей в таблице**. Дополнительную информацию о таблицах см. в разделе [Дальнейшие действия](#next-steps) .

## Оглавление

* [Что такое служба таблиц](#what-is)
* [Основные понятия](#concepts)
* [Создание учетной записи хранения Azure](#create-a-windows-azure-storage-account)
* [Создание приложения Ruby](#create-a-ruby-application)
* [Настройка приложения для доступа к хранилищу](#configure-your-application-to-access-storage)
* [Настройка подключения к хранилищу Azure](#setup-a-windows-azure-storage-connection)
* [Практическое руководство. Создание таблицы](#how-to-create-a-table)
* [Практическое руководство. Добавление сущности в таблицу](#how-to-add-an-entity-to-a-table)
* [Практическое руководство. Обновление сущности](#how-to-update-an-entity)
* [Практическое руководство. Работа с группами сущностей](#how-to-work-with-groups-of-entities)
* [Практическое руководство. Запрос сущности](#how-to-query-for-an-entity)
* [Практическое руководство. Запрос набора сущностей](#how-to-query-a-set-of-entities)
* [Практическое руководство. Запрос подмножества свойств сущности](#how-to-query-a-subset-of-entity-properties)
* [Практическое руководство. Удаление сущности](#how-to-delete-an-entity)
* [Практическое руководство. Удаление таблицы](#how-to-delete-a-table)
* [Дальнейшие действия](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a id="create-a-windows-azure-storage-account"></a>Создание учетной записи хранения Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="create-a-ruby-application"></a>Создание приложения Ruby

Создайте приложение Ruby. Указания см. в разделе [Создание приложения Ruby в Azure](/ru-ru/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-access-storage"></a>Настройка приложения для доступа к хранилищу

Для использования хранилища Azure необходимо загрузить и использовать пакет Ruby Azure, который содержит набор библиотек, взаимодействующих со службами REST хранилища.

### Использование RubyGems для получения пакета

1. Используйте интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix).

2. Введите **gem install azure** в окне командной строки, чтобы установить пакеты и зависимости.

### Импорт пакета

Используйте свой любимый текстовый редактор, чтобы добавить следующий код в начало файла Ruby, где планируется использовать хранилище.

	require "azure"

## <a id="setup-a-windows-azure-storage-connection"></a>Настройка подключения к хранилищу Azure

Модуль Azure считывает переменные среды **AZURE\_STORAGE\_ACCOUNT** и **AZURE\_STORAGE\_ACCESS\_KEY**, чтобы получить информацию, необходимую для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, необходимо указать информацию об учетной записи перед использованием **Azure::TableService** с помощью следующего кода:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"

Для получения этих значений:

1. Войдите на [портал управления Azure](https://manage.windowsazure.com/).

2. Перейдите к учетной записи хранения, которая будет использоваться.

3. Щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ** в нижней части области навигации.

4. В открывшемся диалоговом окне вы увидите имя учетной записи хранения, первичный ключ доступа и вторичный ключ доступа. В качестве ключа доступа можно использовать либо первичный, либо вторичный ключ.

## <a id="how-to-create-a-table"></a>Создание таблицы

Объект **Azure::TableService** позволяет работать с таблицами и сущностями. Чтобы создать таблицу, используйте метод **create\_table()**. В следующем примере создается таблица или выводится ошибка, если она возникла.

	azure_table_service = Azure::TableService.new
	begin
	  azure_table_service.create_table("testtable")
	rescue
	  puts $!
	end

## <a id="how-to-add-an-entity-to-a-table"></a>Добавление сущности в таблицу

Чтобы добавить сущность, сначала создайте хэш-объект, который определяет свойства сущности. Обратите внимание, что для каждой сущности необходимо задать свойства **PartitionKey** и **RowKey**. Это уникальные идентификаторы сущностей, которые можно запросить гораздо быстрее, чем для других свойств. Служба хранилища Azure использует **PartitionKey**, чтобы автоматически распространять сущности таблицы на множество узлов хранилища. Сущности с одним значением **PartitionKey** хранятся на одном узле. **RowKey** - это уникальный идентификатор сущности в разделе, которому она принадлежит. 

	entity = { "content" => "test entity", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.insert_entity("testtable", entity)

## <a id="how-to-update-an-entity"></a>Практическое руководство. Обновление сущности

Для обновления имеющейся сущности доступно несколько методов:

* **update\_entity():** обновление имеющейся сущности с ее заменой.
* **merge\_entity():** обновление сущности посредством объединения новых значений свойств с имеющейся сущностью.
* **insert\_or\_merge\_entity():** обновление имеющейся сущности с ее заменой. Если сущность не существует, будет вставлена новая сущность.
* **insert\_or\_replace\_entity():** обновление сущности посредством объединения новых значений свойств с имеющейся сущностью. Если сущность не существует, будет вставлена новая сущность.

В следующем примере показано, как обновлять сущность с помощью метода **update\_entity()**:

	entity = { "content" => "test entity with updated content", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.update_entity("testtable", entity)

Если сущность, которая обновляется, не существует, при использовании **update\_entity()** и **merge\_entity()** операция обновления завершится ошибкой. Поэтому если вам необходимо сохранить сущность независимо от того, существует ли она, следует использовать **insert\_or\_replace\_entity()** или **insert\_or\_merge\_entity()**.

## <a id="how-to-work-with-groups-of-entities"></a>Практическое руководство: Работа с группами сущностей

Иногда имеет смысл отправлять совместно несколько операций в пакете для атомарной обработки сервером. Для этого сначала создайте объект **Batch**, а затем используйте метод **execute\_batch()** для **TableService**. В следующем примере показана отправка двух сущностей с RowKey 2 и 3 в пакете. Обратите внимание, что пример работает только для сущностей с одинаковым значением PartitionKey.

	azure_table_service = Azure::TableService.new
	batch = Azure::Storage::Table::Batch.new("testtable", 
	  "test-partition-key") do
	  insert "2", { "content" => "new content 2" }
	  insert "3", { "content" => "new content 3" }
	end
	results = azure_table_service.execute_batch(batch)

## <a id="how-to-query-for-an-entity"></a>Практическое руководство: Запрос сущности

Чтобы запросить сущность в таблице, используйте метод **get\_entity()**, передав имя таблицы, **PartitionKey** и **RowKey**.

	result = azure_table_service.get_entity("testtable", "test-partition-key", 
	  "1")

## <a id="how-to-query-a-set-of-entities"></a>Практическое руководство: Запрос набора сущностей

Чтобы запросить набор сущностей в таблице, создайте хэш-объект запроса и используйте метод **query\_entities()**. В следующем примере показано, как получить все сущности с одним значением **PartitionKey**:

	query = { :filter => "PartitionKey eq 'test-partition-key'" }
	result, token = azure_table_service.query_entities("testtable", query)

**Обратите внимание**, что если результирующий набор слишком велик и не может быть возвращен в одном запросе, возвращается маркер продолжения, который можно использовать для извлечения последующих страниц.

## <a id="how-to-query-a-subset-of-entity-properties"></a>Практическое руководство. Запрос подмножества свойств сущности

Запрос к таблице может получить лишь несколько свойств сущности. Этот метод, который называется "проекцией", снижает потребление пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Используйте предложение select и передайте имена свойств, которые необходимо передать клиенту.

	query = { :filter => "PartitionKey eq 'test-partition-key'", 
	  :select => ["content"] }
	result, token = azure_table_service.query_entities("testtable", query)

## <a id="how-to-delete-an-entity"></a>Практическое руководство. Удаление сущности

Чтобы удалить сущность, используйте метод **delete\_entity()**. Необходимо передать имя таблицы, содержащей сущность, а также свойства PartitionKey и RowKey сущности.

		azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <a id="how-to-delete-a-table"></a>Практическое руководство: Удаление таблицы

Чтобы удалить таблицу, используйте метод **delete\_table()** и передайте имя таблицы, которую нужно удалить.

		azure_table_service.delete_table("testtable")

## <a id="next-steps"></a>Дальнейшие действия

Вы изучили основные сведения о табличном хранилище. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

* См. справочник MSDN: [Хранение данных и доступ к ним в Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx)
* Посетите блог [группы разработчиков службы хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* Посетите репозиторий [Пакет SDK для Azure для Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) на веб-сайте GitHub

<!--HONumber=35.1-->
