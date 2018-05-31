---
title: Использование библиотеки BulkExecutor .NET для выполнения массовых операций в Azure Cosmos DB | Документация Майкрософт
description: Использование библиотеки BulkExecutor .NET в Azure Cosmos DB для массового импорта и обновления документов в коллекциях Azure Cosmos DB.
keywords: Массовый исполнитель .NET
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 608551090ce10e08ba517def644c72186a6f25e1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887248"
---
# <a name="using-bulkexecutor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Использование библиотеки BulkExecutor .NET для выполнения массовых операций в Azure Cosmos DB

В этом руководстве приведены инструкции по использованию библиотеки BulkExecutor .NET в Azure Cosmos DB для импорта и обновления документов в коллекциях Azure Cosmos DB. Подробнее о библиотеке BulkExecutor и о том, как с ее помощью эффективно использовать пропускную способность и хранилище, можно узнать в статье с [общими сведениями о библиотеке BulkExecutor](bulk-executor-overview.md). В этом руководстве вы познакомитесь с примером приложения .NET, которое массово импортирует случайно созданные документы в коллекцию Azure Cosmos DB. Затем в нем показано, как можно массово обновить импортированные данные, указав исправления как операции для выполнения в определенных полях документа.

## <a name="prerequisites"></a>предварительным требованиям

