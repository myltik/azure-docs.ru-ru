<properties
	pageTitle="Учебник. Начало работы с библиотекой пакетной службы Azure для .NET | Microsoft Azure"
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
	ms.date="01/08/2016"
	ms.author="marsma"/>

# Начало работы с библиотекой пакетной службы Azure для .NET  

Ознакомьтесь с основными сведениями о библиотеке [пакетной службы Azure][azure_batch] и [пакетной службы .NET][net_api], изучив пошаговую инструкцию по созданию примера приложения C#. Вы узнаете, как библиотека использует пакетную службу для обработки параллельной рабочей нагрузки в облаке и как она взаимодействует со [службой хранилища Azure](./../storage/storage-introduction.md) для промежуточного хранения и извлечения файлов. Введение в общие методы рабочего процесса приложения пакетной службы и основные сведения о главных компонентах пакетной службы, например заданиях, задачах, пулах и вычислительных узлах.

![Рабочий процесс решения пакетной службы (сокращенно)][11]<br/>

## Предварительные требования

В этой статье предполагается, что вы работали с C# и Visual Studio и можете выполнить приведенные ниже требования к созданию учетных записей Azure, пакетной службы и службы хранилища.

### учетные записи;

- **Учетная запись Azure** — если у вас еще нет подписки Azure, можно создать бесплатную пробную учетную запись за несколько минут на странице [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).
- **Учетная запись пакетной службы** — если у вас есть подписка Azure, [создайте учетную запись пакетной службы Azure и управляйте ею](batch-account-create-portal.md).
- **Учетная запись хранения** — см. раздел *Создание учетной записи хранения* в статье [Об учетных записях хранения Azure](../storage-create-storage-account.md).

### Visual Studio

Вам понадобится **Visual Studio 2013 или более поздней версии** для создания примера проекта. Бесплатные и пробные версии Visual Studio можно найти на странице [Overview of Visual Studio 2015 Products][visual_studio] (Обзор продуктов Visual Studio 2015).

### Пример кода *DotNetTutorial*

Пример [DotNetTutorial][github_dotnettutorial] — это один многих примеров кода в репозитории [azure-batch-samples][github_samples] на GitHub. Чтобы загрузить пример, нажмите кнопку Download ZIP (Загрузить ZIP-файл) на главной странице репозитория или щелкните ссылку прямой загрузки [azure-batch-samples-master.zip][github_samples_zip]. После извлечения содержимого ZIP-файла решение будет сохранено в эту папку:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### Обозреватель пакетной службы Azure (необязательно)

[Обозреватель пакетной службы Azure][github_batchexplorer] — это бесплатная служебная программа, включенная в репозиторий [azure-batch-samples][github_samples] на GitHub. Хотя она не требуется для работы с этим учебником, настоятельно рекомендуется использовать ее для отладки и администрирования сущностей в учетной записи пакетной службы. Вы можете почитать о более старой версии обозревателя пакетной службы в записи блога [Azure Batch Explorer Sample Walkthrough][batch_explorer_blog] (Пример пошагового руководства по обозревателю пакетной службы Azure).

## Общие сведения о примере проекта DotNetTutorial

Пример кода *DotNetTutorial* представляет собой решение Visual Studio 2013, состоящее из двух проектов: **DotNetTutorial** и **TaskApplication**.

- **DotNetTutorial** — это клиентское приложение, которое взаимодействует с пакетной службой и службой хранилища для выполнения параллельной рабочей нагрузки на вычислительных узлах (виртуальных машинах). DotNetTutorial работает на локальной рабочей станции.

- **TaskApplication** — это исполняемый файл, который запускается на вычислительных узлах в Azure, чтобы выполнить фактическую работу. В примере файл `TaskApplication.exe` анализирует текст в файле, загруженном из службы хранилища Azure (входной файл), и создает текстовый файл (выходной файл), который содержит список из трех наиболее часто употребляемых слов во входном файле. Файл TaskApplication передает созданный выходной файл в службу хранилища Azure, чтобы клиентское приложение могло его загрузить. TaskApplication выполняется параллельно на нескольких вычислительных узлах в пакетной службе.

