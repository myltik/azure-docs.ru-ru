#####Создание таблицы
Объект **CloudTableClient** позволяет ссылаться на объекты таблиц и сущностей. Следующий код создает объект **CloudTableClient** и использует его для создания новой таблицы. Код ссылается на таблицу под названием people. Если таблица с таким именем отсутствует, она будет создана.

**ПРИМЕЧАНИЕ.** Во всем коде этого руководства предполагается, что приложение создается как проект облачной службы Azure и использует строку подключения к хранилищу, хранимые в конфигурации службы приложения Azure.

	// Create the table client.
	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the table if it doesn't exist.
	CloudTable table = tableClient.GetTableReference("people");
	table.CreateIfNotExists();

#####Добавление сущности в таблицу
Чтобы добавить сущность в таблицу, создайте класс, который определяет свойства сущности. Следующий код определяет класс сущностей с именем **CustomerEntity**, который использует имя клиента как ключ строки, а фамилию клиента - как ключ раздела.

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

Табличные операции, включающие сущности, выполняются с использованием объекта **CloudTable**, созданного в разделе "Создание таблицы". Объект **TableOperation** представляет операции, которые необходимо выполнить. В следующем примере кода показано создание объекта **CloudTable** и объекта **CustomerEntity**. Чтобы подготовить операцию, создается **TableOperation** для вставки сущности customer в таблицу. Наконец, операция выполняется путем вызова CloudTable.Execute.

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

#####Вставка пакета сущностей
В таблицу можно вставить несколько сущностей с помощью одной операции записи. В следующем примере кода показано создание двух объектов сущностей ("Jeff Smith" и "Ben Smith"), добавление их в объект **TableBatchOperation** с помощью метода Insert и запуск операции с помощью вызова CloudTable.Execute.

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

#####Получение всех сущностей в разделе
Чтобы запросить все сущности из таблицы, используйте объект **TableQuery**. Следующий пример кода задает фильтр для сущностей с ключом раздела 'Smith'. Этот пример выводит на консоль поля каждой сущности в результатах запроса.

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

#####Получение одной сущности
Вы можете написать запрос для получения одной конкретной сущности. В следующем коде используется объект **TableOperation** для указания клиента с именем 'Ben Smith'. Этот метод возвращает только одну сущность, а не коллекцию, а возвращаемое значение в TableResult.Result является объектом **CustomerEntity**. Указание ключа раздела и ключа строки в запросе - самый быстрый способ для получения одной сущности из службы **таблиц**.

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

#####Удаление сущности
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
	   table.Execute(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

[Дополнительные сведения о службе хранилища Azure](http://azure.microsoft.com/documentation/services/storage/)
См. также раздел [Общие сведения о ресурсах службы хранилища в обозревателе серверов](http://msdn.microsoft.com/ru-ru/library/azure/ff683677.aspx).

<!--HONumber=42-->