* Если вы еще не установили Visual Studio 2017, вы можете скачать и использовать [выпуск Community для Visual Studio 2017](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность разработки для Azure.

* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу. 

* [Бесплатную пробную версию Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) можно использовать без подписки Azure, без оплаты и каких-либо обязательств. Или вы можете применить [эмулятор Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) с URI `https://localhost:8081`. Первичный ключ предоставляется в разделе [Выполнение проверки подлинности запросов](local-emulator.md#authenticating-requests).

* Создайте учетную запись API SQL Azure Cosmos DB, используя шаги, описанные в разделе [Создание учетной записи базы данных](create-sql-api-dotnet.md#create-a-database-account) в статье о начале работы с .NET. 

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь перейдем к работе с кодом, загрузив некоторые примеры приложений .NET c GitHub. Эти приложения выполняют массовые операции с данными Azure Cosmos DB. Чтобы клонировать приложения, откройте командную строку, перейдите в каталог, в который вы хотите скопировать их, и выполните следующую команду:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Клонированный репозиторий содержит два примера: BulkImportSample и BulkUpdateSample. Вы можете открыть один из примеров приложений, обновить строки подключения в файле App.config, добавив строки подключения учетной записи Azure Cosmos DB, создать решение и запустить его. 

Приложение BulkImportSample создает случайные документы и массово импортирует их в базу данных Azure Cosmos DB. Приложение BulkUpdateSample массово обновляет импортированные документы, указывая исправления как операции для выполнения в определенных полях документа. В следующих разделах вы рассмотрите код в каждом из этих примеров приложений.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Массовый импорт данных в Azure Cosmos DB

1. Перейдите в папку BulkImportSample и откройте файл BulkImportSample.sln.  

2. Строки подключения Azure Cosmos DB извлечены из файла App.config, как показано в следующем коде:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Средство массового импорта создает базу данных и коллекцию с именем базы данных, именем коллекции и значениями пропускной способности, указанными в файле App.config. 

3. Затем объект DocumentClient инициализируется в режиме прямого подключения TCP:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Объект BulkExecutor инициализируется с высокими значениями повторения для времени ожидания и регулируемых запросов. Далее для них устанавливается значение 0, чтобы передать управление перегрузкой в объект BulkExecutor на время его существования.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. Приложение вызывает API BulkImportAsync. Библиотека .NET предоставляет две перегрузки API массового импорта: первая принимает список сериализованных документов JSON, а вторая — список десериализованных документов POCO. Дополнительные сведения об определениях каждого из эти перегруженных методов см. в [документации по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **Метод BulkImportAsync принимает следующие параметры:**
   
   |**Параметр**  |**Описание** |
   |---------|---------|
   |enableUpsert    |   Параметр, активирующий операцию upsert в документах. Если документ с заданным идентификатором уже существует, он обновляется. По умолчанию установлено значение false.      |
   |disableAutomaticIdGeneration    |    Параметр, отключающий автоматическое создание идентификатора. По умолчанию установлено значение true.     |
   |maxConcurrencyPerPartitionKeyRange    | Максимальная степень параллелизма для диапазона ключей разделов. Значение null приведет к использованию значения по умолчанию (20). |
   |maxInMemorySortingBatchSize     |  Максимальное количество документов, выведенных из перечислителя документов, которое передается вызову API на каждом этапе.  Использование значения null для фазы сортировки в рамках предварительной обработки в памяти до массового импорта приведет к тому, что библиотека будет использовать минимальное значение по умолчанию (min(documents.count, 1000000)).       |
   |cancellationToken    |    Токен отмены для корректного выхода из массового импорта.     |

   **Определение объекта ответа массового импорта**. Результат вызова API массового импорта содержит следующие атрибуты:

   |**Параметр**  |**Описание**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  Общее число документов, которые были успешно импортированы, среди предоставленных для вызова API массового импорта.       |
   |TotalRequestUnitsConsumed (double)   |   Общее число единиц запросов (ЕЗ), потребляемых вызовом API массового импорта.      |
   |TotalTimeTaken (TimeSpan)    |   Общее время, затрачиваемое на выполнение вызова API массового импорта.      |
   |BadInputDocuments (List<object>)   |     Список документов с неверным форматом, которые не были успешно импортированы при вызове API массового импорта. Пользователь должен исправить возвращенные документы и повторить импорт. Документы с неверным форматом включают в себя документы, значение идентификаторов которых не является строкой (NULL или любой другой тип данных считается недействительным).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Массовое обновление данных в Azure Cosmos DB

С помощью API BulkUpdateAsync можно обновить имеющиеся документы. В этом примере вы присвоите для поля Name новое значение и удалите поле Description из существующих документов. Полный набор поддерживаемых операций обновления полей см. в [документации по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Перейдите в папку BulkUpdateSample и откройте файл BulkUpdateSample.sln.  

2. Определите элементы для обновления вместе с соответствующими операциями обновления полей. В этом примере используйте SetUpdateOperation для обновления поля Name и UnsetUpdateOperation, чтобы удалить поле Description из всех документов. Вы также можете выполнять другие операции, такие как увеличение значения в поле документа по определенному значению, передача определенных значений в поле массива или удаление определенного значения из него. Дополнительные сведения о различных методах, предоставляемых API массового обновления, см. в [документации по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. Приложение вызывает API BulkUpdateAsync. Дополнительные сведения о методе BulkUpdateAsync см. в [документации по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **Метод BulkUpdateAsync принимает следующие параметры:**

   |**Параметр**  |**Описание** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Максимальная степень параллелизма для диапазона ключей разделов. Значение null приведет к использованию значения по умолчанию (20).   |
   |maxInMemorySortingBatchSize    |    Максимальное количество элементов обновления, извлеченных из перечислителя элементов обновления, передаваемых в вызов API на каждом этапе фазы сортировки в рамках предварительной обработки в памяти перед массовым обновлением. Установка значения null приведет к тому, что библиотека будет использовать минимальное значение по умолчанию (min(updateItems.count, 1000000)).     |
   | cancellationToken|Токен отмены для корректного выхода из массового обновления. |

   **Определение объекта ответа массового обновления**. Результат вызова API массового обновления содержит следующие атрибуты:

   |**Параметр**  |**Описание** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   Общее число документов, которые были успешно обновлены, среди предоставленных для вызова API массового обновления.      |
   |TotalRequestUnitsConsumed (double)   |    Общее число единиц запросов (ЕЗ), потребляемых вызовом API массового обновления.    |
   |TotalTimeTaken (TimeSpan)   | Общее время, затрачиваемое на выполнение вызова API массового обновления. |
    
## <a name="performance-tips"></a>Советы по улучшению производительности 

Учтите следующие моменты для повышения производительности при использовании библиотеки BulkExecutor:

* Для достижения оптимальной производительности запустите приложение на виртуальной машине Azure в том же регионе, где находится регион записи вашей учетной записи Cosmos DB.  

* Рекомендуется создать экземпляр одного объекта BulkExecutor для всего приложения в пределах одной виртуальной машины, соответствующей конкретной коллекции Cosmos DB.  

* Это полезно, так как однократное выполнение API массовой операции потребляет большую часть ресурсов ЦП и операций ввода-вывода сети клиентского компьютера. Это происходит при порождении нескольких задач изнутри. Избегайте появления в процессе приложения нескольких параллельных задач, каждая из которых выполняет вызовы API массовой операции. Если один вызов API массовой операции, который выполняется на одной виртуальной машине, не может потреблять всю пропускную способность вашей коллекции (если пропускная способность вашей коллекции > 1 000 000 ЕЗ/с), предпочтительнее создавать отдельные виртуальные машины для одновременного выполнения вызовов API.  

* Убедитесь, что InitializeAsync () вызывается после создания экземпляра объекта BulkExecutor для извлечения целевой схемы секционирования базы данных Cosmos DB.  

* Убедитесь, что в файле App.Config приложения включен параметр **gcServer** для обеспечения лучшей производительности.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Библиотека создает трассировки, которые могут записываться в файл журнала, либо в консоль. Чтобы включить оба способа, добавьте следующее в файл App.Config приложения.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
```

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о пакете Nuget и заметки о выпуске библиотеки BulkExecutor .NET см. в статье[.NET BulkExecutor library: Download information](sql-api-sdk-bulk-executor-dot-net.md) (Сведения о скачивании библиотеки .NET BulkExecutor). 
