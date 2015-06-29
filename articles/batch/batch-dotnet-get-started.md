<properties
	pageTitle="Учебник «Приступая к работе с библиотекой Пакетной службы Azure для .NET»"
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
	ms.date="05/04/2015"
	ms.author="yidingz"/>

#Приступая к работе с библиотекой Пакетной службы Azure для .NET  

Эта статья содержит два учебника, которые помогут приступить к разработке с использованием библиотеки .NET для Пакетной службы Azure.

-	[Учебник 1. Библиотека пакетной службы Azure для .NET](#tutorial1)
-	[Учебник 2. Библиотека приложений пакетной службы Azure для .NET](#tutorial2)  


Общие сведения и сценарии для пакетной службы Azure см. в статье [Технический обзор пакетной службы Azure](batch-technical-overview.md).

##<a name="tutorial1"></a>Учебник 1. Библиотека пакетной службы Azure для .NET

В этом учебнике рассказывается о том, как создать консольное приложение для настройки распределенных вычислений для пула виртуальных машин с помощью Пакетной службы Azure. Рассматриваемые в этом учебнике задачи анализируют текстовые данные из файлов, которые находятся в службе хранения Azure, и возвращают наиболее часто используемые слова. Примеры написаны на C# и используют библиотеку Пакетной службы Azure для .NET.


>[AZURE.NOTE]Для работы с этим учебником требуется учетная запись Azure. Вы можете создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).
>
>Для получения сборки **Microsoft.Azure.Batch.dll** необходимо использовать NuGet. После создания проекта в Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**. Выполните поиск **Azure.Batch** в Интернете, затем нажмите "Установить", чтобы установить пакет пакетной службы Azure и его зависимости.
>
>Убедитесь, что используется диспетчер пакетов Nuget версии 2.8 или более поздней. Номер версии можно найти в диалоговом окне Visual Studio, выбрав «Справка -> О Microsoft Visual Studio». Если у вас более старая версия диспетчера пакетов Nuget, нужно обновить Visual Studio, в противном случае могут возникнуть проблемы с загрузкой необходимой версии зависимостей Nuget.
>
>Кроме того, вы можете просмотреть [пример пакетной службы Azure Hello World](https://code.msdn.microsoft.com/Azure-Batch-Sample-Hello-6573967c) на портале MSDN, который содержит код по теме этого учебника.



###Основные понятия
Пакетная служба используется для планирования масштабируемых и распределенных вычислений. Она дает возможность выполнять крупномасштабные рабочие нагрузки, которые распределяются между несколькими виртуальными машинами. Данные рабочие нагрузки позволяют эффективно выполнять большие объемы вычислений. При использовании Пакетной службы можно воспользоваться следующими ресурсами:

-	**Учетная запись** — это уникально идентифицируемая сущность в данной службе. Вся обработка данных осуществляется через учетную запись Пакетной службы.
-	**Пул** — это коллекция виртуальных машин задач, на которых выполняются приложения задач.
-	**Виртуальная машина задач** — это назначенная в пул машина, которая используется для выполнения задач из заданий пула.
-	**Пользователь виртуальной машины задач** — это учетная запись пользователя на виртуальной машине задач.
-	**Рабочий элемент** — указывает, как следует выполнять вычисления на виртуальных машинах задач в пуле.
-	**Задание** — это запущенный экземпляр рабочего элемента, который представляет собой набор задач.
-	**Задача** — это приложение, которое связано с заданием и выполняется на виртуальной машине задач.
-	**Файл** — содержит информацию, которая обрабатывается задачей.  

Давайте начнем с наиболее общих сценариев использования.

###Создание учетной записи Пакетной службы Azure
Для создания учетной записи Пакетной службы можно использовать портал управления. После создания учетной записи вам будет предоставлен ключ. Дополнительные сведения см. в статье [Технический обзор Пакетной службы Azure](batch-technical-overview.md).

###Практическое руководство. Добавление пула к учетной записи
Пул виртуальных машин задач — это первый набор ресурсов, которые необходимо создать для выполнения задач.

1.	Откройте Microsoft Visual Studio 2013, в меню **Файл** выберите пункт **Создать** и затем выберите **Проект**.

2.	В разделе **Windows** в подразделе **Visual C#** щелкните **Консольное приложение**, дайте проекту имя **GettingStarted**, назовите решение **AzureBatch** и нажмите кнопку **ОК**.

3.	Добавьте следующие объявления пространств имен в начало Program.cs:

		using Microsoft.Azure.Batch;
		using Microsoft.Azure.Batch.Auth;
		using Microsoft.Azure.Batch.Common;

	Убедитесь, что существует ссылка на сборку Microsoft.WindowsAzure.Storage.dll.

4.	Добавьте в класс Program следующие переменные:

		private const string PoolName = "[name-of-pool]";
		private const int NumOfMachines = 3;
		private const string AccountName = "[name-of-batch-account]";
		private const string AccountKey = "[key-of-batch-account]";
		private const string Uri = "https://batch.core.windows.net";
	Замените следующие значения: - **[name-of-pool]** - имя, которое следует использовать для пула. - **[name-of-batch-account]** - имя учетной записи Пакетной службы. - **[key-of-batch-account]** - ключ, который был предоставлен вам для учетной записи Пакетной службы.
5.	Добавьте следующий код в метод Main, чтобы определить используемые учетные данные:

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
6.	Добавьте следующий код в метод Main, чтобы создать клиент, который будет использоваться для выполнения операций:

		IBatchClient client = BatchClient.Connect(Uri, cred);
7.	Добавьте следующий код в метод Main, чтобы создать пул, если он не существует:

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   IEnumerable<ICloudPool> pools = pm.ListPools();
		   if (!pools.Select(pool => pool.Name).Contains(PoolName))
		   {
		      Console.WriteLine("The pool does not exist, creating now...");
		      ICloudPool newPool = pm.CreatePool(
		         PoolName,
		         osFamily: "3",
		         vmSize: "small",
		         targetDedicated: NumOfMachines);
		       newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}", PoolName);
		Console.ReadLine();
8.	Сохраните и выполните программу. Если пул был добавлен успешно, он будет иметь состояние **Активный**.  

###Практическое руководство. Получение списка пулов в учетной записи
Если вы не знаете имя существующего пула, можно получить список пулов в учетной записи.

1.	Добавьте следующий код в метод Main, чтобы определить используемые учетные данные:  

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
2.	Добавьте следующий код в метод Main, чтобы создать клиент, который будет использоваться для выполнения операций:

		IBatchClient client = BatchClient.Connect(Uri, cred);

3.	Добавьте в процедуру Main следующий код, который записывает имена и состояния всех пулов учетной записи, а также создает пул, если его не существует.

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
		IBatchClient client = BatchClient.Connect(Uri, cred);

		using (IPoolManager pm = client.OpenPoolManager())
		{
		    IEnumerable<ICloudPool> pools = pm.ListPools();

		    Console.WriteLine("Listing Pools\n=================");
		    foreach (var p in pools)
		    {
		        Console.WriteLine("Pool: " + p.Name + " State:" + p.State);
		    }  

		    if (!pools.Select(pool => pool.Name).Contains(PoolName))
		    {
		        Console.WriteLine("The pool does not exist, creating now...");
		        ICloudPool newPool = pm.CreatePool(
		           PoolName,
		           osFamily: "3",
		           vmSize: "small",
		           targetDedicated: NumOfMachines);
		        newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}. Press <Enter> to continue.", PoolName);
		Console.ReadLine();

4.	Сохраните и выполните программу. Вы увидите следующие выходные данные.

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

###Практическое руководство. Получение списка рабочих элементов в учетной записи
Если вы не знаете имя существующего рабочего элемента, можно получить их список в учетной записи.

1.	Добавьте следующий код в конец метода Main, который запишет имена и состояния всех рабочих элементов в учетной записи:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   Console.WriteLine("Listing Workitems\n=================");
		   IEnumerable<ICloudWorkItem> workitems = wm.ListWorkItems();
		   foreach (var w in workitems)
		   {
		      Console.WriteLine("Workitem: " + w.Name + " State:" + w.State);
		   }
		}
		Console.WriteLine("Press <Enter> to continue.");
		Console.ReadLine();
7.	Сохраните и выполните программу. Вероятно, вы ничего не увидите, так как мы еще не отправили ни одного рабочего элемента. Добавление рабочих элементов мы рассмотрим в следующем разделе.  

##Практическое руководство. Добавление рабочего элемента в учетную запись
Чтобы определить способ выполнения задач в пуле, необходимо создать рабочий элемент.

1.	Добавьте в класс Program следующие переменные:

		private static readonly string WorkItemName = Environment.GetEnvironmentVariable("USERNAME") + DateTime.Now.ToString("yyyyMMdd-HHmmss");

2.	При создании рабочего элемента также формируется задание. Вы можете назначить имя рабочему элементу, но заданию всегда будет назначено имя **job-0000000001**. Добавьте следующий код в Main (перед кодом списка рабочих элементов), который добавит рабочий элемент:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   ICloudWorkItem cloudWorkItem = wm.CreateWorkItem(WorkItemName);
           cloudWorkItem.JobExecutionEnvironment = new JobExecutionEnvironment() {PoolName = PoolName};
		   cloudWorkItem.Commit();
		}
		Console.WriteLine("Workitem successfully added. Press <Enter> to continue.");
		Console.ReadLine();
3.	Сохраните и выполните программу. Если рабочий элемент был добавлен успешно, он будет иметь состояние **Активный**. Вы должны увидеть следующие выходные данные.

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

		Workitem successfully added. Press <Enter> to continue.

		Listing Workitems
		=================
		Workitem: yidingz20141106-111211 State:Active
		Press <Enter> to continue.

###Практическое руководство. Добавление задач в задание
Рабочий элемент без задачи не будет ничего делать. После создания рабочего элемента и задания нужно добавить задачи для задания. Давайте добавим в задание простую задачу.

1.	Добавьте в класс Program следующие переменные:

		private const int Count = 4; // number of tasks that will run
		private const string JobName = "job-0000000001";

2.	Добавьте следующий код в метод Main, который добавит задачи в задание, дождется завершения задач и сообщит о результатах:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		    string taskName;
		    ICloudJob job = wm.GetJob(WorkItemName, JobName);
		    for (int i = 1; i <= Count; ++i)
		    {
		        taskName = "taskdata" + i;
		        string commandLine = String.Format("cmd /c echo I am {0}", taskName);
		        ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		        job.AddTask(taskToAdd);
		        job.Commit();
		        job.Refresh();
		    }

		    ICloudJob listjob = wm.GetJob(WorkItemName, JobName);
		    client.OpenToolbox().CreateTaskStateMonitor().WaitAll(listjob.ListTasks(),
		       TaskState.Completed, new TimeSpan(0, 30, 0));
		    Console.WriteLine("The tasks completed successfully. Terminating the workitem...");
		    wm.GetWorkItem(WorkItemName).Terminate();
		    foreach (ICloudTask task in listjob.ListTasks())
		    {
		        Console.WriteLine("Task " + task.Name + " says:\n" + task.GetTaskFile(Constants.StandardOutFileName).ReadAsString());
		    }
		    Console.ReadLine();
		}

