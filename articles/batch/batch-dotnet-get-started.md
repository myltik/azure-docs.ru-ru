<properties
	pageTitle="Руководство. Начало работы с библиотекой пакетной службы Azure для .NET | Microsoft Azure"
	description="Основные понятия пакетной службы Azure и использование пакетной службы в простом сценарии"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="04/11/2016"
	ms.author="marsma"/>

# Начало работы с библиотекой пакетной службы Azure для .NET

Ознакомьтесь с основными сведениями о библиотеке [пакетной службы Azure][azure_batch] и [пакетной службы .NET][net_api], изучив пошаговую инструкцию по созданию примера приложения C#. Мы рассмотрим, как пример приложения использует пакетную службу для обработки параллельной рабочей нагрузки в облаке и взаимодействует со [службой хранилища Azure](../storage/storage-introduction.md) при промежуточном хранении и извлечении файлов. Вы изучите распространенные методы рабочих процессов, используемые в приложении пакетной службы. Кроме того, вы получите базовые знания о главных компонентах пакетной службы, таких как задания, задачи, пулы и вычислительные узлы.

![Рабочий процесс решения пакетной службы (основной)][11]<br/>

## Предварительные требования

В этой статье предполагается, что вы уже работали с C# и Visual Studio. Также предполагается, что вы можете выполнить требования к созданию учетной записи для службы хранилища и пакетной службы Azure. Эти требования перечислены ниже.

### учетные записи;

