<properties urlDisplayName="Table Service" pageTitle="Вы научитесь использовать табличное хранилище из .NET в Microsoft Azure" metaKeywords="Get started Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure table .NET   Azure table storage .NET   Azure table C#   Azure table storage C#" description="Вы узнаете дополнительные сведения об использовании табличного хранилища Microsoft Azure для создания и удаления таблиц, вставки данных в таблицы и создания запросов." services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Вы научитесь использовать табличное хранилище Microsoft Azure." authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Использование табличного хранилища из .NET

В этом руководстве показано, как реализовать типичные сценарии
с использованием службы табличного хранилища Azure. Примеры написаны на C# и используют
библиотеку клиента хранения Azure для .NET. Здесь описаны такие сценарии, как **создание и
удаление таблицы**, а также **работа с сущностями в таблице**. Дополнительные
сведения о таблицах см. в разделе [Дальнейшие действия][Дальнейшие действия].

> [WACOM.NOTE] В этом руководстве используется библиотека клиента хранения Azure для .NET 2.x и выше. Рекомендуется использовать библиотеку клиента хранения 4.x, которая доступна через [NuGet][NuGet] или как часть [Пакета Azure SDK для .NET][Пакета Azure SDK для .NET]. См. [Практическое руководство. Программный доступ к табличному хранилищу][Практическое руководство. Программный доступ к табличному хранилищу] ниже для дополнительной информации о получении библиотеки клиента хранения.

## Оглавление

-   [Что такое служба таблиц][Что такое служба таблиц]
-   [Основные понятия][Основные понятия]
-   [Создание учетной записи хранения Azure][Создание учетной записи хранения Azure]
-   [Настройка строки подключения к хранилищу][Настройка строки подключения к хранилищу]
-   [Практическое руководство. Программный доступ к табличному хранилищу][Практическое руководство. Программный доступ к табличному хранилищу]
-   [Практическое руководство. Создание таблицы][Практическое руководство. Создание таблицы]
-   [Практическое руководство. Добавление сущности в таблицу][Практическое руководство. Добавление сущности в таблицу]
-   [Практическое руководство. Вставка пакета сущностей][Практическое руководство. Вставка пакета сущностей]
-   [Практическое руководство. Получение всех сущностей в разделе][Практическое руководство. Получение всех сущностей в разделе]
-   [Практическое руководство. Получение диапазона сущностей в разделе][Практическое руководство. Получение диапазона сущностей в разделе]
-   [Практическое руководство. Извлечение одной сущности][Практическое руководство. Извлечение одной сущности]
-   [Практическое руководство. Замена сущности][Практическое руководство. Замена сущности]
-   [Практическое руководство. Вставка или замена сущности][Практическое руководство. Вставка или замена сущности]
-   [Практическое руководство. Запрос подмножества свойств сущности][Практическое руководство. Запрос подмножества свойств сущности]
-   [Практическое руководство. Удаление сущности][Практическое руководство. Удаление сущности]
-   [Практическое руководство. Удаление таблицы][Практическое руководство. Удаление таблицы]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [руководство-таблица-хранилище](../includes/howto-table-storage.md)]

## 

## <a name="create-account"></a><span class="short-header">Создание учетной записи</span>Создание учетной записи хранения Azure

