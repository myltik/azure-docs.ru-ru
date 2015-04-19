<properties 
	pageTitle="Начало работы с хранилищем Azure" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Приступая к работе](vs-storage-aspnet5-getting-started-tables.md)
> - [Что произошло?](vs-storage-aspnet5-what-happened.md)

## Начало работы со службой хранилища Azure (проекты ASP.NET vNext)

> [AZURE.SELECTOR]
> - [BLOB-объектов](vs-storage-aspnet5-getting-started-blobs.md)
> - [Очереди](vs-storage-aspnet5-getting-started-queues.md)
> - [Таблицы](vs-storage-aspnet5-getting-started-tables.md)

В службе хранилища таблиц Azure можно хранить большие объемы структурированных данных. Эта служба - хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных.  Подробнее см. в статье [Как использовать хранилище больших двоичных объектов из .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET").

Для программного доступа к таблицам в проектах ASP.NET 5 необходимо добавить следующие элементы, если они еще не существуют.

1. Добавьте следующее объявление пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;
		using Microsoft.Framework.ConfigurationModel;
		using System.Threading.Tasks;

2. Используйте следующий код для получения параметров настройки.

		Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

##### Получение строки подключения к хранилищу
Прежде чем что-либо делать с таблицей, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться эта таблица. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. При использовании проекта ASP.NET vNext можно вызвать метод GET объекта Configuration, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере.

**Примечание.** Интерфейсы API, выполняющие вызовы к хранилищу Azure в ASP.NET 5, являются асинхронными. См. раздел [асинхронного программирования с помощью Async и Await](http://msdn.microsoft.com/library/hh191443.aspx) для получения дополнительных сведений. В следующем примере кода предполагается, что используются асинхронные методы программирования.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

##### Создание таблицы
Объект **CloudTableClient**  позволяет ссылаться на объекты таблиц и сущностей. Следующий код создает объект **CloudTableClient** и использует его для создания новой таблицы. Код ссылается на таблицу под названием people. Если таблица с таким именем отсутствует, она будет создана.

**Примечание** Во всем коде этого руководства предполагается, что приложение создается как проект облачной службы Azure и использует строку подключения к хранилищу, хранимые в конфигурации службы приложения Azure. Также, вставляйте этот код полностью перед кодом из следующих разделов.

	// Create the table client.
	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the table if it doesn't exist.
	CloudTable table = tableClient.GetTableReference("people");
	await table.CreateIfNotExistsAsync();

##### Добавление сущности в таблицу
Чтобы добавить сущность в таблицу, создайте класс, который определяет свойства сущности. Следующий код определяет класс сущностей с именем **CustomerEntity**, который использует имя клиента как ключ строки, а фамилию клиента - как ключ раздела..

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

Табличные операций, включающие сущности, выполнены с использованием объекта **CloudTable**, созданного ранее в разделе "Создание таблицы". Объект **TableOperation** представляет операцию, которую необходимо выполнить. В следующем примере кода показано создание объектов **CloudTable** и **CustomerEntity**. Чтобы подготовить операцию, создается **TableOperation** для вставки сущности customer в таблицу. Наконец, эта операция выполняется путем вызова CloudTable.ExecuteAsync.

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";
	
	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);
	
	// Execute the insert operation.
	await table.ExecuteAsync(insertOperation);

##### Вставка пакета сущностей
В таблицу можно вставить несколько сущностей с помощью одной операции записи. В следующем примере кода показано создание двух объектов сущностей ("Jeff Smith" и "Ben Smith"), добавление их в объект **TableBatchOperation** с помощью метода Insert и запуск операции с помощью вызова CloudTable.ExecuteBatchAsync.

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
	await table.ExecuteBatchAsync(batchOperation);

##### Получение всех сущностей в разделе
Чтобы запросить все сущности из таблицы, используйте объект **TableQuery**. Следующий пример кода задает фильтр для сущностей с ключом раздела 'Smith'. Этот пример выводит на консоль поля каждой сущности в результатах запроса.

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity> resultSegment = await table.ExecuteQuerySegmentedAsync(query, token);
		token = resultSegment.ContinuationToken;

		foreach (CustomerEntity entity in resultSegment.Results)
    	{
    		Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    		entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


##### Получение одной сущности
Можно написать запрос для получения отдельной сущности. В следующем коде используется **TableOperation** для указания клиента с именем 'Ben Smith'. Этот метод возвращает только одну сущность, а не коллекцию, а возвращаемое значение в TableResult.Result является объектом **CustomerEntity**. Указание ключа раздела и ключа строки в запросе - самый быстрый способ для получения одной сущности из службы **таблиц**.

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the retrieve operation.
	TableResult retrievedResult = await table.ExecuteAsync(retrieveOperation);
	
	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

##### Удаление сущности
После нахождения сущности ее можно удалить. В следующем примере код выполняет поиск сущности с именем "Ben Smith" и при нахождении удаляет ее.

	// Create the CloudTable that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the operation.
	TableResult retrievedResult = table.Execute(retrieveOperation);
	
	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
	
	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
	
	   // Execute the operation.
	   await table.ExecuteAsync(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

[Дополнительные сведения о хранилище Azure](http://azure.microsoft.com/documentation/services/storage/)
См. также [Просмотр ресурсов хранилища в обозревателе серверов](http://msdn.microsoft.com/library/azure/ff683677.aspx) и [ASP.NET 5](http://www.asp.net/vnext).
<!--HONumber=42-->
