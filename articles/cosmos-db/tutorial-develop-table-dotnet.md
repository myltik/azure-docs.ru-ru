---
title: "Разработка с помощью API таблицы базы данных Azure Cosmos DB на языке .NET | Документация Майкрософт"
description: "Сведения о разработке с помощью API таблицы базы данных Azure Cosmos DB на языке .NET."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4b22cb49-8ea2-483d-bc95-1172cd009498
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 061e79be546a80d254f2915313d747cf69cee9d2
ms.contentlocale: ru-ru
ms.lasthandoff: 06/03/2017

---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Разработка с помощью API таблицы базы данных Azure Cosmos DB на языке .NET

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования базы данных Azure Cosmos DB.

В рамках этого руководства рассматриваются следующие задачи: 

> [!div class="checklist"] 
> * создание учетной записи Azure Cosmos DB; 
> * включение возможностей в файле app.config; 
> * создание таблицы с помощью [API таблицы](table-introduction.md) (предварительная версия);
> * Добавление сущности в таблицу 
> * Вставка пакета сущностей 
> * Извлечение одной сущности 
> * запрос сущностей с использованием автоматического вторичного индексирования; 
> * Замена сущности 
> * Удаление сущности 
> * Удаление таблицы
 
## <a name="tables-in-azure-cosmos-db"></a>Таблицы в базе данных Azure Cosmos DB 

База данных Azure Cosmos DB предоставляет [API таблицы](table-introduction.md) (предварительная версия) для приложений, которым требуется хранилище пар "ключ — значение", не имеющее схемы. Для работы с этой базой данных можно использовать пакеты SDK для [Хранилища таблиц Azure](../storage/storage-introduction.md) и интерфейсы REST API. Azure Cosmos DB позволяет создавать таблицы с высокими требованиями к пропускной способности. Кроме того, она поддерживает оптимизированные для пропускной способности таблицы, которые неофициально называются таблицами класса "Премиум" и сейчас доступны в общедоступной предварительной версии. 

Хранилище таблиц Azure можно по-прежнему использовать для таблиц, требующих более низкой пропускной способности и большой емкости хранилища. В будущем обновлении мы планируем реализовать в базе данных Azure Cosmos DB поддержку оптимизированных для хранилища таблиц, а имеющиеся и новые учетные записи Хранилища таблиц Azure будут обновлены до учетных записей базы данных Azure Cosmos DB.

Если вы сейчас используете Хранилище таблиц Azure, таблицы класса "Премиум", доступные в предварительной версии, предоставят следующие преимущества:

- поддержка готового к использованию [глобального распределения](distribute-data-globally.md) со множественной адресацией, а также с [ручной и автоматической отработкой отказа](regional-failover.md);
- поддержка автоматического схемонезависимого индексирования данных для всех свойств ("вторичные индексы") и быстрого выполнения запросов; 
- поддержка [независимого масштабирования хранилища и пропускной способности](partition-data.md) в любом количестве регионов;
- поддержка [выделения пропускной способности на таблицу](request-units.md) с возможностью масштабирования числа запросов в секунду от нескольких сотен до миллионов;
- поддержка [пяти настраиваемых уровней согласованности](consistency-levels.md) с возможностью изменять показатели доступности, задержки и согласованности в соответствии с требованиями приложений;
- доступность уровня 99,99 % в отдельном регионе и возможность добавлять дополнительные регионы для более высокого уровня доступности, а также [ведущие в отрасли исчерпывающие Соглашения об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/cosmos-db/) в общедоступной версии;
- поддержка имеющегося пакета SDK .NET для службы хранилища Azure и устранение необходимости изменять код приложения.

