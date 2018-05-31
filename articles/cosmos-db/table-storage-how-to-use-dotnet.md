---
title: Приступая к работе с хранилищем таблиц Azure с помощью .NET | Документация Майкрософт
description: Хранение структурированных данных в облаке в хранилище таблиц Azure (хранилище данных NoSQL).
services: cosmos-db
documentationcenter: .net
author: SnehaGunda
manager: kfile
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: 9f8175742adc5c543b637ab69b3a9583f251da04
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360195"
---
# <a name="get-started-with-azure-table-storage-using-net"></a>Приступая к работе с хранилищем таблиц Azure с помощью .NET
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Хранилище таблиц Azure — это служба, которая хранит структурированные данные NoSQL в облаке, предоставляя хранилище ключей и атрибутов с бессхемной конструкцией. Такая конструкция хранилища таблиц позволяет легко адаптировать данные по мере расширения приложения. Разным типам приложений может быть предоставлен быстрый и экономичный доступ к хранилищу таблиц. Такое хранилище обычно дешевле, чем традиционные хранилища SQL для похожих объемов данных.

Хранилище таблиц можно использовать для хранения гибких наборов данных, например пользовательских данных для веб-приложений, адресных книг, сведений об устройстве или метаданных любого другого типа, которые требуются вашей службе. В таблице можно хранить любое количество сущностей, а учетная запись хранения может содержать любое количество таблиц в пределах емкости учетной записи.