На следующей схеме показаны основные операции, выполняемые клиентским приложением *DotNetTutorial*, а также приложением *TaskApplication*, которое выполняется с помощью задач. Этот основной рабочий процесс является типичным для многих вычислительных решений, созданных с помощью пакетной службы. Хотя он не демонстрирует все возможности, доступные в пакетной службе, почти все сценарии службы будут включать похожие процессы.

![Пример рабочего процесса пакетной службы][8]<br/>

**1.** Создание **контейнеров** в хранилище BLOB-объектов. <br/> **2.** Отправка приложения задач и входных файлов в контейнеры<br/> **3.** Создание **пула**пакетной службы <br/> &nbsp;&nbsp;&nbsp;&nbsp;**3а.** Пул **StartTask** загружает двоичный файл задачи (TaskApplication) на узлы во время их присоединения к пулу<br/> **4.** Создание **задания**пакетной службы<br/> **5.** Добавление **задач** в задание<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Задачи планируются для выполнения на узлах<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5б.** Каждая задача загружает свои входные данные из службы хранилища Azure, а затем начинает выполнение<br/> **6.** Отслеживание задач<br/> &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Выполненные задачи отправляют свои выходные данные в службу хранилища Azure<br/> **7.** Загрузка выходных данных задачи из службы хранилища

Как уже упоминалось, не каждое решение пакетной службы будет выполнять эти конкретные действия. Хотя некоторые решения могут выполнять больше действий, пример приложения *DotNetTutorial* демонстрирует общие процессы в решении пакетной службы.

## Сборка примера проекта *DotNetTutorial*

Чтобы успешно запустить пример, укажите учетные данные учетных записей службы хранения и пакетной службы в файле `Program.cs` проекта *DotNetTutorial*. Если вы это еще не сделали, откройте решение в Visual Studio. Для этого дважды щелкните файл решения `DotNetTutorial.sln` или в среде Visual Studio откройте меню **Файл > Открыть > Проект или решение**.

Откройте файл `Program.cs` в проекте *DotNetTutorial* и добавьте свои учетные данные, как указано в верхней части файла:

```
// Update the Batch and Storage account credential strings below with the values unique to your accounts.
// These are used when constructing connection strings for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

Учетные данные учетных записей службы хранения и пакетной службы находятся в колонке учетных записей каждой службы на [портале Azure][azure_portal]\:

![Учетные данные пакетной службы на портале][9] ![Учетные данные службы хранилища на портале][10]<br/>

Теперь, когда вы обновили проект с помощью учетных данных, щелкните правой кнопкой мыши решение в *Обозревателе решений* и выберите команду **Собрать решение**. Подтвердите восстановление пакетов NuGet, если появится соответствующий запрос.

> [AZURE.TIP]Если пакеты NuGet не восстанавливаются автоматически или возникают ошибки, связанные со сбоем во время их восстановления, убедитесь, что вы установили [Диспетчер пакетов NuGet][nuget_packagemgr], а затем включите загрузку недостающих пакетов. Инструкции по включению загрузки пакетов см. в разделе [Enabling Package Restore During Build][nuget_restore] (Включение восстановления пакетов во время сборки).

В следующих разделах мы разобьем пример приложения на действия, выполняемые для обработки рабочей нагрузки в пакетной службе, а затем подробно их рассмотрим. Во время работы со оставшейся частью статьи рекомендуем сверяться с открытым решением в Visual Studio, так как в примере рассматриваются не все строки кода.

Перейдите к верхней части метода `MainAsync` в файле `Program.cs` проекта *DotNetTutorial*, чтобы начать с шага 1. Каждый следующий шаг в целом соответствует последовательности вызовов метода в `MainAsync`.

## Шаг 1. Создание контейнеров службы хранилища

![Создание контейнеров в службе хранилища Azure][1] <br/>

Пакетная служба включает встроенную поддержку взаимодействия со службой хранилища Azure. Контейнеры в учетной записи службы хранения предоставляют задачи, которые выполняются в учетной записи пакетной службы с помощью файлов, необходимых для их выполнения. Кроме того, они предоставляют место для хранения создаваемых ими выходных данных. Сначала клиентское приложение *DotNetTutorial* создает три контейнера в [хранилище BLOB-объектов Azure](./../storage/storage-introduction.md):

- **application** — в этом контейнере будет размещаться приложение, выполняемое задачами, включая все его зависимости, например библиотеки DLL.
- **input** — задачи будут загружать файлы данных для обработки из контейнера *input*.
- **output** — после завершения обработки входных файлов задачи будут отправлять результаты обработки в контейнер *output*.

Чтобы клиентское приложение могло взаимодействовать с учетной записью службы хранилища и создавать контейнеры, используется [клиентская библиотека для .NET службы хранилища Azure][net_api_storage]. А именно, создается ссылка на учетную запись с [CloudStorageAccount][net_cloudstorageaccount], а затем из этой учетной записи получается [CloudBlobClient][net_cloudblobclient]\:

```
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                                                StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Мы используем ссылку `blobClient` везде в приложении, передавая ее в качестве параметра нескольким методам. Пример этого можно увидеть в блоке кода, который находится сразу за методом blobClient, с помощью которого мы вызываем `CreateContainerIfNotExistAsync` для создания контейнеров.

