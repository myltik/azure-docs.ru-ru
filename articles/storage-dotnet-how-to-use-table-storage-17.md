<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="Служба таблиц (2.0)" pageTitle="Использование табличного хранилища | Microsoft Azure" metaKeywords="Начало работы с таблицей Azure, Azure nosql, большое структурированное хранилище данных Azure, таблица Azure, табличное хранилище Azure, версия .NET таблицы Azure, версия .NET табличного хранилища Azure, версия C# таблицы Azure, версия C# табличного хранилища Azure" description="Узнайте, как использовать табличное хранилище для создания и удаления таблиц, вставки и запроса сущностей в таблице." metaCanonical="" services="storage" documentationCenter=".NET" title="Использование службы табличного хранилища" authors="" solutions="" manager="paulettm" editor="cgronlun" />








# Использование службы табличного хранилища

<div class="dev-center-tutorial-selector">
<a href="/ru-ru/develop/net/how-to-guides/table-services-v17/" title="версия 1.7" class="current">версия 1.7</a>
<a href="/ru-ru/develop/net/how-to-guides/table-services/" title="версия 2.0">версия 2.0</a> 
</div>


В этом руководстве показано, как реализовать типичные сценарии с использованием службы табличного хранилища Azure. Примеры написаны на языке C\#, и в них
используется API-интерфейс .NET. Здесь описаны такие сценарии, как **создание и удаление
таблицы, вставка и запрос сущностей в таблице**. Дополнительные
сведения о таблицах см. в разделе [Дальнейшие действия][Дальнейшие действия].

## Оглавление

-   [Что такое служба таблиц][Что такое служба таблиц]
-   [Основные понятия][Основные понятия]
-   [Создание учетной записи хранения Azure][Создание учетной записи хранения Azure]
-   [Настройка строки подключения к хранилищу][Настройка строки подключения к хранилищу]
-   [Программный доступ к табличному хранилищу][Программный доступ к табличному хранилищу]
-   [Практическое руководство. Создание таблицы][Практическое руководство. Создание таблицы]
-   [Практическое руководство. Добавление сущности в таблицу][Практическое руководство. Добавление сущности в таблицу]
-   [Практическое руководство. Вставка пакета сущностей][Практическое руководство. Вставка пакета сущностей]
-   [Практическое руководство. Получение всех сущностей в разделе][Практическое руководство. Получение всех сущностей в разделе]
-   [Практическое руководство. Получение диапазона сущностей в разделе][Практическое руководство. Получение диапазона сущностей в разделе]
-   [Практическое руководство. Извлечение одной сущности][Практическое руководство. Извлечение одной сущности]
-   [Практическое руководство. Обновление сущности][Практическое руководство. Обновление сущности]
-   [Практическое руководство. Запрос подмножества свойств сущности][Практическое руководство. Запрос подмножества свойств сущности]
-   [Практическое руководство. Вставка или замена сущности][Практическое руководство. Вставка или замена сущности]
-   [Практическое руководство. Удаление сущности][Практическое руководство. Удаление сущности]
-   [Практическое руководство. Удаление таблицы][Практическое руководство. Удаление таблицы]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a><span class="short-header">Создание учетной записи</span>Создание учетной записи хранения Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span class="short-header">Настройка строки подключения</span>Настройка строки подключения к хранилищу</h2>

API хранилища Azure для .NET поддерживает использование строки подключения
для настройки конечных точек и учетных данных для доступа к
службам хранилища. Строку подключения можно разместить в файле конфигурации,
а не жестко задать ее в коде:

- При использовании облачных служб Azure рекомендуется хранить строки подключения с помощью системы конфигурации службы Azure (файлы `*CSDEF` и `*CSCFG`).
- При использовании веб-сайтов Azure или виртуальных машин Azure рекомендуется хранить строки подключения с помощью системы конфигурации .NET (например, в файле `web.config`).

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
	
### Настройка строки подключения при использовании веб-сайтов или виртуальных машин

При использовании веб-сайтов или виртуальных машин рекомендуется использовать систему конфигурации .NET (например, `web.config`).  Строка подключения хранится с помощью элемента `<appSettings>`:

	<configuration>
	    <appSettings>
		    <add key="StorageConnectionString"
			     value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
		</appSettings>
	</configuration>

