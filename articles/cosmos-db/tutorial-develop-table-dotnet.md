---
title: Разработка с помощью API таблицы базы данных Azure Cosmos DB на языке .NET | Документация Майкрософт
description: Сведения о разработке с помощью API таблицы базы данных Azure Cosmos DB на языке .NET.
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: e6511b9511d2598b58fd3afee34803ceb09ac5ce
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763462"
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Разработка с помощью API таблицы базы данных Azure Cosmos DB на языке .NET

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования базы данных Azure Cosmos DB.

В рамках этого руководства рассматриваются следующие задачи: 

> [!div class="checklist"] 
> * создание учетной записи Azure Cosmos DB; 
> * включение возможностей в файле app.config; 
> * создание таблицы с помощью [API таблиц](table-introduction.md);
> * Добавление сущности в таблицу 
> * Вставка пакета сущностей 
> * Извлечение одной сущности 
> * запрос сущностей с использованием автоматического вторичного индексирования; 
> * Замена сущности 
> * Удаление сущности 
> * Удаление таблицы
 
## <a name="tables-in-azure-cosmos-db"></a>Таблицы в базе данных Azure Cosmos DB 

База данных Azure Cosmos DB предлагает [API таблиц](table-introduction.md) для приложений, которым требуется хранилище пар "ключ — значение", не использующее схемы. Как API таблиц Azure Cosmos DB, так и [хранилище таблиц Azure](../storage/common/storage-introduction.md) теперь поддерживают одни и те же пакеты SDK и REST API. Azure Cosmos DB позволяет создавать таблицы с высокими требованиями к пропускной способности.

Это руководство предназначено для разработчиков, которые уже работали с пакетом SDK для хранилища таблиц Azure и хотели бы использовать функции уровня "Премиум", доступные в Azure Cosmos DB. Оно основано на сведениях из статьи [Приступая к работе с хранилищем таблиц Azure с помощью .NET](table-storage-how-to-use-dotnet.md) и содержит информацию о том, как воспользоваться преимуществами дополнительных возможностей, таких как вторичное индексирование, подготовка пропускной способности и множественная адресация. В этом руководстве описывается, как создать учетную запись Azure Cosmos DB на портале Azure, а затем создать и развернуть приложение API таблиц. В этом руководстве также представлены примеры .NET для создания и удаления таблиц, а также для вставки, обновления, удаления и запроса данных таблицы. 

Если вы сейчас используете хранилище таблиц Azure, при использовании API таблиц Azure Cosmos DB вы получите следующие преимущества:

- поддержка готового к использованию [глобального распределения](distribute-data-globally.md) со множественной адресацией, а также с [ручной и автоматической отработкой отказа](regional-failover.md);
- поддержка автоматического схемонезависимого индексирования данных для всех свойств ("вторичные индексы") и быстрого выполнения запросов; 
- поддержка [независимого масштабирования хранилища и пропускной способности](partition-data.md) в любом количестве регионов;
- поддержка [выделения пропускной способности на таблицу](request-units.md) с возможностью масштабирования числа запросов в секунду от нескольких сотен до миллионов;
- поддержка [пяти настраиваемых уровней согласованности](consistency-levels.md) с возможностью изменять показатели доступности, задержки и согласованности в соответствии с требованиями приложений;
- доступность уровня 99,99 % в отдельном регионе и возможность добавлять дополнительные регионы для более высокого уровня доступности, а также [ведущие в отрасли исчерпывающие Соглашения об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) в общедоступной версии;
- поддержка имеющегося пакета SDK .NET для службы хранилища Azure и устранение необходимости изменять код приложения.

