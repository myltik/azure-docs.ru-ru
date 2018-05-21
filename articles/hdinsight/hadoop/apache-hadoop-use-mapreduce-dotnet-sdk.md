---
title: Отправка заданий MapReduce с использованием пакета SDK HDInsight для .NET — Azure | Документы Майкрософт
description: Узнайте, как отправлять задания MapReduce в Azure HDInsight Hadoop с использованием пакета SDK для HDInsight .NET.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: c85e44b0-85fd-4185-ad1c-c34a9fe5ef44
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 068d63b7c96dd6729ebc46fb92a136be43b84385
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Выполнение заданий MapReduce с использованием пакета SDK для HDInsight .NET
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Узнайте, как отправлять задания MapReduce с использованием пакета SDK для HDInsight .NET. В кластерах HDInsight предусмотрен JAR-файл с несколькими примерами MapReduce. Этот JAR-файл — */example/jars/hadoop-mapreduce-examples.jar*.  Один из примеров — *wordcount* (подсчет слов). Вы разрабатываете консольное приложение на C# для отправки задания по подсчету слов.  Задание считывает файл */example/data/gutenberg/davinci.txt* и сохраняет результат в */example/data/davinciwordcount*.  Чтобы снова запустить приложение, необходимо очистить папку выходных данных.

> [!NOTE]
> Действия, описанные в этой статье, необходимо выполнять из клиента Windows. Чтобы получить сведения об использовании клиента Linux, OS X или Unix для работы с Hive, воспользуйтесь выбором вкладок в верхней части статьи.
> 
> 

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этой статьей необходимо иметь следующее:

* **Кластер Hadoop в HDInsight**. См. статью [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Visual Studio 2013, Visual Studio 2015, Visual Studio 2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Отправка заданий MapReduce с использованием пакета SDK для HDInsight .NET
Пакет SDK для HDInsight .NET содержит клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. 

**Отправка заданий**

1. Создайте в Visual Studio консольное приложение C#.
2. Введите следующую команду в окне консоли диспетчера пакетов NuGet:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```
3. Используйте следующий код:

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string existingClusterUsername = "<Cluster Username>";
            private const string existingClusterPassword = "<Cluster User Password>";

            private const string defaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";

            private const string sourceFile = "/example/data/gutenberg/davinci.txt";  
            private const string outputFolder = "/example/data/davinciwordcount";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);

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
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + 
                        defaultStorageAccountName + 
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); 
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }
    ```

4. Нажмите клавишу **F5** для запуска приложения.

Чтобы снова запустить задание, необходимо изменить имя выходной папки задания. В нашем примере это /example/data/davinciwordcount.

По завершении задания приложение печатает содержимое выходного файла part-r-00000.

## <a name="next-steps"></a>Дополнительная информация
В этой статье вы ознакомились с несколькими способами создания кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* Сведения об отправке задания Hive см. в инструкциях по [выполнению запросов Hive с помощью пакета SDK для HDInsight .NET](apache-hadoop-use-hive-dotnet-sdk.md).
* Сведения о создании кластеров HDInsight см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Сведения об управлении кластерами в HDInsight см. в статье [Управление кластерами Hadoop в HDInsight с помощью портала Azure](../hdinsight-administer-use-portal-linux.md).
* Подробные сведения о пакете SDK для HDInsight .NET см. в [соответствующей справке](https://msdn.microsoft.com/library/mt271028.aspx).
* Сведения о неинтерактивной проверке подлинности в Azure см. в статье [Создание приложений .NET HDInsight с неинтерактивной проверкой подлинности](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

