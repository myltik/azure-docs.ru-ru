<properties
	pageTitle="Учебник. Начало работы с библиотекой пакетной службы Azure для .NET | Microsoft Azure"
	description="Основные концепции пакетной службы Azure и использование пакетной службы в простом сценарии"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="09/23/2015"
	ms.author="yidingz"/>

# Начало работы с библиотекой Пакетной службы Azure для .NET  

Начните работу с библиотекой пакетной службы Azure для .NET, создав консольное приложение, устанавливающее файлы поддержки и программу, которая выполняется на нескольких вычислительных узлах в пуле пакетной службы Azure. Создаваемые в этом учебнике задачи анализируют текст из файлов, переданных в службу хранилища Azure, и возвращают наиболее часто используемые слова в этих файлах. Примеры написаны на C# и используют [библиотеку пакетной службы Azure для .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx).

## Предварительные требования

- Учетные записи.

	- **Учетная запись Azure** — вы можете создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).

	- **Учетная запись пакетной службы** — см. статью [Создание учетной записи пакетной службы Azure и управление ею](batch-account-create-portal.md).

	- **Учетная запись хранения** — см. раздел **Создание учетной записи хранения** в статье [Учетные записи хранения Azure](../storage-create-storage-account.md). Следуя инструкциям, приведенным в этом учебнике, вы создадите контейнер в этой учетной записи с именем **testcon1**.

- Проект консольного приложения Visual Studio.

	1.  В меню **Файл** Visual Studio последовательно щелкните **Создать** и **Проект**.

	2.	В разделе **Windows** в подразделе **Visual C#** щелкните **Консольное приложение**, дайте проекту имя **GettingStarted**, назовите решение **AzureBatch** и нажмите кнопку **ОК**.

- Сборки NuGet.

	1. После создания проекта в Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**. Выполните поиск в Интернете по фразе **Azure.Batch**, а затем щелкните **Установить**, чтобы установить пакет пакетной службы Microsoft Azure и зависимые компоненты.

	2. Выполните поиск в Интернете по фразе **WindowsAzure.Storage**, а затем щелкните **Установить**, чтобы установить пакет службы хранилища Azure и зависимые компоненты.

> [AZURE.TIP]В этом учебнике используются некоторые основные понятия, связанные с работой пакетной службы, которые рассматриваются в статье [Основы API для пакетной службы Azure](batch-api-basics.md). Если у вас нет опыта работы с пакетной службой, настоятельно рекомендуется прочитать эту статью.

## Шаг 1. Создание и отправка файлов поддержки

Для поддержки приложения в хранилище Azure создается контейнер и текстовые файлы, а затем эти текстовые файлы и файлы поддержки передаются в контейнер.

### Настройка строки подключения к хранилищу

1. Откройте файл App.config проекта GettingStarted, а затем добавьте элемент *&lt;appSettings&gt;* в блок *&lt;configuration&gt;*.

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
			<appSettings>
				<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[account-name];AccountKey=[account-key]"/>
			</appSettings>
		</configuration>

	Замените следующие значения.

	- **[account-name]** — имя ранее созданной учетной записи хранения.

	- **[account-key]** — первичный ключ учетной записи хранения. Первичный ключ находится на портале управления Azure на странице «Хранилище».

2. Сохраните файл App.config.

Дополнительные сведения о строках подключения службы хранилища Azure см. в разделе [Настройка строк подключения службы хранилища Azure](../storage/storage-configure-connection-string.md).

### Создание контейнера хранилища

1. Добавьте следующие директивы using в начало класса Program.cs в проекте GettingStarted:

		using System.Configuration;
		using System.IO;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

2. Добавьте *System.Configuration* в раздел **Ссылки** **обозревателя решений** для проекта GettingStarted.