3.	Сохраните и выполните программу. Результат должен выглядеть следующим образом:

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		I am taskdata1

		Task taskdata2 says:
		I am taskdata2

		Task taskdata3 says:
		I am taskdata3

###Создание программы обработки задач
Теперь, когда мы можем запустить на виртуальной машине нашу программу «Hello, World!», давайте сделаем что-нибудь более полезное. В этом разделе мы создадим программу обработки задач и передадим ее в виртуальную машину задач для выполнения.

1.	В обозревателе решений создайте новый проект консольного приложения с именем **ProcessTaskData** в решении **AzureBatch**.

2.	Добавьте следующие объявления пространств имен в начало Program.cs:

		using System.Net;

3.	Добавьте следующий код в метод Main, который будет использоваться для обработки данных:

		string blobName = args[0];
		int numTopN = int.Parse(args[1]);

		WebClient myWebClient = new WebClient();
		string content = myWebClient.DownloadString(blobName);

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

4.	Сохраните проект и выполните его сборку.

###Подготовка ресурсов для выполнения задачи

При создании сценария распределенных вычислений с помощью Пакетной службы используется следующий базовый рабочий процесс:

1.	Передайте файлы, которые вы хотите использовать в сценарии распределенных вычислений, в учетную запись хранения Azure. Чтобы Пакетная служба имела к ним доступ, они должны находиться в учетной записи хранения. Файлы загружаются на виртуальную машину во время выполнения задачи.
2.	Передайте зависимые двоичные файлы в учетную запись хранения. Двоичные файлы включают в себя программу, которая выполняется задачей, и зависимые сборки. Эти файлы также должны быть доступны из службы хранения и загружены на виртуальную машину задач.
3.	Создайте пул виртуальных машин задач. При создании пула можно указать размер виртуальной машины задач. При запуске задачи ей будет назначена виртуальная машина из этого пула.
4.	Создайте рабочий элемент. Рабочий элемент позволяет управлять заданием, которое содержит задачи. Задание создается автоматически при создании рабочего элемента.
5.	Добавьте задачи в задание. Каждая задача использует переданную вами программу для обработки информации из переданного вами файла.
6.	Отслеживайте выходные данные.  

