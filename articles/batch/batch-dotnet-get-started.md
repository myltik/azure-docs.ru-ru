<properties
	pageTitle="Учебник — приступая к работе с библиотекой пакетной службы Azure для .NET"
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
	ms.date="07/21/2015"
	ms.author="yidingz"/>

# Приступая к работе с библиотекой Пакетной службы Azure для .NET  

В этом учебнике показано, как создать консольное приложение, которое устанавливает программы и файлы, работающие на нескольких вычислительных узлах в пуле пакетной службы Azure. Рассматриваемые в этом учебнике задачи анализируют текстовые данные из файлов, которые находятся в службе хранения Azure, и возвращают наиболее часто используемые слова. Примеры написаны на C# и используют библиотеку .NET пакетной службы Azure.

## Предварительные требования

- Учетные записи.

	- **Учетная запись Azure** — вы можете создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).

	- **Учетная запись пакетной службы** — см. раздел **Учетная запись пакетной службы** в [Техническом обзоре пакетной службы Azure](batch-technical-overview.md).

	- **Учетная запись хранения** — см. раздел **Создание учетной записи хранения** в статье [Учетные записи хранения Azure](../storage-create-storage-account.md). Следуя инструкциям этого учебника, вы создадите контейнер в этой учетной записи с именем **testcon1**.

- Проект консольного приложения Visual Studio.

	1.  В меню **Файл** Visual Studio последовательно щелкните элементы **Создать** и **Проект**.

	2.	В разделе **Windows** в подразделе **Visual C#** щелкните **Консольное приложение**, дайте проекту имя **GettingStarted**, назовите решение **AzureBatch** и нажмите кнопку **ОК**.

- Сборки NuGet.

	1. После создания проекта в Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**. Выполните поиск в Интернете, введя **Azure.Batch**, затем щелкните **Установить**, чтобы установить пакет пакетной службы Microsoft Azure и его зависимые компоненты.

	2. Выполните поиск в Интернете, введя **WindowsAzure.Storage**, а затем щелкните **Установить**, чтобы установить пакет хранилища Azure и зависимые компоненты.

## Шаг 1. Создание и отправка файлов поддержки

Для поддержки приложения в хранилище Azure создается контейнер и текстовые файлы, а затем эти текстовые файлы и файлы поддержки передаются в контейнер.

### Настройка строки подключения к хранилищу

1. Откройте файл App.config проекта GettingStarted, а затем добавьте элемент &lt;appSettings&gt; в &lt;configuration&gt;.

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
			<appSettings>
				<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[account-name];AccountKey=[account-key]"/>
			</appSettings>
		</configuration>

	Замените следующие значения.

	- **[account-name]** — имя ранее созданной учетной записи хранения.

	- **[account-key]** — первичный ключ учетной записи хранения. Первичный ключ находится странице хранилища на портале управления.

2. Сохраните файл App.config.

