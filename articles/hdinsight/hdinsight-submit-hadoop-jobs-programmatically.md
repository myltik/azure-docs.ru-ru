<properties 
	pageTitle="Отправка заданий Hadoop в HDInsight | Microsoft Azure" 
	description="Вы узнаете, как отправлять задания Hadoop в HDInsight для платформы Azure." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="jgao"/>

# Отправка заданий Hadoop в HDInsight

Узнайте, как с помощью Azure PowerShell отправлять задания MapReduce и Hive, а также как с помощью пакета SDK для HDInsight .NET отправлять задания MapReduce, Hive и задания потоковой передачи Hadoop.

> [AZURE.NOTE]Действия, описанные в этой статье, необходимо выполнять из клиента Windows. Сведения об использовании клиента Linux, OS X или Unix для работы с MapReduce, Hive или Pig в HDInsight см. в следующих статьях (в каждой из них на выбор доступны ссылки **SSH** и **Curl**):
>
> - [Использование Hive с HDInsight](hdinsight-use-hive.md)
> - [Использование Pig с HDInsight](hdinsight-use-pig.md)
> - [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

##Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

* **Кластер Azure HDInsight**. Инструкции см. в разделе [Приступая к работе с HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight][hdinsight-provision].
- **Рабочая станция с Azure PowerShell**. См. раздел [Установка и использование Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



##Отправка заданий MapReduce с помощью Azure PowerShell
Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. Дополнительные сведения об использовании Azure PowerShell с HDInsight см. в статье [Управление службой HDInsight с помощью PowerShell][hdinsight-admin-powershell].

Hadoop MapReduce — это программная платформа для написания приложений, обрабатывающих большие объемы данных. Кластеры HDInsight содержат JAR-файл (в папке *\example\jars\hadoop-mapreduce-examples.jar*), в котором есть несколько примеров MapReduce.

Один из примеров предназначен для подсчета частоты слов в исходных файлах. В этом разделе вы узнаете, как с помощью PowerShell на рабочей станции запустить пример для подсчета частоты слов. Дополнительные сведения о создании и выполнении заданий MapReduce см. в статье [Использование MapReduce в Hadoop в HDInsight][hdinsight-use-mapreduce].

**Запуск программы MapReduce для подсчета слов с помощью PowerShell**

1.	Откройте **Azure PowerShell**. Инструкции по открытию окна консоли Azure PowerShell см. в статье [Установка и настройка Azure PowerShell][powershell-install-configure].

3. Задайте указанные переменные, выполнив следующие команды PowerShell.
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"    

	Имя подписки — то, которое вы указали при создании кластера HDInsight. Кластер HDInsight — тот, на котором будет выполняться задание MapReduce.
	
5. Выполните следующие команды, чтобы создать определение задания MapReduce:

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	Существует два аргумента. Первый из них — имя исходного файла, а второй — путь выходного файла. Дополнительные сведения о префиксе wasb:// см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].

6. Чтобы выполнить задание MapReduce, выполните следующую команду:

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

	Помимо определения задания MapReduce вы также указываете имя кластера HDInsight, в котором будет выполняться задание MapReduce.

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
2. Задайте указанные переменные, выполнив следующие команды PowerShell.

		$subscriptionName = "<SubscriptionName>"       
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"			

	Учетная запись хранения Azure — та, которую вы указали во время подготовки кластера HDInsight. Она используется для размещения контейнера BLOB-объектов, используемого в кластере HDInsight в качестве файловой системы по умолчанию. Имя контейнера хранилища BLOB-объектов обычно совпадает с именем кластера HDInsight, если при подготовке кластера вы не указали другое имя.

3. Создайте объект контекста хранилища BLOB-объектов Azure, выполнив следующие команды.

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	**Select-AzureSubscription** используется для задания активной подписки. Это нужно тогда, когда подписок несколько и по умолчанию установлена не та из них, которая вам требуется.

4. Загрузите результаты задания MapReduce из контейнера BLOB-объектов на рабочую станцию, выполнив следующую команду.

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Папка *example/data/WordCountOutput* — выходная папка, указанная при запуске задания MapReduce. *part-r-00000* — имя файла по умолчанию для выходных данных задания MapReduce. Файл будет загружен в локальную папку с сохранением структуры каталогов. Например, на приведенном ниже снимке экрана текущая папка — корневая папка C:. Файл будет скачан в такой каталог:

*C:\example\data\WordCountOutput*

5. Выполните следующую команду, чтобы напечатать выходной файл задания MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	Задание MapReduce создает файл с именем *part-r-00000*, который содержит слова и их количество. В сценарии используется команда **findstr**, которая создает список всех слов, содержащих there.


> [AZURE.NOTE]Если открыть файл ./example/data/WordCountOutput/part-r-00000 (многострочный файл результатов задания MapReduce) в Блокноте, вы заметите, что разрывы строк отображаются неправильно. Это ожидаемое поведение.









































































































































##Отправка заданий Hive с помощью Azure PowerShell
[Apache Hive][apache-hive] предоставляет средства для выполнения задания MapReduce через SQL-подобный язык сценариев под названием *HiveQL*. Он может использоваться для сведения данных, создания запросов и анализа больших объемов информации.

Кластеры HDInsight поставляются с примером таблицы Hive, которая называется *hivesampletable*. Сейчас вы выполните задание Hive по созданию списка некоторых данных из таблицы Hive с помощью Azure PowerShell.

**Выполнение задания Hive с помощью Azure PowerShell**

1.	Откройте **Azure PowerShell**. Инструкции по открытию окна консоли Azure PowerShell см. в статье [Установка и настройка Azure PowerShell][powershell-install-configure].

2. Задайте первые две переменные в следующих командах, а затем выполните команды:
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"             
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	$querystring — это запрос HiveQL.

3. Выберите подписку Azure и кластер, в котором будет выполняться задание Hive, выполнив следующие команды.

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. Отправьте задание Hive, выполнив следующие команды.

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	С помощью параметра **-File** можно указать файл сценария HiveQL в распределенной файловой системе Hadoop (HDFS).

Дополнительные сведения о Hive см. в разделе [Использование Hive с HDInsight][hdinsight-use-hive].


## Отправка заданий Hive с помощью Visual Studio

См. статью [Начало работы со средствами HDInsight Hadoop для Visual Studio][hdinsight-visual-studio-tools].

##Отправка заданий Sqoop с помощью Azure PowerShell

См. раздел [Использование Sqoop с HDInsight][hdinsight-use-sqoop].

##Отправка заданий MapReduce с использованием пакета SDK для HDInsight .NET
Пакет SDK для HDInsight .NET содержит клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. Кластеры HDInsight содержат JAR-файл (в папке *\example\jars\hadoop-mapreduce-examples.jar*), в котором есть несколько примеров MapReduce. Один из примеров предназначен для подсчета частоты слов в исходных файлах. В этом сеансе вы узнаете, как создать приложение .NET для запуска примера подсчета слов. Дополнительные сведения о создании и выполнении заданий MapReduce см. в статье [￼Использование MapReduce в Hadoop в HDInsight][hdinsight-use-mapreduce].


Подготовка кластера HDInsight с использованием пакета SDK предусматривает следующие процедуры.

- Установка пакета SDK для HDInsight .NET
- Создание консольного приложение
- Выполнение приложения


**Установка пакета SDK для HDInsight .NET** Последнюю опубликованную сборку пакета SDK можно установить из [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Инструкции будут показаны в следующей процедуре.

**Создание консольного приложения Visual Studio**

1. Откройте Visual Studio.

2. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.

3. В окне **Новый проект** введите или выберите следующие значения.

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Свойство</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Значение</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Категория</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Шаблон</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Консольное приложение</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Имя</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td></tr>
</table>

4. Нажмите кнопку **ОК**, чтобы создать проект.


5. В меню **Инструменты** последовательно выберите **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**.

6. Для установки пакетов выполните следующие команды на консоли:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Эта команда добавляет библиотеки .NET и ссылки на них в текущий проект Visual Studio. У версии должен быть номер не ниже 0.11.0.1.

7. В **обозревателе решений** дважды щелкните файл **Program.cs**, чтобы открыть его.

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
	
10. Вставьте в функцию **Main()** следующий код.
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string certFriendlyName = "<certificate friendly name>";

		string clusterName = "<HDInsight cluster name>";
		
		string storageAccountName = "<Azure storage account name>";
		string storageAccountKey = "<Azure storage account key>";
		string containerName = "<Blob container name>";
		
	
	Это все переменные, которые необходимо задать для программы. Узнать имя подписки Azure можно на [портале Azure][azure-management-portal].

	Сведения о сертификате см. в статье [Создание и передача сертификата управления для Azure][azure-certificate]. Чтобы быстро настроить сертификат, выполните командлеты PowerShell **Get-AzurePublishSettingsFile** и **Import-AzurePublishSettingsFile**. Они создадут и автоматически отправят сертификат управления. После выполнения командлетов откройте **certmgr.msc** на рабочей станции и найдите сертификаты в узле **Личные** > **Сертификаты**. В сертификате, который создан командлетами PowerShell, в полях **Кому выдан** и **Кем выдан** указано значение «Средства Azure».

	Имя учетной записи хранения Azure относится к учетной записи, которую вы указали во время подготовки кластера HDInsight. По умолчанию имя контейнера совпадает с именем кластера HDInsight.
	
11. В функции **Main()** определите задание MapReduce, добавив следующий код.


        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

	Существует два аргумента. Первый из них — имя исходного файла, а второй — путь выходного файла. Дополнительные сведения о префиксе wasb:// см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].
		
12. В функции **Main()** создайте объект JobSubmissionCertificateCredential, добавив следующий код.

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. В функции **Main()** запустите задание и дождитесь его завершения, добавив следующий код.

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. В функции **Main()** отправьте на печать выходные данные задания MapReduce, добавив следующий код.

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

	Папка выходных данных указывается при определении задания MapReduce. Имя файла по умолчанию: **part-r-00000**.

**Запуск приложения**

Когда приложение открыто в Visual Studio, нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения и выходные данные приложения.

##Отправка заданий MapReduce для потоковой передачи Hadoop с использованием пакета SDK для HDInsight .NET
Кластеры HDInsight поставляются с программой потоковой передачи Hadoop, которая подсчитывает слова. Эта программа написана на C#. Программа mapper — */example/apps/cat.exe*, а программа reduce — */example/apps/wc.exe*. В этом разделе вы узнаете, как создать приложение .NET, которое выполняет пример с подсчетом слов.

Более подробную информацию о создании приложения .Net для отправки заданий MapReduce см. в разделе [Отправка заданий MapReduce с использованием пакета SDK для HDInsight .NET](#mapreduce-sdk).

Более подробную информацию о разработке и развертывании заданий потоковой передачи Hadoop см. в разделе [Разработка программ потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming-jobs].

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






##Отправка заданий Hive с помощью пакета SDK для HDInsight .NET 
Кластеры HDInsight поставляются с примером таблицы Hive, которая называется *hivesampletable*. Сейчас вы создадите приложение .NET, которое выполнит задание Hive по получению списка таблиц Hive, созданных в кластере HDInsight. Дополнительные сведения об использовании Hive см. в статье [Использование Hive с HDInsight][hdinsight-use-hive].

Подготовка кластера HDInsight с использованием пакета SDK предусматривает следующие процедуры.

- Установка пакета SDK для HDInsight .NET
- Создание консольного приложение
- Выполнение приложения


**Установка пакета SDK для HDInsight .NET** Последнюю опубликованную сборку пакета SDK можно установить из [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Инструкции будут показаны в следующей процедуре.

**Создание консольного приложения Visual Studio**

1. Откройте Visual Studio.

2. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.

3. В окне **Новый проект** введите или выберите следующие значения.

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Свойство</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Значение</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Категория</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Шаблон</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Консольное приложение</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Имя</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitHiveJob</td></tr>
</table>

4. Нажмите кнопку **ОК**, чтобы создать проект.


5. В меню **Инструменты** последовательно выберите **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**.

6. Установите пакет, выполнив в консоли следующую команду.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Эта команда добавляет библиотеки .NET и ссылки на них в текущий проект Visual Studio.

7. В **обозревателе решений** дважды щелкните файл **Program.cs**, чтобы открыть его.

8. Добавьте в начало файла следующие инструкции **using**.

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
	
10. Вставьте в функцию **Main()** следующий код.
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string clusterName = "<HDInsight cluster name>";
		string certFriendlyName = "<certificate friendly name>";		
		
	
	Это все переменные, которые необходимо задать для программы. Имя подписки Azure можно получить у своего системного администратора.

	Сведения о сертификате см. в статье [Создание и передача сертификата управления для Azure][azure-certificate]. Чтобы быстро настроить сертификат, выполните командлеты PowerShell **Get-AzurePublishSettingsFile** и **Import-AzurePublishSettingsFile**. Они создадут и автоматически отправят сертификат управления. После выполнения командлетов откройте **certmgr.msc** на рабочей станции и найдите сертификаты в узле **Личные** > **Сертификаты**. В сертификате, который создан командлетами Azure PowerShell, в полях **Кому выдан** и **Кем выдан** указано значение «Средства Azure».
	
11. В функции **Main()** определите задание Hive, добавив следующий код.

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };

	С помощью параметра **File** можно указать файл сценария HiveQL в HDFS.

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

		
12. В функции **Main()** создайте объект **JobSubmissionCertificateCredential**, добавив следующий код.
	
        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. В функции **Main()** запустите задание и дождитесь его завершения, добавив следующий код.

        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);
		
14. В функции **Main()** отправьте на печать выходные данные задания Hive, добавив следующий код.

        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**Запуск приложения**

Когда приложение открыто в Visual Studio, нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения. Должен быть получен следующий результат:

	hivesampletable




##Дальнейшие действия
В этой статье вы ознакомились с несколькими способами подготовки кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight][hdinsight-get-started]
* [Подготовка кластеров HDInsight к работе][hdinsight-provision]
* [Управление службой HDInsight с помощью PowerShell][hdinsight-admin-powershell]
* [Справочная документация по командлетам HDInsight][hdinsight-powershell-reference]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Pig с HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: http://manage.windowsazure.com/

[hdinsight-visual-studio-tools]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/
 

<!---HONumber=August15_HO6-->