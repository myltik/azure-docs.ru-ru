---
title: Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL | Документация Майкрософт
description: В этом руководстве приведены пошаговые инструкции по копированию данных из хранилища BLOB-объектов Azure в базу данных SQL Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: bfbafa2edb1d9195760a99f63113d28d3a978a78
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30173140"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL Azure с помощью фабрики данных Azure
В этом руководстве вы создадите фабрику данных с конвейером, который перемещает данные из хранилища BLOB-объектов Azure в базу данных SQL Azure. Шаблон конфигурации в этом руководстве применяется к копированию из файлового в реляционное хранилище данных. Список хранилищ данных, которые поддерживаются в качестве источников и приемников, см. в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


В этом руководстве вы выполните следующие шаги:

> [!div class="checklist"]
> * создадите фабрику данных;
> * Создание связанных служб хранилища Azure и базы данных SQL Azure.
> * Создание наборов данных большого двоичного объекта Azure и базы данных SQL Azure.
> * Создание конвейера с действием копирования.
> * Запуск конвейера.
> * Мониторинг конвейера и выполнения действий.

В этом руководстве используется пакет SDK для .NET. Вы можете использовать другие механизмы для взаимодействия с фабрикой данных Azure (см. примеры в разделе "Быстрое начало работы").

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

* **Учетная запись хранения Azure.** В этом руководстве в качестве **источника** будет использоваться хранилище BLOB-объектов. Если у вас нет учетной записи хранения Azure, ознакомьтесь с разделом [Создание учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* **База данных SQL Azure**. Используйте базу данных как хранилище данных-**приемник**. Если у вас нет базы данных SQL Azure, вы можете создать ее, выполнив шаги из статьи [Создание базы данных SQL Azure на портале Azure](../sql-database/sql-database-get-started-portal.md).
* **Visual Studio** 2015 или 2017. В этом руководстве используется Visual Studio 2017.
* **Скачайте и установите [пакет Azure SDK для .NET](http://azure.microsoft.com/downloads/)**.
* [Используйте следующие инструкции](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application), **чтобы создать приложение в Azure Active Directory**. Запишите следующие значения, которые вы используете в следующих шагах: **идентификатор приложения**, **ключ аутентификации** и **идентификатор клиента**. Назначьте приложению роль **Участник**, следуя указаниям в той же статье.

### <a name="create-a-blob-and-a-sql-table"></a>Создание большого двоичного объекта и таблицы SQL

Теперь подготовьте большой двоичный объект Azure и базу данных SQL Azure к изучению этого руководства, выполнив следующие действия.

#### <a name="create-a-source-blob"></a>Создание исходного большого двоичного объекта

1. Запустите Блокнот. Скопируйте следующий текст и сохраните его в файл **inputEmp.txt** на диске.

    ```
    John|Doe
    Jane|Doe
    ```

2. При помощи таких средств, как [обозреватель службы хранилища Azure](http://storageexplorer.com/), создайте контейнер **adfv2tutorial** и отправьте в него файл **inputEmp.txt**.

#### <a name="create-a-sink-sql-table"></a>Создание таблицы-приемника SQL

1. Используйте следующий скрипт SQL, чтобы создать таблицу **dbo.emp** в базе данных SQL Azure.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Предоставьте службам Azure доступ к серверу SQL Server. Убедитесь, что параметр **Разрешить доступ к службам Azure** имеет состояние **ВКЛ** для вашего сервера Azure SQL Server, чтобы служба фабрики данных могла записывать данные на него. Чтобы проверить и при необходимости включить этот параметр, сделайте следующее.

    1. Щелкните **Больше служб** слева и выберите **Серверы SQL**.
    2. Выберите сервер и щелкните **Брандмауэр** в разделе **Параметры**.
    3. На странице **Параметры брандмауэра** щелкните **ВКЛ** для параметра **Разрешить доступ к службам Azure**.


## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

С помощью Visual Studio 2015 или 2017 создайте консольное приложение C# .NET.

1. Запустите **Visual Studio**.
2. Щелкните **Файл**, наведите указатель мыши на пункт **Создать** и щелкните **Проект**.
3. Выберите **Visual C#** -> **Консольное приложение (.NET Framework)** из списка типов проектов справа. Требуется .NET версии 4.5.2 или более поздней.
4. Введите **ADFv2Tutorial** в качестве имени.
5. Нажмите кнопку **ОК** , чтобы создать проект.

## <a name="install-nuget-packages"></a>Установка пакетов Nuget

1. Выберите **Инструменты** -> **Диспетчер пакетов NuGet** -> **Консоль диспетчера пакетов**.
2. В **консоли диспетчера пакетов** выполните следующие команды, чтобы установить пакеты:

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Создание клиента фабрики данных

1. Откройте файл **Program.cs** и включите следующие инструкции, чтобы добавить ссылки на пространства имен.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
    
2. Добавьте приведенный ниже код в метод **Main**, в котором задаются переменные. Замените заполнители своими значениями. Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Добавьте в метод **Main** приведенный ниже код, создающий экземпляр класса **DataFactoryManagementClient**. Этот объект используется не только для создания фабрики данных, связанной службы, наборов данных и конвейера, но и для отслеживания подробностей выполнения конвейера.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Создать фабрику данных

Добавьте следующий код, создающий **фабрику данных**, в метод **Main**.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()

};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Создание связанных служб

Работая с этим руководством, вы создали две связанные службы для источника и приемника соответственно:

### <a name="create-an-azure-storage-linked-service"></a>Создание связанной службы хранилища Azure

Добавьте следующий код, создающий **связанную службу хранилища Azure**, в метод **Main**. Дополнительные сведения о свойствах связанных служб BLOB-объектов Azure см. в [этом разделе](connector-azure-blob-storage.md#linked-service-properties).

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

### <a name="create-an-azure-sql-database-linked-service"></a>Создание связанной службы базы данных SQL Azure

Добавьте следующий код, создающий **связанную службу базы данных SQL Azure**, в метод **Main**. Дополнительные сведения о свойствах связанных служб базы данных SQL Azure см. в [этом разделе](connector-azure-sql-database.md#linked-service-properties).

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>Создание наборов данных

В этом разделе создайте два набора данных: для источника и приемника. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Создание набора данных для исходного большого двоичного объекта Azure

Добавьте следующий код, который создает **набор данных большого двоичного объекта Azure**, в метод **Main**. Дополнительные сведения о свойствах набора данных больших двоичных объектов Azure см. в [этом разделе](connector-azure-blob-storage.md#dataset-properties).

Задайте набор данных, представляющий исходные данные в большом двоичном объекте Azure. Этот набор данных большого двоичного объекта относится к связанной службе хранилища Azure, созданной на предыдущем шаге, и описывает следующее:

- Расположение большого двоичного объекта для копирования: **FolderPath** и **FileName**.
- Формат большого двоичного объекта, указывающий, как анализировать содержимое: **TextFormat** и его настройки (например, разделитель столбцов).
- Структура данных, включая имена столбцов и типы данных, которые в этом случае сопоставляются с таблицей-приемником SQL.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Создание набора данных для базы данных-приемника SQL Azure

Добавьте следующий код, создающий **набор данных базы данных SQL Azure**, в метод **Main**. Дополнительные сведения о свойствах набора данных базы данных SQL Azure см. в [этом разделе](connector-azure-sql-database.md#dataset-properties).

Задайте набор данных, который представляет данные приемника в базе данных SQL Azure. Этот набор данных относится к связанным службам базы данных SQL Azure, созданным на предыдущем шаге. Он также указывает таблицу SQL, которая содержит копируемые данных. 

```csharp
// Create a Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Создание конвейера

Добавьте в метод **Main** следующий код, создающий **конвейер с действием копирования**. В этом руководстве конвейер содержит одно действие: операцию копирования, которая принимает набор данных большого двоичного объекта в качестве источника и другой набор данных SQL в качестве приемника. Дополнительные сведения о действии копирования см. в статье [Действие копирования в фабрике данных Azure](copy-activity-overview.md).

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Создание конвейера

Добавьте в метод **Main** следующий код, **активирующий выполнение конвейера**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Мониторинг выполнения конвейера

1. Добавьте следующий код в метод **Main**, чтобы постоянно проверять состояние выполнения конвейера до завершения копирования данных.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Добавьте в метод **Main** следующий код, извлекающий сведения о выполнении действия копирования, например размер записанных и прочитанных данных.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);
    
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Выполнение кода

Создайте и запустите приложение, а затем проверьте выполнение конвейера.

Консоль выведет ход выполнения создания фабрики данных, связанной службы, наборов данных, конвейера и выполнения конвейера. Затем она проверяет состояние выполнения конвейера. Дождитесь появления сведений о действии копирования с размером записанных и прочитанных данных. Затем используйте такие средства, как SSMS (SQL Server Management Studio) или Visual Studio, для подключения к базе данных SQL Azure и проверьте, скопированы ли данные в указанную таблицу.

### <a name="sample-output"></a>Пример выходных данных

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedCloudDataMovementUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>Дополнительная информация
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Вы научились выполнять следующие задачи: 

> [!div class="checklist"]
> * создадите фабрику данных;
> * Создание связанных служб хранилища Azure и базы данных SQL Azure.
> * Создание наборов данных большого двоичного объекта Azure и базы данных SQL Azure.
> * Создание конвейера с действием копирования.
> * Запуск конвейера.
> * Мониторинг конвейера и выполнения действий.


Перейдите к следующему руководству, чтобы узнать о копировании данных из локальной среды в облако: 

> [!div class="nextstepaction"]
>[Копирование данных между локальной средой и облаком](tutorial-hybrid-copy-powershell.md)
