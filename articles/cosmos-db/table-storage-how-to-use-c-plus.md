---
title: Как использовать хранилище таблиц Azure и Azure Cosmos DB в C++ | Документация Майкрософт
description: Хранение структурированных данных в облаке в хранилище таблиц Azure (хранилище данных NoSQL).
services: cosmos-db
documentationcenter: .net
author: SnehaGunda
manager: kfile
ms.assetid: f191f308-e4b2-4de9-85cb-551b82b1ea7c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 60c419a45e795d5968b58fbb6b469d06750a0679
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Как использовать хранилище таблиц Azure и API таблиц Azure Cosmos DB в C++
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Обзор
В этом руководстве показано, как реализовать типичные сценарии с использованием службы табличного хранилища Azure или API таблиц Azure Cosmos DB. Примеры написаны на C++ и используют [клиентскую библиотеку хранилища Azure для C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Здесь описаны такие сценарии, как **создание и удаление таблицы**, а также **работа с сущностями таблиц**.

> [!NOTE]
> Данное руководство предназначено для клиентской библиотеки хранилища Azure для С++ версии 1.0.0 и выше. Рекомендуемая версия клиентской библиотеки хранилища — 2.2.0. Она доступна на сайте [NuGet](http://www.nuget.org/packages/wastorage) или [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 

## <a name="create-an-azure-service-account"></a>Создание учетной записи службы Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Создание учетной записи API таблиц Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Создание приложения на C++
В этом руководстве будут использоваться компоненты хранилища, которые могут выполняться в приложениях на C++. Для этого необходимо установить клиентскую библиотеку хранилища Azure для C++ и создать учетную запись хранения Azure в подписке Azure.  

Чтобы установить клиентскую библиотеку хранилища для C++, можно использовать следующие методы.

* **Linux:** следуйте инструкциям, указанным на странице [README клиентской библиотеки службы хранилища Azure для C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
* **Windows:** в Visual Studio нажмите **Инструменты > Диспетчер пакетов NuGet > Консоль диспетчера пакетов**. Введите следующую команду в [консоли диспетчера пакетов NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) и нажмите клавишу ВВОД.  
  
     Install-Package wastorage

## <a name="configure-access-to-the-table-client-library"></a>Настройка доступа к клиентской библиотеке таблиц
Добавьте следующие инструкции в начало файла C++, где требуется использовать API-интерфейсы хранилища Azure для доступа к таблицам.  

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Клиент службы хранилища Azure или Cosmos DB хранит в строке подключения идентификаторы конечных точек и учетные данные для доступа к службам управления данными. При запуске клиентского приложения следует предоставить строку в правильном формате для подключения к хранилищу или к Azure Cosmos DB.

## <a name="set-up-an-azure-storage-connection-string"></a>Настройка строки подключения к службе хранилища Azure
 Для параметров *AccountName* и *AccountKey* укажите значения имени и ключа доступа от учетной записи хранения, которые можно найти на [портале Azure](https://portal.azure.com). Сведения об учетных записях хранения и ключах доступа см. в статье [Об учетных записях хранения Azure](../storage/common/storage-create-storage-account.md). В этом примере показано, как объявить статическое поле для размещения строки подключения к службе хранилища Azure:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="set-up-an-azure-cosmos-db-connection-string"></a>Настройка строки подключения к Azure Cosmos DB
Для параметров *Имя учетной записи*, *Первичный ключ* и *Конечная точка* укажите значения имени и первичного ключа учетной записи Azure Cosmos DB, а также конечную точку, которые можно найти на [портале Azure](https://portal.azure.com). В этом примере показано, как объявить статическое поле для размещения строки подключения к Azure Cosmos DB:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_cosmos_db_account;AccountKey=your_cosmos_db_account_key;TableEndpoint=your_cosmos_db_endpoint"));
```


Чтобы протестировать приложение на локальном компьютере с Windows, можно использовать [эмулятор хранения](../storage/common/storage-use-emulator.md) Azure, устанавливаемый с [пакетом Azure SDK](https://azure.microsoft.com/downloads/). Эмулятор хранения — это служебная программа, моделирующая службы больших двоичных объектов, очередей и таблиц, которые доступны в Azure на локальном компьютере разработки. В следующем примере показано, как объявить статическое поле для размещения строки подключения для эмулятора локального хранилища.  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Чтобы запустить эмулятор хранения Azure, нажмите кнопку **Пуск** или клавишу Windows. Начните вводить **эмулятор хранения Azure**, а затем выберите **Эмулятор хранения Microsoft Azure** из списка приложений.  

В приведенных ниже примерах предполагается, что вы использовали одно из этих двух определений для получения строки подключения к хранилищу.  

## <a name="retrieve-your-connection-string"></a>Получить строку подключения
Информацию о своей учетной записи хранения можно представить с помощью класса **cloud_storage_account**. Чтобы получить данные учетной записи хранения из строки подключения хранилища, можно использовать метод **синтаксического анализа** .

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Затем получите ссылку на класс **cloud_table_client**. Он дает возможность извлечь эталонные объекты для таблиц и сущностей, которые хранятся в службе хранилища таблиц. Следующий код создает объект **cloud_table_client** с помощью объекта учетной записи хранения, полученного выше:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-a-table"></a>Создание таблицы
Объект **cloud_table_client** позволяет получить эталонные объекты для таблиц и сущностей. Следующий код создает объект **cloud_table_client** и использует его для создания таблицы.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

## <a name="add-an-entity-to-a-table"></a>Добавление сущности в таблицу
Чтобы добавить сущность в таблицу, создайте объект **table_entity** и передайте его в **table_operation::insert_entity**. Следующий код использует имя пользователя в качестве ключа строки, а фамилию клиента — как ключ раздела. Вместе ключ раздела и ключ строки сущности уникальным образом идентифицируют сущность в таблице. Сущности с одинаковым ключом раздела можно запрашиваться быстрее, чем с разными ключами раздела, но использование различных ключей разделов обеспечивает более высокую масштабируемость параллельных операций. Дополнительные сведения см. в статье [Производительность хранилища Microsoft Azure и контрольный список масштабируемости](../storage/common/storage-performance-checklist.md).

Следующий код создает новый экземпляр **table_entity** с подлежащими сохранению данными клиента. Затем код вызывает **table_operation::insert_entity** в целях создания объекта **table_operation** для вставки сущности в таблицу и связывает с ним новую сущность таблицы. В завершение код вызывает метод Execute для объекта **cloud_table**. Новый объект **table_operation** отправляет запрос в службу таблиц для вставки сущности нового клиента в таблицу пользователей.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

## <a name="insert-a-batch-of-entities"></a>Вставка пакета сущностей
Вы можете вставить пакет сущностей в службу таблиц за одну операцию записи. Следующий код создает объект **table_batch_operation**, а затем добавляет в него три операции вставки. Каждая операция вставки добавляется путем создания нового объекта сущности, установки его значений и последующего вызова метода вставки для объекта **table_batch_operation**, чтобы связать сущность с новой операцией вставки. Затем вызывается метод **cloud_table.execute** для выполнения операции.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

Некоторые другие примечания к пакетным операциям:  

* В отдельном пакете можно выполнить до 100 операций вставки, удаления, объединения, замены, вставки или слияния и вставки или замены в любом сочетании.  
* Пакетная операция может иметь операцию извлечения, если она является единственной операцией в пакете.  
* У всех сущностей в одной пакетной операции должен быть одинаковый ключ раздела.  
* Максимальный объем полезных данных пакетной операции составляет 4 МБ.  

## <a name="retrieve-all-entities-in-a-partition"></a>Получение всех сущностей в разделе
Чтобы запросить таблицу для всех сущностей в разделе, используйте объект **table_query**. Следующий пример кода задает фильтр для сущностей с ключом раздела "Smith". Этот пример выводит на консоль поля каждой сущности в результатах запроса.  

> [!NOTE]
> Эти методы сейчас не поддерживаются для C++ в базе данных Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

В этом примере запрос отображает все сущности, соответствующие условиям фильтра. При наличии больших таблиц и необходимости часто загружать сущности таблицы мы рекомендуем хранить данные в хранилище BLOB-объектов Azure.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Получение диапазона сущностей в разделе
Если вы не хотите запрашивать все сущности в разделе, можно указать диапазон, объединив фильтр ключа раздела с фильтром ключа строк. В следующем примере кода используются два фильтра для получения всех сущностей в разделе Smith, где ключ строки (имя) начинается с первой буквы алфавита до буквы E, после чего результаты запроса выводятся в консоль.  

> [!NOTE]
> Эти методы сейчас не поддерживаются для C++ в базе данных Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

## <a name="retrieve-a-single-entity"></a>Извлечение одной сущности
Можно написать запрос для получения отдельной сущности. Код ниже использует **table_operation::retrieve_entity** для указания клиента Jeff Smith. Данный метод возвращает только одну сущность, а не множество, и возвращенное значение находится в **table_result**. Указание ключа раздела и ключа строки в запросе — самый быстрый способ извлечь одну сущность из службы таблиц.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

## <a name="replace-an-entity"></a>Замена сущности
Чтобы заменить сущность, извлеките ее из службы таблиц и измените объект сущности, а затем сохраните изменения в службе таблиц. Следующий код изменяет существующий номер телефона и адрес электронной почты клиента. Вместо вызова метода **table_operation::insert_entity** этот код использует **table_operation::replace_entity**. Из-за этого сущность будет полностью заменена на сервере, если только сущность на сервере не была изменена с момента извлечения. В подобном случае операция не будет выполнена. Это необходимо, чтобы приложение случайно не перезаписало изменения, внесенные с момента извлечения до обновления другим компонентом приложения. Правильным методом устранения этой ошибки является повторное извлечение объекта, внесение необходимых изменений (если они еще действительны) и затем выполнение еще одной операции **table_operation::replace_entity**. Ниже показано, как изменить это поведение.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

## <a name="insert-or-replace-an-entity"></a>Вставка или замена сущности
Операции **table_operation::replace_entity** завершатся ошибкой, если сущность была изменена с момента ее получения с сервера. Кроме того, для успешного выполнения действия **table_operation::replace_entity** необходимо сначала извлечь сущность с сервера. Но иногда неизвестно, существует ли сущность на сервере и актуальны ли хранящиеся в ней значения. Обновление должно перезаписать их все. Для этого используйте операцию **table_operation::insert_or_replace_entity**. Эта операция вставляет сущность, если она не существует, или заменяет ее, если она существует, независимо от того, когда было выполнено последнее обновление. В примере кода ниже сущность клиента для Jeff Smith все равно извлекается, но затем она сохраняется на сервере с помощью операции **table_operation::insert_or_replace_entity**. Любые обновления сущности, внесенные между операциями извлечения и обновления, будут перезаписаны.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert-or-replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert-or-replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

## <a name="query-a-subset-of-entity-properties"></a>Запрос подмножества свойств сущности
Запрос к таблице может получить лишь несколько свойств сущности. Запрос в следующем коде использует метод **table_query::set_select_columns**, чтобы возвратить только адреса электронной почты сущностей в таблице.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> Запрос нескольких свойств сущности является более эффективной операцией, чем запрос всех свойств.
> 
> 

## <a name="delete-an-entity"></a>Удаление сущности
Сущность можно легко удалить после ее получения. После получения сущности вызовите **table_operation::delete_entity** с подлежащей удалению сущности. Затем вызовите метод **cloud_table.execute**. Следующий код извлекает и удаляет сущность с ключом раздела Smith и ключом строки Jeff.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

## <a name="delete-a-table"></a>Удаление таблицы
Наконец, следующий пример кода удаляет таблицу из учетной записи хранения. Удаленную таблицу нельзя воссоздать в течение определенного времени после удаления.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
    {
        std::cout << "Table deleted!";
    }
    else
    {
        std::cout << "Table didn't exist";
    }
```

## <a name="troubleshooting"></a>Устранение неполадок
* Ошибки сборки в выпуске Visual Studio Community 2017

  Если сборка проекта завершается ошибкой из-за включаемых файлов storage_account.h и table.h, удалите параметр компилятора **/permissive-**. 
  - В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите **Свойства**.
  - В диалоговом окне **Страницы свойств** разверните узел **Свойства конфигурации**, затем разверните **C/C++** и выберите **Язык**.
  - Для параметра **Режим совместимости** установите значение **Нет**.
   
## <a name="next-steps"></a>Дополнительная информация
Перейдите по приведенным ниже ссылкам, чтобы больше узнать о службе хранилища Azure и API таблиц в Azure Cosmos DB. 

* [Общие сведения об API таблиц Azure Cosmos DB](table-introduction.md)
* [Обозреватель хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.
* [Перечисление ресурсов хранилища Azure в C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Справочник по клиентской библиотеке хранилища для C++](http://azure.github.io/azure-storage-cpp)
* [Документация по службе хранилища Azure](https://azure.microsoft.com/documentation/services/storage/)
