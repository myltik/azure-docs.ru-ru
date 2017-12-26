---
title: "Ветвление в конвейере фабрики данных Azure | Документация Майкрософт"
description: "Узнайте, как контролировать поток данных в фабрике данных Azure с помощью ветвления и создания цепочки действий."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/06/2017
ms.author: shlo
ms.openlocfilehash: bcf3095e8e66ea9b3c49919dadb8f7c342a49006
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2017
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Ветвления и создание цепочки действий в конвейере фабрики данных
В этом руководстве создается конвейер фабрики данных, который демонстрирует некоторые функции потока управления. Этот конвейер просто копирует данные из контейнера в хранилище BLOB-объектов Azure в другой контейнер в той же учетной записи хранения. Если действие копирования завершается успешно, нужно отправить подробную информацию об успешной операции копирования (например, количество записанных данных) по электронной почте. Если произошел сбой действия копирования, необходимо отправить данные об ошибке копирования (например, сообщение об ошибке) по электронной почте. В этом руководстве вы научитесь передавать параметры.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Общий обзор сценария: ![Обзор](media/tutorial-control-flow/overview.png)

В этом руководстве вы выполните следующие шаги:

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создание связанной службы хранилища Azure.
> * Создание набора данных больших двоичных объектов Azure
> * Создание конвейера, содержащего действия копирования и веб-действие.
> * Отправка выходных данных действий для последующих действий.
> * Использование передачи параметров и системных переменных.
> * Запуск конвейера.
> * Мониторинг конвейера и выполнения действий.

