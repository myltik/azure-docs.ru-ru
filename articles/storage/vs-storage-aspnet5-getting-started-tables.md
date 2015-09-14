<properties
	pageTitle="Начало работы с табличным хранилищем Azure и подключенными службами Visual Studio | Microsoft Azure"
	description="Как приступить к работе с табличным хранилищем Azure в проекте ASP.NET 5 в Visual Studio"
	services="storage"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="patshea123"/>

# Начало работы с табличным хранилищем Azure и подключенными службами Visual Studio

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-tables.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

## Обзор

В этой статье описывается, как приступить к использованию табличного хранилища Azure в Visual Studio после создания учетной записи хранения Azure или указания ссылки на нее в проекте ASP.NET 5 с помощью диалогового окна **Добавление подключенных служб** в Visual Studio.

В службе хранилища таблиц Azure можно хранить большие объемы структурированных данных. Эта служба — хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных.

Операция **Добавить подключенные службы** устанавливает соответствующие пакеты NuGet для доступа к хранилищу Azure в вашем проекте и добавляет строку подключения для учетной записи хранения в файлы конфигурации проекта.

Дополнительные сведения об использовании табличного хранилища Azure см. в разделе [Использование табличного хранилища в .NET](storage-dotnet-how-to-use-tables.md).

Чтобы начать работу, сначала необходимо создать таблицу в учетной записи хранения. Мы покажем, как создать таблицу Azure в коде. Кроме того, мы покажем, как выполнять базовые операции с таблицами и сущностями, например добавлять, изменять и читать сущности таблицы. Примеры написаны на C#, и в них используется библиотека клиента службы хранилища Azure для .NET.

**ПРИМЕЧАНИЕ.** Некоторые интерфейсы API, которые выполняют вызовы в хранилище Azure в ASP.NET 5, являются асинхронными. Дополнительные сведения см. в статье [Асинхронное программирование с использованием ключевых слов Async и Await](http://msdn.microsoft.com/library/hh191443.aspx). В следующем примере кода предполагается, что используются асинхронные методы программирования.

## Доступ к таблицам в коде

Для доступа к таблицам в проектах ASP.NET 5 необходимо включить следующие элементы во все файлы исходного кода C#, которые обращаются к табличному хранилищу Azure.

1. Убедитесь, что объявления пространств имен в верхней части файла C# содержат следующие операторы `using`.

	    using Microsoft.Framework.Configuration;
	    using Microsoft.WindowsAzure.Storage;
	    using Microsoft.WindowsAzure.Storage.Table;
	    using System.Threading.Tasks;
	    using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Получите объект `CloudStorageAccount`, представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure.

	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **ПРИМЕЧАНИЕ.** Вставьте весь код, представленный выше, перед кодом в следующих примерах.

3. Получите объект `CloudTableClient`, чтобы указать ссылку на объекты таблицы в вашей учетной записи хранения.

	    // Create the table client.
    	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Получите объект ссылки `CloudTable` для указания ссылки на определенную таблицу и сущности.

    	// Get a reference to a table named "peopleTable"
	    CloudTable table = tableClient.GetTableReference("peopleTable");

## Создание таблицы в коде

Чтобы создать таблицу Azure, просто добавьте вызов `CreateIfNotExistsAsync()`.

	// Create the CloudTable if it does not exist
	await table.CreateIfNotExistsAsync();

## Добавление сущности в таблицу

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

Табличные операции, включающие в себя сущности, выполняются с использованием объекта `CloudTable`, ранее созданного в разделе "Доступ к таблицам в коде". Объект `TableOperation` представляет операцию, которую необходимо выполнить. В следующем примере кода показано создание объекта `CloudTable` и объекта `CustomerEntity`. Чтобы подготовить операцию, создается `TableOperation` для вставки сущности customer в таблицу. Наконец, эта операция выполняется путем вызова CloudTable.ExecuteAsync.

	// Get a reference to the CloudTable object named 'peopleTable' as described in "Access a table in code"

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";

	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);

	// Execute the insert operation.
	await peopleTable.ExecuteAsync(insertOperation);

## Вставка пакета сущностей

В таблицу можно вставить несколько сущностей с помощью одной операции записи. В следующем примере кода показано создание двух объектов сущностей (Jeff Smith и Ben Smith), их добавление в объект `TableBatchOperation` с помощью метода **Insert** и запуск операции с помощью вызова CloudTable.ExecuteBatchAsync.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

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

## Получение всех сущностей в разделе
Чтобы запросить из таблицы все сущности в секции, используйте объект `TableQuery`. Следующий пример кода задает фильтр для сущностей с ключом раздела "Smith". Этот пример выводит на консоль поля каждой сущности в результатах запроса.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

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

    return View();


## Получение одной сущности
Можно написать запрос для получения отдельной сущности. Следующий пример кода использует `TableOperation` для указания клиента по имени Ben Smith. Этот метод возвращает только одну сущность, а не коллекцию, а возвращаемое значение в `TableResult.Result` является объектом `CustomerEntity`. Указание ключа раздела и ключа строки в запросе — самый быстрый способ для получения одной сущности из службы **таблиц**.

	// Get a reference to a CloudTableobject named 'peopleTable' as described in "Access a table in code"

	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

	// Execute the retrieve operation.
	TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

## Удаление сущности
После нахождения сущности ее можно удалить. В следующем примере код выполняет поиск сущности с именем "Ben Smith" и при нахождении удаляет ее.

	// Get a reference to a CloudTableobject named 'peopleTable' as described in "Access a table in code"

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

## Дальнейшие действия

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

<!---HONumber=September15_HO1-->