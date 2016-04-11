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
	ms.date="03/23/2016"
	ms.author="jgao"/>

# Отправка заданий Hadoop в HDInsight

Узнайте, как с помощью Azure PowerShell отправлять задания MapReduce и Hive, а также как с помощью пакета SDK для HDInsight .NET отправлять задания MapReduce, Hive и задания потоковой передачи Hadoop.

> [AZURE.NOTE] Действия, описанные в этой статье, необходимо выполнять из клиента Windows. Сведения об использовании клиента Linux, OS X или Unix для работы с MapReduce, Hive или Pig в HDInsight см. в следующих статьях (в каждой из них на выбор доступны ссылки **SSH** и **Curl**):
>
> - [Использование Hive с HDInsight](hdinsight-use-hive.md)
> - [Использование Pig с HDInsight](hdinsight-use-pig.md)
> - [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

##Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- **Кластер Azure HDInsight**. Инструкции см. в разделе [Приступая к работе с HDInsight][hdinsight-get-started] или [Создание кластеров в HDInsight][hdinsight-provision].
- **Рабочая станция с Azure PowerShell.**. См. раздел [Установка Azure PowerShell 1.0 и более поздних версий](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater).

##Отправка заданий MapReduce с использованием PowerShell

См. раздел [Выполнение примеров Hadoop MapReduce в HDInsight на базе Windows](hdinsight-run-samples.md).

##Отправка заданий Hive с использованием PowerShell

См. раздел [Выполнение запросов Hive с использованием PowerShell](hdinsight-hadoop-use-hive-powershell.md)

## Отправка заданий Hive с использованием Visual Studio

См. статью [Начало работы со средствами HDInsight Hadoop для Visual Studio][hdinsight-visual-studio-tools].

##Отправка заданий Sqoop с использованием PowerShell

См. раздел [Использование Sqoop с HDInsight][hdinsight-use-sqoop].

##Отправка заданий Hive, Pig или Sqoop с использованием пакета SDK для HDInsight .NET
Пакет SDK для HDInsight .NET содержит клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET.

В следующем примере используется интерактивная аутентификация пользователя. Сведения об использовании неинтерактивной аутентификации см. в статье [Создание приложений .NET HDInsight с неинтерактивной аутентификацией](hdinsight-create-non-interactive-authentication-dotnet-applications.md)

**Отправка заданий**

1. Создайте в Visual Studio консольное приложение C#.
2. Введите следующую команду в окне консоли диспетчера пакетов NuGet.

		Install-Package Microsoft.Azure.Common.Authentication -Pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre
2. Используйте следующий код:

		using System;
		using System.Collections.Generic;
		using System.Security;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.Resources;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHDInsightJobDotNet
		{
		    class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		        private static HDInsightJobManagementClient _hdiJobManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<Your Subscription ID>");
		        private const string ResourceGroupName = "<Your Resource Group Name>";
		
		        private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
		        private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		        private const string ExistingClusterUsername = "<Cluster Username>";
		        private const string ExistingClusterPassword = "<Cluster User Password>";
		
		        private const string DefaultStorageAccountName = "<Default Storage Account Name>";
		        private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
		        private const string DefaultStorageContainerName = "<Default Blob Container Name>";
		
		        static void Main(string[] args)
		        {
                    System.Console.WriteLine("The application is running ...");
		
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
		            var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
		            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            SubmitHiveJob();
		            SubmitPigJob();
		            SubmitSqoopJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                    
		        }
		
		        public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
		            var accessToken =
		                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
		                    .AccessToken;
		
		            return new TokenCloudCredentials(accessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), creds.Token);
		        }
		
		        private static void SubmitHiveJob()
		        {
		            Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
		            List<string> args = new List<string> { { "argA" }, { "argB" } };
		            var parameters = new HiveJobSubmissionParameters
		            {
		                Query = "SHOW TABLES",
		                Defines = defines,
		                Arguments = args
		            };
		
                    Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    Console.WriteLine("JobId is " + jobId);

                    Console.WriteLine("Waiting for the job completion ...");
		
		            // Wait for job completion
		            var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
		            while (!jobDetail.Status.JobComplete)
		            {
		                Thread.Sleep(1000);
		                jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
		            }
		
		            // Get job output
		            var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
		                DefaultStorageContainerName);
		            var output = (jobDetail.ExitValue == 0)
		                ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
		                : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
                        
                    Console.WriteLine("Job output is: ");
                    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        Console.WriteLine(value);
                    }
		        }
                
   		        private static void SubmitPigJob()
		        {
		            var parameters = new PigJobSubmissionParameters
		            {
		                Query = @"LOGS = LOAD 'wasb:///example/data/sample.log';
		                    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		                    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		                    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		                    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		                    RESULT = order FREQUENCIES by COUNT desc;
		                    DUMP RESULT;"
		            };
		
		            System.Console.WriteLine("Submitting the Pig job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		        }
		
		        private static void SubmitSqoopJob()
		        {
		            var sqlDatabaseServerName = "<SQLDatabaseServerName>";
		            var sqlDatabaseLogin = "<SQLDatabaseLogin>";
		            var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
		            var sqlDatabaseDatabaseName = "<DatabaseName>";
		
		            var tableName = "<TableName>";
		            var exportDir = "/tutorials/usesqoop/data";
		
		            // Connection string for using Azure SQL Database.
		            // Comment if using SQL Server
		            var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
		            // Connection string for using SQL Server.
		            // Uncomment if using SQL Server
		            //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
		
		            var parameters = new SqoopJobSubmissionParameters
		            {
		                Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
		            };
		
		            System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		        }
		    }
		}



5. Нажмите клавишу **F5** для запуска приложения.

##Отправка заданий с помощью инструментов HDInsight для Visual Studio

С помощью инструментов HDInsight для Visual Studio можно выполнять запросы Hive и сценарии Pig. См. раздел [Приступая к работе с инструментами Hadoop для Visual Studio для HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

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
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!---HONumber=AcomDC_0330_2016-->