```
// Use the blob client to create the containers in Azure Storage if they don't yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```
private static async Task CreateContainerIfNotExistAsync(CloudBlobClient blobClient, string containerName)
{
		CloudBlobContainer container = blobClient.GetContainerReference(containerName);

		if (await container.CreateIfNotExistsAsync())
		{
				Console.WriteLine("Container [{0}] created.", containerName);
		}
		else
		{
				Console.WriteLine("Container [{0}] exists, skipping creation.", containerName);
		}
}
```

Когда контейнеры будут созданы, приложение сможет отправлять файлы, которые будут использоваться задачами.

> [AZURE.TIP] [How to use Blob storage from .NET]Статья «Использование хранилища BLOB-объектов из .NET» (./../storage/storage-dotnet-how-to-use-blobs.md) содержит хороший обзор, посвященный работе с контейнерами и большими двоичными объектами службы хранилища Azure. Заглядывайте в нее во время работы с пакетной службой.

## Шаг 2. Отправка приложения задач и файлов данных

![Отправка файлов приложения и входных данных в контейнеры][2] <br/>

Во время операции отправки файлов приложение *DotNetTutorial* сначала определяет коллекции путей к файлам **приложения** и **входным данным** на локальном компьютере, а отправляет эти файлы в контейнеры, созданные на предыдущем шаге.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication, allowing us to
    // determine the path of the task application binary dynamically
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

// Upload the application and its dependencies to Azure Storage. This is the application that will
// process the data files, and will be executed by each of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(blobClient, appContainerName, applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of the tasks that are
// executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(blobClient, inputContainerName, inputFilePaths);
```

В процессе отправки участвуют два метода из файла `Program.cs`.

