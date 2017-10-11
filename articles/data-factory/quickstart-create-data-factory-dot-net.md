---
title: "Создание фабрики данных Azure с помощью .NET | Документация Майкрософт"
description: "Создайте фабрику данных Azure для копирования данных из одного расположения в другое в хранилище BLOB-объектов Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/06/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: d78176eca6bdbf32d6b4400ad2812dea98703d67
ms.contentlocale: ru-ru
ms.lasthandoff: 09/28/2017

---

# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>Создание фабрики данных и конвейера с помощью пакета SDK .NET
Фабрика данных Azure — это облачная служба интеграции данных, которая позволяет создавать управляемые данными рабочие процессы в облаке для оркестрации и автоматизации перемещения и преобразования данных. С помощью фабрики данных Azure можно создавать и включать в расписание управляемые данными рабочие процессы (конвейеры), которые могут принимать данные из разнородных хранилищ данных, обрабатывать и преобразовывать эти данные с помощью служб вычислений (например, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics и машинного обучения Azure), а также публиковать выходные данные в хранилища данных (например, хранилище данных SQL Azure) для использования приложениями бизнес-аналитики. 

В этом кратком руководстве описано создание фабрики данных Azure с помощью пакета SDK .NET. В этой фабрике данных конвейер копирует данные из одной папки в другую в хранилище BLOB-объектов Azure.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования
* **Учетная запись хранения Azure.** Хранилище BLOB-объектов используется как хранилища данных, являющиеся **источником** и **приемником**. Если у вас нет учетной записи хранения Azure, ознакомьтесь с разделом о [создании учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account). 
* Создайте **контейнер больших двоичных объектов** в хранилище BLOB-объектов, в контейнере создайте входную **папку** и отправьте несколько файлов в нее. Такие средства, как [обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/), можно использовать для подключения к хранилищу BLOB-объектов Azure, создания контейнера BLOB-объектов, отправки входного файла и проверки выходного.
* **Visual Studio** 2013, 2015 или 2017. В этом руководстве используется Visual Studio 2017.
* **Скачайте и установите [пакет Azure SDK для .NET](http://azure.microsoft.com/downloads/)**.
* [Используйте следующие инструкции](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application), **чтобы создать приложение в Azure Active Directory**. Запишите следующие значения, которые вы используете в следующих шагах: **идентификатор приложения**, **ключ проверки подлинности** и **идентификатор клиента**. Назначьте приложению роль **Участник**, следуя указаниям в той же статье. 
*  

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

С помощью Visual Studio 2013, 2015 или 2017 создайте консольное приложение C# .NET.

1. Запустите **Visual Studio**.
2. Щелкните **Файл**, наведите указатель мыши на пункт **Создать** и щелкните **Проект**.
3. Выберите **Visual C#** -> **Консольное приложение (.NET Framework)** из списка типов проектов справа. Требуется .NET версии 4.5.2 или более поздней.
4. Введите имя **ADFv2QuickStart**.
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

2. Добавьте следующий код в метод **Main**, который задает следующие переменные. Замените заполнители своими собственными значениями.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
    // Currently, Data Factory V2 allows you to create data factories only in the East US and East US2 regions. 
    // Note that the data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions
    string region = "East US 2";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    // specify the container and input folder from which all files need to be copied to the output folder. 
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/foldername>";
    //specify the contains and output folder where the files are copied
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/foldername>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
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
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
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

## <a name="create-a-linked-service"></a>Создание связанной службы

Добавьте следующий код, создающий **связанную службу хранилища Azure**, в метод **Main**.

Связанная служба в фабрике данных связывает хранилища данных и службы вычислений с фабрикой данных. В этом руководстве необходимо создать одну связанную службу хранилища Azure для копирования хранилища-источника и приемника, который в примере называется AzureStorageLinkedService.

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

## <a name="create-a-dataset"></a>Создание набора данных

Добавьте следующий код, который создает **набор данных большого двоичного объекта Azure**, в метод **Main**.

Вы можете определить набор данных, который будет представлять данные для копирования из источника в приемник. В этом примере набор данных большого двоичного объекта относится к связанным службам хранилища Azure, созданным на предыдущем шаге. Набор данных принимает параметр, значение которого задается в действии, использующем набор данных. Параметр используется для создания folderPath, указывающего, где находятся или хранятся данные.

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
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Создание конвейера

Добавьте в метод **Main** следующий код, создающий **конвейер с действием копирования**.

В этом примере этот конвейер содержит одно действие и принимает два параметра: путь входного и выходного большого двоичного объекта. Значения для этих параметров устанавливаются при активации или выполнении конвейера. Действие копирования ссылается на тот же набор данных большого двоичного объекта, который был создан на предыдущем шаге, в качестве входного и выходного. Если набор данных используется в качестве входного, указывается путь к входным данным. И если набор данных используется в качестве выходного, указывается путь к выходным данным. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Создание конвейера

Добавьте в метод **Main** следующий код, **активирующий выполнение конвейера**.

Этот код также указывает параметрам **inputPath** и **outputPath**, задаваемым в конвейере, фактические значения путей большого двоичного объекта источника и приемника.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters).Result.Body;
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
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Выполнение кода

Создайте и запустите приложение, а затем проверьте выполнение конвейера.

Консоль выведет ход выполнения создания фабрики данных, связанной службы, наборов данных, конвейера и выполнения конвейера. Затем она проверяет состояние выполнения конвейера. Дождитесь появления сведений о действии копирования с размером записанных и прочитанных данных. Затем воспользуйтесь такими средствами, как [обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/), чтобы проверить, скопирован ли большой двоичный объект в outputBlobPath из inputBlobPath, как указано в переменных.

### <a name="sample-output"></a>Пример выходных данных: 
```json
Creating data factory SPv2Factory0907...
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
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 2,
    "billedDuration": 23
}

Press any key to exit...

```

## <a name="clean-up-resources"></a>Очистка ресурсов
Чтобы программно удалить фабрики данных, добавьте следующие строки кода в программу: 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Дальнейшие действия
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Перейдите к [руководствам](tutorial-copy-data-dot-net.md), чтобы узнать об использовании фабрики данных в различных сценариях. 