Дополнительные сведения о строках подключения к хранилищу см. в разделе [Настройка строк подключения][Настройка строк подключения].
	
Теперь все готово для выполнения задач, описанных в данном руководстве.


<h2> <a name="configure-access"> </a><span  class="short-header">Программный доступ</span>Программный доступ к табличному хранилищу</h2>

Добавьте следующие объявления пространств имен кода в начало любого файла C\#,
в котором вы собираетесь получать доступ к хранилищу Azure программным способом:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Можно использовать тип **CloudStorageAccount** и
**CloudConfigurationManager**,
чтобы извлечь строку подключения и сведения об учетной записи
хранения из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));


<h2><a name="create-table"></a><span class="short-header">Создание таблицы</span>Практическое руководство. Создание таблицы</h2>

Объект **CloudTableClient** позволяет ссылаться на объекты таблиц
и сущностей. Следующий код создает объект **CloudTableClient** и использует
его для создания новой таблицы. Для всего кода в этом руководстве используется строка
подключения хранилища, сохраненная в конфигурации службы приложения
Azure. Существуют также другие способы создания
объекта **CloudStorageAccount**.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist
    string tableName = "people";
    tableClient.CreateTableIfNotExist(tableName);

<h2><a name="add-entity"></a><span class="short-header">Добавление сущности в таблицу</span>Практическое руководство. Добавление сущности в таблицу</h2>

Сущности сопоставляются объектам C\# с помощью настраиваемого класса, производного от
**TableServiceEntity** Чтобы добавить сущность в таблицу, сначала
создайте класс, который определяет свойства сущности. Следующий код
определяет класс сущностей, который использует имя клиента как ключ
строки, а фамилию клиента — как ключ раздела. Вместе ключ раздела и ключ строки сущности уникальным образом идентифицируют сущность в таблице. Сущности с одним
ключом раздела можно запрашивать быстрее, чем сущности с разными
ключами раздела.

    public class CustomerEntity : TableServiceEntity
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

Для табличных операций с объектами требуется объект **TableServiceContext**. Этот объект отслеживает клиентское состояние всех сущностей
таблицы, которые создаются и вызываются в клиентском коде. Хранение клиентского объекта,
представляющего каждую сущность, повышает эффективность операций записи, так как
при выполнении операции сохранения в службе таблиц обновляются только
объекты с изменениями. Следующий код создает объект
**TableServiceContext**, вызывая метод **GetDataServiceContext**. Затем код создает экземпляр класса **CustomerEntity**. Код вызывает **serviceContext.AddObject** для вставки нового объекта
в таблицу. Это добавляет объект сущности в
** serviceContext **, но никакие операции службы не выполняются. Наконец, код
отправляет новую сущность в службу таблиц при
вызове метода **SaveChangesWithRetries**.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a new customer entity
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Add the new customer to the people table
    serviceContext.AddObject("people", customer1);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

<h2><a name="insert-batch"></a><span class="short-header">Вставка пакета сущностей</span>Практическое руководство. Вставка пакета сущностей</h2>

Вы можете вставить пакет сущностей в службу таблиц за одну
операцию записи. Следующий код создает три объекта сущностей, которые добавляются
в контекст службы с помощью метода **AddObject**. Затем код вызывает
**SaveChangesWithRetries** с параметром **SaveChangesOptions.Batch**. Если опустить **SaveChangesOptions.Batch**, выполняется три отдельных вызова службы таблиц. Некоторые другие примечания к пакетным операциям:

1.  Можно выполнять пакетные обновления, удаления или вставки.
2.  Одна пакетная операция может включать до 100 сущностей.
3.  У всех сущностей в одной пакетной операции должен быть
    одинаковый ключ раздела.

<!-- -->

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    string tableName = "people";

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a customer entity and add to the table
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.Email = "Jeff@contoso.com";
    customer.PhoneNumber = "425-555-0104";
    serviceContext.AddObject(tableName, customer);

    // Create another customer entity and add to the table
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";
    serviceContext.AddObject(tableName, customer2);

    // Create a customer entity and add to the table
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.Email = "Denise@contoso.com";
    customer3.PhoneNumber = "425-555-0103";
    serviceContext.AddObject(tableName, customer3);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.Batch);

<h2><a name="retrieve-all-entities"></a><span class="short-header">Получение всех сущностей</span>Получение всех сущностей в разделе</h2>