- **Учетная запись Azure**. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][azure_free_account].
- **Учетная запись пакетной службы**. Если у вас есть подписка Azure, [создайте учетную запись пакетной службы Azure](batch-account-create-portal.md).
- **Учетная запись хранения**. См. раздел [Создание учетной записи хранения](../storage/storage-create-storage-account.md#create-a-storage-account) в статье [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md).

### Visual Studio

Чтобы создать пример проекта, вам понадобится программа **Visual Studio 2013** или **Visual Studio 2015**. Бесплатные и пробные версии Visual Studio можно найти на странице [обзора продуктов Visual Studio 2015][visual_studio].

### Пример кода *DotNetTutorial*

Пример [DotNetTutorial][github_dotnettutorial] — это один из многих примеров кода в репозитории [azure-batch-samples][github_samples] на сайте GitHub. Чтобы скачать пример, нажмите кнопку **Download ZIP** (Скачать ZIP-файл) на домашней странице репозитория или щелкните ссылку [azure-batch-samples-master.zip][github_samples_zip], чтобы скачать его напрямую. После извлечения содержимого ZIP-файла решение будет сохранено в эту папку:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### Обозреватель пакетной службы Azure (необязательно)

[Обозреватель пакетной службы Azure][github_batchexplorer] — это бесплатная служебная программа, которая содержится в репозитории [azure-batch-samples][github_samples] на сайте GitHub. Хотя обозреватель пакетной службы не требуется для работы с этим руководством, его настоятельно рекомендуется использовать для отладки и администрирования сущностей в учетной записи пакетной службы. Сведения о предыдущей версии обозревателя пакетной службы см. в записи блога [Azure Batch Explorer Sample Walkthrough][batch_explorer_blog] (Пошаговое руководство по использованию обозревателя пакетной службы Azure).

## Общие сведения о примере проекта DotNetTutorial

Пример кода *DotNetTutorial* представляет собой решение Visual Studio 2013, состоящее из двух проектов — **DotNetTutorial** и **TaskApplication**.

- **DotNetTutorial** — это клиентское приложение, которое взаимодействует с пакетной службой и службой хранилища, чтобы выполнять параллельную рабочую нагрузку на вычислительных узлах (виртуальных машинах). DotNetTutorial работает на локальной рабочей станции.

- **TaskApplication** — это программа, которая запускается на вычислительных узлах в Azure, чтобы выполнить фактическую работу. В этом примере файл `TaskApplication.exe` анализирует текст во входном файле, скачанном из службы хранилища Azure. Затем он создает текстовый файл (выходной файл), который содержит список из трех наиболее часто употребляемых слов во входном файле. После создания выходного файла программа TaskApplication отправляет файл в службу хранилища Azure. Теперь его может загрузить клиентское приложение. TaskApplication выполняется параллельно на нескольких вычислительных узлах в пакетной службе.

На следующей схеме показаны основные операции, которые выполняет клиентское приложение *DotNetTutorial*, а также приложение *TaskApplication*, выполняемое с помощью задач. Этот основной рабочий процесс является типичным для многих вычислительных решений, созданных с помощью пакетной службы. Хотя он не демонстрирует все возможности, доступные в пакетной службе, почти все сценарии пакетной службы будут включать похожие процессы.

![Пример рабочего процесса пакетной службы][8]<br/>

[**Шаг 1.**](#step-1-create-storage-containers) Создайте **контейнеры** в хранилище BLOB-объектов Azure.<br/> 
[**Шаг 2.**](#step-2-upload-task-application-and-data-files) Отправьте файлы приложения задач и входные файлы в контейнеры.<br/> 
[**Шаг 3.**](#step-3-create-batch-pool) Создайте **пул** пакетной службы.<br/> 
	&nbsp;&nbsp;&nbsp;&nbsp;**3.1.** Пул **StartTask** скачивает двоичные файлы задач (TaskApplication) на узлы во время их присоединения к пулу.<br/> 
[**Шаг 4.**](#step-4-create-batch-job) Создайте **задание** пакетной службы.<br/> 
[**Шаг 5.**](#step-5-add-tasks-to-job) Добавьте **задачи** в задание.<br/> 
&nbsp;&nbsp;&nbsp;&nbsp;**5.1.** Планирование задач выполняется на узлах.<br/> 
	&nbsp;&nbsp;&nbsp;&nbsp;**5.2.** Каждая задача скачивает свои входные данные из службы хранилища Azure, а затем начинает выполнение.<br/> 
[**Шаг 6.**](#step-6-monitor-tasks) Выполните мониторинг задач.<br/> 
 &nbsp;&nbsp;&nbsp;&nbsp;**6.1.** После выполнения задач их выходные данные отправляются в службу хранилища Azure.<br/> 
[**Шаг 7.**](#step-7-download-task-output) Скачайте выходные данные задачи из службы хранилища.

Как уже упоминалось, не каждое решение пакетной службы будет выполнять именно эти действия. Некоторые решения могут выполнять больше действий, но пример приложения *DotNetTutorial* демонстрирует общие процессы в решении пакетной службы.

## Сборка примера проекта *DotNetTutorial*

Чтобы успешно запустить пример, необходимо указать учетные данные учетной записи хранения и пакетной службы в файле `Program.cs` проекта *DotNetTutorial*. Если вы еще не сделали этого, откройте решение в Visual Studio, дважды щелкнув файл решения `DotNetTutorial.sln`. Этот файл можно также открыть в среде Visual Studio, последовательно выбрав **Файл > Открыть > Решение или проект**.

Откройте `Program.cs` в проекте *DotNetTutorial*. Затем добавьте учетные данные в начало файла:

```
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

Учетные данные учетных записей хранения и пакетной службы можно найти в колонке учетных записей каждой службы на [портале Azure][azure_portal]\:

![Учетные данные пакетной службы на портале][9] 
![Учетные данные службы хранилища на портале][10]<br/>

Теперь, когда вы обновили проект, используя учетные данные, в обозревателе решений щелкните решение правой кнопкой мыши и выберите команду **Собрать решение**. Подтвердите восстановление пакетов NuGet, если появится соответствующий запрос.

> [AZURE.TIP] Если пакеты NuGet не восстановились автоматически или возникли ошибки, связанные со сбоем их восстановления, убедитесь, что вы установили [диспетчер пакетов NuGet][nuget_packagemgr]. Затем включите загрузку недостающих пакетов. Сведения о том, как включить скачивание пакетов см. в разделе [Enabling Package Restore During Build][nuget_restore] (Включение восстановления пакетов во время сборки).

В следующих разделах мы разобьем пример приложения на действия, выполняемые для обработки рабочей нагрузки в пакетной службе, а затем подробно их рассмотрим. Во время работы с оставшейся частью статьи рекомендуем сверяться с открытым решением в Visual Studio, так как в примере рассматриваются не все строки кода.

Перейдите к началу метода `MainAsync` в файле `Program.cs` проекта *DotNetTutorial*, чтобы начать с шага 1. Каждый из шагов ниже в целом соответствует последовательности вызовов метода в `MainAsync`.

## Шаг 1. Создание контейнеров службы хранилища

![Создание контейнеров в службе хранилища Azure][1] 
<br/>

Пакетная служба включает встроенную поддержку для взаимодействия со службой хранилища Azure. Контейнеры в учетной записи хранения предоставляют файлы, которые нужны для выполнения задач, запускаемых в вашей учетной записи пакетной службы. Контейнеры также предоставляют место для хранения выходных данных, создаваемых задачами. Сначала клиентское приложение *DotNetTutorial* создает три контейнера в [хранилище BLOB-объектов Azure](../storage/storage-introduction.md):

- **application** — в этом контейнере будет храниться приложение, которое выполняют задачи, а также все его зависимости, например библиотеки DLL.
- **input** — задачи будут скачивать файлы данных, которые они должны обрабатывать, из контейнера *input*.
- **output** — после завершения обработки входных файлов задачи будут отправлять результаты обработки в контейнер *output*.

Чтобы клиентское приложение могло взаимодействовать с учетной записью хранения и создавать контейнеры, мы используем [клиентскую библиотеку службы хранилища Azure для .NET][net_api_storage]. Мы создадим ссылку на учетную запись, используя [CloudStorageAccount][net_cloudstorageaccount], а затем из этой учетной записи создадим клиент [CloudBlobClient][net_cloudblobclient]\:

```
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Мы используем ссылку `blobClient` везде в приложении и передаем ее в качестве параметра нескольким методам. Пример этого можно увидеть в блоке кода, который находится сразу за методом blobClient. С помощью него мы вызываем `CreateContainerIfNotExistAsync`, чтобы фактически создать контейнеры.

```
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
		CloudBlobContainer container = blobClient.GetContainerReference(containerName);

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

> [AZURE.TIP] [Использование хранилища BLOB-объектов из .NET](../storage/storage-dotnet-how-to-use-blobs.md)Статья "Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET" содержит хороший обзор работы с контейнерами службы хранилища Azure и большими двоичными объектами. Вы должны ознакомиться с этой статьей, прежде чем приступать к работе с пакетной службой.

## Шаг 2. Отправка приложения задач и файлов данных

![Отправка файлов приложения и входных данных в контейнеры][2] 
<br/>

Во время отправки файлов приложение *DotNetTutorial* сначала определяет коллекции путей к файлам **приложения** и **входным данным** на локальном компьютере. Затем оно отправляет эти файлы в контейнеры, созданные в рамках предыдущего шага.

```
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

При отправке используется два метода из файла `Program.cs`.

- `UploadFilesToContainerAsync` — этот метод возвращает коллекцию объектов [ResourceFile][net_resourcefile] (рассматривается далее) и выполняет внутренний вызов метода `UploadFileToContainerAsync`, чтобы отправить каждый файл, переданный в параметре *filePaths*.
- `UploadFileToContainerAsync` — этот метод фактически отправляет файл и создает объекты [ResourceFile][net_resourcefile]. После отправки файла метод получает подписанный URL-адрес (SAS) для файла и возвращает объект ResourceFile, который его представляет. Подписанные URL-адреса также рассматриваются ниже.

```
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
		await blobData.UploadFromFileAsync(filePath, FileMode.Open);

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

### ResourceFiles

Объект [ResourceFile][net_resourcefile] передает задачи в пакетную службу с URL-адресом файла в службе хранилища Azure, который будет скачан на вычислительный узел перед выполнением этой задачи. Свойство [ResourceFile.BlobSource][net_resourcefile_blobsource] указывает полный URL-адрес файла, по которому его можно найти в службе хранилища Azure. URL-адрес может также включать подписанный URL-адрес (SAS), который обеспечивает безопасный доступ к файлу. Большинство типов задач в пакетной службе .NET, в том числе следующие, включают в себя свойство *ResourceFiles*:

- [CloudTask][net_task]
- [StartTask][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

Пример приложения DotNetTutorial не использует типы задач JobPreparationTask или JobReleaseTask, но вы можете узнать о них больше из статьи [Выполнение задач подготовки и завершения заданий на вычислительных узлах пакетной службы Azure](batch-job-prep-release.md).

### Подписанный URL-адрес (SAS)

Подписанные URL-адреса — это строки, которые могут быть включены как часть URL-адреса. Они предоставляют безопасный доступ к контейнерам и большим двоичным объектам в службе хранилища Azure. Приложение DotNetTutorial использует подписанные URL-адреса как контейнеров, так и больших двоичных объектов. Оно демонстрирует, как получить эти строки подписанных URL-адресов из службы хранилища.

- **Подписанные URL-адреса больших двоичных объектов** — задача StartTask пула в DotNetTutorial использует подписанные URL-адреса больших двоичных объектов во время скачивания двоичных файлов приложения и файлов входных данных из хранилища (см. шаг 3 ниже). Метод `UploadFileToContainerAsync` в файле `Program.cs` приложения DotNetTutorial содержит код, который получает подписанный URL-адрес каждого большого двоичного объекта. Для этого выполняется вызов [CloudBlob.GetSharedAccessSignature][net_sas_blob].

- **Подписанные URL-адреса контейнеров** — когда каждая задача завершает работу на вычислительном узле, она отправляет свой выходной файл в контейнер *output* в службе хранилища Azure. Для этого приложение TaskApplication использует подписанный URL-адрес контейнера, который предоставляет доступ на запись в контейнер как часть пути во время загрузки файла. Получение подписанного URL-адреса контейнера происходит таким же образом, как и получение подписанного URL-адреса большого двоичного объекта. В приложении DotNetTutorial вы увидите, что вспомогательный метод `GetContainerSasUrl` вызывает для этого [CloudBlobContainer.GetSharedAccessSignature][net_sas_container]. Дополнительную информацию о том, как приложение TaskApplication использует подписанный URL-адрес контейнера, см. на шаге 6 "Мониторинг задач".

> [AZURE.TIP] Дополнительные сведения о предоставлении безопасного доступа к данным в своей учетной записи хранения см. в серии из двух статей о подписанных URL-адресах: [Подписанные URL-адреса. Часть 1: общие сведения о модели SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) и [Подписанные URL-адреса. Часть 2: создание и использование подписанного URL-адреса в службе BLOB-объектов](../storage/storage-dotnet-shared-access-signature-part-2.md).

## Шаг 3. Создание пула пакетной службы

![Создание пула пакетной службы][3] 
<br/>

После отправки файлов приложения и файлов данных в учетную запись хранения приложение *DotNetTutorial* начинает их взаимодействие с пакетной службой, используя библиотеку .NET пакетной службы. Для этого сначала создается [BatchClient][net_batchclient]\:

```
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
	...
```

Затем в учетной записи пакетной службы создается пул вычислительных узлов с помощью вызова `CreatePoolAsync`. `CreatePoolAsync` использует метод [BatchClient.PoolOperations.CreatePool][net_pool_create], чтобы создать пул в пакетной службе.

```
private static async Task CreatePoolAsync(
    BatchClient batchClient,
    string poolId,
    IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(),
    // no pool is actually created in the Batch service. This CloudPool instance is
    // therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
			poolId: poolId,
			targetDedicated: 3,           // 3 compute nodes
			virtualMachineSize: "small",  // single-core, 1.75 GB memory, 224 GB disk
			cloudServiceConfiguration:
			    new CloudServiceConfiguration(osFamily: "4")); // Win Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join
    // the pool. In this case, we copy the StartTask's resource files (that will be
    // automatically downloaded to the node by the StartTask) into the shared
    // directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application
        // files to the node's shared directory. Every compute node in a Batch pool
        // is configured with several pre-defined environment variables that you can
        // reference by using commands or applications run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code
        // (e.g. 1 when one or more files were successfully copied) we need to
        // manually exit with a 0 for Batch to recognize StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Во время создания пула с использованием метода [CreatePool][net_pool_create] вам необходимо указать несколько параметров, таких как количество вычислительных узлов, [размер узлов](../cloud-services/cloud-services-sizes-specs.md) и их операционная система. В *DotNetTutorial* мы используем [CloudServiceConfiguration][net_cloudserviceconfiguration], чтобы указать Windows Server 2012 R2 из [облачных служб](../cloud-services/cloud-services-guestos-update-matrix.md). Тем не менее если вместо этого указать [VirtualMachineConfiguration][net_virtualmachineconfiguration], можно создать пулы узлов, созданных на основе образов Marketplace, в том числе образов Windows и Linux. Дополнительные сведения см. в записи блога [Introducing Linux support on Azure Batch][blog_linux] (Введение в поддержку Linux в пакетной службе Azure).

> [AZURE.IMPORTANT] В пакетной службе за использование вычислительных ресурсов взимается плата. Чтобы свести к минимуму затраты, перед запуском примера уменьшите значение параметра `targetDedicated` до 1.

Вместе с этими физическими свойствами узла вы также можете указать для пула задачу [StartTask][net_pool_starttask]. Задача StartTask будет выполняться на каждом узле по мере его присоединения к пулу, а также при каждом перезапуске узла. Задача StartTask успешно используется для установки приложений на вычислительных узлах до выполнения задачи. Например, если задачи обрабатывают данные с помощью сценариев Python, вы может использовать StartTask для установки Python на вычислительных узлах.

В этом примере приложения задача StartTask копирует файлы, которые она скачивает из рабочего каталога StartTask службы хранилища (эти файлы указаны в свойстве [StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles]) в общий каталог, к которому есть доступ у *всех* задач, выполняемых на узле. По сути, это обеспечивает копирование `TaskApplication.exe` и его зависимостей в общий каталог на каждом узле, когда узел присоединяется к пулу, чтобы к нему был доступ у всех задач, запускаемых на узле.

> [AZURE.TIP] Функция использования **пакетов приложений** пакетной службы Azure обеспечивает альтернативный способ доступа к приложению на вычислительных узлах в пуле. Дополнительные сведения см. в статье [Развертывание приложения с помощью пакетов приложений пакетной службы Azure](batch-application-packages.md).

Обратите также внимание на то, что в фрагменте кода выше в свойстве *CommandLine* задачи StartTask используются две переменные среды: `%AZ_BATCH_TASK_WORKING_DIR%` и `%AZ_BATCH_NODE_SHARED_DIR%`. Каждый вычислительный узел в пуле пакетной службы автоматически содержит несколько переменных среды, характерных для пакетной службы. Любой процесс, выполняемый задачей, имеет доступ к этим переменным среды.

> [AZURE.TIP] Дополнительные сведения о переменных среды, доступных на вычислительных узлах в пуле пакетной службы, а также сведения о рабочих каталогах задач см. в разделах "Параметры среды для задач" и "Файлы и каталоги" статьи [Обзор функций пакетной службы Azure](batch-api-basics.md).

## Шаг 4. Создание задания пакетной службы

![Создание задания пакетной службы][4]<br/>

Задание пакетной службы — это коллекция задач, связанных с пулом вычислительных узлов. Вы можете использовать его не только для упорядочивания и отслеживания задач в соответствующих рабочих нагрузках, но и для установления определенных ограничений, включая максимальное время выполнения задания (а следовательно, и его задач). При этом назначается приоритет задания относительно других заданий в учетной записи пакетной службы. Но в этом примере задание связано только с пулом, который был создан на шаге 3. Дополнительные свойства не настроены.

Все задания пакетной службы связаны с конкретным пулом. Эта связь указывает, в каких узлах будут выполняться задачи задания. Вам необходимо указать ее с помощью свойства [CloudJob.PoolInformation][net_job_poolinfo], как показано в следующем фрагменте кода.

```
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

## Шаг 5. Добавление задач в задание

![Добавление задач в задание][5]<br/> 
*(1) Задачи добавляются в задание, (2) планируется запуск задач на узлах, (3) задачи скачивают файлы данных для обработки*

Для фактического выполнения работы необходимо добавить задачи в задание. Каждая задача [CloudTask][net_task] настраивается с помощью свойства командной строки и объекта [ResourceFiles][net_task_resourcefiles] (как и задача StartTask пула), который задача скачивает на узел до автоматического выполнения ее командной строки. В примере проекта *DotNetTutorial* каждая задача обрабатывает только один файл. Поэтому его коллекция ResourceFiles содержит один элемент.

```
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
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 "{1}"",
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

> [AZURE.IMPORTANT] При получении доступа к переменным среды, таким как `%AZ_BATCH_NODE_SHARED_DIR%`, или выполнении приложения, которое не находится в `PATH` узла, командные строки задачи должны начинаться с префикса `cmd /c`. Благодаря этому интерпретатор команд будет выполнен явно и получит инструкцию завершить работу после выполнения команды. Это требование необязательно, если ваши задачи выполняют приложение в `PATH` узла (например, *robocopy.exe* или *powershell.exe*) и не используют переменные среды.

В пределах цикла `foreach` в приведенном выше фрагменте кода видно, что командная строка задачи построена таким образом, чтобы передавать три аргумента командной строки для *TaskApplication.exe*:

1. **Первый аргумент** — это путь к файлу, который нужно обработать. Это локальный путь к файлу, так как он существует на узле. Когда объект ResourceFile впервые создавался в `UploadFileToContainerAsync`, для этого свойства использовалось имя файла (в качестве параметра конструктора ResourceFile). Это значит, что файл находится в том же каталоге, что и приложение *TaskApplication.exe*.

2. **Второй аргумент** указывает *N* наиболее часто используемых слов, которые должны быть записаны в выходной файл. В данном примере жестко задано, чтобы в выходной файл было записано три наиболее часто используемых слова.

3. **Третий аргумент** — это подписанный URL-адрес, который предоставляет доступ на запись в контейнер **output** в службе хранилища Azure. Приложение *TaskApplication.exe* использует этот подписанный URL-адрес, когда отправляет выходной файл в службу хранилища Azure. Вы можете найти соответствующий код в методе `UploadFileToContainer` файла `Program.cs` проекта TaskApplication:

```
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
				blob.UploadFromFile(filePath, FileMode.Open);

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

## Шаг 6. Мониторинг задач

![Мониторинг задач][6]<br/>
 *Клиентское приложение (1) отслеживает выполнение задач и состояние их выполнения, а задачи (2) отправляют данные результатов в службу хранилища Azure*

Добавленные в задание задачи автоматически выстраиваются в очередь и планируются для выполнения на вычислительных узлах пула, связанного с заданием. Пакетная служба обрабатывает постановку задач в очередь, их планирование извлечение и другие задачи администрирования с учетом указанных вами параметров. Есть несколько подходов к отслеживанию выполнения задач. Приложение DotNetTutorial — это простой пример, который сообщает только о трех состояниях: завершение, сбой задачи и успешное завершение.

В методе `MonitorTasks` файла `Program.cs` приложения DotNetTutorial есть три основных понятия пакетной службы .NET, на которые следует обратить внимание. Они перечислены ниже в порядке появления.

1. **ODATADetailLevel**. Чтобы обеспечить выполнение приложения пакетной службы, необходимо указать класс [ODATADetailLevel][net_odatadetaillevel] в операциях получения списков (таких как получение списка задач задания). Ознакомьтесь со статьей [Эффективные запросы к пакетной службе Azure](batch-efficient-list-queries.md), если вы планируете отслеживать состояние в приложениях пакетной службы.

2. **TaskStateMonitor**. Класс [TaskStateMonitor][net_taskstatemonitor] предоставляет приложениям .NET пакетной службы вспомогательные служебные программы для мониторинга состояния задач. В методе `MonitorTasks` приложение *DotNetTutorial* ожидает, пока для всех задач не установится состояние [TaskState.Completed][net_taskstate] за отведенное время. Затем приложение завершает задание.

3. **TerminateJobAsync**. Если завершить задание с помощью метода [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (или блокирующего метода JobOperations.TerminateJob), задание будет отмечено как завершенное. Это очень важно сделать, если решение пакетной службы использует [JobReleaseTask][net_jobreltask]. Это особый тип задачи, который описан в статье [Выполнение задач подготовки и завершения заданий](batch-job-prep-release.md).

Метод `MonitorTasks` из файла `Program.cs` приложения *DotNetTutorial* показан ниже:

```
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...", timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
    bool timedOut = await taskStateMonitor.WaitAllAsync(
        tasks,
        TaskState.Completed,
        timeout);

    if (timedOut)
    {
        allTasksSuccessful = false;

        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);

        Console.WriteLine(failureMessage);
    }
    else
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

        // All tasks have reached the "Completed" state. However, this does not
        // guarantee that all tasks were completed successfully. Here we further
        // check each task's ExecutionInfo property to ensure that it did not
        // encounter a scheduling error or return a non-zero exit code.

        // Update the detail level to populate only the task id and executionInfo
        // properties. We refresh the tasks below, and need only this information
        // for each task.
        detail.SelectClause = "id, executionInfo";

        foreach (CloudTask task in tasks)
        {
            // Populate the task's properties with the latest info from the Batch service
            await task.RefreshAsync(detail);

            if (task.ExecutionInformation.SchedulingError != null)
            {
                // A scheduling error indicates a problem starting the task on the
                // node. It is important to note that the task's state can be
                // "Completed," yet the task still might have encountered a
                // scheduling error.

                allTasksSuccessful = false;

                Console.WriteLine(
                    "WARNING: Task [{0}] encountered a scheduling error: {1}",
                    task.Id,
                    task.ExecutionInformation.SchedulingError.Message);
            }
            else if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by
                // the task encountered an error during execution. As not every
                // application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                allTasksSuccessful = false;

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

## Шаг 7. Загрузка выходных данных задачи

![Загрузка выходных данных задачи из службы хранилища][7]<br/>

Теперь, когда задание выполнено, можно загрузить выходные данные задач из службы хранилища Azure. Для этого нужно вызвать метод `DownloadBlobsFromContainerAsync` в файле `Program.cs` приложения *DotNetTutorial*:

```
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

> [AZURE.NOTE] При вызове `DownloadBlobsFromContainerAsync` в приложении *DotNetTutorial* указывается, что файлы необходимо скачать в папку `%TEMP%`. Вы можете изменить это расположение выходных данных.

## Шаг 8. Удаление контейнеров

Так как вы платите за данные, которые находятся в службе хранилища Azure, рекомендуется всегда удалять все большие двоичные объекты, которые больше не нужны для выполнения заданий пакетной службы. Для этого в файле `Program.cs` приложения DotNetTutorial нужно трижды вызвать вспомогательный метод `DeleteContainerAsync`:

```
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

Сам метод просто получает ссылку на контейнер, а затем вызывает [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]\:

```
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

## Шаг 9. Удаление задания и пула

На последнем шаге пользователю предлагается удалить пул и задание, созданные приложением DotNetTutorial. Хотя вы не платите за задания и задачи, вы *платите* за используемые вычислительные узлы. Поэтому рекомендуется выделять узлы только при необходимости. Удаление неиспользуемых пулов может быть частью процесса обслуживания.

Свойства [JobOperations][net_joboperations] и [PoolOperations][net_pooloperations] BatchClient предусматривают соответствующие методы удаления, которые вызываются, если пользователь подтверждает удаление:

```
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

> [AZURE.IMPORTANT] Помните, что вы платите за использование вычислительных ресурсов, поэтому удаление неиспользуемых пулов позволит сократить затраты. Также не забывайте, что при удалении пула удаляются все вычислительные узлы этого пула, после чего все данные на узлах уже нельзя будет восстановить.

## Запуск примера *DotNetTutorial*

Когда вы запустите пример приложения, консоль будет выглядеть так. Во время выполнения может возникнуть пауза на этапе `Awaiting task completion, timeout in 00:30:00...`, когда будут запускаться вычислительные узлы пула. Используйте [обозреватель пакетной службы][github_batchexplorer] для мониторинга пула, вычислительных узлов, заданий и задач во время и после выполнения. Используйте [портал Azure][azure_portal] или один из [доступных обозревателей службы хранилища Azure][storage_explorers], чтобы просматривать ресурсы службы хранилища (контейнеры и большие двоичные объекты), созданные приложением.

Обычное время выполнения — **примерно 5 минут**, если для приложения задана конфигурация по умолчанию.

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

## Дальнейшие действия

Вы можете внести изменения в приложения *DotNetTutorial* и *TaskApplication*, чтобы поэкспериментировать с разными сценариями вычислений. Например, попробуйте добавить задержку выполнения в приложение *TaskApplication*, к примеру [Thread.Sleep][net_thread_sleep], чтобы имитировать длительно выполняемые задачи и отслеживать их с помощью функции *Тепловая карта* обозревателя пакетной службы. Попробуйте добавить дополнительные задачи или изменить количество вычислительных узлов. Добавьте логику для проверки и разрешите использовать существующий пул, чтобы ускорить выполнение. (*Подсказка*. См. файл `ArticleHelpers.cs` проекта [Microsoft.Azure.Batch.Samples.Common][github_samples_common] в репозитории [azure-batch-samples][github_samples].)

Теперь, когда вы знакомы с основным рабочим процессом решения пакетной службы, пришло время подробно изучить дополнительные возможности пакетной службы.

- Если вы недавно используете пакетную службу, рекомендуем прочитать статью [Обзор функций пакетной службы Azure](batch-api-basics.md).
- Ознакомьтесь с другими статьями на тему разработки в пакетной службе. См. раздел **Подробные сведения о разработке** в каталоге статей [Пакетная служба][batch_learning_path].
- Ознакомьтесь с другими способами обработки рабочей нагрузки "N часто употребляемых слов" с использованием пакетной службы в примере приложения [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_explorer_blog]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
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
[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Создание контейнеров в службе хранилища Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Отправка файлов приложения и входных данных в контейнеры"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Создание пула пакетной службы"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Создание задания пакетной службы"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Добавление задач в задание"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Мониторинг задач"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Загрузка выходных данных задачи из службы хранилища"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Рабочий процесс решения пакетной службы (полная схема)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Учетные данные пакетной службы на портале"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Учетные данные службы хранилища на портале"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Рабочий процесс решения пакетной службы (сокращенная схема)"

<!----HONumber=AcomDC_0413_2016-->