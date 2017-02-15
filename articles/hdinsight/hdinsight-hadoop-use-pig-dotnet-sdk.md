---
title: "Использование Hadoop Pig с .NET в HDInsight | Документация Майкрософт"
description: "Информация об использовании пакета SDK для .NET для Hadoop для отправки заданий Pig в Hadoop в HDInsight."
services: hdinsight
documentationcenter: .net
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: fa11d49a-328c-47e7-b16d-e7ed2a453195
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/17/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 46acf298a489aae34866c90126f3df00fd10c6e3


---
# <a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>Выполнение заданий Pig с помощью пакета SDK для .NET для Hadoop в HDInsight
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

В этом документе приведен пример использования пакета SDK для .NET для Hadoop для отправки заданий Pig в Hadoop в кластере HDInsight.

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. Pig позволяет создавать операции MapReduce путем моделирования ряда преобразований данных. Вы узнаете, как использовать базовое приложение C# для отправки задания Pig в кластер HDInsight.

## <a name="prerequisites"></a>Предварительные требования
Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Windows или Linux).
* Visual Studio 2012, Visual Studio 2013 или Visual Studio 2015.

## <a name="create-the-application"></a>Создание приложения
Пакет SDK для HDInsight .NET содержит клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. 

1. Откройте Visual Studio 2012 или 2013
2. В меню **Файл** выберите **Создать**, а затем — **Проект**.
3. Для нового проекта введите или выберите следующие значения.
   
    <table>
    <tr>
    <th>Свойство</th>
    <th>Значение</th>
    </tr>
    <tr>
    <th>Категория</th>
    <th>Templates/Visual C#/Windows</th>
    </tr>
    <tr>
    <th>Шаблон</th>
    <th>Консольное приложение</th>
    </tr>
    <tr>
    <th>Имя</th>
    <th>SubmitPigJob</th>
    </tr>
    </table>
4. Нажмите кнопку **ОК** , чтобы создать проект.
5. В меню **Сервис** выберите пункт **Диспетчер пакетов библиотеки** или **Диспетчер пакетов Nuget**, а затем — **Консоль диспетчера пакетов**.
6. Выполните следующую команду в консоли, чтобы установить пакеты SDK для .NET.
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
7. В обозревателе решений дважды щелкните **Program.cs** , чтобы открыть файл. Замените имеющийся код следующим.
   
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
   
                    SubmitPigJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitPigJob()
                {
                    var parameters = new PigJobSubmissionParameters
                    {
                        Query = @"LOGS = LOAD 'wasbs:///example/data/sample.log';
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
            }
        }
8. Нажмите **F5** для запуска приложения.
9. Нажмите клавишу **ВВОД** для завершения работы приложения.

## <a name="summary"></a>Сводка
Как вы видите, пакет SDK для .NET для Hadoop позволяет создавать приложения .NET, которые будут отправлять задания Pig в кластер HDInsight и отслеживать состояние задания.

## <a name="next-steps"></a>Дальнейшие действия
Общая информация о Pig в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Nov16_HO3-->