Чтобы запросить из таблицы сущности раздела, можно использовать запрос LINQ.
Вызовите **serviceContext.CreateQuery** для создания запроса из источника
данных. Следующий код задает фильтр для сущностей с ключом
раздела "Smith". Вызовите **AsTableServiceQuery&lt;CustomerEntity&gt;** для
результата запроса LINQ, чтобы завершить создание объекта
**CloudTableQuery**. Затем можно использовать объект **partitionQuery**, созданный в
цикле **foreach**, для применения результатов. Этот код выводит на консоль поля
каждой сущности в результатах запроса.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Specify a partition query, using "Smith" as the partition key
    CloudTableQuery<CustomerEntity> partitionQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith"
         select e).AsTableServiceQuery<CustomerEntity>();

    // Loop through the results, displaying information about the entity
    foreach (CustomerEntity entity in partitionQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-range-entities"></a><span class="short-header">Получение диапазона сущностей</span>Получение диапазона сущностей в разделе</h2>

Если не нужно запрашивать все сущности в разделе, можно
указать диапазон с помощью метода **CompareTo** вместо использования обычных
операторов "больше" (&gt;) и "меньше" (&lt;). Это вызвано тем, что
последний вариант приведет к построению неправильного запроса. В следующем коде
используются два фильтра для получения всех сущностей в разделе "Smith",
где ключ строки (имя) начинается с буквы до "E" в алфавите. После чего
результаты запроса выводятся на консоль. Если вы используете сущности,
добавленные в таблицу во время работы с разделом о пакетной вставке, то на этот раз возвращаются только две сущности (Ben Smith и Denise Smith),
а Jeff Smith не выводится.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Specify a partition query, using "Smith" as the partition key,
    // with the row key being up to the letter "E"
    CloudTableQuery<CustomerEntity> entityRangeQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey.CompareTo("E") < 0
         select e).AsTableServiceQuery<CustomerEntity>();

    // Loop through the results, displaying information about the entity
    foreach (CustomerEntity entity in entityRangeQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-single-entity"></a><span class="short-header">Извлечение одной сущности</span>Извлечение одной сущности</h2>

Можно написать запрос для получения отдельной сущности. Следующий
пример кода использует два фильтра для указания клиента "Jeff Smith".
Вместо вызова **AsTableServiceQuery** этот код вызывает
**FirstOrDefault**. Этот метод возвращает только одну сущность, а не
коллекцию, поэтому код назначает возвращаемое значение непосредственно
объекту **CustomerEntity**. Значение NULL возвращается, если ни одна сущность не подходит по ключам раздела и строки. Указание ключа раздела и ключа строки
в запросе — самый быстрый способ для получения одной сущности из службы
таблиц.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Return the entity with partition key of "Smith" and row key of "Jeff"
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

<h2><a name="update-entity"></a><span class="short-header">Обновление сущности</span>Практическое руководство. Обновление сущности</h2>

Чтобы обновить сущность, извлеките ее из службы таблицы, измените объект сущности и сохраните изменения в службе таблиц. Следующий код изменяет существующий номер телефона клиента. Вместо
вызова **AddObject**, использовавшегося для вставки, этот код вызывает
**UpdateObject**. Метод **SaveChangesWithRetries** вызывает службу таблиц
и обновляет сущность, если только другое приложение не изменило ее с момента
извлечения данным приложением. Когда это происходит,
возникает исключение, и сущность необходимо получить, изменить и
сохранить повторно. Этот шаблон повторения широко применяется в системе распределенного
хранения.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Return the entity with partition key of "Smith" and row key of "Jeff"
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Specify a new phone number
    specificEntity.PhoneNumber = "425-555-0105";

    // Update the entity
    serviceContext.UpdateObject(specificEntity);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

<h2><a name="query-entity-properties"></a><span class="short-header">Запрос подмножества свойств</span>Практическое руководство. Запрос подмножества свойств сущности</h2>

Запрос к таблице может получить лишь несколько свойств сущности.
Этот метод, который называется "проекцией", снижает потребление
пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Запрос в следующем коде возвращает только адреса электронной почты сущностей в таблице. Дополнительные сведения о проекции см. в этой [записи блога][записи блога]. Обратите внимание,
что проекция не поддерживается в эмуляторе локального хранилища,
поэтому этот код выполняется только при использовании учетной записи
хранения в службе таблиц.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Define a projection query that retrieves only the Email property
    var projectionQuery = 
        from e in serviceContext.CreateQuery<CustomerEntity>("people")
        select new
        {
            Email = e.Email
            // You can specify additional fields here
        };

    // Loop through the results, displaying the Email value
    foreach (var person in projectionQuery)
    {
        Console.WriteLine(person.Email);
    }

<h2><a name="insert-entity"></a><span class="short-header">Вставка или замена сущности</span>Практическое руководство. Вставка или замена сущности</h2>

Часто требуется добавить сущность в таблицу, не зная, присутствует ли она там. Операция вставки или замены позволяет сделать
один запрос, который вставит сущность, если она не существует,
или заменит существующую сущность. В продолжение материала предыдущих примеров следующий код вставляет или заменяет сущность "Walter Harp". После
создания новой сущности этот код вызывает метод
**serviceContext.AttachTo**. Затем этот код вызывает **UpdateObject** и, наконец, вызывает
**SaveChangesWithRetries** с
параметром **SaveChangesOptions.ReplaceOnUpdate**. Отсутствие
параметра **SaveChangesOptions.ReplaceOnUpdate** приводит к
выполнению операции вставки или объединения. Обратите внимание, что вставка или замена не поддерживается в эмуляторе локального хранилища,
поэтому этот код выполняется только при использовании учетной записи хранения в
службе таблиц. Дополнительные сведения о вставке или замене и вставке или объединении см. в этой [записи блога][записи блога].

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a new customer entity
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.Email = "Walter@contoso.com";
    customer5.PhoneNumber = "425-555-0106";

    // Attach this customer to the people table
    serviceContext.AttachTo("people", customer5);

    // Insert this customer if new, or replace if exists
    serviceContext.UpdateObject(customer5);

    // Submit the operation the table service, using the ReplaceOnUpdate option
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.ReplaceOnUpdate);