Мы показали шаги с 3 по 6. Теперь давайте посмотрим, как подготовить службу хранилища Azure для выполнения задачи.

####Получение учетной записи хранения Azure
Чтобы успешно завершить оставшуюся часть данного учебника, вам потребуется учетная запись службы хранения. Если вы не знаете, как ее создать, см. раздел [Создание учетной записи хранения Azure](#tutorial1_storage).

####Передача данных

1. Создайте в вашей учетной записи хранения Azure контейнер с названием gettingstarted. Это можно сделать на портале Azure. Убедитесь, что для поля "ДОСТУП" вы выбрали значение "Общедоступный контейнер".

2. Передайте файл ProcessTaskData.exe в контейнер.

3. Создайте три текстовых файла (taskdata1.txt, taskdata2.txt и taskdata3.txt). Каждый файл должен содержать один из указанных абзацев текста. После этого передайте файлы в контейнер:

		You can use Azure Virtual Machines to provision on-demand, scalable compute infrastructure when you need flexible resources for your business needs. From the gallery, you can create virtual machines that run Windows, Linux, and enterprise applications such as SharePoint and SQL Server. Or, you can capture and use your own images to create customized virtual machines.

		Quickly deploy and manage powerful applications and services with Azure Cloud Services. Simply upload your application and Azure handles the deployment details - from provisioning and load balancing to health monitoring for continuous availability. Your application is backed by an industry leading 99.95% monthly SLA. You just focus on the application and not the infrastructure.

		Azure Web Sites provide a scalable, reliable, and easy-to-use environment for hosting web applications. Select from a range of frameworks and templates to create a web site in seconds. Use any tool or OS to develop your site with .NET, PHP, Node.js or Python. Choose from a variety of source control options including TFS, GitHub, and BitBucket to set up continuous integration and develop as a team. Expand your site functionality over time by leveraging additional Azure managed services like storage, CDN, and SQL Database.


>[AZURE.NOTE]В рабочей среде рекомендуется использовать подписанный URL-адрес.


>[AZURE.NOTE]Команда разработчиков службы хранилища Azure написала [статью в блоге](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) со списком обозревателей службы хранилища Azure для отправки файлов.



###Обновление кода отправки задачи

1.	Добавьте в класс Program следующие переменные:

		private const string BlobPath = "[storage-path]";
	Замените следующие значения: - **[storage-path]** - путь к большому двоичному объекту в хранилище. Например: http://yiding.blob.core.windows.net/gettingstarted/

2. Обновите код отправки задачи следующим образом.

		string taskName;
		ICloudJob job = wm.GetJob(WorkItemName, JobName);
		for (int i = 1; i <= Count; ++i)
		{
		    taskName = "taskdata" + i;
		    IResourceFile programFile = new ResourceFile(BlobPath + "ProcessTaskData.exe", "ProcessTaskData.exe");
		    IResourceFile supportFile = new ResourceFile(BlobPath + taskName + ".txt", taskName);
		    string commandLine = String.Format("ProcessTaskData.exe {0} {1}", BlobPath + taskName + ".txt", 3);
		    ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		    taskToAdd.ResourceFiles = new List<IResourceFile>();
		    taskToAdd.ResourceFiles.Add(programFile);
		    taskToAdd.ResourceFiles.Add(supportFile);
		    job.AddTask(taskToAdd);
		}
		job.Commit();

3. Сохраните и выполните программу. Вы должны увидеть следующее:

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

		Workitem successfully added. Press <Enter> to continue.

		Listing Workitems
		=================
		Workitem: yidingz20141106-132140 State:Active
		Press <Enter> to continue.

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		can 3
		you 3
		and 3

		Task taskdata2 says:
		and 5
		application 3
		the 3

		Task taskdata3 says:
		a 5
		and 5
		to 3

###Практическое руководство. Удаление пула и рабочего элемента
После завершения обработки рабочего элемента можно освободить ресурсы, удалив пул и рабочий элемент.

####Удаление пула
1.	Добавьте следующий код в конец метода Main, который удалит пул:

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   pm.DeletePool(PoolName);
		}
		Console.WriteLine("Pool successfully deleted.");
		Console.ReadLine();