3. Добавьте следующий метод к классу Program, который получает строку подключения к хранилищу, создает контейнер и задает разрешения.

		static void CreateStorage()
		{
			// Get the storage connection string
			CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
				ConfigurationManager.AppSettings["StorageConnectionString"]);

			// Create the container
			CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
			CloudBlobContainer container = blobClient.GetContainerReference("testcon1");
			container.CreateIfNotExists();

			// Set permissions on the container
			BlobContainerPermissions containerPermissions = new BlobContainerPermissions();
			containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
			container.SetPermissions(containerPermissions);
			Console.WriteLine("Created the container. Press Enter to continue.");
			Console.ReadLine();
		}

4. Добавьте следующий код в Main, который вызывает только что добавленный метод.

		CreateStorage();

5. Сохраните файл Program.cs.

	> [AZURE.NOTE]В рабочей среде рекомендуется использовать [подписанный URL-адрес](https://msdn.microsoft.com/library/azure/ee395415.aspx).

Дополнительные сведения о хранилище больших двоичных объектов см. в разделе [Использование хранилища BLOB-объектов из .NET](../storage/storage-dotnet-how-to-use-blobs.md).

### Создание программы обработки

1. В **обозревателе решений** создайте новый проект консольного приложения с именем **ProcessTaskData**.

2. После создания проекта в Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**. Выполните поиск в Интернете, введя фразу **WindowsAzure.Storage**, а затем щелкните **Установить**, чтобы установить пакет службы хранилища Azure и зависимые компоненты.

3. Добавьте следующую директиву using в начало файла Program.cs:

		using Microsoft.WindowsAzure.Storage.Blob;

4. Добавьте следующий код в Main, обрабатывающий текст из файлов.

		string blobName = args[0];
		Uri blobUri = new Uri(blobName);
		int numTopN = int.Parse(args[1]);

		CloudBlockBlob blob = new CloudBlockBlob(blobUri);
		string content = blob.DownloadText();
		string[] words = content.Split(' ');
		var topNWords =
		  words.
			Where(word => word.Length > 0).
			GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
			OrderByDescending(x => x.Value).
			Take(numTopN).
			ToList();

		foreach (var pair in topNWords)
		{
			Console.WriteLine("{0} {1}", pair.Key, pair.Value);
		}

5. Сохраните и создайте проект ProcessTaskData.

### Создание файлов данных

1. В проекте GettingStarted создайте новый текстовый файл с именем **taskdata1.txt**, скопируйте в него следующий текст, а затем сохраните файл.

	Вы можете использовать виртуальные машины Azure для подготовки масштабируемой и гибкой вычислительной инфраструктуры для выполнения бизнес-задач. Из коллекции вы можете создать виртуальные машины, работающие под управлением Windows, Linux и корпоративных приложений, таких как SharePoint и SQL Server. Вы также можете записать и использовать собственные образы для создания настраиваемых виртуальных машин.

2. Создайте новый текстовый файл с именем **taskdata2.txt**, скопируйте в него следующий текст, а затем сохраните файл.

	Быстро развертывайте мощные приложения и службы и управляйте ими с помощью облачных служб Azure. Просто загрузите свое приложение, а Azure автоматически осуществит развертывание, выполнив все нужные действия, от подготовки и балансировки нагрузки до мониторинга работоспособности для обеспечения непрерывной доступности. Соглашение об уровне обслуживания гарантирует степень доступность 99,95% для вашего приложения. Вы сможете сосредоточиться на приложении, не отвлекаясь на инфраструктуру.

3. Создайте новый текстовый файл с именем **taskdata3.txt**, скопируйте в него следующий текст, а затем сохраните файл.

	Веб-сайты Azure предоставляют масштабируемую, надежную и удобную среду для размещения веб-приложений. Выбирайте платформы и шаблоны, чтобы создавать веб-сайты за несколько секунд. Используйте любое средство или ОС для разработки веб-сайта с помощью .NET, PHP, Node.js или Python. Используйте широкий выбор систем управления версиями, включая TFS, GitHub и BitBucket, для настройки непрерывной интеграции и разработки в группе. Расширяйте функциональные возможности вашего сайта с помощью дополнительных управляемых служб Azure, таких как хранилище, CDN и база данных SQL.

### Передача файлов в контейнер хранилища

1. Откройте файл Program.cs проекта **GettingStarted** и добавьте следующий метод, который отправляет файлы.

		static void CreateFiles()
		{
		  CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
			ConfigurationManager.AppSettings["StorageConnectionString"]);
		  CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		  CloudBlobContainer container = blobClient.GetContainerReference("testcon1");

		  CloudBlockBlob taskData1 = container.GetBlockBlobReference("taskdata1");
		  CloudBlockBlob taskData2 = container.GetBlockBlobReference("taskdata2");
		  CloudBlockBlob taskData3 = container.GetBlockBlobReference("taskdata3");
		  taskData1.UploadFromFile("..\\..\\taskdata1.txt", FileMode.Open);
		  taskData2.UploadFromFile("..\\..\\taskdata2.txt", FileMode.Open);
	  	taskData3.UploadFromFile("..\\..\\taskdata3.txt", FileMode.Open);

			CloudBlockBlob storageassembly = container.GetBlockBlobReference("Microsoft.WindowsAzure.Storage.dll");
			storageassembly.UploadFromFile("Microsoft.WindowsAzure.Storage.dll", FileMode.Open);

			CloudBlockBlob dataprocessor = container.GetBlockBlobReference("ProcessTaskData.exe");
		  dataprocessor.UploadFromFile("..\\..\\..\\ProcessTaskData\\bin\\debug\\ProcessTaskData.exe", FileMode.Open);

		  Console.WriteLine("Uploaded the files. Press Enter to continue.");
		  Console.ReadLine();
		}