В этом руководстве описывается API таблиц Azure Cosmos DB с использованием пакета SDK для .NET. Вы можете скачать [пакет SDK .NET для API таблиц Azure Cosmos DB](https://aka.ms/tableapinuget) из NuGet.

Дополнительные сведения о сложных задачах Хранилища таблиц Azure см. здесь:

* [Общие сведения об API таблиц Azure Cosmos DB](table-introduction.md)
* Справочную документацию по службе таблиц с подробными сведениями о доступных API-интерфейсах см. в описании [пакета SDK для .NET API таблиц Azure Cosmos DB](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)

### <a name="about-this-tutorial"></a>О данном учебнике
Это руководство предназначено для разработчиков, которые уже работали с пакетом SDK для Хранилища таблиц Azure и хотели бы использовать функции уровня "Премиум", доступные в базе данных Azure Cosmos DB. Оно основано на сведениях из статьи [Приступая к работе с хранилищем таблиц Azure с помощью .NET](table-storage-how-to-use-dotnet.md) и содержит информацию о том, как воспользоваться преимуществами дополнительных возможностей, таких как вторичное индексирование, подготовка пропускной способности и множественная адресация. Мы рассмотрим, как создать учетную запись базы данных Azure Cosmos DB, а затем создать и развернуть приложение "Таблица" на портале Azure. В этом руководстве также представлены примеры .NET для создания и удаления таблиц, а также для вставки, обновления, удаления и запроса данных таблицы. 

Если вы еще не установили Visual Studio 2017, вы можете скачать и использовать **бесплатный** [выпуск Visual Studio Community 2017](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

Сначала создадим учетную запись базы данных Azure Cosmos DB на портале Azure.  
 
> [!IMPORTANT]  
> Для работы с общедоступными пакетами SDK для API таблиц нужно создать новую учетную запись API таблиц. Учетные записи API таблиц, созданные во время работы с предварительной версией, не поддерживаются в общедоступных пакетах SDK. 
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение "Таблица" из GitHub. Задайте строку подключения и выполните ее. Вы узнаете, как можно упростить работу с данными программным способом. 

1. Откройте окно терминала git, например git bash, и выполните команду `cd`, чтобы перейти в папку для установки примера приложения. 

    ```bash
    cd "C:\git-samples"
    ```

2. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Затем откройте файл решения в Visual Studio. 

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение. Так вы обеспечите обмен данными между приложением и размещенной базой данных. 

1. На [портале Azure](http://portal.azure.com/) щелкните **Строка подключения**. 

    Используйте кнопки копирования в правой части экрана, чтобы скопировать основную строку подключения.

    ![Просмотр и копирование строки подключения на панели "Строка подключения"](./media/create-table-dotnet/connection-string.png)

2. В Visual Studio откройте файл app.config. 

3. В этом руководстве не используется эмулятор хранения. Раскомментируйте элемент StorageConnectionString в строке 8 и закомментируйте элемент StorageConnectionString в строке 7. После этого строки 7 и 8 будут выглядеть так:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Вставьте значение основной строки подключения, полученное на портале, вместо значения параметра StorageConnectionString в строке 8. Значение нужно вставлять в кавычках.
   
    > [!IMPORTANT]
    > Если для вашей конечной точки указан адрес documents.azure.com, это значит, что вы используете учетную запись для предварительной версии. В этом случае для работы с общедоступными пакетами SDK для API таблиц вам нужно создать [новую учетную запись API таблиц](#create-a-database-account). 
    >

    Теперь строка 8 должна выглядеть примерно так:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. Сохраните файл app.config.

Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 

## <a name="azure-cosmos-db-capabilities"></a>Возможности базы данных Azure Cosmos DB
База данных Azure Cosmos DB поддерживает ряд возможностей, которые недоступны в API Хранилища таблиц Azure. 

Доступ к некоторым функциям осуществляется через новые перегрузки CreateCloudTableClient, позволяющие указывать политику подключения и уровень согласованности.

| Параметры подключения к таблицам | ОПИСАНИЕ |
| --- | --- |
| Режим подключения  | База данных Azure Cosmos DB поддерживает два режима подключения. В режиме `Gateway` запросы всегда направляются к шлюзу базы данных Azure Cosmos DB, а затем перенаправляются в соответствующие секции данных. В режиме подключения `Direct` клиент получает сведения о сопоставлении таблиц с секциями, а запросы выполняются непосредственно к секциям данных. Мы советуем использовать метод по умолчанию `Direct`.  |
| Протокол подключения | База данных Azure Cosmos DB поддерживает два протокола подключения: `Https` и `Tcp`. По умолчанию используется протокол `Tcp`. Мы рекомендуем использовать его, так как он более легкий. |
| Предпочтительные расположения | Разделенный запятыми список предпочтительных расположений (множественная адресация) для операций чтения. Каждую учетную запись базы данных Azure Cosmos DB можно связать з разным числом регионов (от одного до более тридцати). Каждый экземпляр клиента может указать подмножество этих регионов в порядке предпочтения, чтобы обеспечить низкую задержку операций чтения. Регионам необходимо присвоить их [отображаемые имена](https://msdn.microsoft.com/library/azure/gg441293.aspx), например `West US`. Дополнительные сведения см. в статье [How to setup Azure Cosmos DB global distribution using the Table API](tutorial-global-distribution-table.md) (Как настроить глобальное распределение Azure Cosmos DB с помощью API таблицы). |
| Уровень согласованности | Вы можете изменить показатели доступности, задержки и согласованности, выбрав один из пяти следующих четко определенных уровней согласованности: `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix` и `Eventual`. Значение по умолчанию — `Session`. От выбранного уровня согласованности существенно зависит производительность в конфигурациях с несколькими регионами. Дополнительные сведения см. в статье [Настраиваемые уровни согласованности данных в DocumentDB](consistency-levels.md). |

Другие возможности можно включить, используя приведенные ниже значения конфигурации `appSettings`.

| Ключ | ОПИСАНИЕ |
| --- | --- |
| TableQueryMaxItemCount | Задайте максимальное количество элементов, возвращаемых в запросе таблицы в рамках одного цикла обхода. Значение по умолчанию — `-1`. Оно позволяет базе данных Azure Cosmos DB динамически определять значение во время выполнения. |
| TableQueryEnableScan | Если запрос не может использовать индекс для какого-либо фильтра, все равно выполните его через сканирование. Значение по умолчанию — `false`.|
| TableQueryMaxDegreeOfParallelism | Степень параллелизма для выполнения запроса между секциями. `0` — порядковое выполнение запросов без предварительной выборки. `1` — порядковое выполнение запросов с предварительной выборкой. Более высокие значения приводят к повышению степени параллелизма. Значение по умолчанию — `-1`. Оно позволяет базе данных Azure Cosmos DB динамически определять значение во время выполнения. |

Чтобы изменить значение по умолчанию, откройте файл `app.config` в обозревателе решений Visual Studio. Добавьте содержимое элемента `<appSettings>` , показанное ниже. Замените `account-name` именем своей учетной записи хранения, а `account-key` — ключом доступа своей учетной записи. 

```xml
<configuration>
    ...
    <appSettings>
      <!-- Client options -->
      <add key="CosmosDBStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://account-name.table.cosmosdb.azure.com" />
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

Сделаем краткий обзор того, что происходит в приложении. Откройте файл `Program.cs`, и вы увидите, что эти строки кода создают ресурсы таблиц. 

## <a name="create-the-table-client"></a>Создание клиента таблицы
Сначала необходимо инициализировать `CloudTableClient`, чтобы подключиться к учетной записи таблицы.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
Инициализация клиента осуществляется на основе значений конфигурации `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel` и `TablePreferredLocations`, если они указаны в параметрах приложения.

## <a name="create-a-table"></a>Создание таблицы
Затем создайте таблицу, используя `CloudTable`. Таблицы в базе данных Azure Cosmos DB можно масштабировать независимо друг от друга в контексте хранилища и пропускной способности. Служба выполняет секционирование автоматически. База данных Azure Cosmos DB поддерживает таблицы с фиксированным и неограниченным размером. Дополнительные сведения см. в статье [Секционирование и масштабирование в базе данных Azure Cosmos DB](partition-data.md). 

```csharp
CloudTable table = tableClient.GetTableReference("people");
400
table.CreateIfNotExists(throughput: 800);
```

Создание таблиц существенно отличается. База данных Azure Cosmos DB резервирует пропускную способность, а в службе хранилища Azure к транзакциям применяется модель с оплатой по мере использования. Пропускная способность выделяется или резервируется, поэтому вам не нужно выполнять регулирование при частоте запросов на уровне или ниже подготовленной пропускной способности.

Вы можете настроить пропускную способность по умолчанию, включив ее в качестве параметра CreateIfNotExists.

Для считывания сущности размером 1 КБ требуется 1 единица запроса. В других операциях это значение также фиксировано и зависит от потребления ресурсов ЦП, памяти и числа операций ввода-вывода в секунду. См. дополнительные сведения о [единицах запросов в Azure Cosmos DB](request-units.md) и специально для [хранилищ значений ключей](key-value-store-cost.md).

Далее мы рассмотрим простые операции чтения и записи (CRUD) с использованием пакета SDK для Хранилища таблиц Azure. В этом руководстве показаны такие преимущества базы данных Azure Cosmos DB, как прогнозируемые задержки операций, длительностью менее 10 секунд, и быстрое выполнение запросов.

## <a name="add-an-entity-to-a-table"></a>Добавление сущности в таблицу
Сущности в Хранилище таблиц Azure расширены с помощью класса `TableEntity` и должны иметь свойства `PartitionKey` и `RowKey`. Ниже приведен пример определения сущности клиента.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

В приведенном ниже фрагменте кода показано, как вставить сущность с помощью пакета SDK для службы хранилища Azure. База данных Azure Cosmos DB обеспечивает низкую задержку при любом масштабе и в любом регионе.

Эта база данных гарантирует выполнение операций записи с менее 15 мс задержки при уровне P99 и примерно 6 мс при уровне P50 для приложения, запущенного в том же расположении, что и учетная запись базы данных Azure Cosmos DB. Это время рассчитано с учетом того, что операции записи подтверждаются клиентом только после их синхронной репликации, надежной фиксации и индексирования всего содержимого.


```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Вставка пакета сущностей
Хранилище таблиц Azure поддерживает API пакетных операций для обновления, удаления и вставки в рамках одной и той же операции.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```
## <a name="retrieve-a-single-entity"></a>Извлечение одной сущности
Операции извлечения (GET) в базе данных Azure Cosmos DB выполняются с менее 10 мс задержки при уровне P99 и примерно 1 мс при уровне P50 в том же регионе Azure. Вы можете добавить в учетную запись нужное число регионов для низкой задержки операций чтения и развернуть приложения для чтения из локального региона (множественная адресация), задав параметр `TablePreferredLocations`. 

Следующий фрагмент кода позволяет извлечь одну сущность:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> Дополнительные сведения об API-интерфейсах множественной адресации см. в статье [How to setup Azure Cosmos DB global distribution using the Table API](tutorial-global-distribution-table.md) (Как настроить глобальное распределение Azure Cosmos DB с помощью API таблицы).
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>Запрос сущностей с использованием автоматического вторичного индексирования
Таблицы можно запрашивать с помощью класса `TableQuery`. База данных Azure Cosmos DB содержит ядро СУБД, оптимизированное для операций записи, которое автоматически индексирует все столбцы в таблице. Индексирование в базе данных Azure Cosmos DB не зависит от схемы. Поэтому, даже если схема отличается между строками или меняется с течением времени, она автоматически индексируется. Так как база данных Azure Cosmos DB поддерживает автоматическое вторичное индексирование, запросы к любому свойству могут использовать индекс и эффективно обрабатываются.

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

База данных Azure Cosmos DB поддерживает те же функции обработки запросов, что и служба таблиц Azure для API таблиц. Кроме того, эта база данных поддерживает сортировку, выполнение статистических вычислений, геопространственные запросы, иерархию и множество встроенных функций. Дополнительные возможности API таблицы будут добавлены в будущем обновлении. Общие сведения об этих возможностях см. в статье [SQL-запросы и синтаксис SQL в DocumentDB](sql-api-sql-query.md). 

## <a name="replace-an-entity"></a>Замена сущности
Чтобы обновить сущность, извлеките ее из службы таблиц, измените объект сущности и сохраните изменения в службе таблиц. Следующий код изменяет существующий номер телефона клиента. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
Аналогичным образом можно выполнить операцию `InsertOrMerge` или `Merge`.  

## <a name="delete-an-entity"></a>Удаление сущности
Сущность можно легко удалить после ее получения с использованием того же шаблона, что и для обновления сущностей. Следующий код извлекает и удаляет сущность клиента.

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>Удаление таблицы
Наконец, следующий пример кода удаляет таблицу из учетной записи хранения. Вы можете удалить и воссоздать таблицу с помощью базы данных Azure Cosmos DB.

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве мы рассмотрели, как начать работу с API таблицы в базе данных Azure Cosmos DB, и сделали следующее: 

> [!div class="checklist"] 
> * создали учетную запись Azure Cosmos DB; 
> * включили возможности в файле app.config; 
> * создали таблицу; 
> * добавили сущность в таблицу; 
> * вставили пакет сущностей; 
> * извлекли одну сущность; 
> * запросили сущности в процессе автоматического вторичного индексирования; 
> * заменили сущность; 
> * удалили сущность; 
> * удалили таблицу.  

Теперь вы можете перейти к следующему руководству и более детально рассмотреть запрос данных таблицы. 

> [!div class="nextstepaction"]
> [Запрос данных с помощью API таблицы](tutorial-query-table.md)
