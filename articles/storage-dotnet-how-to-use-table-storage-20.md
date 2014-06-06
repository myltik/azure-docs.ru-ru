<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="Служба таблиц" pageTitle="Использование табличного хранилища в .NET | Microsoft Azure" metaKeywords="Начало работы с таблицей Azure, Azure nosql, большое структурированное хранилище данных Azure, таблица Azure, табличное хранилище Azure, версия .NET таблицы Azure, версия .NET табличного хранилища Azure, версия C# таблицы Azure, версия C# табличного хранилища Azure" description="Узнайте, как использовать табличное хранилище для создания и удаления таблиц, вставки и запроса сущностей в таблице." services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Использование службы табличного хранилища" authors="tamram" />



# Использование службы табличного хранилища в .NET


В этом руководстве показано, как реализовать типичные сценарии с использованием службы табличного хранилища Azure. Примеры написаны на C# и используют библиотеку клиента хранения Azure для .NET. Здесь описаны такие сценарии, как **создание
и удаление таблицы**, а также **работа с сущностями в таблице**. Дополнительные
сведения о таблицах см. в разделе [Дальнейшие действия][].

## Оглавление

-   [Что такое служба таблиц][]
-   [Основные понятия][]
-   [Создание учетной записи хранения Azure][]
-   [Настройка строки подключения к хранилищу][]
-   [Программный доступ к табличному хранилищу][]
-   [Практическое руководство. Создание таблицы][]
-   [Практическое руководство. Добавление сущности в таблицу][]
-   [Практическое руководство. Вставка пакета сущностей][]
-   [Практическое руководство. Получение всех сущностей в разделе][]
-   [Практическое руководство. Получение диапазона сущностей в разделе][]
-   [Практическое руководство. Извлечение одной сущности][]
-   [Замена сущности][]
-   [Практическое руководство. Вставка или замена сущности][]
-   [Практическое руководство. Запрос подмножества свойств сущности][]
-   [Практическое руководство. Удаление сущности][]
-   [Практическое руководство. Удаление таблицы][]
-   [Дальнейшие действия][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a><span class="short-header">Создание учетной записи</span>Создание учетной записи хранения Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span class="short-header">Настройка строки подключения</span>Настройка строки подключения к хранилищу</h2>

Библиотека клиента хранения Azure для .NET поддерживает использование строки подключения для настройки конечных точек и учетных данных для доступа к службам хранилища. Строку подключения можно разместить в файле конфигурации,
а не жестко задать ее в коде:

- При использовании облачных служб Azure рекомендуется хранить строки подключения с помощью системы конфигурации службы Azure (файлы `*CSDEF` и `*CSCFG`).
- Если используются веб-сайты Azure, виртуальные машины Azure или создаются приложения .NET, которые должны работать вне Azure, рекомендуется хранить строки подключения с помощью системы конфигурации .NET (например, в файле `web.config` или `app.config`).

В обоих случаях можно получить строку подключения с помощью метода `CloudConfigurationManager.GetSetting`, как показано далее в этом руководстве.

### Настройка строки подключения при использовании облачных служб

Механизм настройки службы является уникальным для проектов облачных служб Azure
и позволяет динамически изменять параметры конфигурации на портале управления Azure
без повторного развертывания приложения.

Настройка строки подключения в конфигурации службы
Azure:

1.  В обозревателе решений Visual Studio, в папке **Роли**
    вашего проекта развертывания Azure, щелкните правой кнопкой мыши
    веб-роль или рабочую роль и выберите **Свойства**.  
    ![Blob5][Blob5]

2.  Откройте вкладку **Параметры** и нажмите кнопку **Добавить параметр**.  
    ![Blob6][Blob6]

    В таблице параметров появится новая запись **Setting1**.

3.  В раскрывающемся списке **Тип** новой записи **Setting1** выберите
    **Строка подключения**.  
    ![Blob7][Blob7]

4.  Нажмите кнопку **...** в правой части записи **Setting1**.
    Откроется диалоговое окно **Строка подключения учетной записи хранения**.

5.  Выберите, будет ли использоваться эмулятор хранилища (хранилище Windows
    Azure, моделируемое на локальном компьютере) или фактическая
    учетная запись хранения в облаке. Код, представленный в этом руководстве, работает в любом случае. Введите **Первичный ключ доступа**, скопированный из
   предыдущего шага этого учебника, чтобы сохранить данные BLOB-объекта
   в учетную запись хранения, созданную ранее в Azure.   
    ![Blob8][Blob8]

6.  Измените **Имя** для записи с **Setting1** на более понятное имя,
    например **StorageConnectionString**. Эта строка подключения будет использоваться далее в этом руководстве.  
    ![Blob9][Blob9]
	
### Настройка строки подключения с помощью конфигурации .NET

При написании приложения, которое не является облачной службой Azure (см. предыдущий раздел), рекомендуется использовать систему конфигурации .NET (например, файл `web.config` или `app.config`).  Это относится к веб-сайтам Azure или виртуальным машинам Azure, а также приложениям, разработанным для работы вне Azure.  Строка подключения хранится с помощью элемента `<appSettings>` следующим образом:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
  		</appSettings>
	</configuration>

Дополнительные сведения о строках подключения к хранилищу см. в разделе [Настройка строк подключения][].
	
Теперь все готово для выполнения задач, описанных в данном руководстве.

<h2> <a name="configure-access"> </a><span  class="short-header">Программный доступ</span>Программный доступ к табличному хранилищу</h2>

<h3>Получение сборки</h3>
Вы можете получить сборку `Microsoft.WindowsAzure.Storage.dll` с помощью NuGet. Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Управление пакетами NuGet**.  Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет хранилища Azure и зависимые компоненты.

Библиотека `Microsoft.WindowsAzure.Storage.dll` также включена в пакет Azure SDK для .NET, который можно загрузить из <a href="http://www.windowsazure.com/ru-ru/develop/net/#">Центра разработчиков .NET</a>. Эта сборка устанавливается в каталог `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

<h3>Объявления пространств имен</h3>
Добавьте следующие объявления пространств имен кода в начало любого файла C\#,
в котором вы собираетесь получать доступ к хранилищу Azure программным способом:

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Убедитесь, что указана ссылка на сборку `Microsoft.WindowsAzure.Storage.dll`.

<h3>Получение строки подключения</h3>
Для представлений сведений о вашей учетной записи хранения можно использовать тип **CloudStorageAccount**. Если вы используете шаблон проекта Azure или ссылаетесь на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager, с помощью типа **CloudConfigurationManager** можно извлечь строку подключения и сведения об учетной записи хранения из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager и строка подключения находится в файле `web.config` или `app.config`, как показано выше, то для получения строки подключения можно использовать **ConfigurationManager**.  Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Зависимости ODataLib</h3>
Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF.  Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet.  Мы используем пакеты ODataLib [OData], [Edm] и [Spatial].

<h2><a name="create-table"></a><span class="short-header">Создание таблицы</span>Практическое руководство. Создание таблицы</h2>

Объект **CloudTableClient** позволяет ссылаться на объекты таблиц
и сущностей. Следующий код создает объект **CloudTableClient** и использует
его для создания новой таблицы. Во всем коде этого руководства предполагается, что приложение создается как проект облачной службы Azure и использует строки подключения, хранимые в конфигурации службы приложения Azure.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

<h2><a name="add-entity"></a><span class="short-header">Добавление сущности в таблицу</span>Практическое руководство. Добавление сущности в таблицу</h2>

Сущности сопоставляются с объектами C\# с помощью настраиваемого класса, производного от **TableEntity**. Чтобы добавить сущность в таблицу, создайте класс, который определяет свойства сущности. Следующий код определяет класс сущностей, который использует имя клиента как ключ строки, а фамилию клиента — как ключ раздела. Вместе ключ раздела и ключ строки сущности уникальным образом идентифицируют сущность в таблице. Сущности с одинаковым ключом раздела можно запрашиваться быстрее, чем с другими ключами раздела, но использование различных ключей разделов обеспечивает более высокую масштабируемость параллельных операций.  Любое свойство, которое будет храниться в службе таблиц, должно быть открытым свойством поддерживаемого типа, предоставляющим методы `get` и `set`.
Кроме того, тип сущности *должен* предоставлять конструктор без параметров.

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

Табличные операций, включающие сущности, выполняются с использованием объекта **CloudTable**, созданного в разделе "Создание таблицы".  Выполняемая операция представляется объектом **TableOperation**.  В следующем примере кода показано создание объекта **CloudTable** и объекта **CustomerEntity**.  Чтобы подготовить операцию, создается **TableOperation** для вставки сущности customer в таблицу.  Наконец, операция выполняется путем вызова **CloudTable.Execute**.

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

<h2><a name="insert-batch"></a><span class="short-header">Вставка пакета сущностей</span>Практическое руководство. Вставка пакета сущностей</h2>

Вы можете вставить пакет сущностей в таблицу в одной операции записи. Некоторые другие примечания к пакетным операциям:

1.  Можно выполнять операции обновления, удаления и вставки в одной пакетной операции.
2.  Одна пакетная операция может включать до 100 сущностей.
3.  У всех сущностей в одной пакетной операции должен быть
    одинаковый ключ раздела.
4.  Хотя запрос можно выполнить как пакетную операцию, он должен быть единственной операцией в пакете.

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

<h2><a name="retrieve-all-entities"></a><span class="short-header">Получение всех сущностей</span>Получение всех сущностей в разделе</h2>

Чтобы запросить все сущности из таблицы, используйте объект **TableQuery**.
Следующий пример кода задает фильтр для сущностей с ключом раздела "Smith". Этот пример выводит на консоль поля каждой сущности в результатах запроса.

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

<h2><a name="retrieve-range-entities"></a><span class="short-header">Получение диапазона сущностей</span>Получение диапазона сущностей в разделе</h2>

Если вы не хотите запрашивать все сущности в разделе, можно указать диапазон, объединив фильтра ключа раздела с фильтром ключа строк. В следующем примере кода используются два фильтра для получения всех сущностей в разделе "Smith", где ключ строки (имя) начинается с буквы до "E" в алфавите, после чего результаты запроса выводятся на консоль.

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

<h2><a name="retrieve-single-entity"></a><span class="short-header">Извлечение одной сущности</span>Извлечение одной сущности</h2>

Можно написать запрос для получения отдельной сущности. Следующий пример кода использует **TableOperation** для указания клиента "Ben Smith".
Этот метод возвращает только одну сущность, а не коллекцию, а возвращаемое значение в **TableResult.Result** равно **CustomerEntity**.
Указание ключа раздела и ключа строки в запросе — самый быстрый способ для получения одной сущности из службы таблиц.

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

<h2><a name="replace-entity"></a><span class="short-header">Замена сущности</span>Замена сущности</h2>

Чтобы обновить сущность, извлеките ее из службы таблицы, измените объект сущности и сохраните изменения в службе таблиц. Следующий код изменяет существующий номер телефона клиента. Вместо вызова метода **Insert** этот код использует **Replace**. Из-за этого сущность будет полностью заменена на сервере, если сущность на сервере была изменена с момента извлечения. В этом случае операция не будет выполнена.  Это необходимо, чтобы приложение случайно не перезаписало изменения, внесенные с момента извлечения до обновления другим компонентом приложения.  Правильная обработка этой ошибки заключается в том, чтобы снова извлечь объект, внести необходимые изменения (если это еще возможно), а затем выполнить еще одну операцию **Replace**.  Ниже показано, как изменить это поведение.

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

<h2><a name="insert-or-replace-entity"></a><span class="short-header">Вставка или замена сущности</span>Вставка или замена сущности</h2>

Операции **замены** завершаются ошибкой, если сущность была изменена после извлечения с сервера.  Кроме того, для успешного выполнения операции **замены** сначала необходимо извлечь сущность с сервера.
Тем не менее иногда вы не знаете, существует ли сущность на сервере и являются ли текущие значения, хранящиеся на нем, актуальными. - Обновление должно заменить их все.  Для выполнения этой задачи необходимо использовать операцию **InsertOrReplace**.  Эта операция вставляет сущность, если она не существует, или заменяет ее, если она существует, независимо от того, когда было выполнено последнее обновление.  В следующем примере кода сущность customer для Ben Smith все равно извлекается, но затем она сохраняется на сервере с помощью **InsertOrReplace**.  Любые обновления сущности, внесенные между операциями извлечения и обновления, будут перезаписаны.

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

<h2><a name="query-entity-properties"></a><span class="short-header">Запрос подмножества свойств сущности</span>Практическое руководство. Запрос подмножества свойств сущности</h2>

Запрос к таблице может получить лишь несколько свойств сущности, а не все свойства. Этот метод, который называется "проекцией", снижает потребление пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Запрос в следующем коде возвращает только адреса электронной почты сущностей в таблице. Это делается с помощью запроса из **DynamicTableEntity** и **EntityResolver**. Дополнительные сведения о проекции см. в этой [записи блога][]. Обратите внимание, что проекция не поддерживается в эмуляторе локального хранилища, поэтому этот код выполняется только при использовании учетной записи хранения в службе таблиц.

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

<h2><a name="delete-entity"></a><span class="short-header">Удаление сущности</span>Практическое руководство. Удаление сущности</h2>

Сущность можно легко удалить после ее получения с использованием того же шаблона, что и для обновления сущностей.  Следующий код извлекает и удаляет сущность customer.

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

<h2><a name="delete-table"></a><span class="short-header">Удаление таблицы</span>Практическое руководство. Удаление таблицы</h2>

Наконец, следующий пример кода удаляет таблицу из учетной записи хранения. Удаленную таблицу нельзя воссоздать в течение определенного времени после удаления.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

<h2><a name="next-steps"></a><span class="short-header">Дальнейшие действия</span>Дальнейшие действия</h2>

Вы изучили основные сведения о табличном хранилище. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

<ul>
<li>Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе BLOB-объектов:
  <ul>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn495001(v=azure.10).aspx">Справочник по клиентской библиотеке хранения для .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355">Справочник по REST API</a></li>
  </ul>
</li>
<li>Дополнительные сведения о более сложных задачах, которые можно выполнить с хранилищем Azure, см. в статье <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx">Хранение данных и доступ к ним в Azure</a>.</li>
<li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
  <ul>
    <li>Использование <a href="/ru-ru/develop/net/how-to-guides/blob-storage/">хранилища BLOB-объектов</a> для хранения неструктурированных данных.</li>
    <li>Использование <a href="/ru-ru/develop/net/how-to-guides/sql-database/">базы данных SQL</a> для хранения реляционных данных.</li>
  </ul>
</li>
</ul>

  [Дальнейшие действия]: #next-steps
  [Что такое служба таблиц]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Создание проекта Azure в Visual Studio]: #create-project
  [Настройка приложения для доступа к хранилищу]: #configure-access
  [Настройка строки подключения к хранилищу]: #setup-connection-string
  [Программный доступ к табличному хранилищу]: #configure-access
  [Практическое руководство. Создание таблицы]: #create-table
  [Практическое руководство. Добавление сущности в таблицу]: #add-entity
  [Практическое руководство. Вставка пакета сущностей]: #insert-batch
  [Практическое руководство. Получение всех сущностей в разделе]: #retrieve-all-entities
  [Практическое руководство. Получение диапазона сущностей в разделе]: #retrieve-range-entities
  [Практическое руководство. Извлечение одной сущности]: #retrieve-single-entity
  [Замена сущности]: #replace-entity
  [Практическое руководство. Вставка или замена сущности]: #insert-or-replace-entity
  [Практическое руководство. Запрос подмножества свойств сущности]: #query-entity-properties
  [Практическое руководство. Удаление сущности]: #delete-entity
  [Практическое руководство. Удаление таблицы]: #delete-table
  [Загрузите и установите пакет Azure SDK для .NET]: /ru-ru/develop/net/
  [Создание проекта Azure в Visual Studio]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee405487.aspx
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage-20/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage-20/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage-20/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage-20/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage-20/blob9.png
  
  [записи блога]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [Справочник по библиотеке клиента .NET]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn495001(v=azure.10).aspx[Storing and Accessing Data in Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Настройка строк подключения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