2.	Сохраните и выполните программу.


####Удаление рабочего элемента
1.	Добавьте следующий код в метод Main, который удалит рабочий элемент:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   wm.DeleteWorkItem(WorkItemName);
		}
		Console.WriteLine("Workitem successfully deleted.");
		Console.ReadLine();
2.	Сохраните и выполните программу.

###<a name="tutorial1_storage"></a>ПРИЛОЖЕНИЕ. Создание учетной записи хранения Azure
Перед тем как выполнить код из этого учебника, убедитесь, что у вас есть доступ к учетной записи хранения Azure.

1.	Выполните вход на [портал управления Azure](http://manage.windowsazure.com/).
2.	В нижней части области навигации щелкните **СОЗДАТЬ**. ![][1]
3.	Последовательно выберите пункты **СЛУЖБЫ ДАННЫХ**, **ХРАНИЛИЩЕ** и **БЫСТРОЕ СОЗДАНИЕ**. ![][2]

4.	В поле **URL-адрес** введите имя поддомена, который будет использоваться в URI для учетной записи хранения. Записи могут содержать от 3 до 24 строчных букв и цифр. Это значение станет именем узла в URI, который используется для адресации ресурсов большого двоичного объекта, очереди и таблицы в подписке.
5.	В поле **РАСПОЛОЖЕНИЕ ИЛИ ТЕРРИТОРИАЛЬНАЯ ГРУППА** выберите, где требуется расположить хранилище.
6.	При необходимости можно включить георепликацию.
7.	Щелкните **СОЗДАТЬ УЧЕТНУЮ ЗАПИСЬ ХРАНЕНИЯ**.  

Дополнительные сведения о службе хранилища Azure см. в статье [Использование службы хранилища больших двоичных объектов Azure в .NET](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/).


##<a name="tutorial2"></a>Учебник 2. Библиотека приложений пакетной службы Azure для .NET
В этом учебнике будет рассказано, как выполнять параллельные вычисления рабочих нагрузок в Пакетной службе Azure с помощью приложений Пакетной службы.

Приложения Пакетной службы — одна из функций Пакетной службы Azure, которая дает возможность управлять рабочими нагрузками Пакетной службы и выполнять их на уровне приложения. С помощью пакета SDK для приложений Пакетной службы можно создать приложения Пакетной службы, а также развернуть их в собственной учетной записи или же сделать их доступными для других пользователей Пакетной службы. Пакетная служба также дает возможность управлять данными, осуществлять мониторинг заданий, предоставляет встроенные инструменты диагностики и ведения журнала, а также обеспечивает поддержку внутренних зависимостей задач. Кроме того, она включает в себя портал управления, с помощью которого вы можете управлять заданиями, просматривать журналы и загружать выходные данные без необходимости разработки собственного клиентского приложения.

В сценарии приложений Пакетной службы необходимо написать код с использованием пакета SDK для облачных приложений Пакетной службы, чтобы разделять задания на параллельные задачи, описать зависимости между задачами и указать способ выполнения каждой задачи. Этот код развертывается в учетной записи Пакетной службы. После этого клиенты смогут выполнять задания, указывая тип задания и входные файлы в интерфейсе REST API.

>[AZURE.NOTE]Для работы с этим учебником требуется учетная запись Azure. Вы можете создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/). NuGet можно использовать для получения сборок <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">облачных приложений пакетной службы</a> и <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">клиента приложений пакетной службы</a>. После создания проекта в Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**. Вы также можете скачать расширение Visual Studio для приложений пакетной службы, которое включает шаблон проекта для приложений с поддержкой облака и возможность развернуть приложение <a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">здесь</a> или через поиск **приложений пакетной службы** в Visual Studio с помощью пункта меню "Расширения и обновления". Вы также можете найти <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">полные примеры на портале MSDN.</a>
>