2. Добавьте следующий код в Main, который вызывает только что добавленный метод.

		CreateFiles();

3. Сохраните файл Program.cs.

## Шаг 2. Добавление пула в учетную запись пакетной службы

Пул вычислительных узлов — это первый набор ресурсов, который необходимо создать для выполнения задач.

1.	Добавьте следующие директивы using в начало класса Program.cs в проекте GettingStarted:

			using Microsoft.Azure.Batch;
			using Microsoft.Azure.Batch.Auth;
			using Microsoft.Azure.Batch.Common;

2. Добавьте следующий код в Main, который настраивает учетные данные для вызова пакетной службы Azure.

			BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials("[account-url]", "[account-name]", "[account-key]");
			BatchClient client = BatchClient.Open(cred);

	Замените значения в квадратных скобках значениями, связанными с вашей учетной записью пакетной службы. Все эти значения можно найти на [портале предварительной версии Azure](https://portal.azure.com). Чтобы найти эти значения, войдите на [портал предварительной версии Azure](https://portal.azure.com) и сделайте следующее:

	- **[account-name]** — щелкните **Учетные записи пакетной службы** и выберите созданную ранее учетную запись пакетной службы.
	- **[account-url]** — в колонке учетной записи пакетной службы щелкните **Свойства** > **URL-адрес**.
	- **[account-key]** — в колонке учетной записи пакетной службы щелкните **Свойства** > **Ключи** > **Первичный ключ доступа**.

3.	Добавьте следующий метод в класс Program, который создает пул.

			static void CreatePool(BatchClient client)
			{
			  CloudPool newPool = client.PoolOperations.CreatePool(
			    "testpool1",
			    "3",
			    "small",
			    3);
			  newPool.Commit();
			  Console.WriteLine("Created the pool. Press Enter to continue.");
			  Console.ReadLine();
		  	}

4. Добавьте следующий код в Main, который вызывает только что добавленный метод.

		CreatePool(client);

5. Добавьте следующий метод в класс Program, который содержит список пулов в учетной записи. Таким образом вы проверите, создан ли пул.

			static void ListPools(BatchClient client)
			{
				IPagedEnumerable<CloudPool> pools = client.PoolOperations.ListPools();
				foreach (CloudPool pool in pools)
				{
					Console.WriteLine("Pool name: " + pool.Id);
					Console.WriteLine("   Pool status: " + pool.State);
				}
				Console.WriteLine("Press enter to continue.");
				Console.ReadLine();
			}

6. Добавьте следующий код в Main, который вызывает только что добавленный метод.

		ListPools(client);

7. Сохраните файл Program.cs.

## Шаг 3. Добавление задания в учетную запись

Создайте задание, которое используется для управления выполняемыми в пуле задачами. Все задачи необходимо связать с заданием.

1. Добавьте следующий метод в класс Program, который создает задание.

		static CloudJob CreateJob (BatchClient client)
		{
			CloudJob newJob = client.JobOperations.CreateJob();
			newJob.Id = "testjob1";
			newJob.PoolInformation = new PoolInformation() { PoolId = "testpool1" };
			newJob.Commit();
			Console.WriteLine("Created the job. Press Enter to continue.");
			Console.ReadLine();

			return newJob;
		}

2. Добавьте следующий код в Main, который вызывает только что добавленный метод.

		CreateJob(client);

3. Добавьте следующий метод в класс Program, который содержит список заданий в учетной записи. Таким образом вы проверите, создано ли задание.

		static void ListJobs (BatchClient client)
		{
			IPagedEnumerable<CloudJob> jobs = client.JobOperations.ListJobs();
			foreach (CloudJob job in jobs)
			{
				Console.WriteLine("Job id: " + job.Id);
				Console.WriteLine("   Job status: " + job.State);
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}

4. Добавьте следующий код в Main, который вызывает только что добавленный метод.

		ListJobs(client);

5. Сохраните файл Program.cs.

## Шаг 4. Добавление задач в задание

В созданное задание можно добавлять задачи. Каждая задача выполняется на вычислительном узле и обрабатывает текстовый файл. Следуя инструкциям этого учебника, вы добавите три задачи в задание.

1. Добавьте следующий метод в класс Program, который добавляет три задачи в задание.

		static void AddTasks(BatchClient client)
		{
			CloudJob job = client.JobOperations.GetJob("testjob1");
			ResourceFile programFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/ProcessTaskData.exe",
				"ProcessTaskData.exe");
      	  ResourceFile assemblyFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/Microsoft.WindowsAzure.Storage.dll",
				"Microsoft.WindowsAzure.Storage.dll");
			for (int i = 1; i < 4; ++i)
			{
				string blobName = "taskdata" + i;
				string taskName = "mytask" + i;
				ResourceFile taskData = new ResourceFile("https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName, blobName);
				CloudTask task = new CloudTask(taskName, "ProcessTaskData.exe https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName + " 3");
				List<ResourceFile> taskFiles = new List<ResourceFile>();
				taskFiles.Add(taskData);
				taskFiles.Add(programFile);
				taskFiles.Add(assemblyFile);
				task.ResourceFiles = taskFiles;
				job.AddTask(task);
				job.Commit();
				job.Refresh();
			}

			client.Utilities.CreateTaskStateMonitor().WaitAll(job.ListTasks(),
        TaskState.Completed, new TimeSpan(0, 30, 0));
			Console.WriteLine("The tasks completed successfully.");
			foreach (CloudTask task in job.ListTasks())
			{
				Console.WriteLine("Task " + task.Id + " says:\n" + task.GetNodeFile(Constants.StandardOutFileName).ReadAsString());
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}


	Значение **[account-name]** необходимо заменить именем ранее созданной учетной записи хранения. В предыдущем примере замените все четыре экземпляра **[account-name]**.


2. Добавьте следующий код в Main, который вызывает только что добавленный метод.

			AddTasks(client);

3. Добавьте следующий метод в класс Program, который содержит список задач, связанных с заданием.

		static void ListTasks(BatchClient client)
		{
			IPagedEnumerable<CloudTask> tasks = client.JobOperations.ListTasks("testjob1");
			foreach (CloudTask task in tasks)
			{
				Console.WriteLine("Task id: " + task.Id);
				Console.WriteLine("   Task status: " + task.State);
			  Console.WriteLine("   Task start: " + task.ExecutionInformation.StartTime);
			}
			Console.ReadLine();
		}

4. Добавьте следующий код в Main, чтобы он вызывал только что добавленный метод.

		ListTasks(client);

5. Сохраните файл Program.cs.

## Шаг 5. Удаление ресурсов

Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не используются.

### Удаление задач

1.	Добавьте следующий метод в класс Program, который удаляет задачи.

			static void DeleteTasks(BatchClient client)
			{
				CloudJob job = client.JobOperations.GetJob("testjob1");
				foreach (CloudTask task in job.ListTasks())
				{
					task.Delete();
				}
				Console.WriteLine("All tasks deleted.");
				Console.ReadLine();
			}

2. Добавьте следующий код в Main, который вызывает только что добавленный метод.

		DeleteTasks(client);

3. Сохраните файл Program.cs.

### Удаление задания

1.	Добавьте следующий метод в класс Program, который удаляет задание.

			static void DeleteJob(BatchClient client)
			{
				client.JobOperations.DeleteJob("testjob1");
				Console.WriteLine("Job was deleted.");
				Console.ReadLine();
			}

2. Добавьте следующий код в Main, который вызывает только что добавленный метод.

		DeleteJob(client);

3. Сохраните файл Program.cs.

### Удаление пула

1. Добавьте следующий метод в класс Program, который удаляет пул:

		static void DeletePool (BatchClient client)
		{
			client.PoolOperations.DeletePool("testpool1");
			Console.WriteLine("Pool was deleted.");
			Console.ReadLine();
		}

2. Добавьте следующий код в Main, который вызывает только что добавленный метод.

		DeletePool(client);

3. Сохраните файл Program.cs.

## Шаг 6. Выполнение приложения

1. Запустите проект GettingStarted. После создания контейнера вы увидите в окне консоли следующее сообщение.

		Created the container. Press Enter to continue.

2. Нажмите клавишу ВВОД, чтобы создать и передать файлы. Вы увидите в окне новую строку.

		Uploaded the files. Press Enter to continue.

3. Нажмите клавишу ВВОД, чтобы создать пул.

		Created the pool. Press Enter to continue.

4. Нажмите клавишу ВВОД, и вы увидите новый пул в окне консоли.

		Pool name: testpool1
			Pool status: Active
		Press Enter to continue.

5. Нажмите клавишу ВВОД, чтобы создать задание.

		Created the job. Press Enter to continue.

6. Нажмите клавишу ВВОД, и вы увидите новое задание в окне консоли.

		Job id: testjob1
			Job status: Active
		Press Enter to continue.

7. Нажмите клавишу ВВОД, чтобы добавить задачи в задание. После добавления задач они запустятся автоматически.

		The tasks completed successfully.
		Task mytask1 says:
		can 3
		you 3
		and 3

		Task mytask2 says:
		and 5
		application 3
		the 3

		Task mytask3 says:
		a 5
		and 5
		to 3

		Press Enter to continue.

7. Нажмите клавишу ВВОД, чтобы увидеть список задач и их состояние.

		Task id: mytask1
			Task status: Completed
			Task start: 7/17/2015 8:31:58 PM
		Task id: mytask2
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM
		Task id: mytask3
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM

8. На этом этапе можно перейти на портал Azure, чтобы просмотреть созданные ресурсы. Чтобы удалить ресурсы, нажмите клавишу ВВОД до завершения работы программы.

## Дальнейшие действия

1. Вы получили основные сведения о выполнении задач и теперь можете перейти к ознакомлению с функцией автоматического масштабирования вычислительных узлов в соответствии с меняющимися потребностями вашего приложения в ресурсах. Для этого см. статью [Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure](batch-automatic-scaling.md).

2. Некоторые приложения создают большое количество данных, которые может быть трудно обработать. Одним из способов решения этой проблемы является использование [эффективных запросов на получение списка](batch-efficient-list-queries.md).

<!---HONumber=Nov15_HO3-->