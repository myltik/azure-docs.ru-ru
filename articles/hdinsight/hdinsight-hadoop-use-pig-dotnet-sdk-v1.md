<properties
   pageTitle="Использование Hadoop Pig с .NET в HDInsight | Microsoft Azure"
   description="Информация об использовании пакета SDK для .NET для Hadoop для отправки заданий Pig в Hadoop в HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#Выполнение заданий Pig с помощью пакета SDK для .NET для Hadoop в HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

В этом документе приведен пример использования пакета SDK для .NET для Hadoop для отправки заданий Pig в Hadoop в кластере HDInsight.

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. Pig позволяет создавать операции MapReduce путем моделирования ряда преобразований данных. Вы узнаете, как использовать базовое приложение C# для отправки задания Pig в кластер HDInsight.

[AZURE.INCLUDE [azure-preview-portal](../../includes/hdinsight-azure-portal.md)]

* [Выполнение заданий Pig с помощью пакета SDK для .NET для Hadoop в HDInsight](hdinsight-hadoop-use-pig-dotnet-sdk.md)

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Windows или Linux)

* Visual Studio 2012 или 2013

##<a id="certificate"></a>Создание сертификата управления

Для аутентификации приложения в Azure HDInsight необходимо создать самозаверяющий сертификат, установить его на рабочей станции разработки и передать в подписку Azure.

Инструкции о том, как это сделать, см. в статье [Создание самозаверяющего сертификата](http://go.microsoft.com/fwlink/?LinkId=511138).

> [AZURE.NOTE]При создании сертификата, обязательно запомните использованное понятное имя, так как оно будет использоваться позднее.

##<a id="subscriptionid"></a>Поиск идентификатора подписки

Каждая подписка Azure определяется по значению GUID, известному как идентификатор подписки. Чтобы найти это значение, сделайте следующее.

1. Посетите [консоль управления Azure](https://manage.windowsazure.com/).

2. На панели в левой части портала выберите **Параметры**.

3. Среди информации в правой части страницы найдите подписку, которую необходимо использовать, и обратите внимание на значение в столбце **Идентификатор подписки**.

Сохраните идентификатор подписки, так как он будет использоваться позднее.

##<a id="create"></a>Создание приложения

1. Откройте Visual Studio 2012, 2013 или 2015.

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

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

7. В обозревателе решений дважды щелкните **Program.cs**, чтобы открыть файл. Замените имеющийся код следующим.

		using System;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace HDInsightSubmitPigJobsDotNet
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
					var ExistingClusterName = "<HDInsightClusterName>";
					var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
					var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
					var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
		
		            // The Pig Latin statements to run
		            string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";
		
		
		            HDInsightJobManagementClient _hdiJobManagementClient;
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            // Define the Pig job
		            var parameters = new PigJobSubmissionParameters()
		            {
		                UserName = ExistingClusterUsername,
		                Query = queryString,
		            };
		
		            System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            Console.WriteLine("Press ENTER to continue ...");
		            Console.ReadLine();
		        }
		    }
		}

7. Нажмите **F5** для запуска приложения.
8. Нажмите клавишу **ВВОД** для завершения работы приложения.

##<a id="summary"></a>Сводка

Как вы видите, пакет SDK для .NET для Hadoop позволяет создавать приложения .NET, которые будут отправлять задания Pig в кластер HDInsight, отслеживать состояние задания и получать выходные данные.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Pig в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=Oct15_HO3-->