Дополнительные сведения см. в статье [Настройка строк подключения](http://msdn.microsoft.com/library/windowsazure/ee758697.aspx).

### Создание контейнера хранилища

1. Добавьте следующие объявления пространств имен в начало класса Program.cs в проекте GettingStarted.

		using System.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

2. Добавьте следующий метод к классу Program, который получает строку подключения к хранилищу, создает контейнер и задает разрешения.

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

3. Добавьте следующий код в Main, который вызывает только что добавленный метод.

		CreateStorage();

4. Сохраните файл Program.cs.

	> [AZURE.NOTE]В рабочей среде рекомендуется использовать подписанный URL-адрес.

Дополнительные сведения см. в статье [Как использовать хранилище больших двоичных объектов из .NET](../storage-dotnet-how-to-use-blobs.md).

### Создание программы обработки

1. В обозревателе решений создайте новый проект консольного приложения с именем **ProcessTaskData**.

2. Добавьте следующий код в Main, обрабатывающий текст из файлов.

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

3. Сохраните и создайте проект ProcessTaskData.

### Создание файлов данных

1. В проекте GettingStarted создайте новый текстовый файл с именем **taskdata1**, скопируйте в него следующий текст, а затем сохраните файл.

	Вы можете использовать виртуальные машины Azure для подготовки масштабируемой и гибкой вычислительной инфраструктуры для выполнения бизнес-задач. Из коллекции вы можете создать виртуальные машины, работающие под управлением Windows, Linux и корпоративных приложений, таких как SharePoint и SQL Server. Вы также можете записать и использовать собственные образы для создания настраиваемых виртуальных машин.

2. Создайте новый текстовый файл с именем **taskdata2**, скопируйте в него следующий текст, а затем сохраните файл.

	Быстро развертывайте мощные приложения и службы и управляйте ими с помощью облачных служб Azure. Просто загрузите свое приложение, а Azure автоматически осуществит развертывание, выполнив все нужные действия, от подготовки и балансировки нагрузки до мониторинга работоспособности для обеспечения непрерывной доступности. Соглашение об уровне обслуживания гарантирует степень доступность 99,95% для вашего приложения. Вы сможете сосредоточиться на приложении, не отвлекаясь на инфраструктуру.

3. Создайте новый текстовый файл с именем **taskdata3**, скопируйте в него следующий текст, а затем сохраните файл.

	Веб-сайты Azure предоставляют масштабируемую, надежную и удобную среду для размещения веб-приложений. Выбирайте платформы и шаблоны, чтобы создавать веб-сайты за несколько секунд. Используйте любое средство или ОС для разработки веб-сайта с помощью .NET, PHP, Node.js или Python. Используйте широкий выбор систем управления версиями, включая TFS, GitHub и BitBucket, для настройки непрерывной интеграции и разработки в группе. Расширяйте функциональные возможности вашего сайта с помощью дополнительных управляемых служб Azure, таких как хранилище, CDN и база данных SQL.

### Передача файлов в контейнер

1. Откройте файл Program.cs проекта GettingStarted и добавьте следующий метод, который передает файлы.

		static void CreateFiles()
		{
		  privateCloudStorageAccount storageAccount = CloudStorageAccount.Parse(
			ConfigurationManager.AppSettings["StorageConnectionString"]);
		  CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		  CloudBlobContainer container = blobClient.GetContainerReference("testcon1");
		  CloudBlockBlob taskData1 = container.GetBlockBlobReference("taskdata1");
		  CloudBlockBlob taskData2 = container.GetBlockBlobReference("taskdata2");
		  CloudBlockBlob taskData3 = container.GetBlockBlobReference("taskdata3");
	  	CloudBlockBlob dataprocessor = container.GetBlockBlobReference("ProcessTaskData.exe");
	  	CloudBlockBlob storageassembly =
			container.GetBlockBlobReference("Microsoft.WindowsAzure.Storage.dll");
		  taskData1.UploadFromFile("..\..\taskdata1.txt", FileMode.Open);
		  taskData2.UploadFromFile("..\..\taskdata2.txt", FileMode.Open);
	  	taskData3.UploadFromFile("..\..\taskdata3.txt", FileMode.Open);
		  dataprocessor.UploadFromFile("..\..\..\ProcessTaskData\bin\debug\ProcessTaskData.exe", FileMode.Open);
		  storageassembly.UploadFromFile("Microsoft.WindowsAzure.Storage.dll", FileMode.Open);
		  Console.WriteLine("Uploaded the files. Press Enter to continue.");
		  Console.ReadLine();
		}

2. Сохраните файл Program.cs.

## Шаг 2. Добавление пула в учетную запись

Пул вычислительных узлов — это первый набор ресурсов, который необходимо создать для выполнения задач.

1.	Добавьте следующие объявления пространств имен в начало класса Program.cs в проекте GettingStarted.

			using Microsoft.Azure.Batch;
			using Microsoft.Azure.Batch.Auth;
			using Microsoft.Azure.Batch.Common;

2. Добавьте следующий код в Main, который настраивает учетные данные для вызова пакетной службы Azure.

			BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials("https://[account-name].[region].batch.azure.com", "[account-name]", "[account-key]");
			BatchClient client = BatchClient.Open(cred);

	Замените следующие значения.

	- **[account-name]** — именем ранее созданной учетной записи пакетной службы.
	- **[region]** — регионом, в котором находится ваша учетная запись. См. обзор [Регионы Azure](http://azure.microsoft.com/regions/), чтобы найти доступные регионы.
	- **[account-key]** — первичным ключом учетной записи пакетной службы.

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

## Шаг 2. Добавление задания в учетную запись

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
			Console.WriteLine("Press Enter to continue.")
			Console.ReadLine();
		}

4. Добавьте следующий код в Main, который вызывает только что добавленный метод.

		ListJobs(client);

5. Сохраните файл Program.cs.

## Шаг 3. Добавление задач в задание

В созданное задание можно добавлять задачи. Каждая задача выполняется на вычислительном узле и обрабатывает текстовый файл. Следуя инструкциям этого учебника, вы добавите три задачи в задание.

1. Добавьте следующий метод в класс Program, который добавляет три задачи в задание.

		static void AddTasks(BatchClient client)
		{
			CloudJob job = client.JobOperations.GetJob("testjob1");
			ResourceFile programFile = new ResourceFile(
				"https://[account-name].blob.azure.com/[]/ProcessTaskData.exe",
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
			Console.WriteLine("Press Enter to continue.")
			Console.ReadLine();
		}

	Значение **[account-name]** необходимо заменить именем ранее созданной учетной записи хранения. Убедитесь, что вы заменили все четыре значения.

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

## Шаг 4. Удаление ресурсов

Поскольку за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не используются.

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
				client.JobOperations.DeleteJob("davidmujob1");
				Console.WriteLine("Job was deleted.");
				Console.ReadLine();
			}

2. Добавьте следующий код в Main, который запускает только что добавленный метод.

		DeleteJob(client);

3. Сохраните файл Program.cs.

### Удаление пула

1. Добавьте следующий метод в класс Program, который удаляет пул.

		static void DeletePool (BatchClient client)
		{
			client.PoolOperations.DeletePool("davidmupl1");
			Console.WriteLine("Pool was deleted.");
			Console.ReadLine();
		}

2. Добавьте следующий код в Main, который запускает только что добавленный метод.

		DeletePool(client);

3. Сохраните файл Program.cs.

## Шаг 5. Выполнение приложения

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

1. Вы получили основные сведения о выполнении задач и теперь можете перейти к ознакомлению с функцией автоматического масштабирования вычислительных узлов в соответствии с меняющимися потребностями вашего приложения в ресурсах. Для этого см. статью [Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure](batch-automatic-scaling.md)

2. Некоторые приложения создают большое количество данных, которые может быть трудно обработать. Одним из способов устранения этой проблемы является использование [эффективных списков запросов](batch-efficient-list-queries.md).

<!---HONumber=July15_HO4-->