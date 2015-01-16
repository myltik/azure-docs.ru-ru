<properties urlDisplayName="HDInsight Administration" pageTitle="Отправка заданий Hadoop в HDInsight для платформы Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hive, mapreduce, HDInsight .NET SDK, powershell, submit mapreduce jobs, submit hive jobs, development, hadoop, apache" description="Вы узнаете, как отправлять задания Hadoop в HDInsight для платформы Azure." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Submit  Hadoop jobs in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />

# Отправка заданий Hadoop в HDInsight

В этой статье вы узнаете, как отправлять задания MapReduce и Hive с помощью PowerShell и пакета SDK для HDInsight .NET.

**Предварительные требования:**

Перед началом работы с этой статьей необходимо иметь следующее:

* Кластер Azure HDInsight. Указания см. в разделе [Приступая к работе с HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight][hdinsight-provision].
* Установите и настройте Azure PowerShell. Указания см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].


##Содержание

* [Отправка заданий MapReduce с использованием PowerShell](#mapreduce-powershell)
* [Отправка заданий Hive с использованием PowerShell](#hive-powershell)
* [Отправка заданий Sqoop с использованием PowerShell](#sqoop-powershell)
* [Отправка заданий MapReduce с использованием пакета SDK для HDInsight .NET](#mapreduce-sdk)
* [Отправка заданий MapReduce для потоковой передачи Hadoop с использованием пакета SDK для HDInsight .NET](#streaming-sdk)
* [Отправка заданий Hive с использованием пакета SDK для HDInsight .NET](#hive-sdk)
* [Дальнейшие действия](#nextsteps)

##<a id="mapreduce-powershell"></a> Отправка заданий MapReduce с использованием PowerShell
Azure PowerShell - это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. Дополнительную информацию об использовании PowerShell с HDInsight см. в разделе [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell].

Hadoop MapReduce - это программная платформа для создания приложений, обрабатывающих большие объемы данных. Кластеры HDInsight содержат JAR-файл, находящийся в папке \example\jars\hadoop-examples.jar, с несколькими примерами MapReduce. В кластерах HDInsight версии 3.0 этот файл был переименован в hadoop-mapreduce-examples.jar. Один из примеров предназначен для подсчета частоты слов в исходных файлах. В этом сеансе вы узнаете, как использовать PowerShell с рабочей станции для запуска примера подсчета слов. Дополнительную информацию о разработке и выполнении заданий MapReduce см. в разделе [Использование MapReduce с HDInsight][hdinsight-use-mapreduce].

**Запуск программы MapReduce для подсчета слов с помощью PowerShell**

1.	Откройте **Azure PowerShell**. Указания по открытию окна консоли Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

3. Задайте указанные две переменные, выполнив следующие команды PowerShell:
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"    

	Подписка - та, что используется для создания кластера HDInsight. Кластер HDInsight - тот, который вы хотите использовать для выполнения задания MapReduce.
	
5. Выполните следующие команды, чтобы создать определение задания MapReduce:

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	Существует два аргумента. Первый из них - имя исходного файла, а второй - путь выходного файла. Дополнительную информацию о префиксе wasb см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].

6. Чтобы выполнить задание MapReduce, выполните следующую команду:

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

	Помимо определения задания MapReduce вы также указываете имя кластера HDInsight, в котором нужно выполнить задание MapReduce. 

7. Выполните следующую команду для проверки выполнения задания MapReduce:

		# Wait for the job to complete
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 
		

8. Выполните следующую команду для проверки любых ошибок при выполнении задания MapReduce:	

		# Get the job standard error output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
					
	На следующем снимке экрана показан результат успешного выполнения. В противном случае вы увидите сообщения об ошибках.

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]

		
**Получение результатов задания MapReduce**

1. Откройте **Azure PowerShell**.
2. Задайте эти три переменные, выполнив следующие команды PowerShell:

		$subscriptionName = "<SubscriptionName>"       
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"			

	Учетная запись хранения Azure - та, которую вы указали во время подготовки кластера HDInsight. Эта учетная запись хранения используется для размещения контейнера BLOB-объектов, используемого в качестве файловой системы по умолчанию кластера HDInsight.  Имя контейнера хранилища BLOB-объектов обычно совпадает с именем кластера HDInsight, если при подготовке кластера не указано другое имя.

3. Выполните следующие команды для создания объекта контекста хранилища Azure:

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Командлет Select-AzureSubscription используется для задания текущей подписки в случае, если имеется несколько подписок и должна использоваться подписка, отличная от подписки по умолчанию. 

4. Выполните следующую команду, чтобы загрузить результаты задания MapReduce из контейнера BLOB-объектов на рабочую станцию:

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Пример: *example/data/WordCountOutput* (указывается при запуске задания MapReduce) содержит выходные данные. *part-r-00000* является именем по умолчанию для выходных данных задания MapReduce.  Этот файл будет загружен в ту же структуру папок в локальной папке. Например, на следующем снимке экрана текущая папка - корневая папка диска C.  Файл будет скачан в *C:\example\data\WordCountOutput\* папку.

5. Выполните следующую команду, чтобы напечатать выходной файл задания MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	Задание MapReduce создает файл с именем part-r-00000, содержащий слова и их количество.  В этом сценарии для получения списка всех слов, содержащих "there", используется команда findstr.


> [WACOM.NOTE] Если открыть файл ./example/data/WordCountOutput/part-r-00000 (многострочный результат задания MapReduce) в Блокноте, вы заметите, что разрывы строки отображаются неправильно. Это ожидаемое поведение.









































































































































##<a id="hive-powershell"></a> Отправка заданий Hive с использованием PowerShell
Apache [hdinsight-use-hive][apache-hive] предоставляет средства для выполнения задания MapReduce через язык сценариев наподобие SQL под названием HiveQL, который может применяться к суммированию, созданию запросов и анализу больших объемов данных. 

Кластеры HDInsight поставляются с примером таблицы Hive, которая называется hivesampletable. В этом сеансе вы будете использовать PowerShell, чтобы выполнить задание Hive для получения списка некоторых данных из таблицы Hive. 

**Выполнение задания Hive с использованием PowerShell**

1.	Откройте **Azure PowerShell**. Указания по открытию окна консоли Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

2. Задайте первые две переменные в следующих командах, а затем выполните команды:
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"             
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	$querystring - это запрос HiveQL.

3. Чтобы выбрать подписку Azure и кластер для выполнения задания Hive, выполните следующие команды:

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. Отправка задания hive:

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	Можно использовать параметр -File, чтобы указать файл сценария HiveQL в HDFS.

Дополнительную информацию о Hive см. в разделе [Использование Hive с HDInsight][hdinsight-use-hive].

##<a id="sqoop-powershell"></a>Отправка заданий Sqoop с использованием PowerShell

См. раздел [Использование Sqoop с HDInsight][hdinsight-use-sqoop].

##<a id="mapreduce-sdk"></a> Отправка заданий MapReduce с использованием пакета SDK для HDInsight .NET
Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. Кластеры HDInsight содержат JAR-файл, находящийся в папке \example\jars\hadoop-examples.jar, с несколькими примерами MapReduce. Один из примеров предназначен для подсчета частоты слов в исходных файлах. В этом сеансе вы узнаете, как создать приложение .NET для запуска примера подсчета слов. Дополнительную информацию о разработке и выполнении заданий MapReduce см. в разделе [Использование MapReduce с HDInsight][hdinsight-use-mapreduce].


Для подготовки кластера HDInsight с использованием пакета SDK необходимы следующие процедуры:

- Установка пакета SDK для HDInsight .NET
- Создание консольного приложение
- Выполнение приложения


**Установка пакета SDK для HDInsight .NET**
Последнюю опубликованную сборку пакета SDK можно установить из [NuGet].(http://nuget.codeplex.com/wikipage?title=Getting%20Started). Указания будут показаны в следующей процедуре.

**Создание консольного приложения Visual Studio**

1. Откройте Visual Studio 2012.

2. В меню "Файл" выберите **Создать**, а затем щелкните **Проект**.

3. В окне "Новый проект" введите или выберите следующие значения:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Property</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Value</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td></tr>
	</table>

4. Нажмите кнопку **OK**, чтобы создать проект.


5. В меню **Сервис** щелкните **Диспетчер пакетов библиотеки**, затем щелкните **Консоль диспетчера пакетов**.

6. Для установки пакетов выполните следующие команды на консоли:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Эта команда добавляет библиотеки .NET и ссылки на них в текущий проект Visual Studio. Версия должна быть 0.11.0.1 или более поздняя.

7. В обозревателе решений дважды щелкните **Program.cs**, чтобы открыть этот файл.

8. Добавьте в начало файла следующие инструкции using:

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;
		
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. Добавьте в класс следующее определение функции. Эта функция используется для ожидания завершения задания Hadoop.

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }
	
10. В функции Main() скопируйте и вставьте следующий код:
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string certFriendlyName = "<certificate friendly name>";

		string clusterName = "<HDInsight cluster name>";
		
		string storageAccountName = "<Azure storage account name>";
		string storageAccountKey = "<Azure storage account key>";
		string containerName = "<Blob container name>";
		
	
	Это все переменные, которые необходимо задать для программы. Имя подписки Azure можно получить на [портале управления Azure][azure-management-portal]. 

	Сведения о сертификате см. в разделе [Создание и отправка сертификата управления для Azure][azure-certificate]. Простой способ настроить сертификат - выполнить командлеты PowerShell Get-AzurePublishSettingsFile и Import-AzurePublishSettingsFile. Они создадут и автоматически отправят сертификат управления. После выполнения командлетов PowerShell можно открыть certmgr.msc на рабочей станции и найти сертификат, развернув узел "Личные/Сертификаты". Сертификат, созданный командлетами PowerShell, содержит "Инструменты Azure" для обоих полей: "Кому выдан" и "Кем выдан".

	Имя учетной записи хранения Azure относится к учетной записи, которую вы указали во время подготовки кластера HDInsight. По умолчанию имя контейнера совпадает с именем кластера HDInsight.
	
11. В функции Main() добавьте следующий код для определения задания MapReduce:


        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

	Существует два аргумента. Первый из них - имя исходного файла, а второй - путь выходного файла. Дополнительную информацию о префиксе wasb см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].
		
12. В функции Main() добавьте следующий код, чтобы создать объект JobSubmissionCertificateCredential:

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. В функции Main() добавьте следующий код, чтобы запустить задание и ждать его завершения:

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. В функции Main() добавьте следующий код для печати выходных данных задания MapReduce:

		// Print the MapReduce job output
		Stream stream = new MemoryStream();
		
		CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey);
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);
		CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference("example/data/WordCountOutput/part-r-00000");
		
		blockBlob.DownloadToStream(stream);
		stream.Position = 0;
		
		StreamReader reader = new StreamReader(stream);
		Console.WriteLine(reader.ReadToEnd());
		
        Console.WriteLine("Press ENTER to continue.");
		Console.ReadLine();

	Папка выходных данных указывается при определении задания MapReduce. Имя файла по умолчанию: part-r-00000.

**Выполнение приложения**

Когда приложение открыто в Visual Studio, нажмите клавишу **F5**, чтобы запустить его. Должно открыться окно консоли, в котором отображается состояние приложения и выходные данные приложения. 

##<a id="streaming-sdk"></a> Отправка заданий MapReduce для потоковой передачи Hadoop с использованием пакета SDK для HDInsight .NET
Кластеры HDInsight поставляются с программой потоковой передачи Hadoop для счетчика слов, разработанной на C#. Программа mapper - /example/apps/cat.exe, а программа reduce - /example/apps/wc.exe. В этом сеансе вы узнаете, как создать приложение .NET для запуска примера подсчета слов. 

Более подробную информацию о создании приложения .Net для отправки заданий MapReduce см. в разделе [Отправка заданий MapReduce с использованием пакета SDK для HDInsight .NET](#mapreduce-sdk).

Более подробную информацию о разработке и развертывании потоковых заданий Hadoop см. в разделе [Разработка программ потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming-jobs].

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	
	using System.IO;
	using System.Threading;
	using System.Security.Cryptography.X509Certificates;
	
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.Hadoop.Client;
	
	namespace SubmitStreamingJob
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {

				// Set the variables
				string subscriptionID = "<Azure subscription ID>";
				string certFriendlyName = "<certificate friendly name>";
		
				string clusterName = "<HDInsight cluster name>";
				string statusFolderName = @"/tutorials/wordcountstreaming/status";

	            // Define the Hadoop streaming MapReduce job
	            StreamingMapReduceJobCreateParameters myJobDefinition = new StreamingMapReduceJobCreateParameters()
	            {
	                JobName = "my word counting job",
	                StatusFolder = statusFolderName,
	                Input = "/example/data/gutenberg/davinci.txt",
	                Output = "/tutorials/wordcountstreaming/output",
	                Reducer = "wc.exe",
	                Mapper = "cat.exe"
	            };
	
	            myJobDefinition.Files.Add("/example/apps/wc.exe");
	            myJobDefinition.Files.Add("/example/apps/cat.exe");
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
	
	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
	
	            // Create a hadoop client to connect to HDInsight
	            var jobClient = JobSubmissionClientFactory.Connect(creds);
	
	            // Run the MapReduce job
	            Console.WriteLine("----- Submit the Hadoop streaming job ...");
	            JobCreationResults mrJobResults = jobClient.CreateStreamingJob(myJobDefinition);
	
	            // Wait for the job to complete
	            Console.WriteLine("----- Wait for the Hadoop streaming job to complete ...");
	            WaitForJobCompletion(mrJobResults, jobClient);
	
	            // Display the error log
	            Console.WriteLine("----- The hadoop streaming job error log.");
	            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
	            {
	                var reader = new StreamReader(stream);
	                Console.WriteLine(reader.ReadToEnd());
	            }
	
	            // Display the output log
	            Console.WriteLine("----- The hadoop streaming job output log.");
	            using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
	            {
	                var reader = new StreamReader(stream);
	                Console.WriteLine(reader.ReadToEnd());
	            }
	
	            Console.WriteLine("----- Press ENTER to continue.");
	            Console.ReadLine();
	        }
	
	        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
	        {
	            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
	            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
	            {
	                jobInProgress = client.GetJob(jobInProgress.JobId);
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	            }
	        }
	    }
	}






##<a id="hive-sdk"></a> Отправка заданий Hive с использованием пакета SDK для HDInsight .NET 
Кластеры HDInsight поставляются с примером таблицы Hive, которая называется hivesampletable. В этом сеансе вы создадите приложение .NET, чтобы выполнить задание Hive для получения списка таблиц Hive, созданных в кластере HDInsight. Дополнительную информацию об использовании Hive см. в разделе [Использование Hive с HDInsight][hdinsight-use-hive].

Для подготовки кластера HDInsight с использованием пакета SDK необходимы следующие процедуры:

- Установка пакета SDK для HDInsight .NET
- Создание консольного приложение
- Выполнение приложения


**Установка пакета SDK для HDInsight .NET**
Последнюю опубликованную сборку пакета SDK можно установить из [NuGet].(http://nuget.codeplex.com/wikipage?title=Getting%20Started). Указания будут показаны в следующей процедуре.

**Создание консольного приложения Visual Studio**

1. Откройте Visual Studio 2012.

2. В меню "Файл" выберите **Создать**, а затем щелкните **Проект**.

3. В окне "Новый проект" введите или выберите следующие значения:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Property</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Value</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitHiveJob</td></tr>
	</table>

4. Нажмите кнопку **OK**, чтобы создать проект.


5. В меню **Сервис** щелкните **Диспетчер пакетов библиотеки**, затем щелкните **Консоль диспетчера пакетов**.

6. Для установки пакетов выполните следующие команды на консоли:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Эта команда добавляет библиотеки .NET и ссылки на них в текущий проект Visual Studio.

7. В обозревателе решений дважды щелкните **Program.cs**, чтобы открыть этот файл.

8. Добавьте в начало файла следующие инструкции using:

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;

		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. Добавьте в класс следующее определение функции. Эта функция используется для ожидания завершения задания Hadoop.

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }
	
10. В функции Main() скопируйте и вставьте следующий код:
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string clusterName = "<HDInsight cluster name>";
		string certFriendlyName = "<certificate friendly name>";		
		
	
	Это все переменные, которые необходимо задать для программы. Идентификатор подписки Azure можно получить у вашего системного администратора. 

	Сведения о сертификате см. в разделе [Создание и отправка сертификата управления для Azure][azure-certificate]. Простой способ настроить сертификат - выполнить командлеты PowerShell *Get-AzurePublishSettingsFile* и *Import-AzurePublishSettingsFile* . Они создадут и автоматически отправят сертификат управления. После выполнения командлетов PowerShell можно открыть certmgr.msc на рабочей станции и найти сертификат, развернув узел *"Личные/Сертификаты"*. Сертификат, созданный командлетами PowerShell, содержит *инструменты Azure* для полей *"Кем выдано"* и *"Кому выдано"* .
	
11. В функции Main() добавьте следующий код для определения задания Hive:

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };

	Можно также использовать параметр File, чтобы указать файл сценария HiveQL в HDFS. Например:

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

		
12. В функции Main() добавьте следующий код, чтобы создать объект JobSubmissionCertificateCredential:
	
        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. В функции Main() добавьте следующий код, чтобы запустить задание и ждать его завершения:

        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);
		
14. В функции Main() добавьте следующий код для печати выходных данных задания Hive:

        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**Выполнение приложения**

Когда приложение открыто в Visual Studio, нажмите клавишу **F5**, чтобы запустить его. Должно открыться окно консоли, в котором отображается состояние приложения. Результат должен выглядеть следующим образом:

	hivesampletable




##<a id="nextsteps"></a> Дальнейшие действия
В этой статье вы ознакомились с несколькими способами подготовки кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight.][hdinsight-get-started]
* [Подготовка кластеров HDInsight][hdinsight-provision]
* [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]
* [Справочная документация по командлетам HDInsight][hdinsight-powershell-reference]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Pig с HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg551722.aspx
[azure-management-portal]: http://manage.windowsazure.com/

[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-develop-streaming-jobs]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/

[hdinsight-powershell-reference]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn479228.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png 
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!--HONumber=35.1-->