В предварительной версии поддержка API таблицы в базе данных Azure Cosmos DB реализуется за счет пакета SDK для .NET. Вы можете скачать [пакет SDK предварительной версии для службы хранилища Azure](https://aka.ms/premiumtablenuget) на сайте NuGet. Он имеет те же классы и подписи метода, что и [пакет SDK для службы хранилища Azure](https://www.nuget.org/packages/WindowsAzure.Storage), но также может подключаться к учетным записям базы данных Azure Cosmos DB через API таблицы.

Дополнительные сведения о сложных задачах Хранилища таблиц Azure см. здесь:

* [Introduction to Azure Cosmos DB: Table API](table-introduction.md) (Общие сведения об API таблицы в базе данных Azure Cosmos DB)
* Справочная документация по службе таблиц с полными сведениями о доступных API-интерфейсах [клиентской библиотеки службы хранилища для .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

### <a name="about-this-tutorial"></a>О данном учебнике
Это руководство предназначено для разработчиков, которые уже работали с пакетом SDK для Хранилища таблиц Azure и хотели бы использовать функции уровня "Премиум", доступные в базе данных Azure Cosmos DB. Оно основано на сведениях из статьи [Приступая к работе с хранилищем таблиц Azure с помощью .NET](../storage/storage-dotnet-how-to-use-tables.md) и содержит информацию о том, как воспользоваться преимуществами дополнительных возможностей, таких как вторичное индексирование, подготовка пропускной способности и множественная адресация. Мы рассмотрим, как создать учетную запись базы данных Azure Cosmos DB, а затем создать и развернуть приложение "Таблица" на портале Azure. В этом руководстве также представлены примеры .NET для создания и удаления таблиц, а также для вставки, обновления, удаления и запроса данных таблицы. 

Если вы еще не установили Visual Studio 2017, вы можете скачать и использовать **бесплатный** [выпуск Visual Studio Community 2017](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

Сначала создадим учетную запись базы данных Azure Cosmos DB на портале Azure.  

> [!TIP]
> * Уже есть учетная запись Azure Cosmos DB? перейдите к [настройке решения Visual Studio](#SetupVS).
> * У вас была учетная запись Azure DocumentDB? Если да, ваша учетная запись теперь является учетной записью Azure Cosmos DB, поэтому вы можете сразу перейти к [настройке решения Visual Studio](#SetupVS).  
> * Если вы используете эмулятор Azure Cosmos DB, выполните действия, описанные в статье об [эмуляторе Azure Cosmos DB](local-emulator.md), чтобы настроить эмулятор и сразу перейти к [настройке решения Visual Studio](#SetupVS).
<!---Loc Comment: Please, check link [Set up your Visual Studio solution] since it's not redirecting to any location.---> 
>
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение "Таблица" из GitHub. Задайте строку подключения и выполните ее.

1. Откройте окно терминала Git, например Git Bash, и выполните команду `cd`, чтобы перейти в рабочий каталог.  

2. Выполните команду ниже, чтобы клонировать репозиторий с примером. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started
    ```

3. Затем откройте файл решения в Visual Studio.

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение.

1. На [портале Azure](http://portal.azure.com/) перейдите к учетной записи базы данных Azure Cosmos DB и на левой панели навигации щелкните **Ключи**, а затем выберите **Ключи записи-чтения**. На следующем шаге используйте кнопку копирования в правой части экрана, чтобы скопировать строку подключения в файл app.config.

2. В Visual Studio откройте файл app.config. 

3. Скопируйте значение универсального кода ресурса (URI) на портале (с помощью кнопки копирования) и добавьте его в качестве значения параметра account-key в файле app.config. В файле app.config в качестве значения параметра account-name задайте имя созданной ранее учетной записи.
  
```
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;TableEndpoint=https://account-name.documents.azure.com" />
```

> [!NOTE]
> Чтобы использовать это приложение с Хранилищем таблиц Azure класса "Стандартный", необходимо изменить строку подключения в файле `app.config file`. В качестве значения параметра AccountName используйте имя учетной записи таблицы, а в качестве параметра AccountKey — первичный ключ службы хранилища Azure. <br>
>`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;EndpointSuffix=core.windows.net" />`
> 
>

## <a name="build-and-deploy-the-app"></a>Создание и развертывание приложения
1. В Visual Studio в **обозревателе решений** щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. 

2. В поле **Обзор** введите ***WindowsAzure.Storage-PremiumTable***. Установите флажок **Включить предварительные выпуски**.

3. В результатах поиска найдите **WindowsAzure.Storage-PremiumTable** и установите ее. Затем выберите предварительную сборку `0.0.1-preview`. После этого начнется установка пакета Хранилища таблиц Azure и всех зависимостей.

4. Нажмите клавиши CTRL+F5 для запуска приложения. 

Вернитесь в обозреватель данных, где вы можете просматривать, запрашивать и изменять данные этой таблицы, а также работать с ними. 

> [!NOTE]
> Чтобы использовать это приложение с эмулятором базы данных Azure Cosmos DB, необходимо просто изменить строку подключения в файле `app.config file`. Для эмулятора используйте указанные ниже значения. <br>
>`<add key="StorageConnectionString" value=DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=<insertkey>==;TableEndpoint=https://localhost -->`
> 
>

## <a name="azure-cosmos-db-capabilities"></a>Возможности базы данных Azure Cosmos DB
База данных Azure Cosmos DB поддерживает ряд возможностей, которые недоступны в API Хранилища таблиц Azure. Эти новые возможности можно включить, используя приведенные ниже значения конфигурации `appSettings`. В предварительную версию пакета SDK для службы хранилища Azure мы не включали новые подписи или перегрузки. Таким образом, вы можете подключиться к таблицам класса "Стандартный" и "Премиум", а также работать с другими службами в службе хранилища Azure, такими как большие двоичные объекты и очереди. 


| Ключ | Описание |
| --- | --- |
| TableConnectionMode  | База данных Azure Cosmos DB поддерживает два режима подключения. В режиме `Gateway` запросы всегда направляются к шлюзу базы данных Azure Cosmos DB, а затем перенаправляются в соответствующие секции данных. В режиме подключения `Direct` клиент получает сведения о сопоставлении таблиц с секциями, а запросы выполняются непосредственно к секциям данных. Мы советуем использовать метод по умолчанию `Direct`.  |
| TableConnectionProtocol | База данных Azure Cosmos DB поддерживает два протокола подключения: `Https` и `Tcp`. По умолчанию используется протокол `Tcp`. Мы рекомендуем использовать его, так как он более легкий. |
| TablePreferredLocations | Разделенный запятыми список предпочтительных расположений (множественная адресация) для операций чтения. Каждую учетную запись базы данных Azure Cosmos DB можно связать з разным числом регионов (от одного до более тридцати). Каждый экземпляр клиента может указать подмножество этих регионов в порядке предпочтения, чтобы обеспечить низкую задержку операций чтения. Регионам необходимо присвоить их [отображаемые имена](https://msdn.microsoft.com/library/azure/gg441293.aspx), например `West US`. Дополнительные сведения см. в статье [How to setup Azure Cosmos DB global distribution using the Table API](tutorial-global-distribution-table.md) (Как настроить глобальное распределение Azure Cosmos DB с помощью API таблицы).
| TableConsistencyLevel | Вы можете изменить показатели доступности, задержки и согласованности, выбрав один из пяти следующих четко определенных уровней согласованности: `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix` и `Eventual`. Значение по умолчанию — `Session`. От выбранного уровня согласованности существенно зависит производительность в конфигурациях с несколькими регионами. Дополнительные сведения см. в статье [Настраиваемые уровни согласованности данных в DocumentDB](consistency-levels.md). |
| TableThroughput | Зарезервированная пропускная способность таблицы, выраженная в единицах запросов в секунду. Каждая таблица может поддерживать сотни миллионов единиц запросов в секунду. Дополнительные сведения см. в статье [Единицы запросов в DocumentDB](request-units.md). Значение по умолчанию — `400`. |
| TableIndexingPolicy | Согласованное и автоматическое вторичное индексирование всех столбцов в таблицах. | Строка JSON соответствует спецификации политики индексации. Сведения о добавлении или удалении определенных столбцов из политики индексации см. в [этой статье](indexing-policies.md). | Автоматическое индексирование всех свойств (хэш-индексация для строк и диапазонная индексация для чисел). |
| TableQueryMaxItemCount | Задайте максимальное количество элементов, возвращаемых в запросе таблицы в рамках одного цикла обхода. Значение по умолчанию — `-1`. Оно позволяет базе данных Azure Cosmos DB динамически определять значение во время выполнения. |
| TableQueryEnableScan | Если запрос не может использовать индекс для какого-либо фильтра, все равно выполните его через сканирование. Значение по умолчанию — `false`.|
| TableQueryMaxDegreeOfParallelism | Степень параллелизма для выполнения запроса между секциями. `0` — порядковое выполнение запросов без предварительной выборки. `1` — порядковое выполнение запросов с предварительной выборкой. Более высокие значения приводят к повышению степени параллелизма. Значение по умолчанию — `-1`. Оно позволяет базе данных Azure Cosmos DB динамически определять значение во время выполнения. |

Чтобы изменить значение по умолчанию, откройте файл `app.config` в обозревателе решений Visual Studio. Добавьте содержимое элемента `<appSettings>` , показанное ниже. Замените `account-name` именем своей учетной записи хранения, а `account-key` — ключом доступа своей учетной записи. 

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
      <!-- Client options -->
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />
      <add key="TableConnectionMode" value="Direct"/>
      <add key="TableConnectionProtocol" value="Tcp"/>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>
      <add key="TableConsistencyLevel" value="Eventual"/>

      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
      <add key="TableIndexingPolicy" value="{""indexingMode"": ""Consistent""}"/>

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

Сделаем краткий обзор того, что происходит в приложении. Откройте файл `Program.cs`, и вы увидите, что эти строки кода создают ресурсы таблиц. 

## <a name="create-the-table-client"></a>Создание клиента таблицы
Сначала необходимо инициализировать `CloudTableClient`, чтобы подключиться к учетной записи таблицы.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
Инициализация клиента осуществляется на основе значений конфигурации `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel` и `TablePreferredLocations`, если они указаны в параметрах приложения.
    
## <a name="create-a-table"></a>Создание таблицы
Затем создайте таблицу, используя `CloudTable`. Таблицы в базе данных Azure Cosmos DB можно масштабировать независимо друг от друга в контексте хранилища и пропускной способности. Служба выполняет секционирование автоматически. База данных Azure Cosmos DB поддерживает таблицы с фиксированным и неограниченным размером. Дополнительные сведения см. в статье [Секционирование и масштабирование в базе данных Azure Cosmos DB](partition-data.md). 

```csharp
CloudTable table = tableClient.GetTableReference("people");

table.CreateIfNotExists();
```

Создание таблиц существенно отличается. База данных Azure Cosmos DB резервирует пропускную способность, а в службе хранилища Azure к транзакциям применяется модель с оплатой по мере использования. Модель резервирования имеет два основных преимущества:

* Пропускная способность выделяется или резервируется, поэтому вам не нужно выполнять регулирование, если частота запросов на уровне или ниже подготовленной пропускной способности.
* Модель резервирования дает [экономическую выгоду при наличии рабочих нагрузок с высокими требованиями к пропускной способности](key-value-store-cost.md).

Вы можете указать пропускную способность по умолчанию, задав параметр `TableThroughput`. Пропускная способность выражается в единицах запросов в секунду. 

Для считывания сущности размером 1 КБ требуется 1 единица запроса. В других операциях это значение также фиксировано и зависит от потребления ресурсов ЦП, памяти и числа операций ввода-вывода в секунду. Дополнительные сведения о [единицах запроса в Azure Cosmos DB](request-units.md).

> [!NOTE]
> Сейчас пакет SDK для Хранилища таблиц не поддерживает изменение пропускной способности, но вы можете сделать это в любое время на портале Azure или с помощью Azure CLI.

Далее мы рассмотрим простые операции чтения и записи (CRUD) с использованием пакета SDK для Хранилища таблиц Azure. В этом руководстве показаны такие преимущества базы данных Azure Cosmos DB, как прогнозируемые задержки операций, длительностью менее 10 секунд, и быстрое выполнение запросов.

## <a name="add-an-entity-to-a-table"></a>Добавление сущности в таблицу
Сущности в Хранилище таблиц Azure расширены с помощью класса `TableEntity` и должны иметь свойства `PartitionKey` и `RowKey`. Ниже приведен пример определения сущности клиента.

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

В приведенном ниже фрагменте кода показано, как вставить сущность с помощью пакета SDK для службы хранилища Azure. База данных Azure Cosmos DB обеспечивает низкую задержку при любом масштабе и в любом регионе.

Эта база данных гарантирует выполнение операций записи с менее 15 мс задержки при уровне P99 и примерно 6 мс при уровне P50 для приложения, запущенного в том же расположении, что и учетная запись базы данных Azure Cosmos DB. Это время рассчитано с учетом того, что операции записи подтверждаются клиентом только после их синхронной репликации, надежной фиксации и индексирования всего содержимого.

API таблицы базы данных Azure Cosmos DB предоставляется в предварительной версии. В общедоступной версии гарантии низкой задержки на уровне P99 обеспечиваются Соглашениями об уровне обслуживания, как и для других API-интерфейсов базы данных Azure Cosmos DB. 

```csharp
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
Хранилище таблиц Azure поддерживает API пакетных операций, который позволяет выполнять обновление, удаление и вставку в одной пакетной операции. В отличие от Хранилища таблиц Azure база данных Azure Cosmos DB не имеет ограничений на использование API пакетной службы. Например, в одном пакете вы можете выполнить несколько операций чтения и несколько операций записи в ту же сущность без ограничения в 100 операций на пакет. 

```csharp
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
## <a name="retrieve-a-single-entity"></a>Извлечение одной сущности
Операции извлечения (GET) в базе данных Azure Cosmos DB выполняются с менее 10 мс задержки при уровне P99 и примерно 1 мс при уровне P50 в том же регионе Azure. Вы можете добавить в учетную запись нужное число регионов для низкой задержки операций чтения и развернуть приложения для чтения из локального региона (множественная адресация), задав параметр `TablePreferredLocations`. 

Следующий фрагмент кода позволяет извлечь одну сущность:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> Дополнительные сведения об API-интерфейсах множественной адресации см. в статье [How to setup Azure Cosmos DB global distribution using the Table API](tutorial-global-distribution-table.md) (Как настроить глобальное распределение Azure Cosmos DB с помощью API таблицы).
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>Запрос сущностей с использованием автоматического вторичного индексирования
Таблицы можно запрашивать с помощью класса `TableQuery`. База данных Azure Cosmos DB содержит ядро СУБД, оптимизированное для операций записи, которое автоматически индексирует все столбцы в таблице. Индексирование в базе данных Azure Cosmos DB не зависит от схемы. Поэтому, даже если схема отличается между строками или меняется с течением времени, она автоматически индексируется. Так как база данных Azure Cosmos DB поддерживает автоматическое вторичное индексирование, запросы к любому свойству могут использовать индекс и эффективно обрабатываются.

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

В предварительной версии база данных Azure Cosmos DB поддерживает те же функции обработки запросов, что и Хранилище таблиц Azure для API таблицы. Кроме того, эта база данных поддерживает сортировку, выполнение статистических вычислений, геопространственные запросы, иерархию и множество встроенных функций. Дополнительные возможности API таблицы будут добавлены в будущем обновлении. Общие сведения об этих возможностях см. в статье [SQL-запросы и синтаксис SQL в DocumentDB](documentdb-sql-query.md). 

## <a name="replace-an-entity"></a>Замена сущности
Чтобы обновить сущность, извлеките ее из службы таблиц, измените объект сущности и сохраните изменения в службе таблиц. Следующий код изменяет существующий номер телефона клиента. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
Аналогичным образом можно выполнить операцию `InsertOrMerge` или `Merge`.  

## <a name="delete-an-entity"></a>Удаление сущности
Сущность можно легко удалить после ее получения с использованием того же шаблона, что и для обновления сущностей. Следующий код извлекает и удаляет сущность клиента.

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>Удаление таблицы
Наконец, следующий пример кода удаляет таблицу из учетной записи хранения. Вы можете удалить и воссоздать таблицу с помощью базы данных Azure Cosmos DB.

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>Очистка ресурсов 

Если вы не собираетесь использовать это приложение дальше, удалите все ресурсы, созданные в ходе работы с этим руководством, на портале Azure, выполнив следующие действия.   

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданного ресурса.  
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**. 

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве мы рассмотрели, как начать работу с API таблицы в базе данных Azure Cosmos DB, и сделали следующее: 

> [!div class="checklist"] 
> * создали учетную запись Azure Cosmos DB; 
> * включили возможности в файле app.config; 
> * создали таблицу; 
> * добавили сущность в таблицу; 
> * вставили пакет сущностей; 
> * извлекли одну сущность; 
> * запросили сущности в процессе автоматического вторичного индексирования; 
> * заменили сущность; 
> * удалили сущность; 
> * удалили таблицу.  

Теперь вы можете перейти к следующему руководству и более детально рассмотреть запрос данных таблицы. 

> [!div class="nextstepaction"]
> [Запрос данных с помощью API таблицы](tutorial-query-table.md)

