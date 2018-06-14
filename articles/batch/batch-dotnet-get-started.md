---
title: Руководство. Использование клиентской библиотеки пакетной службы Azure для .NET | Документация Майкрософт
description: Изучите основные принципы работы пакетной службы Azure и создайте простое решения с использованием .NET.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 76cb9807-cbc1-405a-8136-d1e53e66e82b
ms.service: batch
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 06/28/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dbd96187ad73a9c7e27b28f137e25fe66e2944ad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31514410"
---
# <a name="get-started-building-solutions-with-the-batch-client-library-for-net"></a>Приступая к созданию решений с помощью клиентской библиотеки пакетной службы для .NET

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

Ознакомьтесь с основными сведениями о библиотеке [пакетной службы Azure][azure_batch] и [.NET для пакетной службы][net_api] в этой статье в пошаговой инструкции по созданию примера приложения C#. Мы рассмотрим, как пример приложения использует пакетную службу для обработки параллельной рабочей нагрузки в облаке и взаимодействует со [службой хранилища Azure](../storage/common/storage-introduction.md) при промежуточном хранении и извлечении файлов. Вы узнаете об общем рабочем процессе приложения пакетной службы и получите базовые знания о главных компонентах пакетной службы, например заданиях, задачах, пулах и вычислительных узлах.

![Рабочий процесс решения пакетной службы (основной)][11]<br/>

## <a name="prerequisites"></a>предварительным требованиям
В этой статье предполагается, что вы уже работали с C# и Visual Studio. Также предполагается, что вы можете выполнить требования к созданию учетной записи для службы хранилища и пакетной службы Azure. Эти требования перечислены ниже.

