<properties urlDisplayName="Table Service" pageTitle="Вы научитесь использовать табличное хранилище  из .NET в Microsoft Azure" metaKeywords="Get started Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure table .NET   Azure table storage .NET   Azure table C#   Azure table storage C#" description="Learn how to use Microsoft Azure Table storage to create and delete tables and insert and query entities in a table." services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="How to use Microsoft Azure Table storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />



# Использование табличного хранилища из .NET


В этом руководстве показано, как реализовать типичные сценарии с использованием службы 
хранилища таблиц Azure. Примеры кода написаны на C\#
и используют библиотеку клиента хранения Azure для .NET. Описываемые здесь сценарии включают **создание и
удаление таблицы**, а также **работу с сущностями таблицы.**. Дополнительные
сведения о таблицах см. в разделе [Дальнейшие действия][Дальнейшие действия].

> [WACOM.NOTE] В этом руководстве используется библиотека клиента хранения Azure для .NET 2.x и выше. Рекомендуется использовать библиотеку клиента хранения 4.x, которая доступна через [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) или как часть [Пакета Azure SDK для .NET](/ru-ru/downloads/). См. [Практическое руководство. Программный доступ к табличному хранилищу][Практическое руководство. Программный доступ к табличному хранилищу] ниже для дополнительной информации о получении библиотеки клиента хранения.

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

## <h2><a name="create-account"></a><span class="short-header">Создание учетной записи</span>Создание учетной записи хранения Azure</h2>

