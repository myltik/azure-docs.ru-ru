---
title: Выполнение заданий Apache Pig с помощью пакета SDK для .NET для Hadoop в Azure HDInsight | Документы Майкрософт
description: Информация об использовании пакета SDK для .NET для Hadoop для отправки заданий Pig в Hadoop в HDInsight.
services: hdinsight
documentationcenter: .net
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: fa11d49a-328c-47e7-b16d-e7ed2a453195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: larryfr
ms.openlocfilehash: 986e6fe0e71c4e1361814e22d89fa5121341cc79
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32771758"
---
# <a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>Выполнение заданий Pig с помощью пакета SDK для .NET для Hadoop в HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Узнайте, как использовать пакет SDK для .NET для Hadoop с целью отправки заданий Apache Pig в Hadoop в Azure HDInsight.

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. Pig позволяет создавать операции MapReduce путем моделирования ряда преобразований данных. В этом документе вы узнаете, как отправлять задания Pig в кластер HDInsight с помощью базового приложения C#.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Windows или Linux).

  > [!IMPORTANT]
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio 2012, 2013, 2015 или 2017.

## <a name="create-the-application"></a>Создание приложения

Пакет SDK для HDInsight .NET содержит клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET.

1. В Visual Studio откройте меню **Файл** и выберите **Создать**, а затем — **Проект**.

2. Для нового проекта введите или выберите следующие значения.

   | Свойство | Значение |
   | ------ | ------ |
   | Категория | Templates/Visual C#/Windows |
   | Шаблон | Консольное приложение |
   | ИМЯ | SubmitPigJob |

3. Нажмите кнопку **ОК** , чтобы создать проект.

4. В меню **Сервис** выберите **диспетчер пакетов библиотеки** или **диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**.

5. Чтобы установить пакеты SDK для .NET, выполните следующую команду:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. В обозревателе решений дважды щелкните **Program.cs** , чтобы открыть файл. Замените имеющийся код следующим.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitPigJob
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
                    Query = @"LOGS = LOAD '/example/data/sample.log';
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
    ```

7. Чтобы запустить приложение, нажмите клавишу **F5**.

8. Чтобы завершить работу приложения, нажмите клавишу **ВВОД**.

## <a name="next-steps"></a>Дополнительная информация

Сведения о Pig в HDInsight см. в разделе [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md).

Дополнительные сведения об использовании Hadoop в HDInsight см. в следующих статьях.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
