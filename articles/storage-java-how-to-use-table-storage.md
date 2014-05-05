<properties linkid="dev-java-how-to-use-table-storage" urlDisplayName="Служба таблиц" pageTitle="Использование хранилища таблиц (Java) | Microsoft Azure" metaKeywords="служба хранилища таблиц в Azure, служба таблиц Java в Azure, служба таблиц Java" description="Узнайте, как использовать службу хранилища таблиц в Azure. Примеры кода написаны на Java." metaCanonical="" services="storage" documentationCenter="Java" title="Использование службы хранилища таблиц в Java" authors="" solutions="" manager="" editor="" />


# Использование службы хранилища таблиц в Java

В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища таблиц Azure. Примеры кода написаны на
Java. Здесь описаны такие сценарии, как **создание и удаление таблицы,
вставка и запрос сущностей в таблице**. Дополнительные
сведения о таблицах см. в разделе [Дальнейшие действия](#NextSteps).

## <a name="Contents"> </a>Оглавление

* [Что такое хранилище таблиц](#what-is)
* [Основные понятия](#Concepts)
* [Создание учетной записи хранилища Azure](#CreateAccount)
* [Создание приложения Java](#CreateApplication)
* [Настройка приложения для доступа к хранилищу таблиц](#ConfigureStorage)
* [Настройка строки подключения к хранилищу Azure](#ConnectionString)
* [Практическое руководство. Создание таблицы](#CreateTable)
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
* [Следующие шаги](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

##<a name="CreateAccount"></a>Создание учетной записи хранения Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"></a>Создание приложения Java

В этом руководстве будут использоваться компоненты хранилища, которые могут быть вызваны локально в приложении Java или в коде, работающем в веб-роли или роли работника в Azure. Предполагается, что комплект разработчика Java (JDK) уже загружен и установлен
и что вы, следуя инструкциям, приведенным в статье [Пакет Azure SDK для Java], установили библиотеки Azure
для Java и пакет Azure SDK и создали учетную запись хранения Azure в своей
подписке Azure.

Для создания приложения можно использовать любые средства разработки, включая Блокнот. Все, что нужно — это возможность компилировать проект Java и сослаться на библиотеки Azure для Java.

## <a name="ConfigureStorage"> </a>Настройка приложения для доступа к хранилищу таблиц

Если нужно использовать API-интерфейсы Azure для доступа к таблицам, добавьте следующие инструкции импорта в верхнюю часть файла Java:

    // Include the following imports to use table APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.table.client.*;
    import com.microsoft.windowsazure.services.table.client.TableQuery.*;

## <a name="ConnectionString"> </a>Настройка строки подключения к хранилищу Azure

Клиент хранилища Azure использует строку подключения с целью хранения конечных точек и учетных данных для доступа к службам управления данными. При работе
в клиентском приложении необходимо указать для хранилища строку подключения
в следующем формате, используя имя своей учетной записи хранения и первичный ключ
доступа для учетной записи хранения, указанные на портале управления
значениями *AccountName* и *AccountKey*. В этом примере показано,
как объявить статическое поле для размещения строки подключения:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Если приложение выполняется в роли Azure, эта строка может
храниться в файле конфигурации службы ServiceConfiguration.cscfg и
для доступа к ней можно использовать вызов
метода **RoleEnvironment.getConfigurationSettings**. Ниже приведен пример
получения строки подключения из элемента **Setting** с именем
*StorageConnectionString* в файле конфигурации службы:

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

В приведенных ниже примерах предполагается, что вы использовали одно из этих двух определений
для получения строки подключения к хранилищу.

## <a name="CreateTable"> </a>Практическое руководство. Создание таблицы

Объект **CloudTableClient** позволяет ссылаться на объекты таблиц
и сущностей. Следующий код создает объект **CloudTableClient** и использует
его для создания новой таблицы. Для всего кода в этом руководстве используется строка
подключения хранилища, сохраненная в конфигурации службы приложения
Azure. Существуют также другие способы создания
объекта **CloudStorageAccount**.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create the table if it doesn't exist.
    String tableName = "people";
    tableClient.createTableIfNotExists(tableName);

## <a name="AddEntity"> </a>Практическое руководство. Добавление сущности в таблицу

Сущности сопоставляются с объектами Java с помощью настраиваемого класса, реализующего
**TableEntity**. Для удобства класс **TableServiceEntity** 
реализует **TableEntity** и использует отражение для сопоставления свойств с
указанными для свойства методами get и set. Чтобы добавить сущность
в таблицу, сначала создайте класс, который определяет свойства
сущности. Следующий код определяет класс сущностей, который использует
имя клиента как ключ строки, а фамилию клиента — как ключ
раздела. Вместе ключ раздела и ключ строки сущности уникальным образом идентифицируют
сущность в таблице. Сущности с одним ключом раздела можно запрашивать быстрее, чем сущности с разными ключами раздела.

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

Табличные операций, включающие сущности, требуют объект **TableOperation**.
Этот объект определяет выполняемую для сущности операцию, которую можно запустить с помощью объекта **CloudTableClient**. В следующем коде
создается новый экземпляр класса **CustomerEntity** с сохраняемыми
данными клиента. Затем код вызывает
**TableOperation.insert**, чтобы создать объект **TableOperation** для вставки сущности в таблицу, а также связывает с ним новый **CustomerEntity**. Наконец, код вызывает метод **execute** для
**CloudTableClient**, указав таблицу "people" и новый
**TableOperation**, который затем отправляет запрос в службу хранилища для вставки новой сущности клиента в таблицу "people".

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insert(customer1);

    // Submit the operation to the table service.
    tableClient.execute("people", insertCustomer1);

## <a name="InsertBatch"> </a>Практическое руководство. Вставка пакета сущностей

Вы можете вставить пакет сущностей в службу таблиц за одну
операцию записи. Следующий код создает объект **TableBatchOperation**, а затем добавляет в него три операции вставки. Каждая операция вставки добавляется путем создания нового объекта сущности, установки его значений и последующего вызова метода **insert** для объекта **TableBatchOperation**, чтобы связать сущность с новой операцией вставки. Затем код вызывает **execute**
для **CloudTableClient**, указав таблицу "people", и
для объекта **TableBatchOperation**, который отправляет пакет
табличных операций в службу хранилища в одном запросе. Некоторые другие примечания
к пакетным операциям:

1.  В отдельном пакете можно выполнить до 100 операций вставки,
    удаления, объединения или замены в любом сочетании.
2.  Пакетная операция может иметь операцию извлечения, если она
    является единственной операцией в пакете.
3.  У всех сущностей в одной пакетной операции должен быть
    одинаковый ключ раздела.
4.  Объем полезных данных пакетной операции ограничен размером 4 МБ.

<!-- -->

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insert(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insert(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insert(customer3);

    // Execute the batch of operations on the "people" table.
    tableClient.execute("people", batchOperation);

## <a name="RetrieveEntities"> </a>Практическое руководство. Получение всех сущностей в разделе

Чтобы запросить из таблицы сущности раздела, можно использовать
**TableQuery**. Вызовите **TableQuery.from**, чтобы создать для определенной
таблицы запрос, который возвращает результаты заданного типа. Следующий код
задает фильтр для сущностей с ключом раздела "Smith".
**TableQuery.generateFilterCondition** — это вспомогательный метод для создания фильтров для запросов. Вызовите **where** по ссылке, которую вернул
метод **TableQuery.from**, чтобы применить фильтр к запросу. При выполнении запроса с помощью вызова **execute** для объекта **CloudTableClient**
он возвращает **Iterator** с указанным типом результата
**CustomerEntity**. Затем можно использовать возвращенное значение **Iterator** в каждом
цикле для получения результатов. Этот код выводит на консоль поля каждой сущности
в результатах запроса.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from("people", CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : tableClient.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

## <a name="RetrieveRange"> </a>Практическое руководство. Получение диапазона сущностей в разделе

Если вы не хотите запрашивать все сущности в разделе, можно
указать диапазон с помощью операторов сравнения в фильтре. В следующем коде совместно используются два фильтра для получения всех сущностей в разделе "Smith",
где ключ строки (имя) начинается с буквы до "E" в алфавите.
После чего результаты запроса выводятся на консоль. Если вы используете сущности,
добавленные в таблицу во время работы с разделом о пакетной вставке, то на этот раз возвращаются только две сущности (Ben Smith и Denise Smith),
а Jeff Smith не выводится.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        TableConstants.ROW_KEY, 
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter, 
            Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from("people", CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : tableClient.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

## <a name="RetriveSingle"> </a>Практическое руководство. Извлечение одной сущности

Можно написать запрос для получения отдельной сущности. В следующем коде выполняется вызов **TableOperation.retrieve** с
параметрами ключа раздела и ключа строки для указания клиента "Jeff Smith"
вместо создания **TableQuery** и применения фильтров с таким же результатом. При выполнении операции извлечения возвращает только одну сущность,
а не коллекцию. Метод **GetResultAsType** приводит результат к типу назначенной цели — объекта **CustomerEntity**. Если этот тип не совместим с типом, указанным в запросе, возникает
исключение. Значение NULL возвращается, если ни одна сущность не подходит по ключам раздела и строки. Указание ключа раздела и ключа строки в запросе — самый
быстрый способ для получения одной сущности из службы таблиц.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        tableClient.execute("people", retrieveSmithJeff).getResultAsType();

## <a name="ModifyEntity"> </a>Практическое руководство. Изменение сущности

Чтобы изменить сущность, извлеките ее из службы таблиц, измените
объект сущности и сохраните изменения в службе таблиц с помощью
операции замены или объединения. Следующий код изменяет существующий
номер телефона клиента. Вместо вызова метода **TableOperation.insert**,
который мы осуществляли при вставке, этот код вызывает **TableOperation.replace**. Метод
**CloudTableClient.execute** вызывает службу таблиц и заменяет
сущность, если только другое приложение не изменило ее с момента
извлечения данным приложением. Когда это происходит, возникает исключение,
и сущность необходимо получить, изменить и сохранить повторно.
Этот оптимистичный шаблон повторения в случае конфликтов
широко применяется в системе распределенного хранения.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        tableClient.execute("people", retrieveSmithJeff).getResultAsType();
        
    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    tableClient.execute("people", replaceEntity);

## <a name="QueryProperties"> </a>Практическое руководство. Запрос подмножества свойств сущности

Запрос к таблице может получить лишь несколько свойств сущности.
Этот метод, который называется "проекцией", снижает потребление
пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Запрос в следующем
коде использует метод **select**, чтобы возвратить только адреса электронной почты
сущностей в таблице. Результаты проецируются
в коллекцию **String** с помощью **EntityResolver**, который
выполняет преобразование типов сущностей, возвращенных с сервера. Дополнительные
сведения о проекции см. в этой [записи блога][]. Обратите внимание,
что проекция не поддерживается в эмуляторе локального хранилища, поэтому этот код выполняется только при использовании учетной записи хранения в службе таблиц.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery = 
        TableQuery.from("people", CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define a Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp,
                HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString : 
            tableClient.execute(projectionQuery, emailResolver)) {
        System.out.println(projectedString);
    }

## <a name="InsertOrReplace"> </a>Практическое руководство. Вставка или замена сущности

Часто требуется добавить сущность в таблицу, не зная, присутствует ли она там. Операция вставки или замены позволяет сделать
один запрос, который вставит сущность, если она не существует,
или заменит существующую сущность. В продолжение материала предыдущих примеров следующий код вставляет или заменяет сущность "Walter Harp". После
создания новой сущности этот код вызывает
метод **TableOperation.insertOrReplace**. Затем этот код вызывает
**execute** для **CloudTableClient**, используя таблицу и операцию вставки или замены в качестве параметров. Чтобы обновить только часть
сущности, можно использовать метод **TableOperation.insertOrMerge**.
Обратите внимание, что вставка или замена не поддерживается в эмуляторе локального хранилища,
поэтому этот код выполняется только при использовании учетной записи хранения в
службе таблиц. Дополнительные сведения о вставке или замене и вставке или объединении см. в этой [записи блога][].

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    tableClient.execute("people", insertCustomer5);

## <a name="DeleteEntity"> </a>Практическое руководство. Удаление сущности

Сущность можно легко удалить после ее получения. После
получения сущности вызовите **TableOperation.delete** с удаляемой
сущностью. Затем вызовите **execute** для **CloudTableClient**. Следующий
код извлекает и удаляет сущность customer.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
            tableClient.execute("people", retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    tableClient.execute("people", deleteSmithJeff);

## <a name="DeleteTable"> </a>Практическое руководство. Удаление таблицы

Наконец, следующий код удаляет таблицу из учетной записи хранения. Удаленную
таблицу нельзя воссоздать в течение определенного времени
после удаления, этот период обычно составляет менее сорока секунд.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Delete the table and all its data if it exists.
    tableClient.deleteTableIfExists("people");

## <a name="NextSteps"> </a>Дальнейшие действия

Вы изучили основные сведения о табличном хранилище. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. ссылку MSDN: [Хранение данных и доступ к ним в Windows
    Azure]
-   Посетите блог [команды разработчиков хранилища Azure][]


[использование REST API]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh264518.aspx
[запись блога]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
[Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
[Блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Пакет Azure SDK для Java]: http://www.windowsazure.com/ru-ru/develop/java/
[Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx

