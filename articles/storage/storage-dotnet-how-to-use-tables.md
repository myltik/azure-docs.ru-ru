<properties
	pageTitle="Как использовать табличное хранилище из .NET | Microsoft Azure"
	description="Используйте хранилище таблиц для хранения бессхемных неструктурированных данных. Хранилище таблиц представляет собой хранилище ключей (атрибутов) NoSQL. Клиентская библиотека хранилища для .NET предоставляет API-интерфейсы для создания и удаления таблиц, а также для вставки, обновления, удаления и запрашивания данных."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="01/07/2016"
	ms.author="tamram"/>


# Использование табличного хранилища из .NET

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Обзор

В этой статье объясняется, как реализовать типовые сценарии с использованием службы хранения таблиц Azure. Примеры написаны на C#, и в них используется библиотека клиента службы хранилища Azure для .NET. Здесь описаны такие сценарии, как создание и удаление таблицы, а также работа с сущностями таблиц.

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## Программный доступ к табличному хранилищу

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### Объявления пространств имен
Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Обязательно используйте ссылку на сборку `Microsoft.WindowsAzure.Storage.dll`.

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

## Создание таблицы

Объект **CloudTableClient** позволяет ссылаться на объекты таблиц и сущностей. Следующий код создает объект **CloudTableClient** и использует его для создания новой таблицы. Во всем коде в этой статье предполагается, что приложение создается как проект облачной службы Azure и использует строку подключения к хранилищу, хранящуюся в конфигурации службы приложения Azure.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

## Добавление сущности в таблицу

Сущности сопоставляются с объектами C# с помощью настраиваемого класса, производного от **TableEntity**. Чтобы добавить сущность в таблицу, создайте класс, который определяет свойства сущности. Следующий код определяет класс сущностей, который использует имя клиента как ключ строки, а фамилию клиента — как ключ раздела. Вместе ключ раздела и ключ строки сущности уникальным образом идентифицируют сущность в таблице. Сущности с одинаковым ключом раздела можно запрашивать быстрее, чем с разными ключами раздела, но использование различных ключей разделов обеспечивает более высокую масштабируемость параллельных операций. Любое свойство, которое будет храниться в службе таблиц, должно быть открытым свойством поддерживаемого типа, предоставляющим методы `get` и `set`. Кроме того, тип сущности *должен* предоставлять конструктор без параметров.

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

Табличные операции, включающие сущности, выполняются с использованием объекта **CloudTable**, созданного ранее в разделе "Создание таблицы". Выполняемая операция представляется объектом**TableOperation**. В следующем примере кода показано создание объекта **CloudTable** и объекта **CustomerEntity**. Чтобы подготовить операцию, создается объект **TableOperation** для вставки сущности customer в таблицу. Наконец, операция выполняется путем вызова **CloudTable.Execute**.

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

    // Create the TableOperation object that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## Вставка пакета сущностей

Вы можете вставить пакет сущностей в таблицу в одной операции записи. Некоторые другие примечания к пакетным операциям:

-  Можно выполнять операции обновления, удаления и вставки в одной пакетной операции.
-  Одна пакетная операция может включать до 100 сущностей.
-  У всех сущностей в одной пакетной операции должен быть одинаковый ключ раздела.
-  Хотя запрос можно выполнить как пакетную операцию, он должен быть единственной операцией в пакете.

<!-- -->
В следующем примере кода создается два объекта сущности, которые добавляются в **TableBatchOperation** с помощью метода **Insert**. Затем вызывается метод **CloudTable.Execute** для выполнения операции.

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

## Получение всех сущностей в разделе

Чтобы запросить таблицу для всех сущностей в разделе, используйте объект **TableQuery**. Следующий пример кода задает фильтр для сущностей с ключом раздела «Smith». Этот пример выводит на консоль поля каждой сущности в результатах запроса.

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

## Получение диапазона сущностей в разделе

Если вы не хотите запрашивать все сущности в разделе, можно указать диапазон, объединив фильтр ключа раздела с фильтром ключа строк. В следующем примере кода используются два фильтра для получения всех сущностей в разделе «Smith», где ключ строки (имя) начинается с первой буквы алфавита до буквы «E», после чего результаты запроса выводятся в консоль.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
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

## Получение отдельной сущности

Можно написать запрос для получения отдельной сущности. В следующем примере кода с помощью объекта **TableOperation** задается клиент Ben Smith. Этот метод возвращает только одну сущность, а не коллекцию, а возвращаемое значение в **TableResult.Result** является объектом **CustomerEntity**. Указание ключа раздела и ключа строки в запросе — самый быстрый способ для получения одной сущности из службы таблиц.

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

## Замена сущности