</h2>
[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

## 

## <a name="setup-connection-string"></a><span class="short-header">Настройка строки подключения</span>Настройка строки подключения к хранилищу

</h2>
[WACOM.INCLUDE [хранилище-настройка-подключение-строка](../includes/storage-configure-connection-string.md)]

## 

## <a name="configure-access"> </a><span class="short-header">Программный доступ</span>Практическое руководство: Программный доступ к табличному хранилищу

</h2>
### Получение сборки

Можно использовать NuGet для получения сборки `Microsoft.WindowsAzure.Storage.dll`. Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Управление пакетами NuGet**. Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет хранилища Azure и зависимые компоненты.

`Microsoft.WindowsAzure.Storage.dll` также включена в состав пакета Azure SDK для .NET, который можно загрузить из [Центра разработчиков .NET][Центра разработчиков .NET]. Сборка устанавливается в каталог `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`lt;sdk-version\>\\ref\\</code>.

### Объявления пространств имен

Добавьте следующие объявления пространств имен кода в начало любого файла C#
,в котором вы собираетесь получать доступ к хранилищу Azure программным способом:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Убедитесь, что указана ссылка на сборку `Microsoft.WindowsAzure.Storage.dll`.

### Получение строки подключения

Для представлений сведений о вашей учетной записи хранения можно использовать тип **CloudStorageAccount**.
 Если вы используете шаблон проекта
Azure или ссылаетесь на
пространство имен Microsoft.WindowsAzure.CloudConfigurationManager, с помощью типа
**CloudConfigurationManager**
 можно извлечь строку подключения и сведения об учетной записи хранения из
конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager и строка подключения находится в файле `web.config` или `app.config`, как показано выше, то для получения строки подключения можно использовать **ConfigurationManager**. Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### Зависимости ODataLib

Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF. Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet. Мы используем пакеты ODataLib [OData][OData], [Edm][Edm] и [Spatial][Spatial].

## <a name="create-table"></a><span class="short-header">Создание таблицы</span>Практическое руководство: Создание таблицы

Объект **CloudTableClient** позволяет ссылаться на объекты таблиц
и сущностей. Следующий код создает объект **CloudTableClient** и использует
его для создания новой таблицы. Во всем коде этого руководства
предполагается, что приложение создается как проект облачной службы Azure и использует
строки подключения, хранимые в конфигурации службы приложения Azure.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

## <a name="add-entity"></a><span class="short-header">Добавление сущности в таблицу</span>Практическое руководство: Добавление сущности в таблицу

Сущности сопоставляются с объектами C# с помощью настраиваемого класса, производного от
**TableEntity**. Чтобы добавить сущность в таблицу,
создайте класс, который определяет свойства сущности. Следующий код
определяет класс сущностей, который использует имя клиента как ключ
строки, а фамилию клиента — как ключ раздела. Вместе ключ раздела и ключ
строки сущности уникальным образом идентифицируют сущность в таблице. Сущности с одинаковым
ключом раздела можно запрашиваться быстрее, чем с другими
ключами раздела, но использование различных ключей
разделов обеспечивает более высокую масштабируемость параллельных операций. Любое свойство, которое будет храниться в службе таблиц,
должно быть открытым свойством поддерживаемого типа, предоставляющим методы `get` и `set`.
Кроме того, тип сущности *обязан* предоставлять конструктор без параметров.

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

Табличные операций, включающие сущности, выполняются с использованием объекта **CloudTable**
, созданного в разделе "Создание таблицы". Выполняемая операция
представляется объектом **TableOperation**. В следующем примере кода показано создание объекта **CloudTable** и объекта **CustomerEntity**. Чтобы подготовить операцию, создается **TableOperation** для вставки сущности customer в таблицу. Наконец, операция выполняется путем вызова **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## <a name="insert-batch"></a><span class="short-header">Вставка пакета сущностей</span>Практическое руководство: Вставка пакета сущностей

Вы можете вставить пакет сущностей в таблицу в одной
операции записи. Некоторые другие примечания к пакетным
операциям:

1.  Можно выполнять операции обновления, удаления и вставки в одной операции пакета.
2.  Одна пакетная операция может включать до 100 сущностей.
3.  У всех сущностей в одной пакетной операции должен быть
    одинаковый ключ раздела.
4.  Хотя для запрос можно выполнить как пакетную операцию, он должен быть единственной операцией в пакете.

<!-- -->

В следующем примере кода создается два объекта сущности, которые добавляются в
**TableBatchOperation** с помощью метода **Insert**. Затем вызывается метод **CloudTable.Execute** для выполнения операции.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

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

## <a name="retrieve-all-entities"></a><span class="short-header">Получение всех сущностей</span>Практическое руководство: Получение всех сущностей в разделе

Чтобы запросить все сущности из таблицы, используйте объект **TableQuery**.
Следующий пример кода задает фильтр для сущностей с ключом раздела "Smith"
. Этот пример выводит на консоль
поля каждой сущности в результатах запроса.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-range-entities"></a><span class="short-header">Получение диапазона сущностей в разделе</span>Практическое руководство: Получение диапазона сущностей в разделе

Если вы не хотите запрашивать все сущности в разделе, можно
указать диапазон, объединив фильтра ключа раздела с фильтром ключа строк. В следующем примере кода
используются два фильтра для получения всех сущностей в разделе "Smith", где ключ строки
(имя) начинается с буквы до "E" в алфавите, после чего
результаты запроса выводятся на консоль.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-single-entity"></a><span class="short-header">Извлечение одной сущности</span>Практическое руководство: Извлечение одной сущности

Можно написать запрос для получения отдельной сущности.
Следующий пример кода использует **TableOperation** для указания клиента "Ben Smith".
 Этот метод возвращает только одну сущность, а не
коллекцию, а возвращаемое значение в **TableResult.Result** равно **CustomerEntity**.
Указание ключа раздела и ключа строки в запросе — самый быстрый способ для
получения одной сущности из службы таблиц.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="replace-entity"></a><span class="short-header">Замена сущности</span>Практическое руководство: Замена сущности

Чтобы обновить сущность, извлеките ее из службы таблицы, измените
объект сущности и сохраните изменения в службе таблиц. Следующий
код изменяет существующий номер телефона клиента. Вместо вызова
 метода **Insert** этот код использует
**Replace**. Из-за этого сущность будет полностью заменена на сервере, если только
сущность на сервере не была изменена с момента извлечения. В подобном случае
операция не будет выполнена. Это необходимо, чтобы приложение
случайно не перезаписало изменения, внесенные с момента извлечения до
обновления другим компонентом приложения. Правильная обработка этой ошибки
заключается в том, чтобы снова извлечь объект, внести необходимые изменения (если это еще возможно), а затем
выполнить еще одну операцию **Replace**. Ниже показано, как
изменить это поведение.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-0105";

       // Create the InsertOrReplace TableOperation
       TableOperation updateOperation = TableOperation.Replace(updateEntity);

       // Execute the operation.
       table.Execute(updateOperation);

       Console.WriteLine("Entity updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="insert-or-replace-entity"></a><span class="short-header">Вставка или замена сущности</span>Практическое руководство: Вставка или замена сущности

Операции **замены** завершаются ошибкой, если сущность была
изменена после извлечения с сервера. Более того, необходимо сначала извлечь
сущность с сервера для того, чтобы операция **Замена** была успешной.
Однако, иногда неизвестно, где находится сущность на сервере
и текущие значения, сохраняемые в ней, не имеют значения. Обновление должно
заменить их всех. Для выполнения этой задачи необходимо использовать операцию **InsertOrReplace**
. Эта операция вставляет сущность, если она не существует, или
заменяет ее, если она существует, независимо от того, когда было выполнено последнее обновление. В следующем
примере кода сущность клиента для Ben Smith все равно извлекается, но затем она сохраняется на сервере с помощью **InsertOrReplace**. Любые обновления
сущности, внесенные между операциями извлечения и обновления, будут
 перезаписаны.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-1234";

       // Create the InsertOrReplace TableOperation
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

       // Execute the operation.
       table.Execute(insertOrReplaceOperation);

       Console.WriteLine("Entity was updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="query-entity-properties"></a><span class="short-header">Запрос подмножества свойств сущности</span>Практическое руководство: Запрос подмножества свойств сущности

Запрос к таблице может получить лишь несколько свойств сущности, а не все свойства. Этот метод, который называется "проекцией", снижает потребление пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Запрос в следующем
коде возвращает только адреса электронной почты сущностей в
таблице. Это делается с помощью запроса из **DynamicTableEntity** и
также **EntityResolver**. Дополнительные сведения о проекции см. в этой [записи блога][записи блога]. Обратите внимание, что проекция не поддерживается в эмуляторе локального хранилища, поэтому этот код выполняется только при использовании учетной записи хранения в службе таблиц.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and only select the Email property
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## <a name="delete-entity"></a><span class="short-header">Удаление сущности</span>Практическое руководство: Удаление сущности

Сущность можно легко удалить после ее получения с использованием того же
шаблона, что и для обновления сущностей. Следующий код
извлекает и удаляет сущность клиента.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       table.Execute(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Could not retrieve the entity.");

## <a name="delete-table"></a><span class="short-header">Удаление таблицы</span>Практическое руководство: Удаление таблицы

Наконец, следующий пример кода удаляет таблицу из учетной записи хранения.
Удаленную таблицу нельзя воссоздать в течение определенного времени
после удаления.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## <a name="next-steps"></a><span class="short-header">Дальнейшие действия </span>Дальнейшие действия

Вы изучили основные сведения о хранилище таблиц. Дополнительные
сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе таблиц:
    -   [Справочник по клиентской библиотеке хранения для .NET][Справочник по клиентской библиотеке хранения для .NET]
    -   [Справочник по REST API][Справочник по REST API]
-   Дополнительные сведения о более сложных задачах, которые можно выполнить с хранилищем Azure, см. в статье [Хранение данных и доступ к ним в Azure][Хранение данных и доступ к ним в Azure].
-   Дополнительные сведения о работе с хранилищем Azure в серверных процессах для веб-сайтов Azure см. в статье [Начиная работу с пакетом Azure WebJobs SDK][Начиная работу с пакетом Azure WebJobs SDK].
-   Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
    -   Использование [Хранилища Blob-объектов][Хранилища Blob-объектов] для хранения неструктурированных данных.
    -   Использование [Хранилища очередей][Хранилища очередей] для хранения структурированных данных.
    -   Использование [Базы данных SQL][Базы данных SQL] для хранения реляционных данных.

  [Дальнейшие действия]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Пакета Azure SDK для .NET]: /ru-ru/downloads/
  [Практическое руководство. Программный доступ к табличному хранилищу]: #configure-access
  [Что такое служба таблиц]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Настройка строки подключения к хранилищу]: #setup-connection-string
  [Практическое руководство. Создание таблицы]: #create-table
  [Практическое руководство. Добавление сущности в таблицу]: #add-entity
  [Практическое руководство. Вставка пакета сущностей]: #insert-batch
  [Практическое руководство. Получение всех сущностей в разделе]: #retrieve-all-entities
  [Практическое руководство. Получение диапазона сущностей в разделе]: #retrieve-range-entities
  [Практическое руководство. Извлечение одной сущности]: #retrieve-single-entity
  [Практическое руководство. Замена сущности]: #replace-entity
  [Практическое руководство. Вставка или замена сущности]: #insert-or-replace-entity
  [Практическое руководство. Запрос подмножества свойств сущности]: #query-entity-properties
  [Практическое руководство. Удаление сущности]: #delete-entity
  [Практическое руководство. Удаление таблицы]: #delete-table
  [Центра разработчиков .NET]: http://www.windowsazure.com/ru-ru/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [записи блога]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [Справочник по клиентской библиотеке хранения для .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Справочник по REST API]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Начиная работу с пакетом Azure WebJobs SDK]: /ru-ru/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Хранилища Blob-объектов]: /ru-ru/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Хранилища очередей]: /ru-ru/documentation/articles/storage-dotnet-how-to-use-queues/
  [Базы данных SQL]: /ru-ru/documentation/articles/sql-database-dotnet-how-to-use/
