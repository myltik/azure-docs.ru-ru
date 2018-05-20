---
title: Выполнение массовых операций в Azure Cosmos DB с помощью библиотеки BulkExecutor Java | Документация Майкрософт
description: Массовый импорт и обновление документов в коллекции Azure Cosmos DB с помощью библиотеки BulkExecutor Java в Azure Cosmos DB.
keywords: Массовый исполнитель Java
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 648b67f4b782b2a2318170ae2b655af40b7bd2a5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="use-bulkexecutor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Выполнение массовых операций с данными Azure Cosmos DB с помощью библиотеки BulkExecutor Java

В этом руководстве приведены инструкции по использованию библиотеки BulkExecutor Java в Azure Cosmos DB для импорта и обновления документов Azure Cosmos DB. В этом руководстве вы создадите приложение Java, которое формирует случайные документы, которые массово импортируются в коллекцию Azure Cosmos DB. После импорта вы массово обновите некоторые свойства документа. 

## <a name="prerequisites"></a>предварительным требованиям

* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.  

* [Бесплатную пробную версию Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) можно использовать без подписки Azure, без оплаты и каких-либо обязательств. Или вы можете применить [эмулятор Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) с URI `https://localhost:8081`. Первичный ключ предоставляется в разделе [Выполнение проверки подлинности запросов](local-emulator.md#authenticating-requests).  

* [Комплект разработчика Java (JDK 1.7+)](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)  
  - В Ubuntu выполните команду `apt-get install default-jdk`, чтобы установить JDK.  

  - Обязательно настройте переменную среды JAVA_HOME так, чтобы она указывала на папку, в которой установлен пакет JDK.

* [Скачайте](http://maven.apache.org/download.cgi) и [установите](http://maven.apache.org/install.html) двоичный архив [Maven](http://maven.apache.org/).  
  
  - В Ubuntu выполните команду `apt-get install maven`, чтобы установить Maven.

* Создайте учетную запись API SQL Azure Cosmos DB, используя шаги, описанные в разделе [Создание учетной записи базы данных](create-sql-api-java.md#create-a-database-account) статьи о начале работы с Java.

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь перейдем к работе с кодом, скачав пример приложения Java из GitHub. Это приложение выполняют массовые операции с данными Azure Cosmos DB. Чтобы клонировать приложения, откройте командную строку, перейдите в каталог, в который вы хотите его скопировать, и выполните следующую команду:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Клонированный репозиторий содержит два примера bulkimport и bulkupdate, указанных относительно папки \azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor. Приложение bulkimport создает случайные документы и импортирует их в Azure Cosmos DB. Приложение bulkupdate обновляет некоторые документы в Azure Cosmos DB. В следующих разделах мы рассмотрим код в каждом из этих примеров приложений. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Массовый импорт данных в Azure Cosmos DB

1. Строки подключения Azure Cosmos DB считываются в качестве аргументов и присваиваются переменным, определенным в файле CmdLineConfiguration.java.  

2. Далее инициализируется объект DocumentClient с помощью следующих операторов:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. Объект DocumentBulkExecutor инициализируется с высокими значениями повторения для запросов времени ожидания и регулируемых запросов. Далее для них устанавливается значение 0, чтобы передать управление перегрузкой DocumentBulkExecutor на время его существования.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the collection's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
```

4. Вызовите интерфейс API importAll, который создает случайные документы для массового импорта в коллекцию Azure Cosmos DB. Вы можете настроить конфигурации командной строки в файле CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
```
   API массового импорта принимает коллекцию сериализованных документов JSON и имеет указанный ниже синтаксис. Дополнительные сведения см. в [документации по API](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client/async).

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Метод importAll принимает следующие параметры:
 
   |**Параметр**  |**Описание**  |
   |---------|---------|
   |isUpsert    |   Параметр, активирующий операцию upsert в документах. Если документ с заданным идентификатором уже существует, он обновляется.  |
   |disableAutomaticIdGeneration     |   Параметр, отключающий автоматическое создание идентификатора. По умолчанию установлено значение true.   |
   |maxConcurrencyPerPartitionRange    |  Максимальная степень параллелизма для одного диапазона ключей разделов. Значение по умолчанию — 20.  |

   **Определение объекта ответа массового импорта**. Результат вызова API массового импорта содержит следующие методы get:

   |**Параметр**  |**Описание**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Общее число документов, которые были успешно импортированы, среди предоставленных для вызова API массового импорта.      |
   |double getTotalRequestUnitsConsumed()   |  Общее число единиц запросов (ЕЗ), потребляемых вызовом API массового импорта.       |
   |Duration getTotalTimeTaken()   |    Общее время, затрачиваемое на выполнение вызова API массового импорта.     |
   |List<Exception> getErrors() |  Получает список ошибок, если не удается вставить некоторые документы за пределами пакета, предоставленного вызову API массового импорта.       |
   |List<Object> getBadInputDocuments()  |    Список документов с неверным форматом, которые не были успешно импортированы при вызове API массового импорта. Пользователь должен исправить возвращенные документы и повторить импорт. Документы с неверным форматом включают в себя документы, значение идентификаторов которых не является строкой (NULL или любой другой тип данных считается недействительным).     |

5. После того, как приложение массового импорта будет готово, создайте программу командной строки из источника, выполнив команду mvn clean package. Эта команда создает JAR-файл в целевой папке:  

   ```java
   mvn clean package
   ```

6. После создания целевых зависимостей вы можете вызвать приложения массового импорта, выполнив следующую команду:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint URI>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Средство массового импорта создает базу данных и коллекцию с именем базы данных, именем коллекции и значениями пропускной способности, указанными в файле App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Массовое обновление данных в Azure Cosmos DB

С помощью API BulkUpdateAsync можно обновить имеющиеся документы. В этом примере вы присвоите для поля Name новое значение и удалите поле Description из существующих документов. Полный набор поддерживаемых операции обновления полей см. в [документации по API](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client/async). 

1. Определите элементы для обновления вместе с соответствующими операциями обновления полей. В этом примере используйте SetUpdateOperation для обновления поля Name и UnsetUpdateOperation, чтобы удалить поле Description из всех документов. Вы также можете выполнять другие операции, такие как увеличение значения в поле документа по определенному значению, передача определенных значений в поле массива или удаление определенного значения из него. Дополнительные сведения о различных методах, предоставляемых API массового обновления, см. в [документации по API](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client/async).  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                      
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Вызовите интерфейс API updateAll, который создает случайные документы для массового импорта в коллекцию Azure Cosmos DB. Вы можете настроить конфигурации командной строки, которые будут передаваться в файл CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   API массового обновления принимает коллекцию обновляемых элементов. Каждый обновленный элемент указывает список операций обновлений поля, которые будут выполнены в документе, определенном значением идентификатора и ключа раздела. Дополнительные сведения см. в [документации по API](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client/async).

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   Метод updateAll принимает следующие параметры:

   |**Параметр** |**Описание** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Максимальная степень параллелизма для одного диапазона ключей разделов. Значение по умолчанию — 20.  |
 
   **Определение объекта ответа массового импорта**. Результат вызова API массового импорта содержит следующие методы get:

   |**Параметр** |**Описание**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Общее число документов, которые были успешно обновлены из документов, предоставленных для вызова API массового обновления.      |
   |double getTotalRequestUnitsConsumed() |  Общее число единиц запросов (ЕЗ), потребляемых вызовом API массового обновления.       |
   |Duration getTotalTimeTaken()  |   Общее время, затрачиваемое на выполнение вызова API массового обновления.      |
   |List<Exception> getErrors()   |     Получает список ошибок, если не удается вставить некоторые документы за пределами пакета, предоставленного вызову API массового обновления.      |

3. После того, как приложение массового обновления будет готово, создайте программу командной строки из источника, выполнив команду mvn clean package. Эта команда создает JAR-файл в целевой папке:  

   ```
   mvn clean package
   ```

4. После создания целевых зависимостей вы можете вызвать приложения массового обновления, выполнив следующую команду:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint URI>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Советы по улучшению производительности 

Учтите следующие моменты для повышения производительности при использовании библиотеки BulkExecutor:

* Для достижения оптимальной производительности запустите приложение на виртуальной машине Azure в том же регионе, где находится регион записи вашей учетной записи Cosmos DB.  
* Чтобы получить более высокую пропускную способность, сделайте следующее:  

   * Установите для размера кучи виртуальной машины Java достаточно высокое значение, чтобы избежать проблем с памятью при обработке большого количества документов. Рекомендуемый размер кучи: максимум (3 ГБ, 3 * sizeof (все документы, переданные в API массового импорта в одном пакете)).  
   * Имеется время предварительной обработки, из-за которого вы получаете более высокую пропускную способность при выполнении массовых операций с большим количеством документов. Таким образом, если вы хотите импортировать 10 000 000 документов, лучше всего 10 раз выполнить массовый импорт 10 пакетов документов, размер которых соответствует 1 000 000 документов, чем 100 раз выполнить массовый импорт 100 пакетов документов, размер которых соответствует 100 000 документов.  

* Рекомендуется создать экземпляр одного объекта DocumentBulkExecutor для всего приложения в пределах одной виртуальной машины, соответствующей конкретной коллекции Azure Cosmos DB.  

* Это полезно, так как однократное выполнение API массовой операции потребляет большую часть ресурсов ЦП и операций ввода-вывода сети клиентского компьютера. Это происходит при порождении нескольких задач изнутри. Избегайте появления в процессе приложения нескольких параллельных задач, каждая из которых выполняет вызовы API массовой операции. Если один вызов API массовой операции, который выполняется на одной виртуальной машине, не может использовать всю пропускную способность вашей коллекции (если пропускная способность вашей коллекции > 1 000 000 ЕЗ/с), предпочтительнее создавать отдельные виртуальные машины для одновременного выполнения вызовов API.

    
## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о пакете Maven и заметки о выпуске библиотеки BulkExecutor Java, см. в статье[Java BulkExecutor library: Download information](sql-api-sdk-bulk-executor-java.md) (Сведения о скачивании библиотеки Java BulkExecutor).


