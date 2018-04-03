---
title: Создание фабрики данных Azure с помощью .NET | Документация Майкрософт
description: Создайте фабрику данных Azure для копирования данных из одного расположения в хранилище BLOB-объектов Azure в другое.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/28/2018
ms.author: jingwang
ms.openlocfilehash: c5b7af290a5e5c45d3f64ccb50586db0811dd592
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>Создание фабрики данных и конвейера с помощью пакета SDK .NET
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Версия 2 — предварительная](quickstart-create-data-factory-dot-net.md)

В этом кратком руководстве описано создание фабрики данных Azure с помощью пакета SDK .NET. Конвейер, который вы создадите в этой фабрике данных, **копирует** данные из одной папки в другую в хранилище BLOB-объектов Azure. Инструкции по **преобразованию** данных с помощью фабрики данных Azure см. в [руководстве по преобразованию данных с помощью Spark](transform-data-using-spark.md). 

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью о [начале работы со службой фабрики данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Эта статья не содержит подробный обзор службы фабрики данных. Общие сведения о службе фабрики данных Azure см. в статье [Введение в фабрику данных Azure](introduction.md).

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

### <a name="azure-subscription"></a>Подписка Azure.
Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

### <a name="azure-roles"></a>Роли Azure
Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль **участника**, **владельца** либо **администратора** подписки Azure. На портале Azure щелкните **имя пользователя** в правом верхнем углу и выберите **Разрешения**, чтобы просмотреть существующие разрешения в подписке. Если у вас есть доступ к нескольким подпискам, выберите соответствующую подписку. Примеры инструкций по назначению пользователю роли см. в статье [о добавлении ролей](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Учетная запись хранения Azure
В этом кратком руководстве в качестве **исходного** и **целевого** хранилища данных используется учетная запись хранения Azure общего назначения (в частности, хранилища BLOB-объектов). Если у вас нет учетной записи хранения Azure общего назначения, см. инструкции по [созданию учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account). 

#### <a name="get-storage-account-name-and-account-key"></a>Получение имени и ключа учетной записи хранения
В этом кратком руководстве вы будете использовать имя и ключ своей учетной записи хранения Azure. Далее описана процедура получения имени и ключа учетной записи хранения. 

1. Откройте браузер и перейдите на [портал Azure](https://portal.azure.com). Войдите, используя имя пользователя и пароль Azure. 
2. Щелкните **Больше служб >** в меню слева, отфильтруйте содержимое по ключевому слову **хранение**, затем выберите пункт **Учетные записи хранения**.

    ![Поиск учетной записи хранения](media/quickstart-create-data-factory-dot-net/search-storage-account.png)
3. В списке учетных записей хранения найдите с помощью фильтра свою учетную запись хранения (при необходимости), а затем выберите **эту учетную запись**. 
4. На странице **учетной записи хранения** выберите в меню параметр **Ключи доступа**.

    ![Получение имени и ключа учетной записи хранения](media/quickstart-create-data-factory-dot-net/storage-account-name-key.png)
5. Скопируйте значения полей **Имя учетной записи хранения** и **key1** в буфер обмена. Вставьте их в Блокнот или другой редактор и сохраните файл.  

#### <a name="create-input-folder-and-files"></a>Создание входной папки и файлов
В этом разделе вы создадите контейнер больших двоичных объектов с именем **adftutorial** в хранилище BLOB-объектов Azure. Затем вы создадите папку с именем **input** в контейнере и отправите в нее пример файла. 

1. На странице **Учетная запись хранения** перейдите на вкладку **Обзор** и щелкните **BLOB-объекты**. 

    ![Выбор параметра "BLOB-объекты"](media/quickstart-create-data-factory-dot-net/select-blobs.png)
2. На странице **Служба BLOB-объектов** щелкните **+ Контейнер** на панели инструментов. 

    ![Кнопка добавления контейнера](media/quickstart-create-data-factory-dot-net/add-container-button.png)    
3. В диалоговом окне **Создание контейнера** введите **adftutorial** в поле имени и нажмите кнопку **ОК**. 

    ![Ввод имени контейнера](media/quickstart-create-data-factory-dot-net/new-container-dialog.png)
4. Щелкните **adftutorial** в списке контейнеров. 

    ![Выбор контейнера](media/quickstart-create-data-factory-dot-net/select-adftutorial-container.png)
1. На странице **Контейнер** щелкните **Отправить** на панели инструментов.  

    ![Кнопка "Отправить"](media/quickstart-create-data-factory-dot-net/upload-toolbar-button.png)
6. На странице **Отправить BLOB-объект** щелкните **Дополнительно**.

    ![Переход по ссылке "Дополнительно"](media/quickstart-create-data-factory-dot-net/upload-blob-advanced.png)
7. Откройте **Блокнот** и создайте файл **emp.txt** с содержимым, приведенным ниже. Сохраните файл в папке **c:\ADFv2QuickStartPSH**. Если папка **ADFv2QuickStartPSH** отсутствует, создайте ее.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. На странице **Отправить BLOB-объект** на портале Azure найдите и выберите файл **emp.txt** в поле **Файлы**. 
9. Введите в поле **Отправить в папку** значение **input**. 

    ![Параметры отправки больших двоичных объектов](media/quickstart-create-data-factory-dot-net/upload-blob-settings.png)    
10. Убедитесь, что указана папка **input** и файл **emp.txt**, а затем щелкните **Отправить**.
11. В списке должен отобразиться файл **emp.txt** с состоянием отправки. 
12. Закройте страницу **Отправить BLOB-объект**, нажав значок **X** в углу. 

    ![Закрытие страницы отправки BLOB-объекта](media/quickstart-create-data-factory-dot-net/close-upload-blob.png)
1. Не закрывайте страницу **контейнера**. Она понадобится для проверки выходных данных в конце этого руководства.

### <a name="visual-studio"></a>Visual Studio
В этом руководстве используется Visual Studio 2017. Вы также можете использовать Visual Studio 2013 или 2015.

### <a name="azure-net-sdk"></a>Пакет Azure SDK для .NET
Скачайте и установите [пакет Azure SDK для .NET](http://azure.microsoft.com/downloads/) на компьютер.

### <a name="create-an-application-in-azure-active-directory"></a>Создание приложения в Azure Active Directory
Следуйте инструкциям из разделов [этой статьи](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application), чтобы выполнить следующие задачи: 

1. **Создайте приложение Azure Active Directory**. Создайте приложение в Azure Active Directory, которое представляет приложение .NET, создаваемое в этом руководстве. В качестве URL-адреса входа можно указать фиктивный URL-адрес, как показано в статье (`https://contoso.org/exampleapp`).
2. Получите **идентификатор приложения** и **ключ аутентификации** и запишите эти значения. Они потребуются в дальнейшем при выполнении инструкций этого руководства. 
3. Получите **идентификатор клиента** и запишите это значение. Оно потребуется в дальнейшем при выполнении инструкций этого руководства.
4. Назначьте приложению роль **участника** на уровне подписки, чтобы приложение могло создавать фабрики данных в подписке.

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

2. Добавьте следующий код в метод **Main**, который задает следующие переменные. Замените заполнители своими собственными значениями. Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
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

1. Добавьте следующий код в метод **Main**, чтобы постоянно проверять состояние до завершения копирования данных.

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

## <a name="verify-the-output"></a>Проверка выходных данных
Конвейер автоматически создает выходную папку в контейнере больших двоичных объектов adftutorial. Затем он копирует файл emp.txt из входной папки в выходную. 

1. На странице контейнера **adftutorial** на портале Azure нажмите кнопку **Обновить**, чтобы появилась папка output. 
    
    ![Обновить](media/quickstart-create-data-factory-dot-net/output-refresh.png)
2. Щелкните **output** в списке папок. 
2. Убедитесь, что файл **emp.txt** скопирован в папку output. 

    ![Обновить](media/quickstart-create-data-factory-dot-net/output-file.png)

## <a name="clean-up-resources"></a>Очистка ресурсов
Чтобы программно удалить фабрики данных, добавьте следующие строки кода в программу: 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Дополнительная информация
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Перейдите к [руководствам](tutorial-copy-data-dot-net.md), чтобы узнать об использовании фабрики данных в различных сценариях. 