В этом руководстве используется пакет SDK для .NET. Вы можете использовать другие механизмы для взаимодействия с фабрикой данных Azure (см. раздел "Быстрое начало работы" в оглавлении).

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* **Учетная запись хранения Azure.** В этом руководстве в качестве **источника** будет использоваться хранилище BLOB-объектов. Если у вас нет учетной записи хранения Azure, ознакомьтесь с разделом [Создание учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* **База данных SQL Azure**. Используйте базу данных как хранилище данных-**приемник**. Если у вас нет базы данных SQL Azure, вы можете создать ее, выполнив шаги из статьи [Создание базы данных SQL Azure на портале Azure](../sql-database/sql-database-get-started-portal.md).
* **Visual Studio** 2013, 2015 или 2017. В этом руководстве используется Visual Studio 2017.
* **Скачайте и установите [пакет Azure SDK для .NET](http://azure.microsoft.com/downloads/)**.
* [Используйте следующие инструкции](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application), **чтобы создать приложение в Azure Active Directory**. Запишите следующие значения, которые вы используете в следующих шагах: **идентификатор приложения**, **ключ аутентификации** и **идентификатор клиента**. Назначьте приложению роль **Участник**, следуя указаниям в той же статье.

### <a name="create-blob-table"></a>Создание таблицы больших двоичных объектов

1. Запустите Блокнот. Скопируйте следующий текст и сохраните его в файл **input.txt** на диске.

    ```
    John|Doe
    Jane|Doe
    ```
2. При помощи таких средств, как [обозреватель службы хранилища Azure](http://storageexplorer.com/), создайте контейнер **adfv2branch** и отправьте в него файл **input.txt**.

## <a name="create-visual-studio-project"></a>Создание проекта Visual Studio

С помощью Visual Studio 2015 или 2017 создайте консольное приложение C# .NET.

1. Запустите **Visual Studio**.
2. Щелкните **Файл**, наведите указатель мыши на пункт **Создать** и щелкните **Проект**. Требуется .NET версии 4.5.2 или более поздней.
3. Выберите **Visual C#** -> **Консольное приложение (.NET Framework)** из списка типов проектов справа.
4. Введите **ADFv2BranchTutorial** в качестве имени.
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

2. Add these static variables to the **Program class**. Replace place-holders with your own values. Currently, Data Factory V2 allows you to create data factories only in the East US, East US2, and West Europe regions. The data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions.

    ```csharp
        // Set variables
        static string tenantID = "<tenant ID>";
        static string applicationId = "<application ID>";
        static string authenticationKey = "<Authentication key for your application>";
        static string subscriptionId = "<Azure subscription ID>";
        static string resourceGroup = "<Azure resource group name>";

        static string region = "East US";
        static string dataFactoryName = "<Data factory name>";

        // Specify the source Azure Blob information
        static string storageAccount = "<Azure Storage account name>";
        static string storageKey = "<Azure Storage account key>";
        // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
        static string inputBlobPath = "adfv2branch/input";
        static string inputBlobName = "input.txt";
        static string outputBlobPath = "adfv2branch/output";
        static string emailReceiver = "<specify email address of the receiver>";

        static string storageLinkedServiceName = "AzureStorageLinkedService";
        static string blobSourceDatasetName = "SourceStorageDataset";
        static string blobSinkDatasetName = "SinkStorageDataset";
        static string pipelineName = "Adfv2TutorialBranchCopy";

        static string copyBlobActivity = "CopyBlobtoBlob";
        static string sendFailEmailActivity = "SendFailEmailActivity";
        static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
    
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
Создайте функцию CreateOrUpdateDataFactory в файле Program.cs:

```csharp
static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating data factory " + dataFactoryName + "...");
    Factory resource = new Factory
    {
        Location = region
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

    Factory response;
    {
        response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
    }

    while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
    {
        System.Threading.Thread.Sleep(1000);
    }
    return response;
}
```



Добавьте следующий код, создающий **фабрику данных**, в метод **Main**. 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Создание связанной службы хранилища Azure
Создайте функцию StorageLinkedServiceDefinition в файле Program.cs:

```csharp
static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
    AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
    LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
    return linkedService;
}
```
Добавьте следующий код, создающий **связанную службу хранилища Azure**, в метод **Main**. Дополнительные сведения о свойствах связанных служб BLOB-объектов Azure см. в [этом разделе](connector-azure-blob-storage.md#linked-service-properties).

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>Создание наборов данных

В этом разделе создайте два набора данных: для источника и приемника. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Создание набора данных для исходного большого двоичного объекта Azure
Добавьте следующий код, который создает **набор данных большого двоичного объекта Azure**, в метод **Main**. Дополнительные сведения о свойствах набора данных больших двоичных объектов Azure см. в [этом разделе](connector-azure-blob-storage.md#dataset-properties).

Задайте набор данных, представляющий исходные данные в большом двоичном объекте Azure. Этот набор данных большого двоичного объекта относится к связанной службе хранилища Azure, созданной на предыдущем шаге, и описывает следующее:

- Расположение большого двоичного объекта для копирования: **FolderPath** и **FileName**.
- Обратите внимание на использование параметров FolderPath. sourceBlobContainer — это имя параметра. Выражение заменяется значениями, передаваемыми в конвейере. Синтаксис для определения параметров: `@pipeline().parameters.<parameterName>`

Создание функции StorageLinkedServiceDefinition в файле Program.cs

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Создание набора данных для большого двоичного объекта Azure приемника

Создание функции StorageLinkedServiceDefinition в файле Program.cs

```csharp
static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
    return dataset;
}
```

Добавьте следующий код, создающий наборы данных-приемники и источники большого двоичного объекта Azure, в метод **Main**. 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>Создание класса C#: EmailRequest
В проекте C# создайте класс с именем **EmailRequest**. Он определяет свойства, которые конвейер отправляет в тексте запроса при отправке электронной почты. В этом руководстве конвейер отправляет четыре свойства из конвейера по адресу электронной почты:

- **Сообщение**: текст сообщения электронной почты. В случае успешного копирования это свойство содержит сведения о выполнении (количество записанных данных). В случае сбоя копирования это свойство содержит сведения об ошибке.
- **Имя фабрики данных**: имя фабрики данных.
- **Имя конвейера**: имя конвейера.
- **Получатель**: передаваемый параметр. Это свойство указывает получателя сообщения электронной почты.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```
## <a name="create-email-workflow-endpoints"></a>Создание конечных точек рабочего процесса электронной почты
Чтобы инициировать отправку сообщения электронной почты, используйте [Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) для определения рабочего процесса. Сведения о создании рабочего процесса приложения логики см. в статье [Создание первого рабочего процесса приложения логики для автоматизации процессов между облачными приложениями и облачными службами](../logic-apps/logic-apps-create-a-logic-app.md). 

### <a name="success-email-workflow"></a>Рабочий процесс успешной отправки сообщения электронной почты 
Создайте рабочий процесс приложения логики с именем `CopySuccessEmail`. Определите триггер рабочего процесса `When an HTTP request is received` и добавьте действие `Office 365 Outlook – Send an email`.

![Рабочий процесс успешной отправки сообщения электронной почты](media/tutorial-control-flow/success-email-workflow.png)

Для триггера запроса укажите в `Request Body JSON Schema` следующий фрагмент кода JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```
Это соответствует классу **EmailRequest**, созданному в предыдущем разделе. 

В конструкторе приложений логики запрос должен выглядеть следующим образом:

![Запрос в окне конструктора приложений логики](media/tutorial-control-flow/logic-app-designer-request.png)

Для действия **Отправка электронного сообщения** настройте способ форматирования электронного сообщения, используя свойства, переданные в схеме запроса текста JSON. Пример:

![Конструктор приложения логики. Действие отправки электронной почты](media/tutorial-control-flow/send-email-action.png)

Запишите URL-адрес запроса HTTP Post рабочего процесса успешной отправки электронной почты:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>Рабочий процесс сбоя отправки сообщения электронной почты 
Клонируйте действие **CopySuccessEmail** и создайте еще один рабочий процесс Logic Apps **CopyFailEmail**. В триггере запроса действие `Request Body JSON schema` такое же. Просто измените формат электронной почты, например `Subject`, чтобы настроить процесс сбоя отправки сообщения электронной почты. Пример:

![Конструктор приложения логики. Рабочий процесс сбоя отправки сообщения электронной почты](media/tutorial-control-flow/fail-email-workflow.png)

Запишите URL-адрес запроса HTTP Post для рабочего процесса успешной отправки электронной почты:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Теперь у вас должно быть два URL-адреса рабочих процессов:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```
## <a name="create-a-pipeline"></a>Создание конвейера
Добавьте следующий код, создающий конвейер с действием копирования и свойством dependsOn, в метод Main. В этом руководстве конвейер содержит одно действие: операцию копирования, которая принимает набор данных большого двоичного объекта в качестве источника и другой набор данных большого двоичного объекта в качестве приемника. После завершения действия копирования (успех или сбой) вызываются разные задачи электронной почты.

В этом конвейере используются следующие функции:

- Параметры
- Веб-действие
- зависимость действия;
- использование выходных данных действия в качестве входных данных для последующего действия.

Давайте разобьем следующий конвейер на части.

```csharp

static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
        {
            Console.WriteLine("Creating pipeline " + pipelineName + "...");
            PipelineResource resource = new PipelineResource
            {
                Parameters = new Dictionary<string, ParameterSpecification>
                {
                    { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                },
                Activities = new List<Activity>
                {
                    new CopyActivity
                    {
                        Name = copyBlobActivity,
                        Inputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSourceDatasetName
                            }
                        },
                        Outputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSinkDatasetName
                            }
                        },
                        Source = new BlobSource { },
                        Sink = new BlobSink { }
                    },
                    new WebActivity
                    {
                        Name = sendSuccessEmailActivity,
                        Method = WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Succeeded" }
                            }
                        }
                    },
                    new WebActivity
                    {
                        Name = sendFailEmailActivity,
                        Method =WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Failed" }
                            }
                        }
                    }
                }
            };
            Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
            return resource;
        }
```
Добавьте следующий код, создающий конвейер, в метод **Main**:

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```
### <a name="parameters"></a>Параметры
В первой части нашего конвейера определяются параметры. 

- Параметр sourceBlobContainer в конвейере использует исходный набор данных большого двоичного объекта.
- Параметр sinkBlobContainer в конвейере использует набор данных-приемник большого двоичного объекта.
- Параметр receiver используется двумя веб-действиями в конвейере для отправки получателю сообщений электронной почты об успешном или неуспешном выполнении (адрес электронной почты получателя указан в этом параметре).


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```
### <a name="web-activity"></a>Веб-действие
Веб-действие разрешает выполнять вызов любой конечной точки REST. Дополнительные сведения о действиях см. в статье [Веб-действие в фабрике данных Azure](control-flow-web-activity.md). Этот конвейер использует веб-действие для вызова рабочего процесса электронной почты Logic Apps. Вы создаете два веб-действия: вызывающие рабочий процесс **CopySuccessEmail** и **CopyFailWorkFlow**.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```
В свойстве Url вставьте конечные точки URL-адреса запроса из соответствующего рабочего процесса Logic Apps. В свойстве Body передайте экземпляр класса EmailRequest. Запрос сообщения электронной почты содержит следующие свойства.

- Сообщение — передает значение `@{activity('CopyBlobtoBlob').output.dataWritten`. Обращается к свойству предыдущего действия копирования и передает значение dataWritten. В случае сбоя передает выходные данные ошибки вместо `@{activity('CopyBlobtoBlob').error.message`.
- Имя фабрики данных — передает значение `@{pipeline().DataFactory}`. Это системная переменная, которая позволяет получить доступ к соответствующему имени фабрики данных. Список поддерживаемых системных переменных см. в статье [Системные переменные, поддерживаемые фабрикой данных Azure](control-flow-system-variables.md).
- Имя конвейера — передает значение `@{pipeline().Pipeline}`. Это системная переменная, которая позволяет обращаться к соответствующему имени конвейера. 
- Получатель — передает значение "@pipeline().parameters.receiver"). Получает доступ к параметрам конвейера.
 
Этот код создает зависимость действия на основе предыдущей успешной операции копирования.

## <a name="create-a-pipeline-run"></a>Создание конвейера
Добавьте в метод **Main** следующий код, **активирующий выполнение конвейера**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Класс Main 
Итоговый метод Main должен выглядеть следующим образом. Создайте и запустите программу для запуска конвейера.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
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
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Выполнение кода
Создайте и запустите приложение, а затем проверьте выполнение конвейера.
Консоль выведет ход выполнения создания фабрики данных, связанной службы, наборов данных, конвейера и выполнения конвейера. Затем она проверяет состояние выполнения конвейера. Дождитесь появления сведений о действии копирования с размером записанных и прочитанных данных. Затем воспользуйтесь такими средствами, как обозреватель службы хранилища Azure, чтобы проверить, скопирован ли большой двоичный объект в outputBlobPath из inputBlobPath, как указано в переменных.

**Пример выходных данных**

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве вы выполнили следующие шаги: 

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создание связанной службы хранилища Azure.
> * Создание набора данных больших двоичных объектов Azure
> * Создание конвейера, содержащего действия копирования и веб-действие.
> * Отправка выходных данных действий для последующих действий.
> * Использование передачи параметров и системных переменных.
> * Запуск конвейера.
> * Мониторинг конвейера и выполнения действий.

Теперь вы можете перейти к разделу ключевых концепций, чтобы получить дополнительные сведения о фабрике данных Azure.
> [!div class="nextstepaction"]
>[Конвейеры и действия](concepts-pipelines-activities.md)