###Основные положения приложений Пакетной службы Azure
Пакетная служба предназначена для работы с существующими приложениями, которые разработаны для крупномасштабных вычислений. Она использует существующий код приложения и запускает его в динамической виртуализированной среде общего назначения. Вот что нужно сделать, чтобы приложение стало совместимым со приложениями Пакетной службы.

1.	Подготовьте ZIP-файл с исполняемыми файлами существующего приложения (это те же исполняемые файлы, которые могут выполняться на традиционной ферме серверов или кластерах) и всеми необходимыми файлами. Затем отправьте этот ZIP-файл в вашу учетную запись Пакетной службы с помощью портала управления или REST API.
2.	Создайте ZIP-файл "облачных сборок", которые распределяют рабочую нагрузку для приложения, и отправьте его с помощью портала управления или REST API. Облачная сборка состоит из двух компонентов, которые собираются с помощью пакета SDK для облачных приложений:
	1.	Разделитель заданий разбивает задание на задачи, которые могут быть обработаны независимо друг от друга. Например, в сценарии анимации разделитель заданий будет принимать в качестве задания фильм и разбивать его на отдельные кадры.
	2.	Обработчик задач вызывает исполняемый файл приложения для заданной задачи. Например, в сценарии анимации обработчик задач будет вызывать программу визуализации для отрисовки отдельного, указанного задачей кадра.