### <a name="about-this-tutorial"></a>О данном учебнике
Из этого руководства вы узнаете, как использовать [библиотеку таблиц Microsoft Azure Cosmos DB для .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) в распространенных сценариях хранилища таблиц Azure. Имя пакета указывает на то, что он предназначен для использования с Azure Cosmos DB. Но пакет можно использовать и с Azure Cosmos DB, и с хранилищем таблиц Azure, просто для каждой службы предусмотрена уникальная конечная точка. Эти сценарии рассматриваются с использованием примеров C#, которые демонстрируют выполнение следующих задач:
* создание и удаление таблиц;
* вставка, обновление и удаление записей;
* Запросы к таблицам

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством требуются следующие компоненты.

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Общая библиотека службы хранилища Azure для .NET (предварительная версия)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). Это обязательный пакет в предварительной версии, который поддерживается в рабочих средах. 
* [Библиотека таблиц Microsoft Azure Cosmos DB для .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).
* [Диспетчер конфигураций Azure для .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Учетная запись хранения Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Другие примеры
Дополнительные примеры использования хранилища таблиц см. в статье [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/) (Приступая к работе с хранилищем таблиц Azure в .NET). Вы можете скачать пример приложения и запустить его или просмотреть код на GitHub.

## <a name="create-an-azure-service-account"></a>Создание учетной записи службы Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure
Самый простой способ создать первую учетную запись хранения Azure — воспользоваться [порталом Azure](https://portal.azure.com). Дополнительную информацию см. в статье [Об учетных записях хранения Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account).

Кроме того, создать учетную запись хранения Azure можно с помощью [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md) или [клиентской библиотеки поставщика ресурсов хранилища для .NET](/dotnet/api/microsoft.azure.management.storage).

Если вы не хотите сейчас создавать учетную запись хранения, код можно протестировать в локальной среде с помощью эмулятора хранения Azure. Дополнительные сведения см. в статье [Использование эмулятора хранения Azure для разработки и тестирования](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Создание учетной записи API таблиц Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="set-up-your-development-environment"></a>Настройка среды разработки
Теперь настройте среду разработки в Visual Studio для работы с примерами кода из этого руководства.

### <a name="create-a-windows-console-application-project"></a>Создание нового проекта консольного приложения Windows
В Visual Studio создайте новое консольное приложение Windows. Ниже показано, как создать консольное приложение в Visual Studio 2017 г. Эти же действия можно выполнить и в других версиях Visual Studio.

1. Выберите **Файл** > **Создать** > **Проект**.
2. Выберите **Установлено** > **Visual C#** > **Классический рабочий стол Windows**.
3. Выберите **Консольное приложение (.NET Framework)**.
4. В поле **Имя** введите имя приложения.
5. Нажмите кнопку **ОК**.

Все примеры кода из этого руководства можно добавить в метод `Main()` в файле `Program.cs` консольного приложения.

Вы можете использовать библиотеку таблиц Azure Cosmos DB в любом приложении .NET, в том числе в облачной службе Azure, веб-приложении Azure, классическом или мобильном приложении. Для упрощения в этом руководстве мы будем использовать консольное приложение.

### <a name="use-nuget-to-install-the-required-packages"></a>Установка необходимых пакетов с помощью NuGet
Для работы с этим руководством рекомендуем указать в проекте три пакета:

* [Общая библиотека службы хранилища Azure для .NET (предварительная версия)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common). 
* [Библиотека таблиц Microsoft Azure Cosmos DB для .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Этот пакет предоставляет программный доступ к ресурсам данных в вашей учетной записи хранилища таблиц или API таблиц Azure Cosmos DB.
* [Библиотека Microsoft Azure Configuration Manager для .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) — этот пакет предоставляет класс для анализа строки подключения в файле конфигурации независимо от среды выполнения приложения.

Вы можете использовать NuGet для установки обоих пакетов. Выполните следующие действия.

1. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**.
2. Выполните поиск в Интернете по запросу "Microsoft.Azure.Storage.Common" и выберите **Установить**, чтобы установить общую библиотеку службы хранилища Azure для .NET (предварительная версия) и ее зависимости. Убедитесь, что установлен флажок **Включить предварительные выпуски**, так как используется предварительная версия пакета.
3. Выполните поиск в Интернете по запросу "Microsoft.Azure.CosmosDB.Table" и выберите **Установить**, чтобы установить библиотеку таблиц Microsoft Azure Cosmos DB.
4. Выполните поиск в Интернете по запросу "WindowsAzure.ConfigurationManager" и нажмите кнопку **Установить**, чтобы установить библиотеку диспетчера конфигураций Microsoft Azure.

> [!NOTE]
> Зависимости ODataLib в общей библиотеке хранилища для .NET разрешаются с помощью пакетов ODataLib, доступных в NuGet, а не в WCF Data Services. Библиотеки ODataLib можно скачать напрямую или указать на них ссылку в проекте через NuGet. К специальным пакетам ODataLib, используемым клиентскими библиотеками хранения, относятся [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) и [Spatial](http://nuget.org/packages/System.Spatial/). Хотя эти библиотеки используются классами хранилища таблиц Azure, они являются обязательными зависимостями для программирования с использованием общей библиотеки хранилища.
> 
> 

> [!TIP]
> Разработчики, знакомые с хранилищем таблиц Azure, возможно, в прошлом применяли пакет [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/). Рекомендуем использовать для всех новых приложений таблиц [общую библиотеку службы хранилища Azure](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) и [библиотеку таблиц Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Но пакет WindowsAzure.Storage по-прежнему поддерживается. Если вы используете библиотеку WindowsAzure.Storage, включите Microsoft.WindowsAzure.Storage.Table в операторы using.
>
>

### <a name="determine-your-target-environment"></a>Определение целевой среды
Примеры из этого руководства можно выполнять в трех средах.

* Вы можете выполнить код в учетной записи хранения Azure в облаке. 
* Вы можете выполнить код в учетной записи Azure Cosmos DB в облаке.
* Вы можете выполнить код в эмуляторе хранения Azure. Эмулятор хранения — это локальная среда, эмулирующая учетную запись хранения Azure в облаке. Эмулятор можно использовать как бесплатный вариант для тестирования и отладки кода, пока приложение находится на стадии разработки. Эмулятор использует известную учетную запись и ключ. Дополнительные сведения см. в руководстве по [использованию эмулятора хранения Azure для разработки и тестирования](../storage/common/storage-use-emulator.md).

Выбрав учетную запись хранения в облаке, скопируйте первичный ключ доступа к этой учетной записи хранения с портала Azure. Дополнительные сведения см. в разделе [Просмотр и копирование ключей доступа к хранилищу](../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [!NOTE]
> Вы можете указать эмулятор хранения, чтобы избежать затрат, связанных с хранилищем Azure. Однако если вы выберете учетную запись хранения Azure в облаке, затраты на выполнение заданий в учебнике будут незначительны.
> 
> 

Выбрав учетную запись Azure Cosmos DB, скопируйте первичный ключ доступа к учетной записи API таблиц с портала Azure. Дополнительные сведения см. в разделе [Обновление строки подключения](create-table-dotnet.md#update-your-connection-string).

### <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища
Общая библиотека службы хранилища Azure для .NET поддерживает использование строки подключения для настройки конечных точек и учетных данных для доступа к службам хранилища. Строку подключения хранилища рекомендуется хранить в файле конфигурации. 

Дополнительные сведения о строках подключения см. в руководстве по [настройке строк подключения службы хранилища Azure](../storage/common/storage-configure-connection-string.md).

> [!NOTE]
> Ключ учетной записи похож на корневой пароль для вашей учетной записи хранения. Не забудьте защитить ключ учетной записи хранения. Не сообщайте его другим пользователям, не определяйте его в коде и не храните его в текстовом файле, доступном другим пользователям. Повторно создайте ключ с помощью портала Azure, если вы считаете, что он мог быть скомпрометирован.
> 
> 

Чтобы настроить строку подключения, откройте файл `app.config` в обозревателе решений Visual Studio. Добавьте содержимое элемента `<appSettings>` , показанное ниже. Замените `account-name` именем своей учетной записи, а `account-key` — ключом доступа своей учетной записи:

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Например, если вы используете учетную запись хранения Azure, параметры конфигурации будут приблизительно следующими:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Если вы используете учетную запись Azure Cosmos DB, параметры конфигурации будут приблизительно следующими:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

Чтобы указать эмулятор хранения, можно использовать ярлык, который сопоставляется с хорошо известным именем и ключом. В этом случае параметр строки подключения будет таким:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Добавление директив using
Добавьте в верхнюю часть файла `Program.cs` следующие директивы **using**:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Проанализируйте строку подключения
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Создание клиента службы таблиц
Класс [CloudTableClient][dotnet_CloudTableClient] позволяет получать таблицы и сущности, хранящиеся в хранилище таблиц. Вот один из способов создать клиент службы таблиц.

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

Теперь вы можете написать код, который считывает и записывает данные в хранилище таблиц.

## <a name="create-a-table"></a>Создание таблицы
В этом примере показано, как создать таблицу, если она не существует:

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Добавление сущности в таблицу
Сущности сопоставляются с объектами C# с помощью настраиваемого класса, производного от [TableEntity][dotnet_TableEntity]. Чтобы добавить сущность в таблицу, создайте класс, который определяет свойства сущности. Следующий код определяет класс сущностей, который использует имя клиента как ключ строки, а фамилию клиента — как ключ раздела. Вместе ключ раздела и ключ строки сущности уникальным образом идентифицируют сущность в таблице. Сущности с одинаковым ключом раздела можно запрашивать быстрее, чем с разными ключами раздела, но использование различных ключей разделов обеспечивает более высокую масштабируемость параллельных операций. Чтобы сущности могли храниться в таблицах, они должны быть поддерживаемого типа, например производными от класса [TableEntity][dotnet_TableEntity]. Свойства сущности, которые вы хотите хранить в таблице, должны быть открытыми свойствами типа и поддерживать получение и настройку значений. Кроме того, тип сущности *должен* предоставлять конструктор без параметров.

```csharp
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

Табличные операции, включающие сущности, выполняются с использованием объекта [CloudTable][dotnet_CloudTable], созданного ранее в разделе "Создание таблицы". Выполняемая операция представляется объектом [TableOperation][dotnet_TableOperation]. В следующем примере кода показано создание объекта [CloudTable][dotnet_CloudTable] и объекта **CustomerEntity**. Чтобы подготовить операцию, создается объект [TableOperation][dotnet_TableOperation] для вставки сущности customer в таблицу. Наконец, операция выполняется при вызове [CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute].

```csharp
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

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Вставка пакета сущностей
Вы можете вставить пакет сущностей в таблицу в одной операции записи. Некоторые другие примечания к пакетным операциям:

* Можно выполнять операции обновления, удаления и вставки в одной операции пакета.
* Одна пакетная операция может включать до 100 сущностей.
* У всех сущностей в одной пакетной операции должен быть одинаковый ключ раздела.
* Хотя запрос можно выполнить как пакетную операцию, он должен быть единственной операцией в пакете.

В следующем примере кода создается два объекта сущности, которые добавляются в [TableBatchOperation][dotnet_TableBatchOperation] с помощью метода [Insert][dotnet_TableBatchOperation_Insert]. Затем вызывается метод [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch] для выполнения операции.

```csharp
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
```

## <a name="retrieve-all-entities-in-a-partition"></a>Получение всех сущностей в разделе
Чтобы запросить таблицу для всех сущностей в разделе, используйте объект [TableQuery][dotnet_TableQuery]. Следующий пример кода задает фильтр для сущностей с ключом раздела Smith. Этот пример выводит на консоль поля каждой сущности в результатах запроса.

```csharp
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
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Получение диапазона сущностей в разделе
Если вы не хотите запрашивать все сущности в разделе, можно указать диапазон, объединив фильтр ключа раздела с фильтром ключа строк. В следующем примере кода используются два фильтра для получения всех сущностей в разделе Smith, где ключ строки (имя) начинается с буквы алфавита до "E", после чего результаты запроса выводятся в консоль.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
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
```

## <a name="retrieve-a-single-entity"></a>Извлечение одной сущности
Можно написать запрос для получения отдельной сущности. В следующем примере кода с помощью объекта [TableOperation][dotnet_TableOperation] задается клиент Ben Smith. Этот метод возвращает только одну сущность, а не коллекцию, а возвращаемое значение в [TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result] является объектом **CustomerEntity**. Указание ключа раздела и ключа строки в запросе — самый быстрый способ извлечь одну сущность из службы таблиц.

```csharp
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
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>Замена сущности
Чтобы обновить сущность, извлеките ее из службы таблиц, измените объект сущности и сохраните изменения в службе таблиц. Следующий код изменяет существующий номер телефона клиента. Вместо вызова метода [Insert][dotnet_TableOperation_Insert] этот код использует операцию [Replace][dotnet_TableOperation_Replace]. Операция [Replace][dotnet_TableOperation_Replace] полностью заменяет сущность на сервере, если только сущность на сервере не была изменена с момента извлечения. В таком случае операция не будет выполнена. Это необходимо, чтобы приложение случайно не перезаписало изменения, внесенные с момента извлечения до обновления другим компонентом приложения. Правильный алгоритм обработки этой ошибки заключается в том, чтобы снова получить сущность, внести необходимые изменения (если это еще возможно), а затем выполнить еще одну операцию [Replace][dotnet_TableOperation_Replace]. Ниже показано, как изменить это поведение.

```csharp
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
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Вставка или замена сущности
Операции [Replace][dotnet_TableOperation_Replace] завершаются ошибкой, если сущность изменена с момента получения с сервера. Более того, для успешного выполнения операции [Replace][dotnet_TableOperation_Replace] сначала нужно получить сущность с сервера. Тем не менее иногда вы не знаете, существует ли сущность на сервере и являются ли текущие значения, хранящиеся на нем, актуальными. Обновление должно заменить их все. Для выполнения этой задачи необходимо использовать операцию [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]. Эта операция вставляет сущность, если она не существует, или заменяет ее, если она существует, независимо от того, когда было выполнено последнее обновление.

В следующем примере кода сущность клиента для Fred Jones создается и вставляется в таблицу people. Далее мы используем операцию [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] для сохранения сущности с одинаковым ключом раздела (Jones) и ключом строки (Fred) на сервере. На этот раз мы используем ее с другим значением свойства PhoneNumber. Так как мы используем операцию [InsertOrReplace][dotnet_TableOperation_InsertOrReplace], все значения свойства заменяются. Но если сущности Fred Jones еще нет в таблице, ее нужно вставить.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>Запрос подмножества свойств сущности
Запрос к таблице может получить лишь несколько свойств сущности, а не все свойства. Этот метод, который называется "проекцией", снижает потребление пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Запрос в следующем коде возвращает только электронные адреса сущностей в таблице. Это делается с помощью запроса [DynamicTableEntity][dotnet_DynamicTableEntity], а также [EntityResolver][dotnet_EntityResolver]. Дополнительные сведения о проекции см. в записи блога [Windows Azure Tables: Introducing Upsert and Query Projection][blog_post_upsert] (Таблицы Microsoft Azure: введение в Upsert и проекции в запросах). Проекция не поддерживается в эмуляторе хранения, поэтому этот код выполняется только при использовании учетной записи хранения в службе таблиц.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Удаление сущности
Сущность можно легко удалить после ее получения с использованием того же шаблона, что и для обновления сущностей. Следующий код извлекает и удаляет сущность клиента.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
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
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Удаление таблицы
Наконец, следующий пример кода удаляет таблицу из учетной записи хранения. Удаленную таблицу нельзя воссоздать в течение определенного времени после удаления.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Асинхронное получение объектов со страниц
Если необходимо считать большое количество объектов, обрабатывая или отображая их по мере получения (до завершения всей операции), для их получения можно использовать сегментированный запрос. В этом примере объясняется, как расположить запрошенные результаты на странице с помощью алгоритма Async-Await для того, чтобы не блокировать выполнение задачи ожиданием большого объема возвращаемых данных. Дополнительные сведения об использовании алгоритма Async-Await в .NET см. в статье [Асинхронное программирование с использованием ключевых слов Async и Await (C# и Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>Дополнительная информация
Вы изучили основные сведения о табличном хранилище. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

* [Обозреватель хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.
* Дополнительные примеры использования хранилища таблиц см. в статье [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/) (Приступая к работе с хранилищем таблиц Azure в .NET).
* Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе таблиц:
* [Справочник по клиентской библиотеке хранилища для .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [Справочник по REST API](http://msdn.microsoft.com/library/azure/dd179355)
* Узнайте, как упростить код, предназначенный для работы со службой хранилища Azure, с помощью [пакета SDK для веб-заданий Azure](https://github.com/Azure/azure-webjobs-sdk/wiki)
* Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
* [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) .
* Информацию о хранении реляционных данных см. в статье [Подключение к базе данных SQL с помощью .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md).

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[blog_post_upsert]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
