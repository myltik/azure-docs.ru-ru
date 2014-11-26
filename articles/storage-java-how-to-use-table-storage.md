<properties urlDisplayName="Table Service" pageTitle="Использование хранилища таблиц (Java) - Windows Azure" metaKeywords="Azure table storage service, Azure table service Java, table storage Java" description="Learn how to use the table storage service in Azure. Code samples are written in Java code." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Table storage service from Java" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Использование табличного хранилища из Java

В этом руководстве показано, как реализовать типичные сценарии с использованием службы табличного хранилища Azure. Примеры написаны на Java и используют [Пакет SDK хранилища Azure для Java][Пакет SDK хранилища Azure для Java]. Рассматриваются сценарии **создание**, **перечисление** и **удаление** таблиц, а также **вставка**, **запрос**, **изменение** и **удаление** сущностей в таблице. Дополнительные сведения о таблицах см. в разделе [Дальнейшие действия](#NextSteps).

Примечание. Пакет SDK доступен разработчикам, использующим хранилище Azure на устройствах Android. Дополнительные сведения см. в разделе [Пакет SDK хранилища Azure для Android][Пакет SDK хранилища Azure для Android]. 

## <a name="Contents"> </a>Оглавление

* [Что такое хранилище таблиц](#what-is)
* [Основные понятия](#Concepts)
* [Создание учетной записи хранения Azure](#CreateAccount)
* [Создание приложения Java](#CreateApplication)
* [Настройка приложения для доступа к хранилищу таблиц](#ConfigureStorage)
* [Настройка строки подключения к хранилищу Azure](#ConnectionString)
* [Практическое руководство. Создание таблицы](#CreateTable)
* [Практическое руководство. Перечисление таблиц](#ListTables)
* [Практическое руководство. Добавление сущности в таблицу](#AddEntity)
* [Практическое руководство. Вставка пакета сущностей](#InsertBatch)
* [Практическое руководство. Получение всех сущностей в разделе](#RetrieveEntities)
* [Практическое руководство. Получение диапазона сущностей в разделе](#RetrieveRange)
* [Практическое руководство. Извлечение одной сущности](#RetriveSingle)
* [Практическое руководство. Изменение сущности](#ModifyEntity)
* [Практическое руководство. Запрос подмножества свойств сущности](#QueryProperties)
* [Практическое руководство. Вставка или замена сущности](#InsertOrReplace)
* [Практическое руководство. Удаление сущности](#DeleteEntity)
* [Практическое руководство. Удаление таблицы](#DeleteTable)
* [Дальнейшие действия](#NextSteps)

[WACOM.INCLUDE [руководство-таблица-хранилище](../includes/howto-table-storage.md)]

##<a name="CreateAccount"></a>Создание учетной записи хранения Azure

[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

## <a name="CreateApplication"></a>Создание приложения Java

В этом руководстве будут использоваться компоненты хранилища, которые могут быть вызваны локально в приложении Java или в коде, работающем в веб-роли или рабочей роли в Azure.

Чтобы это сделать, необходимо установить комплект разработчика Java (JDK) и создать учетную запись Azure в подписке Azure. После того, как это будет сделано, необходимо убедиться, что ваша система разработки отвечает минимальным требованиям и зависимостям, указанным в репозитории [Пакет SDK хранилища Azure для Java][Пакет SDK хранилища Azure для Java] в GitHub. Если ваша система отвечает указанным требованиям можно приступить к выполнению инструкций по загрузке библиотек хранилища Azure для Java из репозитория и их установке на своей системе. После завершение этих задач вы сможете приступить к созданию приложения Java с использованием примеров из данной статьи.

## <a name="ConfigureStorage"> </a>Настройка приложения для доступа к хранилищу таблиц

Если нужно использовать API-интерфейсы Azure для доступа к таблицам, добавьте следующие инструкции импорта в верхнюю часть файла Java:

    // Включите следующий импорт для интерфейсов API BLOB-объектов.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## <a name="ConnectionString"> </a>Настройка строки подключения к хранилищу Azure

Клиент хранилища Azure использует строку подключения с целью хранения конечных точек и учетных данных для доступа к службам управления данными. При работе в клиентском приложении необходимо указать для хранилища строку подключения в следующем формате, используя имя своей учетной записи хранения и первичный ключ доступа для учетной записи хранения, указанные на портале управления значениями *AccountName* и *AccountKey*. В этом примере показано, как объявить статическое поле для размещения строки подключения:

    // Определить строку подключения с вашим значением.
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Если приложение выполняется в роли Azure, эта строка может храниться в файле конфигурации службы, *ServiceConfiguration.cscfg*, и для доступа к ней можно использовать вызов метода **RoleEnvironment.getConfigurationSettings**. Ниже приведен пример получения строки подключения из элемента **Setting** с именем *StorageConnectionString* в файле конфигурации службы:

    // Получить учетную запись хранения из строки подключения.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

В приведенных ниже примерах предполагается, что вы использовали одно из этих двух определений для получения строки подключения к хранилищу.

## <a name="CreateTable"> </a>Практическое руководство. Создание таблицы

Объект **CloudTableClient** позволяет ссылаться на объекты очередей
и сущностей. Следующий код создает объект **CloudTableClient**
и использует его для создания нового объекта **CloudTable** который представляет таблицу с именем "people". (Примечание. Существуют также другие способы создания объектов **CloudStorageAccount**. Дополнительную информацию см. в разделе **CloudStorageAccount** in the [Справочник по пакету SDK для клиента хранилища Azure].)

    try
    {
    	// Получить учетную запись хранения из строки подключения.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Создать клиент таблицы.
	   CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Создать таблицу, если она не существует.
	   String tableName = "people";
	   CloudTable cloudTable = new CloudTable(tableName,tableClient);
	   cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="ListTables"></a>Практическое руководство. Перечисление таблиц

Чтобы получить список таблиц, вызовите метод **CloudTableClient.listTables()** для извлечения пригодного к итерации списка имен таблиц.

    try
    {
    	// Получить учетную запись хранения из строки подключения.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Создать клиент таблицы.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Пройтись через набор имен таблиц.
    	for (String table : tableClient.listTables())
    	{
		  // Вывести каждое имя таблицы.
		  System.out.println(table);
	   }
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="AddEntity"> </a>Практическое руководство. Добавление сущности в таблицу

Сущности сопоставляются с объектами Java с помощью настраиваемого класса, реализующего **TableEntity**. Для удобства класс **TableServiceEntity** реализует **TableEntity** и использует отражение для сопоставления свойств с указанными для свойства методами geteer и setter. Чтобы добавить сущность в таблицу, сначала создайте класс, который определяет свойства сущности. Следующий код определяет класс сущностей, который использует имя клиента как ключ строки, а фамилию клиента - как ключ раздела. Вместе ключ раздела и ключ строки сущности уникальным образом идентифицируют сущность в таблице. Сущности с одним ключом раздела можно запрашивать быстрее, чем сущности с разными ключами раздела.

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;
        
        public String getEmail() {
            return this.email;
        }
        
        public void setEmail(String email) {
            this.email = email;
        }
        
        public String getPhoneNumber() {
            return this.phoneNumber;
        }
        
        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

Табличные операций, включающие сущности, требуют объект **TableOperation**. Этот объект определяет выполняемую для сущности операцию, которую можно запустить с помощью объекта **CloudTable**. В следующем коде создается новый экземпляр класса **CustomerEntity** с сохраняемыми данными клиента. Далее код вызывает **TableOperation.insert**, чтобы создать объект **TableOperation** для вставки сущности в таблицу, а также связывает с ним новый **CustomerEntity**. Наконец, код вызывает метод **execute** объекта **CloudTable** определяя таблицу "people" и новый **TableOperation**, который затем отправляет запрос в службу хранилища для вставки новой сущности клиента в таблицу "people" или замены сущности, если она уже существует.

    try
    {
    	// Получить учетную запись хранения из строки подключения.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Создать клиент таблицы.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();
			
    	// Создать объект облачной таблицы для таблицы.
    	CloudTable cloudTable = tableClient.getTableReference("people");
			
    	// Создать новую сущность customer.
    	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    	customer1.setEmail("Walter@contoso.com");
    	customer1.setPhoneNumber("425-555-0101");
			
    	// Создать операцию по добавлению нового клиента в таблицу с людьми.
    	TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    	// Отправить операцию в службу таблиц.
    	cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="InsertBatch"> </a>Практическое руководство. Вставка пакета сущностей

Вы можете вставить пакет сущностей в таблицу в одной операции записи. Следующий код создает объект **TableBatchOperation**, а затем добавляет в него три операции вставки. Каждая операция вставки добавляется путем создания нового объекта сущности, установки его значений и последующего вызова метода **insert** для объекта **TableBatchOperation**, чтобы связать сущность с новой операцией вставки. Затем код вызывает **execute** для  объекта **CloudTable** указав таблицу "people", и **TableBatchOperation** объект, который отправляет пакет операций таблицы в службу хранилища в одном запросе.

    try
    {
    	// Получить учетную запись хранения из строки подключения.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Создать клиент таблицы.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Определить пакетную операцию.
    	TableBatchOperation batchOperation = new TableBatchOperation();

    	// Создать объект облачной таблицы для таблицы.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Создать сущность клиента и добавить в таблицу.
    	CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    	customer.setEmail("Jeff@contoso.com");
    	customer.setPhoneNumber("425-555-0104");
    	batchOperation.insertOrReplace(customer);

	   // Создать еще одну сущность клиента и добавить в таблицу.
	   CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	   customer2.setEmail("Ben@contoso.com");
	   customer2.setPhoneNumber("425-555-0102");
	   batchOperation.insertOrReplace(customer2);

	   // Создать сущность третьего клиента и добавить в таблицу.
	   CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
	   customer3.setEmail("Denise@contoso.com");
	   customer3.setPhoneNumber("425-555-0103");
	   batchOperation.insertOrReplace(customer3);

	   // Выполнить пакет операций в таблице people".
	   cloudTable.execute(batchOperation);
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

Некоторые другие примечания к пакетным операциям:

- В отдельном пакете можно выполнить до 100 операций вставки, удаления, объединения, замены, вставки или замены и вставки или замены операций в любом сочетании.
- Пакетная операция может иметь операцию извлечения, если она является единственной операцией в пакете.
- У всех сущностей в одной пакетной операции должен быть одинаковый ключ раздела.
- Объем полезных данных пакетной операции ограничен размером 4 МБ.

## <a name="RetrieveEntities"> </a>Практическое руководство. Получение всех сущностей в разделе

Чтобы запросить из таблицы сущности раздела, можно использовать **TableQuery**. Вызовите **TableQuery.from**, чтобы создать запрос для определенной таблицы, который возвращает результаты заданного типа. Следующий код задает фильтр для сущностей с ключом раздела "Smith". **TableQuery.generateFilterCondition** - это вспомогательный метод для создания фильтров запросов. Вызовите **where** по ссылке, которую вернул метод **TableQuery.from**, чтобы применить фильтр к запросу. При выполнении запроса с помощью вызова **execute** для объекта **CloudTable** он возвращает **Iterator** с указанным типом результата **CustomerEntity**. Затем можно использовать возвращенное значение **Iterator** в каждом цикле для получения результатов. Этот код выводит на консоль поля каждой сущности в результатах запроса.

    try
    {
    	// Определить константу для фильтров.
    	final String PARTITION_KEY = "PartitionKey";
    	final String ROW_KEY = "RowKey";
    	final String TIMESTAMP = "Timestamp";

    	// Получить учетную запись хранения из строки подключения.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Создать клиент таблицы.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();
			
	   // Создать объект облачной таблицы для таблицы.
	   CloudTable cloudTable = tableClient.getTableReference("people");

    	// Создать условия фильтра с ключом раздела "Smith". 
    	String partitionFilter = TableQuery.generateFilterCondition(
	       PARTITION_KEY, 
	       QueryComparisons.EQUAL,
	       "Smith");

	   // Указать запрос раздел с использованием "Smith" в качестве ключевого фильтра.
	   TableQuery<CustomerEntity> partitionQuery =
	       TableQuery.from(CustomerEntity.class)
	       .where(partitionFilter);

        // Выполнить цикл по результатам с отображением информации о сущности.
        for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() + 
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
	   }
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="RetrieveRange"> </a>Практическое руководство. Получение диапазона сущностей в разделе

Если вы не хотите запрашивать все сущности в разделе, можно указать диапазон с помощью операторов сравнения в фильтре. В следующем коде совместно используются два фильтра для получения всех сущностей в разделе "Smith", где ключ строки (имя) начинается с буквы до "E" в алфавите. После чего результаты запроса выводятся на консоль. Если вы используете сущности, добавленные в таблицу во время работы с разделом о пакетной вставке, то на этот раз возвращаются только две сущности (Ben Smith и Denise Smith), а Jeff Smith не выводится.

    try
    {
    	// Определить константу для фильтров.
    	final String PARTITION_KEY = "PartitionKey";
    	final String ROW_KEY = "RowKey";
    	final String TIMESTAMP = "Timestamp";
			
    	// Получить учетную запись хранения из строки подключения.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Создать клиент таблицы.
	   CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Создать объект облачной таблицы для таблицы.
	   CloudTable cloudTable = tableClient.getTableReference("people");

    	// Создать условия фильтра с ключом раздела "Smith".
    	String partitionFilter = TableQuery.generateFilterCondition(
	       PARTITION_KEY, 
	       QueryComparisons.EQUAL,
	       "Smith");

    	// Создать условие для фильтра, где где ключ строки (имя) начинается с буквы до "E" в алфавите.
    	String rowFilter = TableQuery.generateFilterCondition(
	       ROW_KEY, 
	       QueryComparisons.LESS_THAN,
	       "E");

    	// Объединить оба условия в критерий фильтра.
    	String combinedFilter = TableQuery.combineFilters(partitionFilter, 
	        Operators.AND, rowFilter);

    	// Указать запрос раздела с использованием "Smith" в качестве ключа раздела, 
    	// с ключом строки, расположенным до буквы "E"
    	TableQuery<CustomerEntity> rangeQuery =
	       TableQuery.from(CustomerEntity.class)
	       .where(combinedFilter);

    	// Выполнить цикл по результатам с отображением информации о сущности
        for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
        }
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="RetriveSingle"> </a>Практическое руководство. Извлечение одной сущности

Можно написать запрос для получения отдельной сущности. В следующем коде выполняется вызов **TableOperation.retrieve** с параметрами ключа раздела и ключа строки для указания клиента "Jeff Smith" вместо создания **TableQuery** и применения фильтров с таким же результатом. При выполнении операция извлечения возвращает только одну сущность, а не коллекцию. Метод **GetResultAsType** приводит результат к типу назначенной цели - объекту **CustomerEntity**. Если этот тип не совместим с типом, указанным в запросе, возникает исключение. Значение NULL возвращается, если ни одна сущность не подходит по ключам раздела и строки. Указание ключа раздела и ключа строки в запросе - самый быстрый способ для получения одной сущности из службы таблиц.

    try
    {
    	// Получить учетную запись хранения из строки подключения.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Создать клиент таблицы.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Создать объект облачной таблицы для таблицы.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Извлечь сущность с ключом раздела "Smith" и ключом строки "Jeff"
    	TableOperation retrieveSmithJeff = 
	       TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

	   // Отправить операцию в службу таблиц и получить определенную сущность. 
	   CustomerEntity specificEntity =
    		cloudTable.execute(retrieveSmithJeff).getResultAsType();
			
    	// Вывести сущность
    	if (specificEntity != null)
    	{
            System.out.println(specificEntity.getPartitionKey() +
                " " + specificEntity.getRowKey() +
                "\t" + specificEntity.getEmail() +
                "\t" + specificEntity.getPhoneNumber());
	   }
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="ModifyEntity"> </a>Практическое руководство. Изменение сущности

Чтобы изменить сущность, извлеките ее из службы таблиц, измените объект сущности и сохраните изменения в службе таблиц с помощью операции замены или объединения. Следующий код изменяет существующий номер телефона клиента. Вместо вызова метода **TableOperation.insert**,который мы осуществляли при вставке, этот код вызывает **TableOperation.replace**. Метод **CloudTableClient.execute** вызывает службу таблиц и заменяет сущность, если только другое приложение не изменило ее с момента извлечения данным приложением. Когда это происходит, возникает исключение, и сущность необходимо получить, изменить и сохранить повторно. Этот оптимистичный шаблон повторения в случае конфликтов широко применяется в системе распределенного хранения.

    try
    {
    	// Получить учетную запись хранения из строки подключения.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Создать клиент таблицы.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Создать объект облачной таблицы для таблицы.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Извлечь сущность с ключом раздела "Smith" и ключом строки "Jeff".
    	TableOperation retrieveSmithJeff = 
	       TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    	// Отправить операцию в службу таблиц и получить определенную сущность.
    	CustomerEntity specificEntity =
		  cloudTable.execute(retrieveSmithJeff).getResultAsType();

    	// Указать новый номера телефона
    	specificEntity.setPhoneNumber("425-555-0105");

    	// Создать операцию по замене сущности.
    	TableOperation replaceEntity = TableOperation.replace(specificEntity);

    	// Отправить операцию в службу таблиц.
    	cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="QueryProperties"> </a>Практическое руководство. Запрос подмножества свойств сущности

Запрос к таблице может получить лишь несколько свойств сущности. Этот метод, который называется "проекцией", снижает потребление пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Запрос в следующем коде использует метод **select**, чтобы возвратить только адреса электронной почты сущностей в таблице. Результаты проецируются в коллекцию **String** с помощью **EntityResolver**, который выполняет преобразование типов сущностей, возвращенных с сервера. Дополнительные сведения о проекции см. в этой [записи блога][записи блога]. Обратите внимание, что проекция не поддерживается в эмуляторе локального хранилища, поэтому этот код выполняется только при использовании учетной записи хранения в службе таблиц.

    try
    {
        // Получить учетную запись хранения из строки подключения.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

    	// Создать клиент таблицы.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Создать объект облачной таблицы для таблицы.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Определить запрос проекции, который извлекает свойство Email
    	TableQuery<CustomerEntity> projectionQuery = 
	       TableQuery.from(CustomerEntity.class)
	       .select(new String[] {"Email"});

    	// Определить арбитр сущностей для проектирования сущностей в значении Email.
    	EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Выполнить цикл по результатам с отображением значения Email.
        for (String projectedString : 
            cloudTable.execute(projectionQuery, emailResolver)) {
                System.out.println(projectedString);
        }
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="InsertOrReplace"> </a>Практическое руководство. Вставка или замена сущности

Часто требуется добавить сущность в таблицу, не зная, присутствует ли она там. Операция вставки или замены позволяет сделать один запрос, который вставит сущность, если она не существует, или заменит существующую сущность. В продолжение материала предыдущих примеров следующий код вставляет или заменяет сущность "Walter Harp". После создания новой сущности этот код вызывает метод **TableOperation.insertOrReplace**. Далее код вызывает метод **execute** объекта **CloudTable** с таблицей, а также табличными операциями вставить или заменить в качестве параметров. Чтобы обновить только часть сущности можно вместо этого использовать метод **TableOperation.insertOrMerge**. Обратите внимание, что операция вставить-или-заменить не поддерживается в эмуляторе локального хранилища, поэтому этот код выполняется только при использовании учетной записи хранения в службе таблиц. Дополнительные сведения о вставке или замене и вставке или объединении см. в этой [записи блога][записи блога].

    try
    {
        // Получить учетную запись хранения из строки подключения.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Создать клиент таблицы.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Создать объект облачной таблицы для таблицы.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Создать новую сущность customer.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Создать операцию по добавлению нового клиента в таблицу с людьми.
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Отправить операцию в службу таблиц.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="DeleteEntity"> </a>Практическое руководство. Удаление сущности

Сущность можно легко удалить после ее получения. После получение сущности вызовите **TableOperation.delete** с удаляемой сущностью. Затем вызовите **execute** объекта **CloudTable**. Следующий код извлекает и удаляет сущность клиента.

    try
    {
        // Получить учетную запись хранения из строки подключения.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Создать клиент таблицы.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Создать объект облачной таблицы для таблицы.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // СОздать операцию по извлечению сущности с ключом раздела "Smith" и ключом строки "Jeff".
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Извлечь сущность с ключом раздела "Smith" и ключом строки "Jeff".
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Создать операцию по удалению сущности.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Отправить операцию по удалению в службу таблиц.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="DeleteTable"> </a>Практическое руководство. Удаление таблицы

Наконец, следующий код удаляет таблицу из учетной записи хранения. Удаленную таблицу нельзя воссоздать в течение определенного времени после удаления. Этот период обычно составляет менее сорока секунд.

    try
    {
        // Получить учетную запись хранения из строки подключения.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Создать клиент таблицы.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Удалить таблицу и все данные в ней, если они существуют.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Дальнейшие действия

Вы изучили основные сведения о табличном хранилище. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- [Пакет Azure SDK для Java]
- [Справочник по пакету SDK для клиента хранилища Azure]
- [REST API хранилища Azure]
- [Блог команды разработчиков хранилища Azure]

[Пакет Azure SDK для Java]: http://www.windowsazure.com/ru-ru/develop/java/
[Пакет SDK хранилища Azure для Java]: https://github.com/azure/azure-storage-java
[Пакет SDK хранилища Azure для Android]: https://github.com/azure/azure-storage-android
[Справочник по пакету SDK для клиента хранилища Azure]: http://dl.windowsazure.com/storage/javadoc/
[REST API хранилища Azure]: http://msdn.microsoft.com/ru-ru/library/azure/gg433040.aspx
[Блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[записи блога]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
