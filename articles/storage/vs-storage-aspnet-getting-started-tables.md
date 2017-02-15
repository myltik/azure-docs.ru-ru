---
title: "Приступая к работе с хранилищем таблиц и подключенными службами Visual Studio (ASP.NET) | Документация Майкрософт"
description: "Как приступить к работе, используя табличное хранилище Azure в проекте ASP.NET в Visual Studio после подключения к учетной записи хранения с помощью подключенных служб Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4beefcd636b72d876bd2749307b9616c33742f6b


---
# <a name="get-started-with-table-storage-and-visual-studio-connected-services-aspnet"></a>Начало работы с табличным хранилищем и подключенными службами Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Обзор
В этой статье описывается, как приступить к использованию хранилища таблиц Azure в Visual Studio после создания учетной записи хранения Azure или указания ссылки на нее в проекте ASP.NET с помощью диалогового окна **Добавление подключенных служб** в Visual Studio. В этой статье показано, как выполнять типовые задачи в таблицах Azure, включая создание и удаление таблицы, а также работать с сущностями таблицы. Примеры написаны на языке C\# и используют [клиентскую библиотеку службы хранилища Microsoft Azure для .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Более общие сведения об использовании хранилища таблиц Azure см. в разделе [Приступая к работе с хранилищем таблиц Azure с помощью .NET](storage-dotnet-how-to-use-tables.md).

В табличном хранилище Azure можно хранить большие объемы структурированных данных. Эта служба — хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных.

## <a name="access-tables-in-code"></a>Доступ к таблицам в коде
1. Убедитесь, что объявления пространств имен в верхней части файла C# содержат указанные ниже операторы **using** .
   
         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;
2. Получите объект **CloudStorageAccount** , представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
   
    **ПРИМЕЧАНИЕ.** Вставьте весь код, представленный выше, перед кодом в следующих примерах.
3. Получите объект **CloudTableClient** , чтобы указать ссылку на объекты таблицы в своей учетной записи хранения.  
   
        // Create the table client.
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Получите объект ссылки **CloudTable** для указания ссылки на определенную таблицу и сущности.
   
        // Get a reference to a table named "peopleTable"
        CloudTable table = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Создание таблицы в коде
Чтобы создать таблицу Azure, просто добавьте вызов **CreateIfNotExistsAsync()** в указанный выше код.

    // Create the CloudTable if it does not exist
    await table.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Добавление сущности в таблицу
Чтобы добавить сущность в таблицу, создайте класс, который определяет свойства сущности. Следующий код определяет класс сущностей с именем **CustomerEntity** который использует имя клиента как ключ строки, а фамилию клиента — как ключ раздела.

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

Операции с таблицами с участием сущностей выполняются с использованием объекта **CloudTable**. Его создание описано ранее в разделе "Доступ к таблицам в коде". Объект **TableOperation** представляет операции, которые необходимо выполнить. В следующем примере кода показано создание объектов **CloudTable** и **CustomerEntity**. Чтобы подготовить операцию, создается **TableOperation** для вставки сущности customer в таблицу. Наконец, эта операция выполняется путем вызова CloudTable.ExecuteAsync.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);

## <a name="insert-a-batch-of-entities"></a>Вставка пакета сущностей
В таблицу можно вставить несколько сущностей с помощью одной операции записи. Указанный ниже пример кода создает два объекта сущностей (Jeff Smith и Ben Smith), добавляет их в объект **TableBatchOperation** с помощью метода Insert и запускает операцию с помощью вызова **CloudTable.ExecuteBatchAsync**.

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
    await peopleTable.ExecuteBatchAsync(batchOperation);

## <a name="get-all-of-the-entities-in-a-partition"></a>Получение всех сущностей в разделе
Чтобы запросить все сущности из таблицы, используйте объект **TableQuery** . Следующий пример кода задает фильтр для сущностей с ключом раздела "Smith". Этот пример выводит на консоль поля каждой сущности в результатах запроса.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity>
        resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
        }
        } while (token != null);

        return View();


## <a name="get-a-single-entity"></a>Получение одной сущности
Можно написать запрос для получения отдельной сущности. Следующий пример кода использует **TableOperation** для указания клиента "Ben Smith". Этот метод возвращает только одну сущность, а не коллекцию, и возвращаемое значение в **TableResult.Result** является объектом **CustomerEntity**. Указание ключа секции и ключа строки в запросе — самый быстрый способ для получения одной сущности из службы таблиц.

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
        Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Удаление сущности
После нахождения сущности ее можно удалить. В следующем примере код выполняет поиск сущности с именем "Ben Smith" и при нахождении удаляет ее.

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation and then execute it.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       await peopleTable.ExecuteAsync(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Couldn't delete the entity.");

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]




<!--HONumber=Nov16_HO3-->


