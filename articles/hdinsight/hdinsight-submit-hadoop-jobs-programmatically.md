<properties
	pageTitle="Отправка заданий Hadoop в HDInsight | Microsoft Azure"
	description="Вы узнаете, как отправлять задания Hadoop в HDInsight для платформы Azure."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
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

* **Кластер Azure HDInsight**. Инструкции см. в разделе [Приступая к работе с HDInsight][hdinsight-get-started] или [Создание кластеров в HDInsight][hdinsight-provision].
- **Рабочая станция с Azure PowerShell**. См. раздел [Установка и использование Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



##Отправка заданий MapReduce с помощью Azure PowerShell
Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. Дополнительные сведения об использовании Azure PowerShell с HDInsight см. в статье [Управление службой HDInsight с помощью PowerShell][hdinsight-admin-powershell].

Hadoop MapReduce — это программная платформа для написания приложений, обрабатывающих большие объемы данных. Кластеры HDInsight содержат JAR-файл (в папке *\\example\\jars\\hadoop-mapreduce-examples.jar*), в котором есть несколько примеров MapReduce.

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

	Учетная запись хранения Azure — та, которую вы указали при создании кластера HDInsight. Она используется для размещения контейнера BLOB-объектов, используемого в кластере HDInsight в качестве файловой системы по умолчанию. Имя контейнера хранилища больших двоичных объектов обычно совпадает с именем кластера HDInsight, если при создании кластера вы не указали другое имя.

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
Пакет SDK для HDInsight .NET содержит клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. Кластеры HDInsight содержат JAR-файл (в папке *\\example\\jars\\hadoop-mapreduce-examples.jar*), в котором есть несколько примеров MapReduce. Один из примеров предназначен для подсчета частоты слов в исходных файлах. В этом сеансе вы узнаете, как создать приложение .NET для запуска примера подсчета слов. Дополнительные сведения о создании и выполнении заданий MapReduce см. в статье [Использование MapReduce в Hadoop в HDInsight][hdinsight-use-mapreduce].

**Отправка задания WordCount MapReduce**

1. Создайте в Visual Studio консольное приложение C#.
2. Введите следующую команду в окне консоли диспетчера пакетов NuGet.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Используйте следующие инструкции using в файле Program.cs:

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Добавьте следующий код в функцию Main():

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
	    HDInsightJobManagementClient _hdiJobManagementClient;

	    List<string> arguments = new List<string> { { "wasb:///example/data/gutenberg/davinci.txt" }, { "wasb:///example/data/WordCountOutput" } };
	
	    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
	    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
	
	    var parameters = new MapReduceJobSubmissionParameters
	    {
	        UserName = ExistingClusterUsername,
	        JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
	        JarClass = "wordcount",
	        Arguments = ConvertArgsToString(arguments)
	    };
	
	    System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
	    var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(parameters);
	    System.Console.WriteLine("Validating that the response is as expected...");
	    System.Console.WriteLine("Response status code is " + response.StatusCode);
	    System.Console.WriteLine("Validating the response object...");
	    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
	    Console.WriteLine("Press ENTER to continue ...");
	    Console.ReadLine();

4. Добавьте в класс следующие вспомогательные методы:

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. Нажмите клавишу **F5** для запуска приложения.










##Отправка заданий MapReduce для потоковой передачи Hadoop с использованием пакета SDK для HDInsight .NET
Кластеры HDInsight поставляются с программой потоковой передачи Hadoop, которая подсчитывает слова. Эта программа написана на C#. Программа mapper — */example/apps/cat.exe*, а программа reduce — */example/apps/wc.exe*. В этом разделе вы узнаете, как создать приложение .NET, которое выполняет пример с подсчетом слов.

Более подробную информацию о создании приложения .Net для отправки заданий MapReduce см. в разделе [Отправка заданий MapReduce с использованием пакета SDK для HDInsight .NET](#mapreduce-sdk).

Более подробную информацию о разработке и развертывании заданий потоковой передачи Hadoop см. в разделе [Разработка программ потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming-jobs].

Описанная ниже процедура подходит только для кластеров HDInsight под управлением Windows. В кластерах Linux потоковая передача пока не поддерживается. При этом программа .NET позволяет отправлять задания потоковой передачи на других языках программирования, которые поддерживаются кластерами Linux, например на языке Python. Пример задания потоковой передачи на языке Python см. в статье [Разработка программ потоковой передачи Python для HDInsight](hdinsight-hadoop-streaming-python.md).

**Отправка задания WordCount MapReduce**

1. В Консоли диспетчера пакетов Visual Studio выполните следующую команду Nuget, чтобы импортировать пакет:

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Используйте следующие инструкции using в файле Program.cs:

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Добавьте следующий код в функцию Main():

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";

        List<string> arguments = new List<string> { { "/example/apps/cat.exe" }, { "/example/apps/wc.exe" } };

        HDInsightJobManagementClient _hdiJobManagementClient;
        var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
        _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

        var parameters = new MapReduceStreamingJobSubmissionParameters
        {
            UserName = ExistingClusterUsername,
            File = ConvertArgsToString(arguments),
            Mapper = "cat.exe",
            Reducer = "wc.exe",
            Input = "/example/data/gutenberg/davinci.txt",
            Output = "/tutorials/wordcountstreaming/output"
        };

        System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
        var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceStreamingJob(parameters);
        System.Console.WriteLine("Validating that the response is as expected...");
        System.Console.WriteLine("Response status code is " + response.StatusCode);
        System.Console.WriteLine("Validating the response object...");
        System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadLine();

4. Добавьте в класс функцию справки:

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. Нажмите клавишу **F5** для запуска приложения.

##Отправка заданий Hive с помощью пакета SDK для HDInsight .NET
Кластеры HDInsight поставляются с примером таблицы Hive, которая называется *hivesampletable*. Сейчас вы создадите приложение .NET, которое выполнит задание Hive по получению списка таблиц Hive, созданных в кластере HDInsight. Дополнительные сведения об использовании Hive см. в статье [Использование Hive с HDInsight][hdinsight-use-hive].

Создание кластера HDInsight с использованием пакета SDK предусматривает следующие процедуры.

- Установка пакета SDK для HDInsight .NET
- Создание консольного приложение
- Выполнение приложения


**Установка пакета SDK для HDInsight .NET** Последнюю опубликованную сборку пакета SDK можно установить из [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Инструкции будут показаны в следующей процедуре.

**Создание консольного приложения Visual Studio**

1. Откройте Visual Studio 2013 или 2015.

2. Создайте новый проект со следующими параметрами:

	|Свойство|Значение|
	|--------|-----|
	|Шаблон|Templates/Visual C#/Windows/Console Application|
	|Имя|SubmitHiveJob|

3. В меню **Средства** щелкните **Диспетчер пакетов Nuget**, а затем щелкните **Консоль диспетчера пакетов**.
4. Чтобы установить пакеты, выполните в консоли следующую команду:

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

	Эти команды добавляют библиотеки .NET и ссылки на них в текущий проект Visual Studio.

5. В обозревателе решений дважды щелкните файл **Program.cs**, чтобы открыть его, вставьте указанный ниже код и укажите значения для переменных:

		using System.Collections.Generic;
		using System.Linq;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHiveJob
		{
		    class Program
		    {
		        private static HDInsightJobManagementClient _hdiJobManagementClient;
		
		        private const string ExistingClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		
		        private const string ExistingClusterUsername = "<HDINSIGHT HTTP USER NAME>";  //The default name is admin.
		        private const string ExistingClusterPassword = "<HDINSIGHT HTTP USER PASSWORD>";
		
		        private static void Main(string[] args)
		        {
		
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            SubmitHiveJob();
		        }
		
		        private static void SubmitHiveJob()
		        {
		            Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
		            List<string> args = new List<string> { { "argA" }, { "argB" } };
		            var parameters = new HiveJobSubmissionParameters
		            {
		                UserName = ExistingClusterUsername,
		                Query = "SHOW TABLES",
		                Defines = ConvertDefinesToString(defines),
		                Arguments = ConvertArgsToString(args)
		            };
		
		            System.Console.WriteLine("Submitting the Hive job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            System.Console.WriteLine("Press ENTER to continue ...");
		            System.Console.ReadLine();
		        }
		
		        private static string ConvertDefinesToString(Dictionary<string, string> defines)
		        {
		            if (defines.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&define=", defines.Select(x => x.Key + "%3D" + x.Value).ToArray());
		        }
		        private static string ConvertArgsToString(List<string> args)
		        {
		            if (args.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&arg=", args.ToArray());
		        }
		    }
		}

6. Нажмите клавишу **F5** для запуска приложения.

##Отправка заданий с помощью инструментов HDInsight для Visual Studio

С помощью инструментов HDInsight для Visual Studio можно выполнять запросы Hive и сценарии Pig. См. статью [Приступая к работе с инструментами Hadoop в Visual Studio для HDInsight для выполнения запроса Hive](hdinsight-hadoop-visual-studio-tools-get-started.md).


##Дальнейшие действия
В этой статье вы ознакомились с несколькими способами создания кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight][hdinsight-get-started]
* [Создание кластеров Hadoop в HDInsight][hdinsight-provision]
* [Управление службой HDInsight с помощью PowerShell][hdinsight-admin-powershell]
* [Справочная документация по командлетам HDInsight][hdinsight-powershell-reference]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Pig с HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: https://portal.azure.com/

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

<!---HONumber=Oct15_HO2-->