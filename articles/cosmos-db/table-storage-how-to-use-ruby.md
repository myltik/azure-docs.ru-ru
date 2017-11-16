---
title: "Как использовать хранилище таблиц Azure с Ruby | Документация Майкрософт"
description: "Хранение структурированных данных в облаке в хранилище таблиц Azure (хранилище данных NoSQL)."
services: cosmos-db
documentationcenter: ruby
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: b1ad9fc7b609209ca867e64226c3a0e393081408
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-use-azure-table-storage-with-ruby"></a>Как использовать хранилище таблиц Azure с Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Обзор
В этом руководстве показано, как реализовать типичные сценарии с использованием службы таблиц Azure. Примеры написаны с помощью Ruby API. Здесь описаны такие сценарии, как **создание и удаление таблицы, вставка и запрос сущностей в таблице**.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Создание приложения Ruby
Инструкции по созданию приложение Ruby см. в статье [Веб-приложение Ruby on Rails на виртуальной машине Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Настройка приложения для доступа к хранилищу
Чтобы использовать службу хранилища Azure, скачайте пакет Azure для Ruby, который содержит набор библиотек, взаимодействующих со службами REST хранилища.

### <a name="use-rubygems-to-obtain-the-package"></a>Использование RubyGems для получения пакета
1. Используйте интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix).
2. Введите **gem install azure** в окне командной строки, чтобы установить пакеты и зависимости.

### <a name="import-the-package"></a>Импорт пакета
С помощью любого текстового редактора добавьте указанный код в начало файла Ruby, где планируется использовать службу хранилища.

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Настройка подключения к службе хранилища Azure
Модуль Azure считывает переменные среды **AZURE\_STORAGE\_ACCOUNT** и **AZURE\_STORAGE\_ACCESS\_KEY**, чтобы получить информацию, необходимую для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, необходимо указать сведения об учетной записи перед использованием **Azure::TableService** с помощью следующего кода:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

Вот как можно получить эти значения из классический учетной записи хранения или учетной записи хранения Resource Manager на портале Azure.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите к учетной записи хранения, которая будет использоваться.
3. В колонке "Параметры" справа щелкните **Ключи доступа**.
4. В колонке "Ключи доступа" вы увидите ключи доступа 1 и 2. Можно использовать любой из них.
5. Щелкните значок копирования, чтобы скопировать ключ в буфер обмена.

## <a name="create-a-table"></a>Создание таблицы
Объект **Azure::TableService** позволяет работать с таблицами и сущностями. Чтобы создать таблицу, используйте метод **create\_table()**. В следующем примере создается таблица или выводится ошибка, если она возникла.

```ruby
azure_table_service = Azure::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Добавление сущности в таблицу
Чтобы добавить сущность, сначала создайте хэш-объект, который определяет свойства сущности. Обратите внимание, что для каждой сущности необходимо указать **PartitionKey** и **RowKey**. Это уникальные идентификаторы сущностей, которые можно запросить гораздо быстрее, чем для других свойств. Служба хранилища Azure использует **PartitionKey** , чтобы автоматически распространять сущности таблицы на множество узлов хранилища. Сущности с одним значением **PartitionKey** хранятся на одном узле. **RowKey** — это уникальный идентификатор сущности в разделе, которому она принадлежит.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Обновление сущности
Для обновления имеющейся сущности доступно несколько методов:

* **update\_entity():** обновляет имеющуюся сущность путем ее замены.
* **merge\_entity():** обновляет сущность посредством объединения новых значений свойств с имеющейся сущностью.
* **insert\_or\_merge\_entity():** обновляет имеющуюся сущность путем ее замены. Если сущность не существует, будет вставлена новая сущность.
* **insert\_or\_replace\_entity():** обновляет сущность посредством объединения новых значений свойств с имеющейся сущностью. Если сущность не существует, будет вставлена новая сущность.

В следующем примере показано обновление сущности с помощью метода **update\_entity()**.

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Если сущность, которая обновляется, не существует, при использовании метода **update\_entity()** или **merge\_entity()** операция завершится ошибкой. Поэтому, если вы хотите сохранить сущность независимо от того, существует она или нет, используйте метод **insert\_or\_replace\_entity()** или **insert\_or\_merge\_entity()**.

## <a name="work-with-groups-of-entities"></a>Работа с группами сущностей
Иногда имеет смысл отправлять совместно несколько операций в пакете для атомарной обработки сервером. Для этого сначала требуется создать объект **Batch**, а затем использовать метод **execute\_batch()** для **TableService**. В следующем примере показана отправка двух сущностей с RowKey 2 и 3 в пакете. Обратите внимание, что пример работает только для сущностей с одинаковым значением PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Запрос сущности
Чтобы отправить запрос к сущности в таблице, используйте метод **get\_entity()**: передайте имя таблицы, **PartitionKey** и **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Запрос набора сущностей
Чтобы отправить запрос к набору сущностей в таблице, создайте хэш-объект запроса и используйте метод **query\_entities()**. В следующем примере показано, как получить все сущности с одним значением **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Если полученный в результате набор слишком велик и не может быть возвращен в одном запросе, возвращается токен продолжения, который можно использовать для извлечения последующих страниц.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Запрос подмножества свойств сущности
Запрос к таблице может получить лишь несколько свойств сущности. Этот метод, который называется "проекцией", снижает потребление пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Используйте предложение select и передайте имена свойств, которые необходимо передать клиенту.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Удаление сущности
Чтобы удалить сущность, используйте метод **delete\_entity()**. Необходимо передать имя таблицы, содержащей сущность, а также свойства PartitionKey и RowKey сущности.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Удаление таблицы
Чтобы удалить таблицу, используйте метод **delete\_table()** и передайте имя удаляемой таблицы.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Дальнейшие действия

* [Обозреватель хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.
* [пакетов SDK Azure для Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) на веб-сайте GitHub