- `UploadFilesToContainerAsync` — этот метод возвращает коллекцию объектов [ResourceFile][net_resourcefile] (рассматривается далее) и внутренне вызывает метод `UploadFileToContainerAsync`, чтобы отправить каждый файл, переданный в параметре *filePaths*.
- `UploadFileToContainerAsync` — этот метод фактически отправляет файл и создает объекты [ResourceFile][net_resourcefile]. После отправки файла метод получает подписанный URL-адрес (SAS) для файла и возвращает объект ResourceFile, который он представляет. Подписанные URL-адреса также рассматриваются ниже.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(CloudBlobClient blobClient, string containerName, string filePath)
{
		Console.WriteLine("Uploading file {0} to container [{1}]...", filePath, containerName);

		string blobName = Path.GetFileName(filePath);

		CloudBlobContainer container = blobClient.GetContainerReference(containerName);
		CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
		await blobData.UploadFromFileAsync(filePath, FileMode.Open);

		// Set the expiry time and permissions for the blob shared access signature. In this case, no start time is specified,
		// so the shared access signature becomes valid immediately
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

Объект [ResourceFile][net_resourcefile] передает задачи в пакетную службу с URL-адресом файла в службе хранилища Azure, который будет загружен на вычислительный узел перед выполнением этой задачи. Свойство [ResourceFile.BlobSource][net_resourcefile_blobsource] указывает полный URL-адрес файла в службе хранилища Azure, который также может содержать подписанный URL-адрес (SAS) для обеспечения безопасного доступа к файлу. Большинство типов задач в пакетной службе .NET включают свойство *ResourceFiles*; они перечислены ниже.

- [CloudTask][net_task]
- [StartTask][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

Пример приложения DotNetTutorial не использует задачи JobPreparationTask или JobReleaseTask, но вы можете больше узнать о них из статьи [Выполнение задач подготовки и завершения заданий на вычислительных узлах пакетной службы Azure](batch-job-prep-release.md).

### Подписанный URL-адрес (SAS)

Подписанные URL-адреса — это строки, которые могут быть включены как часть URL-адреса. Они предоставляют безопасный доступ к контейнерам и большим двоичным объектам в службе хранилища Azure. Приложение DotNetTutorial использует подписанные URL-адреса (SAS) как контейнеров, так и больших двоичных объектов. Оно демонстрирует, как можно получить эти строки SAS из службы хранилища.

- **SAS большого двоичного объекта** — задача StartTask пула в DotNetTutorial использует подписанные URL-адреса больших двоичных объектов во время загрузки двоичных файлов приложения и файлов входных данных из хранилища (см. шаг 3 ниже). Метод `UploadFileToContainerAsync` в файле `Program.cs` приложения DotNetTutorial содержит код, который получает строку SAS каждого большого двоичного объекта, вызывая [CloudblobData.GetSharedAccessSignature][net_sas_blob].

- **SAS контейнера** — когда каждая задача завершает работу на вычислительном узле, она отправляет свой выходной файл в контейнер *output* в службе хранилища Azure. Для этого приложение TaskApplication использует контейнер SAS, который предоставляет доступ записи в контейнер как часть пути во время загрузки файла. Получение строки SAS контейнера аналогично получению SAS большого двоичного объекта. В приложении DotNetTutorial это демонстрируется в ходе вызовов [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] вспомогательного метода `GetContainerSasUrl`. Дополнительную информацию о том, как приложение TaskApplication использует строки SAS контейнера см. на шаге 6 «Мониторинг задач».

> [AZURE.TIP]Прочтите обе части статьи о подписанных URL-адресах [Часть 1. Общие сведения о модели SAS](./../storage/storage-dotnet-shared-access-signature-part-1.md) и [Часть 2. Создание и использование SAS с помощью службы BLOB-объектов](./../storage/storage-dotnet-shared-access-signature-part-2.md), чтобы узнать больше о предоставлении безопасного доступа к данным в своей учетной записи хранилища.

## Шаг 3. Создание пула пакетной службы

![Создание пула пакетной службы][3] <br/>

После отправки файлов приложения и данных в учетную запись службы хранилища приложение *DotNetTutorial* запускает их взаимодействие с помощью пакетной службы, используя библиотеку .NET пакетной службы. Для этого сначала создается [BatchClient][net_batchclient]\:

```
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);
using (BatchClient batchClient = BatchClient.Open(cred))
{
	...
```

Затем создается пул вычислительных узлов в учетной записи пакетной службы с помощью вызова `CreatePoolAsync`. `CreatePoolAsync` использует метод [BatchClient.PoolOperations.CreatePool][net_pool_create] для создания пула пакетной службы.

```
private static async Task CreatePoolAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
    // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
				poolId: poolId,
				targetDedicated: 3,           // 3 compute nodes
				virtualMachineSize: "small",  // single-core, 1.75 GB memory, 225 GB disk
				osFamily: "4");               // Windows Server 2012 R2

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
```

Во время создания пула с помощью метода [CreatePool][net_pool_create] вы указываете несколько параметров, например количество вычислительных узлов, [размер узлов](./../cloud-services/cloud-services-sizes-specs.md) и [операционную систему](./../cloud-services/cloud-services-guestos-update-matrix.md) узлов.

> [AZURE.IMPORTANT]В пакетной службе за использование вычислительных ресурсов взимается плата. Чтобы свести к минимуму затраты, перед запуском примера вы можете снизить значение параметра `targetDedicated` до 1.

Вместе с этими физическими свойствами узла вы также можете указать задачу [StartTask][net_pool_starttask] для пула. Задача StartTask будет выполняться на каждом узле по мере его присоединения к пулу, а также при каждом перезапуске узла. Задача StartTask успешно используется для установки приложений на вычислительных узлах до выполнения задачи. Например, если задачи обрабатывают данные с помощью сценариев Python, вы может использовать StartTask для установки Python на вычислительных узлах.

В этом примере приложения задача StartTask копирует файлы, которые она загружает из рабочего каталога StartTask службы хранилища (эти файлы указаны в свойстве *ResourceFiles* задачи StartTask) в общий каталог, к которому имеют доступ *все* задачи, выполняемые на узле.

Также обратите внимание на то, что в приведенном выше фрагменте кода в свойстве *CommandLine* задачи StartTask используются две переменные среды: `%AZ_BATCH_TASK_WORKING_DIR%` и `%AZ_BATCH_NODE_SHARED_DIR%`. Каждый вычислительный узел в пуле пакетной службы автоматически настраивается с помощью нескольких переменных среды, относящихся к пакетной службе. И любой процесс, выполняемый задачей, имеет доступ к этим переменным среды.

> [AZURE.TIP]Дополнительные сведения о переменных среды, доступных на вычислительных узлах в пуле пакетной службы, а также сведения о рабочих каталогах задачи см. в разделах **Параметры среды для задач** и **Файлы и каталоги** статьи [Обзор функций пакетной службы Azure](batch-api-basics.md).

## Шаг 4. Создание задания пакетной службы

![Создание задания пакетной службы][4]<br/>

Задание пакетной службы — это коллекция задач, связанных с пулом вычислительных узлов. Задание используется не только для упорядочивания и отслеживания задач в соответствующих рабочих нагрузках, но и для установления определенных ограничений, включая максимальное время выполнения задания (и, конечно, его задач). При этом назначается приоритет задания относительно других заданий в учетной записи пакетной службы. Но в этом примере задание связано только с пулом созданном на шаге 3, и его дополнительные свойства не настроены.

Все задания пакетной службы связаны с конкретным пулом. Это задание определяет, на каких узлах будут выполняться задачи задания. Задание создается с помощью свойства [CloudJob.PoolInformation][net_job_poolinfo], как показано во фрагменте кода ниже.

```
private static async Task CreateJobAsync(BatchClient batchClient, string jobId, string poolId)
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

![Добавление задач в задание][5]<br/> *(1) Задачи добавляются в задание, (2) задачи планируются на узлах и (3) задачи загружают файлы данных для обработки*

Для фактического выполнения работы необходимо добавить задачи в задание. Каждая задача [CloudTask][net_task] настраивается с помощью командной строки и (как и задача StartTask пула) объекта [ResourceFiles][net_task_resourcefiles], который задача загружает на узел до автоматического выполнения ее командной строки. В примере проекта *DotNetTutorial* каждая задача обрабатывает только один файл, поэтому ее коллекция ResourceFiles содержит один элемент.

```
private static async Task<List<CloudTask>> AddTasksAsync(BatchClient batchClient, string jobId, List<ResourceFile> inputFiles, string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on whichever node each task will run.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format("cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 "{1}"", inputFile.FilePath, outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection opposed to a separate AddTask call for each. Bulk task submission
    // helps to ensure efficient underlying API calls to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT]Во время доступа к переменным среды, таким как `%AZ_BATCH_NODE_SHARED_DIR%`, или выполнения приложения, которое не находится на `PATH` узла, командные строки задачи должны начинаться с префикса `cmd /c`. Это нужно для того, чтобы явно выполнить интерпретатор команд и передать ему инструкцию завершить работу после выполнения команды. Это требование является необязательным, если ваши задачи выполняют приложение в PATH узла (например, *robocopy.exe* или *powershell.exe*) и не используют переменные среды.

В пределах цикла `foreach` в приведенном выше фрагменте кода видно, что командная строка задачи построена таким образом, чтобы передавать три аргумента командной строки для *TaskApplication.exe*.

1. **Первый аргумент** — это путь к файлу для обработки. Это локальный путь к файлу, так как он существует на узле. Во время первого создания объекта ResourceFile в `UploadFileToContainerAsync` выше для этого свойства использовалось имя файла (в качестве параметра, который передается конструктору ResourceFile), указывая, что файл находится в том же каталоге, что и *TaskApplication.exe*.

2. **Второй аргумент** указывает *N* наиболее часто используемых слов, которые должны быть записаны в выходной файл. В данном примере жестко задано, чтобы в выходной файл было записано 3 наиболее часто используемых слова.

3. **Третий аргумент** — это подписанный URL-адрес (SAS), который предоставляет доступ на запись в контейнер **output** в службе хранилища Azure. Приложение *TaskApplication.exe* использует этот URL-адрес SAS во время отправки выходного файла в службу хранилища Azure, код которого находится в методе `UploadFileToContainer` файла `Program.cs` приложения TaskApplication:

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

				// Indicate that a failure has occurred so that when the Batch service sets the
				// CloudTask.ExecutionInformation.ExitCode for the task that executed this application,
				// it properly indicates that there was a problem with the task.
				Environment.ExitCode = -1;
		}
}
```

## Шаг 6. Мониторинг задач

![Мониторинг задач][6]<br/> *Клиентское приложение (1) отслеживает выполнение задач и состояние их выполнения, а задачи (2) отправляют данные результатов в службу хранилища Azure*

Добавленные в задание задачи автоматически выстраиваются в очередь и планируются для выполнения на вычислительных узлах пула, связанного с заданием. Пакетная служба обрабатывает постановку задач в очередь, их планирование извлечение и другие задачи администрирования с учетом указанных вами параметров. Есть несколько подходов по отслеживанию выполнения задач. Приложение DotNetTutorial — это простой пример, который сообщает только о трех состояниях: завершение, сбой задачи и успешное завершение.

В методе `MonitorTasks` файла `Program.cs` приложения DotNetTutorial есть три основные понятия пакета .NET, которые следует обсудить. Они перечислены ниже в порядке их появления.

1. **ODATADetailLevel**. Чтобы обеспечить выполнение приложения пакетной службы, необходимо указать класс [ODATADetailLevel][net_odatadetaillevel] в операциях списка (таких как получение списка задач задания). Добавьте статью [Эффективные запросы к пакетной службе Azure](batch-efficient-list-queries.md) в список литературы, если вы планируете выполнять любое отслеживание состояния в приложениях пакетной службы.

2. **TaskStateMonitor**. Класс [TaskStateMonitor][net_taskstatemonitor] предоставляет приложениям .NET пакетной службы вспомогательные служебные программы для мониторинга состояний задачи. В методе `MonitorTasks` приложение *DotNetTutorial* ожидает, пока все задачи не достигнут состояния [TaskState.Completed][net_taskstate] за отведенное время, а затем завершает задание.

3. **TerminateJobAsync**. Если завершить задание с помощью метода [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (или блокирующего метода JobOperations.TerminateJob), задание будет отмечено как завершенное. Это необходимо делать, если ваше решение пакетной службы использует задачу [JobReleaseTask][net_jobreltask] (особый тип задачи, описанный в статье [Job preparation and completion tasks](batch-job-prep-release) (Подготовка заданий и выполнение задач)).

Метод `MonitorTasks` из файла `Program.cs` приложения *DotNetTutorial* показан ниже:

```
private static async Task<bool> MonitorTasks(BatchClient batchClient, string jobId, TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use a detail level to
    // specify that only the "id" property of each task should be populated. Using a detail level for
    // all list operations helps to lower response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks = await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...", timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we will wait for all tasks to
    // reach the Completed state.
    TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
    bool timedOut = await taskStateMonitor.WaitAllAsync(tasks, TaskState.Completed, timeout);

    if (timedOut)
    {
        allTasksSuccessful = false;

        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);

        Console.WriteLine(failureMessage);
    }
    else
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

        // All tasks have reached the "Completed" state, however, this does not guarantee all tasks completed successfully.
        // Here we further check each task's ExecutionInfo property to ensure that it did not encounter a scheduling error
        // or return a non-zero exit code.

        // Update the detail level to populate only the task id and executionInfo properties.
        // We refresh the tasks below, and need only this information for each task.
        detail.SelectClause = "id, executionInfo";

        foreach (CloudTask task in tasks)
        {
            // Populate the task's properties with the latest info from the Batch service
            await task.RefreshAsync(detail);

            if (task.ExecutionInformation.SchedulingError != null)
            {
                // A scheduling error indicates a problem starting the task on the node. It is important to note that
                // the task's state can be "Completed," yet still have encountered a scheduling error.

                allTasksSuccessful = false;

                Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}", task.Id, task.ExecutionInformation.SchedulingError.Message);
            }
            else if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
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

Теперь, когда задание выполнено, можно загрузить выходные данные задачи из службы хранилища Azure. Для этого нужно вызвать метод `DownloadBlobsFromContainerAsync` в файле `Program.cs` приложения *DotNetTutorial*:

```
private static async Task DownloadBlobsFromContainerAsync(CloudBlobClient blobClient, string containerName, string directoryPath)
{
		Console.WriteLine("Downloading all files from container [{0}]...", containerName);

		// Retrieve a reference to a previously created container
		CloudBlobContainer container = blobClient.GetContainerReference(containerName);

		// Get a flat listing of all the block blobs in the specified container
		foreach (IListBlobItem item in container.ListBlobs(prefix: null, useFlatBlobListing: true))
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

> [AZURE.NOTE]Вызов `DownloadBlobsFromContainerAsync` в приложении *DotNetTutorial* указывает, что файлы должны быть загружены в папку `%TEMP%`. Вы можете изменить это расположение выходных данных.

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
private static async Task DeleteContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.", containerName);
    }
}
```

## Шаг 9. Удаление задания и пула

На последнем шаге пользователю предлагается удалить пул и здание, созданные приложением DotNetTutorial. Хотя вы не платите за работу с заданиями и задачами, вы *платите* за использование вычислительных узлов. Поэтому рекомендуется выделять узлы только по мере необходимости, удаляя неиспользуемые пулы во время процесса обслуживания.

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

> [AZURE.IMPORTANT]Помните, что вы платите за использование вычислительных ресурсов, поэтому удаление неиспользуемых пулов позволит сократить затраты. Не забывайте, что при удалении пула удаляются все вычислительные узлы этого пула, после чего все данные на узлах уже нельзя будет восстановить.

## Запуск примера проекта *DotNetTutorial*

Когда вы запустите пример приложения, консоль будет выглядеть так. Во время выполнения может возникнуть пауза на состоянии `Awaiting task completion, timeout in 00:30:00...`, когда будут запускаться вычислительные узлы пула. Используйте [обозреватель пакетной службы][github_batchexplorer] для наблюдения за пулом, вычислительными узлами, заданиями и задачами во время и после выполнения. Используйте [портал Azure][azure_portal] или один из [доступных обозревателей службы хранилища Azure][storage_explorers] для просмотра ресурсов службы хранилища (контейнеры и большие двоичные объекты), созданных приложением.

Обычное время выполнения — **примерно 5 минут** при выполнении приложения в конфигурации по умолчанию.

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

Вы можете внести изменения в приложения *DotNetTutorial* и *TaskApplication*, чтобы поэкспериментировать с разными сценариями вычисления. Попробуйте добавить задержку выполнения в приложение *TaskApplication*, например [Thread.Sleep][net_thread_sleep], чтобы имитировать длительно выполняемые задачи и отслеживать их с помощью функции *Тепловая карта* обозревателя пакетной службы. Попробуйте добавить дополнительные задачи или изменить количество вычислительных узлов. Добавьте логику для проверки и разрешите использовать существующий пул для сокращения времени выполнения (*подсказка*: см. файл `ArticleHelpers.cs` проекта [Microsoft.Azure.Batch.Samples.Common][github_samples_common] в репозитории [azure-batch-samples][github_samples]).

Теперь, когда вы знакомы с основным рабочим процессом решения пакетной службы, пришло время подробно изучить дополнительные возможности пакетной службы.

- [Обзор функций пакетной службы Azure](batch-api-basics.md) — эта статья содержит обзор функций пакетной службы и рекомендуется для чтения тем, кто недавно работает с этой службой.
- Ознакомьтесь с другими статьями на тему разработки в пакетной службе: см. раздел **Подробные сведения о разработке** в каталоге статей [Пакетная служба][batch_learning_path]
- Ознакомьтесь с другими способами обработки рабочей нагрузки «N часто употребляемых слов» с помощью пакетной службы: см. пример приложения [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_portal]: https://portal.azure.com
[batch_explorer_blog]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
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
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
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

<!---HONumber=AcomDC_0114_2016-->