<h2><a name="delete-entity"></a><span class="short-header">Удаление сущности</span>Практическое руководство. Удаление сущности</h2>

Сущность можно легко удалить после ее получения. Можно также
использовать метод **AttachTo**, чтобы начать отслеживать ее без извлечения
с сервера (см. приведенное выше описание операции вставки или замены). При отслеживании
сущности с помощью **serviceContext** вызовите **DeleteObject**
для удаляемой сущности. Затем вызовите **SaveChangesWithRetries**. Следующий код извлекает и удаляет сущность customer.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Delete the entity
    serviceContext.DeleteObject(specificEntity);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

<h2><a name="delete-table"></a><span class="short-header">Удаление таблицы</span>Практическое руководство. Удаление таблицы</h2>

Наконец, следующий код удаляет таблицу из учетной записи хранения. Удаленную таблицу нельзя воссоздать в течение определенного времени после удаления.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Delete the table it if exists
    tableClient.DeleteTableIfExist("people");

<h2><a name="next-steps"></a><span class="short-header">Дальнейшие действия</span>Дальнейшие действия</h2>

Вы изучили основные сведения о табличном хранилище. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

<ul>
<li>Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе BLOB-объектов:
  <ul>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/wl_svchosting_mref_reference_home">Справочник по библиотеке клиента .NET</a>
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
  [Настройка строки подключения к хранилищу]: #setup-connection-string
  [Программный доступ к табличному хранилищу]: #configure-access
  [Практическое руководство. Создание таблицы]: #create-table
  [Практическое руководство. Добавление сущности в таблицу]: #add-entity
  [Практическое руководство. Вставка пакета сущностей]: #insert-batch
  [Практическое руководство. Получение всех сущностей в разделе]: #retrieve-all-entities
  [Практическое руководство. Получение диапазона сущностей в разделе]: #retrieve-range-entities
  [Практическое руководство. Извлечение одной сущности]: #retrieve-single-entity
  [Практическое руководство. Обновление сущности]: #update-entity
  [Практическое руководство. Запрос подмножества свойств сущности]: #query-entity-properties
  [Практическое руководство. Вставка или замена сущности]: #insert-entity
  [Практическое руководство. Удаление сущности]: #delete-entity
  [Практическое руководство. Удаление таблицы]: #delete-table
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage-17/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage-17/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage-17/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage-17/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage-17/blob9.png
  
  [записи блога]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [Настройка строк подключения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758697.aspx

