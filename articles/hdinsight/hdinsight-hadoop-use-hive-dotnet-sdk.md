<properties
	pageTitle="Выполнение запросов Hive с помощью пакета SDK HDInsight для .NET | Microsoft Azure"
	description="Узнайте, как отправлять задания Hadoop в Azure HDInsight Hadoop с помощью пакета SDK HDInsight для .NET."
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
   ms.date="06/28/2016"
	ms.author="jgao"/>

# Выполнение запросов Hive с помощью пакета SDK HDInsight для .NET

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


Узнайте, как отправлять запросы Hive с помощью пакета SDK HDInsight для .NET.

> [AZURE.NOTE] Действия, описанные в этой статье, необходимо выполнять из клиента Windows. Чтобы получить сведения об использовании клиента Linux, OS X или Unix для работы с Hive, воспользуйтесь выбором вкладок в верхней части статьи.

##Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- **Кластер Hadoop в HDInsight**. См. раздел [Создание кластера и базы данных SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
- **Visual Studio 2012, Visual Studio 2013 или Visual Studio 2015**.

##Отправка запросов Hive с помощью пакета SDK HDInsight для .NET

Пакет SDK для HDInsight .NET содержит клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET.

**Отправка заданий**

1. Создайте в Visual Studio консольное приложение C#.
2. Введите следующую команду в окне консоли диспетчера пакетов NuGet.

		Install-Package Microsoft.Azure.Management.HDInsight.Job

2. Используйте следующий код:

        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;

        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;

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

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitHiveJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
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

                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);

                    System.Console.WriteLine("Waiting for the job completion ...");

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

                    System.Console.WriteLine("Job output is: ");

                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }

5. Нажмите клавишу **F5** для запуска приложения.


## Дальнейшие действия

В этой статье вы ознакомились с несколькими способами создания кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight][hdinsight-get-started]
* [Создание кластеров Hadoop в HDInsight][hdinsight-provision]
* [Управление кластерами Hadoop в HDInsight с помощью портала Azure](hdinsight-administer-use-management-portal.md)
* [Справочник по пакетам SDK HDInsight для .NET](https://msdn.microsoft.com/library/mt271028.aspx)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование Sqoop с HDInsight](hdinsight-use-sqoop-mac-linux.md)
* [Создание приложений .NET HDInsight с неинтерактивной проверкой подлинности](hdinsight-create-non-interactive-authentication-dotnet-applications.md)


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

<!---HONumber=AcomDC_0629_2016-->