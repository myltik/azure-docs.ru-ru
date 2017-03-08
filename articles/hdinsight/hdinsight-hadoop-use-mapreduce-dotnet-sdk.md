---
title: "Отправка заданий MapReduce с использованием пакета SDK для HDInsight .NET | Документация Майкрософт"
description: "Узнайте, как отправлять задания MapReduce в Azure HDInsight Hadoop с использованием пакета SDK для HDInsight .NET."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: c85e44b0-85fd-4185-ad1c-c34a9fe5ef44
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: fcc47e3e054d88fe40c13a0fd9949c315dbfce9b
ms.openlocfilehash: 1617aebffeec5b385b47a7425f6c849267da68a6
ms.lasthandoff: 02/04/2017


---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Выполнение заданий MapReduce с использованием пакета SDK для HDInsight .NET
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Узнайте, как отправлять задания MapReduce с использованием пакета SDK для HDInsight .NET. В кластерах HDInsight предусмотрен JAR-файл с несколькими примерами MapReduce. Этот JAR-файл — */example/jars/hadoop-mapreduce-examples.jar*.  Один из примеров — *wordcount* (подсчет слов). Вы разрабатываете консольное приложение на C# для отправки задания по подсчету слов.  Задание считывает файл */example/data/gutenberg/davinci.txt* и сохраняет результат в */example/data/davinciwordcount*.  Чтобы снова запустить приложение, необходимо очистить папку выходных данных.

> [!NOTE]
> Действия, описанные в этой статье, необходимо выполнять из клиента Windows. Чтобы получить сведения об использовании клиента Linux, OS X или Unix для работы с Hive, воспользуйтесь выбором вкладок в верхней части статьи.
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этой статьей необходимо иметь следующее:

* **Кластер Hadoop в HDInsight**. См. статью [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
* **Visual Studio 2012, Visual Studio 2013 или Visual Studio 2015**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Отправка заданий MapReduce с использованием пакета SDK для HDInsight .NET
Пакет SDK для HDInsight .NET содержит клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. 

**Отправка заданий**

1. Создайте в Visual Studio консольное приложение C#.
2. Введите следующую команду в окне консоли диспетчера пакетов NuGet.
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Используйте следующий код:
   
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
   
                private const string DefaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitMRJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitMRJob()
                {
                    List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
   
                    var paras = new MapReduceJobSubmissionParameters
                    {
                        JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                        JarClass = "wordcount",
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the MR job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
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
4. Нажмите клавишу **F5** для запуска приложения.

Чтобы снова запустить задание, необходимо изменить имя выходной папки задания. В нашем примере это /example/data/davinciwordcount.

Выходные данные выполненного задания будут пустыми. Просмотреть результаты задания MapReduce можно на портале Azure в контейнере хранилища по умолчанию в хранилище двоичных объектов.  Имя файла — part-r-00000.

## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы ознакомились с несколькими способами создания кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* Сведения об отправке задания Hive см. в инструкциях по [выполнению запросов Hive с помощью пакета SDK для HDInsight .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md).
* Сведения о создании кластеров HDInsight см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Сведения об управлении кластерами в HDInsight см. в статье [Управление кластерами Hadoop в HDInsight с помощью портала Azure](hdinsight-administer-use-portal-linux.md).
* Подробные сведения о пакете SDK для HDInsight .NET см. в [соответствующей справке](https://msdn.microsoft.com/library/mt271028.aspx).
* Сведения о неинтерактивной проверке подлинности в Azure см. в статье [Создание приложений .NET HDInsight с неинтерактивной проверкой подлинности](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


