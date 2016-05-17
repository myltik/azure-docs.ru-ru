<properties
   pageTitle="Использование Hadoop Pig с .NET в HDInsight | Microsoft Azure"
   description="Информация об использовании пакета SDK для .NET для Hadoop для отправки заданий Pig в Hadoop в HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/06/2016"
   ms.author="larryfr"/>

#Выполнение заданий Pig с помощью пакета SDK для .NET для Hadoop в HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

В этом документе приведен пример использования пакета SDK для .NET для Hadoop для отправки заданий Pig в Hadoop в кластере HDInsight.

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. Pig позволяет создавать операции MapReduce путем моделирования ряда преобразований данных. Вы узнаете, как использовать базовое приложение C# для отправки задания Pig в кластер HDInsight.

[AZURE.INCLUDE [azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Выполнение заданий Pig с помощью пакета SDK для .NET для Hadoop в HDInsight](hdinsight-hadoop-use-pig-dotnet-sdk-v1.md)

## Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Windows или Linux).
* Visual Studio 2012, Visual Studio 2013 или Visual Studio 2015.

## Поиск идентификатора подписки

Каждая подписка Azure определяется по значению GUID, известному как идентификатор подписки. Чтобы найти это значение, сделайте следующее.

1. Откройте [портал Azure][preview-portal].

2. На панели в левой части портала выберите __ПРОСМОТРЕТЬ ВСЕ__, а затем в колонке __Обзор__ выберите __Подписки__.

3. Среди информации в колонке __Подписки__ найдите подписку, которую необходимо использовать, и обратите внимание на значение в столбце **Идентификатор подписки**.

Сохраните идентификатор подписки, так как он будет использоваться позднее.

## Создание приложения

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
4. Нажмите кнопку **ОК**, чтобы создать проект.
5. В меню **Сервис** выберите пункт **Диспетчер пакетов библиотеки** или **Диспетчер пакетов Nuget**, а затем — **Консоль диспетчера пакетов**.
6. Выполните следующую команду в консоли, чтобы установить пакеты SDK для .NET.

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

7. В обозревателе решений дважды щелкните **Program.cs**, чтобы открыть файл. Замените имеющийся код следующим.

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

                    SubmitPigJob();

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
            }
        }


7. Нажмите **F5** для запуска приложения.
8. Нажмите клавишу **ВВОД** для завершения работы приложения.

## Сводка

Как вы видите, пакет SDK для .NET для Hadoop позволяет создавать приложения .NET, которые будут отправлять задания Pig в кластер HDInsight, отслеживать состояние задания и получать выходные данные.

## Дальнейшие действия

Общая информация о Pig в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->