---
title: Как приступить к работе с хранилищем таблиц и подключенными службами Visual Studio (ASP.NET Core) | Документация Майкрософт
description: Начало работы с хранилищем таблиц Azure в проекте ASP.NET Core в Visual Studio после подключения к учетной записи хранения с использованием подключенных служб Visual Studio.
services: storage
author: ghogen
manager: douge
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: e53e8ed27cfc048f24bda4ef92fcd2a50a85ed07
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31794122"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Начало работы с табличным хранилищем Azure и подключенными службами Visual Studio

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

В этой статье описывается, как приступить к использованию хранилища таблиц Azure в Visual Studio после создания учетной записи хранения Azure или указания ссылки на нее в проекте ASP.NET Core с помощью функции **подключенных служб** в Visual Studio. Операция **подключенных служб** устанавливает соответствующие пакеты NuGet для доступа к службе хранилища Azure в вашем проекте и добавляет строку подключения для учетной записи хранения в файлы конфигурации проекта. Общие сведения о службе хранилища Azure см. в [документации по службе хранилища](https://azure.microsoft.com/documentation/services/storage/).

В службе хранилища таблиц Azure можно хранить большие объемы структурированных данных. Эта служба — хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных. Более общие сведения об использовании хранилища таблиц Azure см. в разделе [Приступая к работе с хранилищем таблиц Azure с помощью .NET](../storage/storage-dotnet-how-to-use-tables.md).

Чтобы приступить к работе, сначала необходимо создать таблицу в учетной записи хранения. Затем в этой статье объясняется, как создать таблицу с помощью языка C# и как выполнять базовые операции с таблицами, например добавлять, изменять, считывать и удалять записи в таблицах.  В коде используется клиентская библиотека службы хранилища Microsoft Azure для .NET. Дополнительные сведения см. на сайте [ASP.NET](http://www.asp.net).

Некоторые интерфейсы API службы хранилища Azure являются асинхронными, и в коде, приведенном в этой статье, предполагается, что используются асинхронные методы. Дополнительные сведения см. в статье [Асинхронное программирование](https://docs.microsoft.com/dotnet/csharp/async).

## <a name="access-tables-in-code"></a>Доступ к таблицам в коде

Для доступа к таблицам в проектах ASP.NET Core необходимо добавить следующие элементы во все файлы исходного кода C#, которые обращаются к хранилищу таблиц Azure.

1. Добавьте необходимые инструкции `using`.

    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Получите объект `CloudStorageAccount`, представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure.

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Получите `CloudTableClient` для указания ссылки на объекты таблицы в учетной записи хранения.

    ```cs
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Получите объект ссылки `CloudTable` для указания ссылки на определенную таблицу и сущности.

    ```cs
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Создание таблицы в коде

Чтобы создать таблицу Azure, добавьте вызов CreateIfNotExistsAsync().

```cs
// Create the CloudTable if it does not exist
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Добавление сущности в таблицу

Чтобы добавить сущность в таблицу, создайте класс, который определяет свойства сущности. Следующий код определяет класс сущностей `CustomerEntity`, который использует имя клиента как ключ строки, а фамилию клиента — как ключ секции.

```cs
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

Операции с таблицами с участием сущностей выполняются с использованием объекта `CloudTable`. Его создание описано ранее в разделе [Доступ к таблицам в коде](#access-tables-in-code). Объект `TableOperation` представляет операции, которые необходимо выполнить. В следующем примере кода показано создание объектов `CloudTable` и `CustomerEntity`. Чтобы подготовить операцию, создается `TableOperation` для вставки сущности клиента в таблицу. Наконец, операция выполняется посредством вызова `CloudTable.ExecuteAsync`.

```cs
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Вставка пакета сущностей

В таблицу можно вставить несколько сущностей с помощью одной операции записи. Указанный ниже пример кода создает два объекта сущностей ("Jeff Smith" и "Ben Smith"), добавляет их в объект `TableBatchOperation` с помощью метода `Insert` и запускает операцию с помощью вызова `CloudTable.ExecuteBatchAsync`.

```cs
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
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Получение всех сущностей в разделе

Чтобы запросить все сущности из таблицы, используйте объект `TableQuery`. Следующий пример кода задает фильтр для сущностей с ключом раздела Smith. Этот пример выводит на консоль поля каждой сущности в результатах запроса.

```cs
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Получение одной сущности

Можно написать запрос для получения отдельной сущности. Следующий пример кода использует объект `TableOperation` для указания клиента "Ben Smith". Данный метод возвращает только одну сущность, а не множество, и возвращаемое значение `TableResult.Result` является объектом `CustomerEntity`. Указание ключа раздела и ключа строки в запросе — самый быстрый способ извлечь отдельную сущность из службы `Table`.

```cs
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Удаление сущности

После нахождения сущности ее можно удалить. В следующем код выполняется поиск сущности клиента "Ben Smith".

```cs
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
```

## <a name="next-steps"></a>Дополнительная информация
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