### <a name="accounts"></a>учетные записи;
* **Учетная запись Azure.** Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][azure_free_account].
* **Учетная запись пакетной службы**. Если у вас есть подписка Azure, [создайте учетную запись пакетной службы Azure](batch-account-create-portal.md).
* **Учетная запись хранения.** Параметры учетной записи хранения в пакетной службе см. в [обзоре функций пакетной службы](batch-api-basics.md#azure-storage-account).


### <a name="visual-studio"></a>Visual Studio
Вам понадобится **Visual Studio 2015 или более поздней версии** для создания примера проекта. Бесплатные и пробные версии Visual Studio можно найти на странице [обзора продуктов Visual Studio][visual_studio].

### <a name="dotnettutorial-code-sample"></a>*DotNetTutorial* 
Пример [DotNetTutorial][github_dotnettutorial] — это один из многих примеров кода в репозитории [azure-batch-samples][github_samples] на сайте GitHub. Чтобы скачать все примеры, на домашней странице репозитория последовательно выберите **Clone or download > Download ZIP**  (Клонировать или скачать > Скачать ZIP-файл) или щелкните ссылку [azure-batch-samples-master.zip][github_samples_zip] и скачайте их напрямую. После извлечения содержимого ZIP-файла решение будет сохранено в такую папку:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="batchlabs-optional"></a>BatchLabs (необязательно)
[BatchLabs][github_batchlabs] — это бесплатное автономное клиентское средство с множеством функций для создания, отладки и мониторинга приложений пакетной службы Azure. Хотя его не обязательно использовать при работе по этому руководству, этот обозреватель может пригодиться во время разработки и отладки решений пакетной службы.

## <a name="dotnettutorial-sample-project-overview"></a>Общие сведения о примере проекта DotNetTutorial
Пример кода *DotNetTutorial* представляет собой решение Visual Studio, состоящее из двух проектов: **DotNetTutorial** и **TaskApplication**.

* **DotNetTutorial** — это клиентское приложение, которое взаимодействует с пакетной службой и службой хранилища, чтобы выполнять параллельную рабочую нагрузку на вычислительных узлах (виртуальных машинах). DotNetTutorial работает на локальной рабочей станции.
* **TaskApplication** — это программа, которая запускается на вычислительных узлах в Azure, чтобы выполнять фактическую работу. В этом примере `TaskApplication.exe` анализирует текст во входном файле, скачанном из службы хранилища Azure. Затем он создает текстовый файл (выходной файл), который содержит список из трех наиболее часто употребляемых слов во входном файле. После создания выходного файла программа TaskApplication отправляет файл в службу хранилища Azure. Теперь его может загрузить клиентское приложение. TaskApplication выполняется параллельно на нескольких вычислительных узлах в пакетной службе.

На следующей схеме показаны основные операции, которые выполняет клиентское приложение *DotNetTutorial*, а также приложение *TaskApplication*, выполняемое с помощью задач. Этот основной рабочий процесс является типичным для многих вычислительных решений, созданных с помощью пакетной службы. Хотя он не демонстрирует все возможности, доступные в пакетной службе, почти все ее сценарии включают в себя части этого рабочего процесса.

![Пример рабочего процесса пакетной службы][8]<br/>

[**Шаг 1.**](#step-1-create-storage-containers) Создайте **контейнеры** в хранилище BLOB-объектов Azure.<br/>
[**Шаг 2.**](#step-2-upload-task-application-and-data-files) Отправьте файлы приложения задач и входные файлы в контейнеры.<br/>
[**Шаг 3.**](#step-3-create-batch-pool) Создайте **пул** пакетной службы.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Задача **StartTask** пула скачивает двоичные файлы задач (TaskApplication) на узлы во время их присоединения к пулу.<br/>
[**Шаг 4.**](#step-4-create-batch-job) Создайте **задание** пакетной службы.<br/>
[**Шаг 5.**](#step-5-add-tasks-to-job) Добавьте **задачи** в задание.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Планируется выполнение задач на узлах.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Каждая задача скачивает свои входные данные из службы хранилища Azure, а затем начинает выполнение.<br/>
[**Шаг 6.**](#step-6-monitor-tasks) Мониторинг задач.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Когда задачи выполнены, их выходные данные отправляются в службу хранилища Azure.<br/>
[**Шаг 7.**](#step-7-download-task-output) Скачайте выходные данные задачи из службы хранилища.

Как уже упоминалось, не каждое решение пакетной службы будет выполнять именно эти действия. Некоторые решения могут выполнять больше действий, но в примере приложения *DotNetTutorial* показаны общие процессы в решении пакетной службы.

## <a name="build-the-dotnettutorial-sample-project"></a>Создание примера проекта *DotNetTutorial*
Чтобы успешно запустить пример, нужно указать учетные данные учетных записей пакетной службы и службы хранилища в файле `Program.cs` проекта *DotNetTutorial*. Если вы еще не сделали этого, откройте решение в Visual Studio, дважды щелкнув файл решения `DotNetTutorial.sln` . Этот файл можно также открыть в среде Visual Studio, последовательно выбрав в меню **Файл > Открыть > Project/Solution** (Решение или проект).

Откройте `Program.cs` в проекте *DotNetTutorial* . Затем добавьте учетные данные в начало файла:

```csharp
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

Учетные данные учетных записей пакетной службы и службы хранилища можно найти в колонке учетной записи каждой службы на [портале Azure][azure_portal]:

![Учетные данные пакетной службы на портале][9]
![Учетные данные службы хранилища на портале][10]<br/>

Теперь, когда вы обновили проект, используя свои учетные данные, в обозревателе решений щелкните решение правой кнопкой мыши и выберите команду **Собрать решение**. Подтвердите восстановление пакетов NuGet, если появится соответствующий запрос.

> [!TIP]
> Если пакеты NuGet не восстановились автоматически или во время их восстановления возникли ошибки, убедитесь, что [диспетчер пакетов NuGet][nuget_packagemgr] установлен. Затем включите загрузку недостающих пакетов. Чтобы включить скачивание пакетов, см. инструкции в статье [Enabling Package Restore During Build][nuget_restore] (Включение восстановления пакетов во время сборки).
>
>

В следующих разделах мы разобьем пример приложения на действия, выполняемые для обработки рабочей нагрузки в пакетной службе, а затем подробно их рассмотрим. Во время работы с оставшейся частью статьи рекомендуем сверяться с открытым решением в Visual Studio, так как в примере рассматриваются не все строки кода.

Перейдите в начало метода `MainAsync` в файле `Program.cs` проекта *DotNetTutorial*, чтобы начать с шага 1. Каждый из шагов ниже в целом соответствует последовательности вызовов методов в `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Шаг 1. Создание контейнеров службы хранилища
![Create containers in Azure Storage][1]
<br/>

Пакетная служба включает встроенную поддержку для взаимодействия со службой хранилища Azure. Контейнеры в учетной записи хранения предоставляют файлы, которые нужны для выполнения задач, запускаемых в вашей учетной записи пакетной службы. Контейнеры также предоставляют место для хранения выходных данных, создаваемых задачами. Сначала клиентское приложение *DotNetTutorial* создает три контейнера в [хранилище BLOB-объектов Azure](../storage/common/storage-introduction.md).

* **application**— в этом контейнере будет храниться приложение, которое выполняют задачи, а также все его зависимости, например библиотеки DLL.
* **input**— задачи будут скачивать файлы данных, которые они должны обрабатывать, из контейнера *input* .
* **output**— после завершения обработки входных файлов задачи будут отправлять результаты обработки в контейнер *output* .

Чтобы клиентское приложение могло взаимодействовать с учетной записью хранения и создавать контейнеры, мы используем [клиентскую библиотеку службы хранилища Azure для .NET][net_api_storage]. Мы создадим ссылку на учетную запись, используя [CloudStorageAccount][net_cloudstorageaccount], а затем из этой учетной записи создадим клиент [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Мы используем ссылку `blobClient` везде в приложении и передаем ее в качестве параметра нескольким методам. Пример этого можно увидеть в блоке кода, который находится сразу за методом blobClient. С его помощью вызывается метод `CreateContainerIfNotExistAsync`, чтобы создать контейнеры.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Когда контейнеры будут созданы, приложение сможет отправлять файлы, которые будут использоваться задачами.

> [!TIP]
> Статья [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) содержит хороший обзор работы с контейнерами службы хранилища Azure и большими двоичными объектами. Вы должны ознакомиться с этой статьей, прежде чем приступать к работе с пакетной службой.
>
>

## <a name="step-2-upload-task-application-and-data-files"></a>Шаг 2. Отправка приложения задач и файлов данных
![Upload task application and input (data) files to containers][2]
<br/>

Во время отправки файлов приложение *DotNetTutorial* сначала определяет коллекции путей к файлам **application** и **input** на локальном компьютере. Затем оно отправляет эти файлы в контейнеры, созданные в рамках предыдущего шага.

```csharp
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

При отправке используются два метода из файла `Program.cs` :

* `UploadFilesToContainerAsync` — этот метод возвращает коллекцию объектов [ResourceFile][net_resourcefile] (рассматривается далее) и выполняет внутренний вызов метода `UploadFileToContainerAsync`, чтобы отправить каждый файл, переданный в параметре *filePaths*.
* `UploadFileToContainerAsync` — этот метод отправляет файл и создает объекты [ResourceFile][net_resourcefile]. После отправки файла метод получает подписанный URL-адрес (SAS) для файла и возвращает объект ResourceFile, который его представляет. Подписанные URL-адреса также рассматриваются ниже.

```csharp
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles
Объект [ResourceFile][net_resourcefile] передает задачи в пакетную службу с URL-адресом файла в службе хранилища Azure, который будет скачан на вычислительный узел перед выполнением этой задачи. В свойстве [ResourceFile.BlobSource][net_resourcefile_blobsource] указывается полный URL-адрес файла, по которому его можно найти в службе хранилища Azure. URL-адрес может также включать подписанный URL-адрес (SAS), который обеспечивает безопасный доступ к файлу. Большинство типов задач в пакетной службе .NET, в том числе перечисленные ниже, содержат свойство *ResourceFiles* .

* [CloudTask][net_task]
* [StartTask][net_pool_starttask]
* [JobPreparationTask][net_jobpreptask]
* [JobReleaseTask][net_jobreltask]

В примере приложения DotNetTutorial не используются типы задач JobPreparationTask или JobReleaseTask, но дополнительные сведения о них можно узнать в статье [Выполнение задач подготовки и завершения заданий на вычислительных узлах пакетной службы Azure](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Подписанный URL-адрес (SAS)
Подписанные URL-адреса — это строки, которые могут быть включены как часть URL-адреса. Они предоставляют безопасный доступ к контейнерам и большим двоичным объектам в службе хранилища Azure. Приложение DotNetTutorial использует подписанные URL-адреса как контейнеров, так и больших двоичных объектов. Оно демонстрирует, как получить эти строки подписанных URL-адресов из службы хранилища.

* **Подписанные URL-адреса больших двоичных объектов**. Задача StartTask пула в DotNetTutorial использует подписанные URL-адреса больших двоичных объектов во время скачивания двоичных файлов приложения и файлов входных данных из службы хранилища (см. шаг 3 ниже). В методе `UploadFileToContainerAsync` в файле `Program.cs` приложения DotNetTutorial содержится код, с помощью которого можно получить подписанный URL-адрес каждого большого двоичного объекта. Для этого выполняется вызов [CloudBlob.GetSharedAccessSignature][net_sas_blob].
* **Подписанные URL-адреса контейнеров**. Когда каждая задача завершает работу на вычислительном узле, она отправляет свой выходной файл в контейнер *output* в службе хранилища Azure. Для этого приложение TaskApplication использует подписанный URL-адрес контейнера, который предоставляет доступ на запись в контейнер как часть пути во время загрузки файла. Получение подписанного URL-адреса контейнера происходит таким же образом, как и получение подписанного URL-адреса большого двоичного объекта. В приложении DotNetTutorial вы увидите, что вспомогательный метод `GetContainerSasUrl` вызывает для этого [CloudBlobContainer.GetSharedAccessSignature][net_sas_container]. Дополнительную информацию о том, как приложение TaskApplication использует подписанный URL-адрес контейнера, см. на шаге 6 "Мониторинг задач".

> [!TIP]
> Дополнительные сведения о предоставлении безопасного доступа к данным в своей учетной записи хранения см. в серии из двух статей о подписанных URL-адресах: [Часть 1: общие сведения о модели SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) и [Часть 2: создание и использование подписанного URL-адреса в службе BLOB-объектов](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md).
>
>

## <a name="step-3-create-batch-pool"></a>Шаг 3. Создание пула пакетной службы
![Create a Batch pool][3]
<br/>

**Пул** пакетной службы — это коллекция вычислительных узлов (виртуальных машин), на которых пакетная служба выполняет задачи задания.

Загрузив приложения и файлы данных в учетную запись хранилища с помощью API-интерфейсов хранилища Azure, *DotNetTutorial* начинает обращаться к пакетной службе с помощью API-интерфейсов, предоставляемых библиотекой .NET пакетной службы. Код сначала создает [BatchClient][net_batchclient]:

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Затем в учетной записи пакетной службы создается пул вычислительных узлов с помощью вызова `CreatePoolIfNotExistsAsync`. `CreatePoolIfNotExistsAsync` использует метод [BatchClient.PoolOperations.CreatePoolnet][net_pool_create] для создания пула в пакетной службе:

```csharp
private static async Task CreatePoolIfNotExistAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    CloudPool pool = null;
    try
    {
        Console.WriteLine("Creating pool [{0}]...", poolId);

        // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
        // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
        pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicatedComputeNodes: 3,                                             // 3 compute nodes
            virtualMachineSize: "small",                                                // single-vCPU, 1.75 GB memory, 225 GB disk
            cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));   // Windows Server 2012 R2

        // Create and assign the StartTask that will be executed when compute nodes join the pool.
        // In this case, we copy the StartTask's resource files (that will be automatically downloaded
        // to the node by the StartTask) into the shared directory that all tasks will have access to.
        pool.StartTask = new StartTask
        {
            // Specify a command line for the StartTask that copies the task application files to the
            // node's shared directory. Every compute node in a Batch pool is configured with a number
            // of pre-defined environment variables that can be referenced by commands or applications
            // run by tasks.

            // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
            // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
            // StartTask execution success.
            CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
            ResourceFiles = resourceFiles,
            WaitForSuccess = true
        };

        await pool.CommitAsync();
    }
    catch (BatchException be)
    {
        // Swallow the specific error code PoolExists since that is expected if the pool already exists
        if (be.RequestInformation?.BatchError != null && be.RequestInformation.BatchError.Code == BatchErrorCodeStrings.PoolExists)
        {
            Console.WriteLine("The pool {0} already existed when we tried to create it", poolId);
        }
        else
        {
            throw; // Any other exception is unexpected
        }
    }
}
```

Во время создания пула с использованием метода [CreatePool][net_pool_create] нужно указать несколько параметров, например количество вычислительных узлов, [размер узлов](../cloud-services/cloud-services-sizes-specs.md) и их операционную систему. В приложении *DotNetTutorial* используется [CloudServiceConfiguration][net_cloudserviceconfiguration], чтобы указать Windows Server 2012 R2 из [облачных служб](../cloud-services/cloud-services-guestos-update-matrix.md). 

Также можно создать пулы вычислительных узлов, представленные виртуальными машинами Azure, указав [VirtualMachineConfiguration][net_virtualmachineconfiguration] для пула. Пул вычислительных узлов виртуальных машин можно создать из образов Windows или [Linux](batch-linux-nodes.md). Источник для образов виртуальных машин может быть следующим:

- [Магазин виртуальных машин Azure Marketplace][vm_marketplace], который предоставляет образы Windows и Linux, готовые к использованию. 
- Настраиваемый образ, подготавливаемый и предоставляемый пользователем. Дополнительные сведения о пользовательских образах см. в руководстве по [разработке решений для крупномасштабных параллельных вычислений с помощью пакетной службы](batch-api-basics.md#pool).

> [!IMPORTANT]
> В пакетной службе за использование вычислительных ресурсов взимается плата. Чтобы свести к минимуму затраты, перед запуском примера уменьшите значение параметра `targetDedicatedComputeNodes` до 1.
>
>

Вместе с этими физическими свойствами узла можно также указать для пула задачу [StartTask][net_pool_starttask]. Задача StartTask выполняется на каждом узле по мере его присоединения к пулу, а также при каждом перезапуске узла. Задача StartTask успешно используется для установки приложений на вычислительных узлах до выполнения задачи. Например, если задачи обрабатывают данные с помощью сценариев Python, вы может использовать StartTask для установки Python на вычислительных узлах.

В этом примере приложения задача StartTask копирует файлы, которые она скачивает из рабочего каталога StartTask службы хранилища (эти файлы указаны в свойстве [StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles]) в общий каталог, к которому есть доступ у *всех* задач, выполняемых на узле. По сути, это обеспечивает копирование `TaskApplication.exe` и его зависимостей в общий каталог на каждом узле, когда узел присоединяется к пулу, чтобы к нему был доступ у всех задач, запускаемых на узле.

> [!TIP]
> Функция использования **пакетов приложений** пакетной службы Azure обеспечивает альтернативный способ развертывания приложения на вычислительных узлах в пуле. Дополнительные сведения см. в статье [Развертывание приложений на вычислительных узлах с помощью пакетов приложений пакетной службы](batch-application-packages.md).
>
>

Обратите также внимание на то, что во фрагменте кода выше в свойстве *CommandLine* задачи StartTask используются две переменные среды: `%AZ_BATCH_TASK_WORKING_DIR%` и `%AZ_BATCH_NODE_SHARED_DIR%`. На каждом вычислительном узле в пуле пакетной службы автоматически настраивается несколько переменных среды, характерных для пакетной службы. Любой процесс, выполняемый задачей, имеет доступ к этим переменным среды.

> [!TIP]
> Дополнительные сведения о переменных среды, доступных на вычислительных узлах в пуле пакетной службы, а также сведения о рабочих каталогах задач см. в разделах [Параметры среды для задач](batch-api-basics.md#environment-settings-for-tasks) и [Файлы и каталоги](batch-api-basics.md#files-and-directories) статьи [Обзор функций пакетной службы для разработчиков](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Шаг 4. Создание задания пакетной службы
![Создание задания пакетной службы][4]<br/>

**Задание** пакетной службы — это коллекция задач, связанных с пулом вычислительных узлов. Задачи задания выполняются на вычислительных узлах связанного пула.

Вы можете использовать задание не только для упорядочивания и отслеживания задач в соответствующих рабочих нагрузках, но и для установления определенных ограничений, включая максимальное время выполнения задания (а следовательно, и его задач). При этом назначается приоритет задания относительно других заданий в учетной записи пакетной службы. Но в этом примере задание связано только с пулом, который был создан на шаге 3. Дополнительные свойства не настроены.

Все задания пакетной службы связаны с конкретным пулом. Эта связь указывает, в каких узлах будут выполняться задачи задания. Вам необходимо указать ее, используя свойство [CloudJob.PoolInformation][net_job_poolinfo], как показано в следующем фрагменте кода.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

В созданное задание добавляются задачи для выполнения работы.

## <a name="step-5-add-tasks-to-job"></a>Шаг 5. Добавление задач в задание
![Добавление задач в задание][5]<br/>
*(1) Задачи добавляются в задание, (2) планируется запуск задач на узлах, (3) задачи скачивают файлы данных для обработки*

**Задачи** пакетной службы представляют собой отдельные рабочие единицы, выполняемые на вычислительных узлах. У задачи есть командная строка, она запускает сценарии или исполняемые файлы, указанные в этой строке.

Для фактического выполнения работы необходимо добавить задачи в задание. Каждая задача [CloudTask][net_task] настраивается с использованием свойства командной строки и объекта [ResourceFiles][net_task_resourcefiles] (как и задача StartTask пула), который задача скачивает на узел до автоматического выполнения ее командной строки. В примере проекта *DotNetTutorial* каждая задача обрабатывает только один файл. Поэтому его коллекция ResourceFiles содержит один элемент.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [!IMPORTANT]
> При получении доступа к переменным среды, таким как `%AZ_BATCH_NODE_SHARED_DIR%`, или выполнении приложения, которое не находится в `PATH` на узле, командные строки задачи должны начинаться с префикса `cmd /c`. Благодаря этому интерпретатор команд будет выполнен явно и получит инструкцию завершить работу после выполнения команды. Это требование необязательно, если ваши задачи выполняют приложения в `PATH` на узле (например, *robocopy.exe* или *powershell.exe*) и не используют переменные среды.
>
>

В пределах цикла `foreach` в приведенном выше фрагменте кода видно, что командная строка задачи построена таким образом, чтобы передавать три аргумента командной строки для *TaskApplication.exe*.

1. **Первый аргумент** — это путь к файлу, который нужно обработать. Это локальный путь к файлу, так как он существует на узле. Когда объект ResourceFile впервые создавался в `UploadFileToContainerAsync` , для этого свойства использовалось имя файла (в качестве параметра конструктора ResourceFile). Это значит, что файл находится в том же каталоге, что и приложение *TaskApplication.exe*.
2. **Второй аргумент** указывает *N* самых часто используемых слов, которые должны быть записаны в выходной файл. В данном примере жестко задано, чтобы в выходной файл было записано три наиболее часто используемых слова.
3. **Третий аргумент** — это подписанный URL-адрес (SAS), который предоставляет доступ на запись в контейнер **output** в службе хранилища Azure. *TaskApplication.exe* использует этот подписанный URL-адрес при отправке выходного файла в службу хранилища Azure. Имеющийся код можно найти в методе `UploadFileToContainer` файла `Program.cs` проекта TaskApplication.

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Шаг 6. Мониторинг задач
![Мониторинг задач][6]<br/>
*Клиентское приложение (1) отслеживает выполнение задач и состояние их выполнения, а задачи (2) отправляют данные результатов в службу хранилища Azure*

Добавленные в задание задачи автоматически выстраиваются в очередь и планируются для выполнения на вычислительных узлах пула, связанного с заданием. Пакетная служба обрабатывает постановку задач в очередь, их планирование извлечение и другие задачи администрирования с учетом указанных вами параметров.

Есть несколько подходов к отслеживанию выполнения задач. Приложение DotNetTutorial — это простой пример, который сообщает только о трех состояниях: завершение, сбой задачи и успешное завершение. В методе `MonitorTasks` файла `Program.cs` приложения DotNetTutorial есть три основных объекта пакетной службы .NET, на которые следует обратить внимание. Они перечислены ниже в порядке появления.

1. **ODATADetailLevel.** Чтобы обеспечить выполнение приложения пакетной службы, необходимо указать класс [ODATADetailLevel][net_odatadetaillevel] в операциях получения списков (таких как получение списка задач задания). Ознакомьтесь со статьей [Эффективные запросы к пакетной службе Azure](batch-efficient-list-queries.md), если вы планируете выполнять мониторинг состояния в приложениях пакетной службы.
2. **TaskStateMonitor.** Класс [TaskStateMonitor][net_taskstatemonitor] предоставляет приложениям .NET пакетной службы вспомогательные служебные программы для мониторинга состояния задач. В методе `MonitorTasks` приложение *DotNetTutorial* ожидает, пока для всех задач не установится состояние [TaskState.Completed][net_taskstate] за отведенное время. Затем приложение завершает задание.
3. **TerminateJobAsync.** Если завершить задание с помощью метода [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (или блокирующего метода JobOperations.TerminateJob), задание будет отмечено как завершенное. Это очень важно сделать, если в решении пакетной службы используется [JobReleaseTask][net_jobreltask]. Это особый тип задачи, который описан в статье о [задачах подготовки и завершения заданий](batch-job-prep-release.md).

Метод `MonitorTasks` из файла `Program.cs` приложения *DotNetTutorial* показан ниже:

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a failure
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.Result == TaskExecutionResult.Failure)
        {
            // A task with failure information set indicates there was a problem with the task. It is important to note that
            // the task's state can be "Completed," yet still have encountered a failure.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a failure: {1}", task.Id, task.ExecutionInformation.FailureInformation.Message);
            if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Шаг 7. Загрузка выходных данных задачи
![Загрузка выходных данных задачи из службы хранилища][7]<br/>

Теперь, когда задание выполнено, можно загрузить выходные данные задач из службы хранилища Azure. Для этого нужно вызвать метод `DownloadBlobsFromContainerAsync` в файле `Program.cs` приложения *DotNetTutorial*:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [!NOTE]
> При вызове `DownloadBlobsFromContainerAsync` в приложении *DotNetTutorial* указывается, что файлы необходимо скачать в папку `%TEMP%`. Вы можете изменить это расположение выходных данных.
>
>

## <a name="step-8-delete-containers"></a>Шаг 8. Удаление контейнеров
Так как вы платите за данные, которые находятся в службе хранилища Azure, мы рекомендуем всегда удалять BLOB-объекты, которые больше не нужны для выполнения заданий пакетной службы. Для этого в файле `Program.cs` приложения DotNetTutorial нужно трижды вызвать вспомогательный метод `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

Сам метод просто получает ссылку на контейнер, а затем вызывает [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Шаг 9. Удаление задания и пула
На последнем шаге вам предлагается удалить пул и задание, созданные приложением DotNetTutorial. Вы не оплачиваете задания и задачи, но *платите* за используемые вычислительные узлы. Поэтому рекомендуется выделять узлы только при необходимости. Удаление неиспользуемых пулов может быть частью процесса обслуживания.

Свойства [JobOperations][net_joboperations] и [PoolOperations][net_pooloperations] метода BatchClient предусматривают соответствующие методы удаления, которые вызываются, если пользователь подтверждает удаление:

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [!IMPORTANT]
> Помните, что вы платите за использование вычислительных ресурсов, поэтому удаление неиспользуемых пулов позволит сократить затраты. Также не забывайте, что при удалении пула удаляются все вычислительные узлы этого пула, после чего все данные на узлах уже нельзя будет восстановить.
>
>

## <a name="run-the-dotnettutorial-sample"></a>Запуск примера *DotNetTutorial*
Когда вы запустите пример приложения, консоль будет выглядеть так. Во время выполнения может возникнуть пауза на этапе `Awaiting task completion, timeout in 00:30:00...` , когда будут запускаться вычислительные узлы пула. Используйте [портал Azure][azure_portal] для мониторинга пула, вычислительных узлов, заданий и задач во время и после выполнения. Используйте [портал Azure][azure_portal] или [обозреватель службы хранилища Azure][storage_explorers], чтобы просматривать ресурсы службы хранилища (контейнеры и большие двоичные объекты), созданные приложением.

Обычное время выполнения — **примерно 5 минут** , если для приложения задана конфигурация по умолчанию.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Дополнительная информация
Вы можете вносить изменения в приложения *DotNetTutorial* и *TaskApplication*, чтобы поэкспериментировать с разными сценариями вычислений. Например, попробуйте добавить задержку выполнения в приложение *TaskApplication*, например [Thread.Sleep][net_thread_sleep], чтобы сымитировать длительно выполняемые задачи и следить за ними на портале. Попробуйте добавить дополнительные задачи или изменить количество вычислительных узлов. Добавьте логику для проверки и разрешите использовать имеющийся пул, чтобы ускорить выполнение. (*Подсказка.* Ознакомьтесь с файлом `ArticleHelpers.cs` проекта [Microsoft.Azure.Batch.Samples.Common][github_samples_common] в репозитории [azure-batch-samples][github_samples].)

Теперь, когда вы знакомы с основным рабочим процессом решения пакетной службы, пришло время подробно изучить дополнительные возможности пакетной службы.

* Если вы недавно используете пакетную службу, рекомендуем прочитать статью [с обзором функций пакетной службы Azure](batch-api-basics.md) .
* Ознакомьтесь с другими статьями на тему разработки в пакетной службе в разделе **Подробные сведения о разработке** на [схеме обучения "Пакетная служба"][batch_learning_path].
* Ознакомьтесь с другими способами обработки рабочей нагрузки "N часто употребляемых слов" с использованием пакетной службы на примере [TopNWords][github_topnwords].
* Просмотрите [заметки о выпуске](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/Batch/DataPlane/changelog.md#azurebatch-release-notes) .NET для пакетной службы, в которых описываются последние изменения в библиотеке.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchlabs]: https://azure.github.io/BatchLabs/
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/vs/
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Создание контейнеров в службе хранилища Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Отправка файлов приложения и входных данных в контейнеры"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Создание пула пакетной службы"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Создание задания пакетной службы"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Добавление задач в задание"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Мониторинг задач"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Скачивание выходных данных задачи из службы хранилища"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Рабочий процесс решения пакетной службы (полная схема)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Учетные данные пакетной службы на портале"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Учетные данные службы хранилища на портале"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Рабочий процесс решения пакетной службы (сокращенная схема)"