[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a><span class="short-header">Настройка строки подключения</span>Настройка строки подключения к хранилищу</h2>

[WACOM.INCLUDE [хранилище-настройка-подключение-строка](../includes/storage-configure-connection-string.md)]

## <h2> <a name="configure-access"> </a><span  class="short-header">Программный доступ</span>Практическое руководство: Программный доступ к табличному хранилищу</h2>

<h3>Получение сборки</h3>
Вы можете получить сборку "Microsoft.WindowsAzure.Storage.dll" с помощью NuGet. Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Управление пакетами NuGet**.  Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет хранилища Azure и зависимые компоненты.

Библиотека `Microsoft.WindowsAzure.Storage.dll` также включена в пакет Azure SDK для .NET, который можно загрузить из <a href="http://www.windowsazure.com/ru-ru/develop/net/#">Центра разработчиков .NET</a>. Эта сборка устанавливается в каталог `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

<h3>Объявления пространств имен</h3>
Добавьте следующие объявления пространств имен кода в начало любого файла\#,
в котором вы собираетесь получать доступ к хранилищу Azure программным способом:

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Убедитесь, что указана ссылка на сборку `Microsoft.WindowsAzure.Storage.dll`.

<h3>Получение строки подключения</h3>
Можно использовать тип **CloudStorageAccount** для представлений 
сведений о вашей учетной записи хранения. Если вы используете 
шаблон проекта Azure или ссылаетесь на
пространство имен Microsoft.WindowsAzure.CloudConfigurationManager, 
с помощью типа **CloudConfigurationManager**
можно извлечь строку подключения и сведения об учетной записи
хранения из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на Microsoft.WindowsAzure.CloudConfigurationManager, и строка подключения находится в `web.config` или `app.config` как показано выше, для получения строки подключения можно использовать **ConfigurationManager** для получения строки подключения.  Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Зависимости ODataLib</h3>
Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF.  Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet.  Мы используем пакеты [OData], [Edm] и [Spatial].

<h2><a name="create-table"></a><span class="short-header">Создание таблицы</span>Практическое руководство: Создание таблицы</h2>

Объект **CloudTableClient** позволяет ссылаться на объекты очередей
и сущностей. Следующий код создает объект **CloudTableClient**
и использует его для создания новой таблицы. Во всем коде этого руководства предполагается, что
что приложение создается как проект облачной службы Azure и
использует строки подключения, хранимые в конфигурации службы приложения Azure.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент таблицы.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Создать таблицу, если она не существует.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

<h2><a name="add-entity"></a><span class="short-header">Добавление сущности в таблицу</span>Практическое руководство: Добавление сущности в таблицу</h2>

Сущности сопоставляются объектам C\# с помощью настраиваемого класса, производного от
**TableEntity**. Чтобы добавить сущность в таблицу, создайте
класс, который определяет свойства сущности. Следующий код
определяет класс сущностей, который использует имя клиента как
ключ строки, а фамилию клиента - как ключ раздела. Вместе ключ раздела и ключ строки сущности
уникальным образом идентифицируют сущность в таблице. Сущности с
одним ключом раздела можно запрашивать быстрее, чем сущности с разными
ключами раздела, но использование различных ключей разделов обеспечивает более высокую
масштабируемость параллельных операций.  Любое свойство, которое будет храниться в службе таблиц, 
должно быть открытым свойством поддерживаемого типа, предоставляющим методы `get` и `set`.
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

Табличные операций, включающие сущности, выполняются с использованием объекта **CloudTable**
созданного в разделе "Создание таблицы".  Выполняемая операция
представляется объектом **TableOperation**.  В следующем примере кода показано создание объекта **CloudTable** и объекта **CustomerEntity**.  Чтобы подготовить операцию, создается **TableOperation** для вставки сущности customer в таблицу.  Наконец, операция выполняется путем вызова **CloudTable.Execute**.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент таблицы.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Создать объект CloudTable, который представляет таблицу "people".
	CloudTable table = tableClient.GetTableReference("people");

    // Создать новую сущность customer.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Создать операцию TableOperation, которая вставляет сущность customer.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Выполнить операцию вставки.
    table.Execute(insertOperation);

<h2><a name="insert-batch"></a><span class="short-header">Вставка пакета сущностей</span>Практическое руководство: Вставка пакета сущностей</h2>

Вы можете вставить пакет сущностей в таблицу в одной
операции записи. Некоторые другие примечания к пакетным
операциям:

1.  Можно выполнять операции обновления, удаления и вставки в одной операции пакета.
2.  Одна пакетная операция может включать до 100 сущностей.
3.  У всех сущностей в одной пакетной операции должен быть одинаковый
    ключ раздела.
4.  Хотя для запрос можно выполнить как пакетную операцию, он должен быть единственной операцией в пакете.

<!-- -->
В следующем примере кода создается два объекта сущности, которые добавляются в
**TableBatchOperation** с помощью метода **Insert**. Затем вызывается метод **CloudTable.Execute** для выполнения операции.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент таблицы.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
	// Создать объект CloudTable, который представляет таблицу "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Создать пакетную операцию.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Создать сущность customer и добавить ее в таблицу.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";
            
	// Создать другую сущность customer и добавить ее в таблицу.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";
            
	// Добавить обе сущности customer в пакетную операции вставки.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// Выполнить пакетную операцию.
	table.ExecuteBatch(batchOperation);

<h2><a name="retrieve-all-entities"></a><span class="short-header">Получение всех сущностей</span>Практическое руководство: Получение всех сущностей в разделе</h2>

Чтобы запросить все сущности из таблицы, используйте объект **TableQuery**.
Следующий пример кода задает фильтр для сущностей
с ключом раздела "Smith". Этот пример выводит на консоль поля
каждой сущности в результатах запроса.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент таблицы.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Создать объект CloudTable, который представляет таблицу "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Создать операцию запроса для всех сущностей customer, для которых PartitionKey = "Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Вывести поля для каждого клиента.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-range-entities"></a><span class="short-header">Получение диапазона сущностей в разделе</span>Практическое руководство: Получение диапазона сущностей в разделе</h2>

Если вы не хотите запрашивать все сущности в разделе, можно
указать диапазон с помощью операторов сравнения в фильтре. В следующем коде
используются два фильтра для получения всех сущностей в разделе "Smith", 
ключ строки (имя) начинается с буквы до "E" в алфавите,
после чего результаты запроса выводятся на консоль.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент таблицы.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Создать объект CloudTable, который представляет таблицу "people".
    CloudTable table = tableClient.GetTableReference("people");

	// Создать запрос таблицы.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Выполнить цикл по результатам с отображением информации о сущности.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-single-entity"></a><span class="short-header">Извлечение одной сущности</span>Практическое руководство: Извлечение одной сущности</h2>

Можно написать запрос для получения отдельной сущности. Следующий
пример кода использует **TableOperation** для указания клиента "Ben Smith".
Этот метод возвращает только одну сущность, а не
коллекцию, а возвращаемое значение в **TableResult.Result** равно **CustomerEntity**.
Указание ключа раздела и ключа строки в запросе - самый быстрый способ для 
получения одной сущности из службы таблиц.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент таблицы.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Создать объект CloudTable, который представляет таблицу "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Создать операцию извлечения, принимающую сущность customer.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Выполнить операцию извлечения.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Вывести полученный номер телефона.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("Невозможно получить номер телефона.");

<h2><a name="replace-entity"></a><span class="short-header">Замена сущности</span>Практическое руководство: Замена сущности</h2>

Чтобы обновить сущность, извлеките ее из службы таблицы,
измените объект сущности и сохраните изменения в службе таблиц. Следующий
код изменяет существующий номер телефона клиента. Вместо
вызова **Insert** этот код использует 
**Replace**. Из-за этого сущность будет полностью заменена на сервере,
если сущность на сервере была изменена с момента извлечения.
В этом случае операция не будет выполнена.  Это необходимо, чтобы приложение
случайно не перезаписало изменения, внесенные с момента извлечения 
до обновления другим компонентом приложения.  Правильная обработка этой ошибки
заключается в том, чтобы снова извлечь объект, внести необходимые изменения (если это еще возможно), а затем 
выполнить еще одну операцию **Replace**.  Ниже показано,
как изменить это поведение.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент таблицы
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Создать объект CloudTable, который представляет таблицу "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Создать операцию извлечения, принимающую сущность customer.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Выполнить операцию.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Присвоить результат объекту CustomerEntity.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Изменить номер телефона.
	   updateEntity.PhoneNumber = "425-555-0105";

	   // Создать операцию InsertOrReplace TableOperation
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // Выполнить операцию.
	   table.Execute(updateOperation);

	   Console.WriteLine("Entity updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

<h2><a name="insert-or-replace-entity"></a><span class="short-header">Вставка или замена сущности</span>Практическое руководство: Вставка или замена сущности</h2>

**Replace** - эти операции замены завершаются ошибкой, если сущность была изменена
после извлечения с сервера.  Кроме того, для успешного выполнения
операции замены **Replace** сначала необходимо извлечь сущность с сервера.
Тем не менее иногда вы не знаете, существует ли сущность на сервере
и являются ли текущие значения, хранящиеся на нем, актуальными. Обновление должно
заменить их все.  Для выполнения этой задачи необходимо использовать операцию **InsertOrReplace**
.  Эта операция вставляет сущность, если она не существует, или
заменяет ее, если она существует, независимо от того, когда было выполнено последнее обновление.  В диалоговом окне 
В следующем примере кода сущность customer для Ben Smith все равно извлекается, но затем она сохраняется на сервере с помощью **InsertOrReplace**.  Любые обновления
сущности, внесенные между операциями извлечения и обновления, будут 
перезаписаны.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент таблицы.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Создать объект CloudTable, который представляет таблицу "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Создать операцию извлечения, принимающую сущность customer.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Выполнить операцию.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Присвоить результат объекту CustomerEntity.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Изменить номер телефона.
	   updateEntity.PhoneNumber = "425-555-1234";

	   // Создать операцию InsertOrReplace TableOperation
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // Выполнить операцию.
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("Entity was updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

<h2><a name="query-entity-properties"></a><span class="short-header">Запрос подмножества свойств сущности</span>Практическое руководство: Запрос подмножества свойств сущности</h2>

Запрос к таблице может получить лишь несколько свойств сущности, а не все свойства. Этот метод, который называется "проекцией", снижает потребление пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Запрос в следующем коде 
возвращает только адреса электронной почты сущностей
в таблице. Это делается с помощью запроса из **DynamicTableEntity** а 
также **EntityResolver**. Дополнительные сведения о проекции см. в этой [записи блога][записи блога]. Обратите внимание, что проекция не поддерживается в эмуляторе локального хранилища, поэтому этот код выполняется только при использовании учетной записи хранения в службе таблиц.

    // Получить учетную запись хранения из строки подключения
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент таблицы
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Создать объект CloudTable, который представляет таблицу "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Определить запрос и выбрать только свойство Email
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Определить арбитр сущностей для работы с сущностью после извлечения.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

<h2><a name="delete-entity"></a><span class="short-header">Удаление сущности</span>Практическое руководство: Удаление сущности</h2>

Сущность можно легко удалить после ее получения с использованием того же шаблона,
что и для обновления сущностей.  Следующий код
извлекает и удаляет сущность customer.

    // Получить учетную запись хранения из строки подключения
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент таблицы
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Создать объект CloudTable, который представляет таблицу "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Создать операцию извлечения, принимающую сущность customer.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Выполнить операцию.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Присвоить результат объекту CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Создать операцию Delete TableOperation.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Выполнить операцию.
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Could not retrieve the entity.");

<h2><a name="delete-table"></a><span class="short-header">Удаление таблицы</span>Практическое руководство: Удаление таблицы</h2>

Наконец, следующий пример кода удаляет таблицу из учетной записи хранения. Удаленную
таблицу нельзя воссоздать в течение
определенного времени после удаления.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент таблицы.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Создать объект CloudTable, который представляет таблицу "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Удалить таблицу, если она существует.
    table.DeleteIfExists();

<h2><a name="next-steps"></a><span class="short-header">Дальнейшие действия</span>Дальнейшие действия</h2>

Вы изучили основные сведения о табличном хранилище. Дополнительные сведения о более
сложных задачах по использованию хранилища можно найти по следующим ссылкам.

<ul>
<li>Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе таблиц:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Справочник по клиентской библиотеке хранения для .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355">Справочник по REST API</a></li>
  </ul>
</li>
<li>Дополнительные сведения о более сложных задачах, которые можно выполнить с хранилищем Azure, см. в статье <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx">Хранение данных и доступ к ним в Azure</a>.</li>
<li>Дополнительные сведения о работе с хранилищем Azure в серверных процессах для веб-сайтов Azure см. в статье <a href="/ru-ru/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Начиная работу с пакетом Azure WebJobs SDK</a>.</li>
<li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
  <ul>
    <li>Использование <a href="/ru-ru/documentation/articles/storage-dotnet-how-to-use-blobs/">хранилища BLOB-объектов</a> для хранения неструктурированных данных.</li>
    <li>Использование <a href="/ru-ru/documentation/articles/storage-dotnet-how-to-use-queues/">Хранилища очередей</a> для хранения структурированных данных.</li>
    <li>Использование <a href="/ru-ru/documentation/articles/sql-database-dotnet-how-to-use/">базы данных SQL</a> для хранения реляционных данных.</li>
  </ul>
</li>
</ul>

  [Дальнейшие действия]: #next-steps
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
  
  
  [записи блога]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