3.	Предоставьте способ отправки заданий приложению в Azure. Это может быть подключаемый модуль пользовательского интерфейса приложения, веб-портал или же автоматическая служба, входящая в ваш конвейер выполнения. См. <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">примеры</a> на портале MSDN.



###Основные понятия приложений Пакетной службы
Модель программирования и использования приложений Пакетной службы основывается на следующих ключевых понятиях:

####Задания
**Задание** — это элемент работы, отправленный пользователем. При отправке задания пользователь указывает его тип, параметры задания и необходимые данные. Для действующей реализации пользователь может не указать данную информацию либо указать ее явно с помощью клиента. После завершения задание возвращает результаты. Каждое задание содержит основные выходные данные и необязательные данные предварительного просмотра. При необходимости задания могут возвращать дополнительные выходные данные.

####Задачи
**Задача** — это элемент работы, выполняемый в рамках задания. Когда пользователь отправляет задание, оно разбивается на более мелкие задачи. Пакетная служба обрабатывает отдельные задачи, а затем собирает результаты и предоставляет их в виде общих выходных данных задания. Характер задачи зависит от типа задания. Основываясь на знаниях о блоках работы, которые приложение должно обрабатывать, разделитель заданий определяет, каким образом задание будет разбито на задачи. Можно скачать выходные данные отдельных задач, и в некоторых случаях это может быть полезным. Например, пользователю может потребоваться скачать отдельные задачи из задания анимации.

