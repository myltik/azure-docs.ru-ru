---
title: Использование Hadoop Sqoop в HDInsight | Microsoft Docs
description: Узнайте, как использовать пакет SDK HDInsight для .NET, чтобы выполнять импорт и экспорт Sqoop между кластером HDInsight и базой данных SQL Azure.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2016
ms.author: jgao

---
# Выполнение заданий Sqoop с помощью пакета SDK для .NET для Hadoop в HDInsight
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать пакет SDK HDInsight для .NET для выполнения заданий Sqoop, осуществляющих импорт и экспорт между кластером HDInsight и базой данных SQL Azure или базой данных SQL Server.

> [!NOTE]
> Действия, описанные в этой статье можно использовать для кластера HDInsight под управлением Windows или Linux. Но эти действия могут быть запущены только из клиента Windows. Используйте выбор вкладок в верхней части этой статьи, чтобы выбрать другие методы.
> 
> 

### Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

* **Кластер Hadoop в HDInsight**. См. раздел [Создание кластера и базы данных SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## Запуск Sqoop с использованием пакета SDK
Пакет SDK для HDInsight .NET содержит клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. В этом разделе вы создадите и настроите консольное приложение C# для экспорта hivesampletable в созданную ранее таблицу базы данных SQL.

**Отправка задания Sqoop**

1. Создайте в Visual Studio консольное приложение C#.
2. В Консоли диспетчера пакетов Visual Studio выполните следующую команду Nuget, чтобы импортировать пакет:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Используйте следующий код в файле Program.cs.
   
        using System.Collections.Generic;
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
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
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
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
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
4. Нажмите клавишу **F5** для запуска программы.

## Ограничения
* Массовый экспорт: при использовании HDInsight на основе Linux соединитель Sqoop, применяемый для экспорта данных в Microsoft SQL Server или базу данных SQL Azure, пока не поддерживает операции массовой вставки.
* Пакетная обработка: при использовании HDInsight на основе Linux, когда для выполнения вставок применяется переключатель `-batch`, Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## Дальнейшие действия
Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

* [Использование Oozie с HDInsight](hdinsight-use-oozie.md): используйте действие Sqoop в рабочем процессе Oozie.
* [Анализ данных о задержке рейсов с помощью HDInsight](hdinsight-analyze-flight-delay-data.md): используйте Hive для анализа данных о задержке рейсов, а затем используйте Sqoop для экспорта данных в базу данных SQL Azure.
* [Передача данных в HDInsight](hdinsight-upload-data.md): узнайте о других способах отправки данных в HDInsight и хранилище больших двоичных объектов Azure.

<!---HONumber=AcomDC_0914_2016-->