Чтобы обновить сущность, извлеките ее из службы таблиц, измените объект сущности и сохраните изменения в службе таблиц. Следующий код изменяет существующий номер телефона клиента. Вместо вызова метода **Insert** этот код использует **Replace**. Из-за этого сущность будет полностью заменена на сервере, если только сущность на сервере не была изменена с момента извлечения. В подобном случае операция не будет выполнена. Это необходимо, чтобы приложение случайно не перезаписало изменения, внесенные с момента извлечения до обновления другим компонентом приложения. Правильный алгоритм обработки этой ошибки заключается в том, чтобы снова получить сущность, внести необходимые изменения (если это еще возможно), а затем выполнить еще одну операцию **Replace**. Ниже показано, как изменить это поведение.

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
	   updateEntity.PhoneNumber = "425-555-0105";

	   // Create the InsertOrReplace TableOperation.
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // Execute the operation.
	   table.Execute(updateOperation);

	   Console.WriteLine("Entity updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

## Вставка или замена сущности

Операции **Replace** завершаются ошибкой, если сущность была изменена с момента получения с сервера. Более того, для успешного выполнения операции **Replace** сначала нужно получить сущность с сервера. Тем не менее иногда вы не знаете, существует ли сущность на сервере и являются ли текущие значения, хранящиеся на нем, актуальными. Обновление должно заменить их все. Для выполнения этой задачи необходимо использовать операцию **InsertOrReplace**. Эта операция вставляет сущность, если она не существует, или заменяет ее, если она существует, независимо от того, когда было выполнено последнее обновление. В следующем примере кода сущность клиента для Ben Smith все равно извлекается, но затем она сохраняется на сервере с помощью **InsertOrReplace**. Любые обновления сущности, внесенные между операциями извлечения и обновления, будут перезаписаны.

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

	   // Create the InsertOrReplace TableOperation.
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // Execute the operation.
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("Entity was updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

## Запрос подмножества свойств сущности

Запрос к таблице может получить лишь несколько свойств сущности, а не все свойства. Этот метод, который называется "проекцией", снижает потребление пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Запрос в следующем коде возвращает только электронные адреса сущностей в таблице. Это делается с помощью запроса **DynamicTableEntity**, а также **EntityResolver**. Дополнительные сведения о проекции см. в записи блога [Введение в Upsert и проекции в запросах][]. Обратите внимание, что проекция не поддерживается в локальном эмуляторе хранения, поэтому этот код выполняется только при использовании учетной записи хранения в службе таблиц.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and select only the Email property.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## Удаление сущности

Сущность можно легко удалить после ее получения с использованием того же шаблона, что и для обновления сущностей. Следующий код извлекает и удаляет сущность клиента.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
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

## Удаление таблицы

Наконец, следующий пример кода удаляет таблицу из учетной записи хранения. Удаленную таблицу нельзя воссоздать в течение определенного времени после удаления.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## Асинхронное получение объектов со страниц

Если необходимо считать большое количество объектов, обрабатывая или отображая их по мере получения (до завершения всей операции), для их получения можно использовать сегментированный запрос. В этом примере объясняется, как расположить запрошенные результаты на странице с помощью алгоритма Async-Await для того, чтобы не блокировать выполнение задачи ожиданием большого объема возвращаемых данных. Дополнительные сведения об использовании алгоритма Async-Await в .NET см. в статье [Асинхронное программирование с использованием ключевых слов Async и Await (C# и Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Initialize a default TableQuery to retrieve all the entities in the table.
    TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

    // Initialize the continuation token to null to start from the beginning of the table.
    TableContinuationToken continuationToken = null;

    do
    {
        // Retrieve a segment (up to 1,000 entities).
        TableQuerySegment<CustomerEntity> tableQueryResult =
            await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

        // Assign the new continuation token to tell the service where to
        // continue on the next iteration (or null if it has reached the end).
        continuationToken = tableQueryResult.ContinuationToken;

        // Print the number of rows retrieved.
        Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

    // Loop until a null continuation token is received, indicating the end of the table.
    } while(continuationToken != null);

## Дальнейшие действия

Вы изучили основные сведения о табличном хранилище. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе таблиц:
    - [Справочник по клиентской библиотеке хранилища для .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Справочник по REST API](http://msdn.microsoft.com/library/azure/dd179355)
- Узнайте, как упростить код, предназначенный для работы со службой хранилища Azure, с помощью [пакета SDK для веб-заданий Azure](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md).
- Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
    - Использование [Хранилища Blob-объектов](storage-dotnet-how-to-use-blobs.md) для хранения неструктурированных данных.
    - Использование [Базы данных SQL](sql-database-dotnet-how-to-use.md) для хранения реляционных данных.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

  [Введение в Upsert и проекции в запросах]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [How to: Programmatically access Table storage]: #tablestorage

<!---HONumber=AcomDC_0114_2016-->