####Задачи слияния
**Задача слияния** — это особый тип задачи, которая объединяет результаты выполнения отдельных задач в окончательные результаты задания. Для задания визуализации фильма задача слияния может собирать отрисованные кадры в фильм или ZIP-файл готовых кадров. Каждое задание содержит задачу слияния, даже если в слиянии нет необходимости.

####Файлы
**Файл** — это элемент данных, который используется в качестве входных данных для задания. Задание может содержать как один или несколько входных файлов, так и не иметь их вовсе. Один и тот же файл можно использовать в нескольких заданиях: например, для задания отрисовки фильма эти файлы могут быть текстурами, моделями и т. д. Для задания анализа данных файлы могут содержать набор наблюдений или измерений.

###Реализация облачного приложения
Ваше приложение должно содержать статическое поле или свойство, содержащее всю информацию о приложении. Оно определяет имя приложения и тип задания (или типы заданий, которые обрабатываются приложением). Для этого можно использовать шаблон из пакета SDK, который можно загрузить с помощью коллекции Visual Studio.

Ниже приведен пример объявления облачного приложения для параллельной рабочей нагрузки:

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####Реализация разделителя заданий и обработчика задач
Для параллельной обработки рабочих нагрузок необходимо реализовать разделитель заданий и обработчик задач.

####Реализация JobSplitter.SplitIntoTasks
Реализация SplitIntoTasks должна возвращать последовательность задач. Каждая задача представляет собой отдельную часть работы, которая помещается в очередь для обработки на вычислительном узле. Каждая задача должна быть автономной и содержать всю необходимую информацию, которая может потребоваться обработчику задач.

Задачи, указанные разделителем заданий, представлены в виде объектов TaskSpecifier. TaskSpecifier содержит ряд свойств, которые вы можете установить перед возвратом задачи.


-	Свойство TaskIndex не указывается, однако остается доступным для обработчика задач. Его можно использовать для передачи индекса в обработчик задач. Если передавать индекс не требуется, определять это свойство не нужно.
-	Параметры – это пустая коллекция по умолчанию. В нее можно добавить новые элементы или заменить ее новой коллекцией. Записи из коллекции параметров задания можно копировать с помощью метода WithJobParameters или WithAllJobParameters.  


RequiredFiles — это пустая коллекция по умолчанию. В нее можно добавить новые элементы или заменить ее новой коллекцией. Записи из коллекции файлов задания вы можете копировать с помощью метода RequiringJobFiles или RequiringAllJobFiles.

Вы можете указать задачу, которая зависит от какой-либо другой задачи. Для этого определите свойство TaskSpecifier.DependsOn, передав идентификатор задачи, от которой она зависит.

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

Задача не начнет выполняться до тех пор, пока не станут доступны выходные данные задачи, от которой она зависит.

Также вы можете указать, что обработка всей группы задач не должна начинаться до полного завершения задач другой группы. Для этого можно определить свойство TaskSpecifier.Stage. Задачи с определенным значением свойства Stage не будут переданы на обработку, пока не завершены все задачи с меньшими значениями свойства Stage. Например, задачи, у которых свойству Stage присвоено значение 3, не начнут обрабатываться, пока не будут завершены все задачи, у которых свойство Stage имеет значение 0, 1 или 2. Значения свойства Stage должны начинаться с 0, последовательность этапов не должна иметь разрывов, а SplitIntoTasks должен возвращать задачи в порядке номеров этапов. Например, будет считаться ошибкой возвращение задачи со значением свойства Stage 0 после задачи со Stage 1.

Каждое задание с параллельной обработкой завершается специальной задачей слияния. Задача слияния объединяет результаты задач параллельной обработки в итоговый результат. Приложения Пакетной службы автоматически создадут задачу слияния.

