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
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Выполнение заданий Pig с помощью пакета SDK для .NET для Hadoop в HDInsight

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

В этом документе приведен пример использования пакета SDK для .NET для Hadoop для отправки заданий Pig в Hadoop в кластере HDInsight.

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. Pig позволяет создавать операции MapReduce путем моделирования ряда преобразований данных. Вы узнаете, как использовать базовое приложение C# для отправки задания Pig в кластер HDInsight.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Windows или Linux)

* Visual Studio 2012 или 2013

##<a id="certificate"></a>Создание сертификата управления

Для аутентификации приложения в Azure HDInsight необходимо создать самозаверяющий сертификат, установить его на рабочей станции разработки и передать в подписку Azure.

Инструкции о том, как это сделать, см. в разделе <a href="http://go.microsoft.com/fwlink/?LinkId=511138" target="_blank">Создание самозаверяющего сертификата</a>.

> [AZURE.NOTE]При создании сертификата, обязательно запомните использованное понятное имя, так как оно будет использоваться позднее.

##<a id="subscriptionid"></a>Поиск идентификатора подписки

Каждая подписка Azure определяется по значению GUID, известному как идентификатор подписки. Чтобы найти это значение, сделайте следующее.

1. Посетите <a href="https://manage.windowsazure.com/" target="_blank">консоль управления Azure</a>.

2. На панели в левой части портала выберите **Параметры**.

3. Среди информации в правой части страницы найдите подписку, которую необходимо использовать, и обратите внимание на значение в столбце **Идентификатор подписки**.

Сохраните идентификатор подписки, так как он будет использоваться позднее.

##<a id="create"></a>Создание приложения

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

		Install-Package Microsoft.Windowsazure.Management.HDInsight

7. В обозревателе решений дважды щелкните **Program.cs**, чтобы открыть файл. Замените имеющийся код следующим.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		
		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;
		
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;
		
		namespace SubmitPigJob
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            // Get the subscription ID
		            string subscriptionID = PromptForInput("Enter your Azure Subscription ID:");

		            // Get the certificate name
		            string certFriendlyName = PromptForInput("Enter the management certificate name:");
		
		            // Get the cluster name
		            string clusterName = PromptForInput("Enter the HDInsight cluster name:");
		
		            // Set the folder that job status is written to
		            string statusFolderName = @"/tutorials/usepig/status";
		
		            // The Pig Latin statements to run
		            string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";
		
		            // Define the Pig job
		            PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
		            {
		                Query = queryString,
		                StatusFolder = statusFolderName
		            };
		
		            // Get the certificate object from certificate store using the friendly name to identify it
		            X509Store store = new X509Store();
		            store.Open(OpenFlags.ReadOnly);
		            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
		
		            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
		            // Create a hadoop client to connect to HDInsight
		            var jobClient = JobSubmissionClientFactory.Connect(creds);
		
		            // Run the MapReduce job
		            Console.WriteLine("----- Submit the Pig job ...");
		            JobCreationResults mrJobResults = jobClient.CreatePigJob		(myJobDefinition);
		
		            // Wait for the job to complete
		            Console.WriteLine("----- Wait for the Pig job to complete ...");
		            WaitForJobCompletion(mrJobResults, jobClient);
		
		            // Display the error log
		            Console.WriteLine("----- The Pig job error log.");
		            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }
		
		            // Display the output log
		            Console.WriteLine("----- The Pig job output log.");
		            using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }
		
		            Console.WriteLine("----- Press ENTER to continue.");
		            Console.ReadLine();
		        }
		
		        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
		        {
		            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
		            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
		            {
		                jobInProgress = client.GetJob(jobInProgress.JobId);
		                Thread.Sleep(TimeSpan.FromSeconds(10));
		            }
		        }
		
		        private static string PromptForInput(string message)
		        {
		            Console.WriteLine(message);
		            return Console.ReadLine();
		        }
		    }
		}


7. Сохраните файл.

##<a id="run"></a>Выполнение приложения

Используйте **F5** для запуска приложения. При появлении запроса введите **идентификатор подписки**, **понятное имя сертификата** и **имя кластера HDInsight**. Приложение создаст несколько строк данных во время выполнения, добавив в конце нечто похожее на следующее.

```
----- The Pig job output log.
(TRACE,816)
(DEBUG,434)
(INFO,96)
(WARN,11)
(ERROR,6)
(FATAL,2)

----- Press ENTER to continue.
```

Нажмите клавишу **ВВОД** для завершения работы приложения.

##<a id="summary"></a>Сводка

Как вы видите, пакет SDK для .NET для Hadoop позволяет создавать приложения .NET, которые будут отправлять задания Pig в кластер HDInsight, отслеживать состояние задания и получать выходные данные.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Pig в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=54-->