В редких случаях может потребоваться явно управлять задачей слияния. Например, чтобы настроить ее параметры. Чтобы указать задачу слияния, нужно вернуть набор свойств TaskSpecifier, в котором свойство IsMerge имеет значение true. Таким образом автоматически созданная задача слияния будет переопределена. Если вы явно создаете задачу слияния:

-	Можно создать только одну явную задачу слияния.
-	Она должна быть последней задачей в последовательности.
-	Она должна содержать свойство IsMerge со значением true, а все другие задачи должны содержать свойство IsMerge со значением false.  


Однако помните, что в общем случае не требуется создавать задачу слияния явно.

В следующем примере кода показана простая реализация метода SplitIntoTasks.

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;

	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####Реализация ParallelTaskProcessor.RunExternalTaskProcess

RunExternalTaskProcess вызывается для всех задач без слияния, которые были возвращены разделителем заданий. Данный метод вызовет ваше приложение с соответствующими аргументами и вернет коллекцию выходных данных, которые должны быть сохранены для последующего использования.

Следующий фрагмент показывает, как вызвать программу под названием application.exe. Обратите внимание, что вы можете использовать вспомогательный метод ExecutablePath для создания абсолютного пути к файлам.

Класс ExternalProcess из пакета SDK для облачных приложений предоставляет полезные вспомогательные методы для исполняемых объектов вашего приложения. ExternalProcess может позаботиться об отмене действий, преобразовании кодов выхода в исключения, захвате стандартных потоков вывода и ошибок, а также обеспечить настройку переменных среды. Для непосредственного запуска программ вы можете дополнительно использовать класс .NET Process.

Метод RunExternalTaskProcess возвращает TaskProcessResult, который включает в себя список выходных файлов. К ним относятся как минимум все файлы, необходимые для слияния. Также можно возвращать файлы журнала, файлы предварительного просмотра и промежуточные файлы (например, для диагностики, если задача завершена с ошибкой). Обратите внимание, что метод возвращает пути к файлам, а не их содержание.

Каждый файл должен быть идентифицирован по типу вывода, который в нем содержится: вывод (то есть часть возможного вывода задания), предварительный просмотр, журнал или промежуточный. Эти значения берутся из перечисления TaskOutputFileKind. Следующий фрагмент возвращает выходные данные одной задачи без данных предварительного просмотра или данных журнала. Метод TaskProcessResult.FromExternalProcessResult упрощает распространенный сценарий записи кода завершения, выходных данных обработчика и выходных файлов программы командной строки:

В следующем примере кода демонстрируется простая реализация ParallelTaskProcessor.RunExternalTaskProcess.

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");

	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);

	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();

	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####Реализация ParallelTaskProcessor.RunExternalMergeProcess

Этот метод вызывается задачей слияния. Он вызывает приложение для объединения выходных данных предыдущих задач, которые соответствуют вашему приложению, и возвращает итоговый результат.

Реализация RunExternalMergeProcess очень похожа на реализацию RunExternalTaskProcess за небольшим исключением:

-	RunExternalMergeProcess использует выходные данные предыдущих задач, а не входные файлы пользователя. Следовательно, вам нужно определять имена обрабатываемых вами файлов на основании идентификатора задачи, а не коллекции Task.RequiredFiles.
-	RunExternalMergeProcess возвращает файл JobOutput и, при необходимости, файл JobPreview.


В следующем коде демонстрируется простая реализация ParallelTaskProcessor.RunExternalMergeProcess.

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";

	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);

	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();

	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###Отправка заданий в приложения Пакетной службы
Задание описывает рабочую нагрузку и должно включать в себя всю необходимую информацию о ней, за исключением содержимого файлов. Например, задание содержит параметры конфигурации, которые передаются от клиента к задаче через разделитель заданий. В MSDN приводятся примеры отправки заданий и работы с несколькими клиентами, включая веб-порталы и клиенты командной строки.


<!--Anchors-->


<!--Image references-->
[1]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-01.jpg
[2]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-02.jpg
[3]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-03.jpg
[4]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-04.jpg

<!---HONumber=58_postMigration-->