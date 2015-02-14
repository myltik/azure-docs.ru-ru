<properties 
	pageTitle="Использование Hadoop Pig в HDInsight для платформы Azure" 
	description="Вы узнаете, как использовать Pig в HDInsight. Вы сможете создать операторы на Pig Latin для анализа файла журнала приложений и выполнения запросов данных с целью анализа результатов." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="jgao"/>



# Использование Pig с Hadoop в HDInsight

В этом учебнике описано, как проводить анализ массивов данных с помощью заданий  [Apache Pig][apachepig-home] в HDInsight. Pig предоставляет язык написания сценариев для выполнения заданий  *MapReduce* в качестве альтернативы для написания кода Java. Язык написания сценариев Pig называется *Pig Latin*.

**Предварительные требования**

* Для работы вам понадобится кластер Azure HDInsight. Инструкции см. в разделе [Приступая к работе с Azure HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight][hdinsight-provision]. Для завершения обучающего задания вам понадобится имя кластера.

* Вам понадобится установленная среда Azure PowerShell на рабочей станции. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure]. 

**Предполагаемое время выполнения:** 30 минут

## Содержание

* [Почему именно Pig?](#usage)
* [Что я буду делать в этом руководстве?](#what)
* [Определение данных для анализа](#data)
* [Общие сведения о Pig Latin](#understand)
* [Отправка заданий Pig с использованием PowerShell](#powershell)
* [Отправка заданий Pig с использованием пакета SDK для HDInsight](#sdk)
* [Дальнейшие действия](#nextsteps)
 
## <a id="usage"></a>Почему именно Pig?
Работа с данными большого размера становится очень сложной при использовании реляционных баз данных и пакетов для статистики и визуализации. Вследствие больших объемов данных и вычислений, связанных с этими данными, распараллеленное программное обеспечение, работающее на десятках, сотнях и даже тысячах серверов часто становится необходимым для расчета этих данных в приемлемые сроки. Hadoop предоставляет инфраструктуру *MapReduce* для создания приложений, которые обрабатывают большие объемы структурированных и неструктурированных данных посредством параллельных вычислений в больших компьютерных кластерах. При этом они отличаются высокой надежностью и отказоустойчивостью.

![HDI.Pig.Architecture][image-hdi-pig-architecture]

[Apache *Pig*][apachepig-home] обеспечивает необходимый уровень абстрагирования при работе с каркасами приложения MapReduce на основе Java, позволяя пользователям, не владеющим Java или MapReduce, проводить анализ данных. Pig - это платформа для проведения анализа массивов данных с помощью простого в использовании потокового языка *Pig Latin*. Использование Pig снижает время, необходимое для написания программ сопоставления и редукторов. Для использования платформы не требуется знание Java. Вы также получаете гибкость для объединения кода Java с языком Pig. Многие сложные алгоритмы могут быть написаны с использованием менее чем пяти строчек кода Pig. доступного для понимания человеком. 

Инструкции Pig Latin соответствуют следующей схеме процессов:   

- **Загрузка**: чтение данных для обработки в файловой системе
- **Преобразование**: обработка данных 
- **Дамп или сохранение**: вывод данных на экран или сохранение их для дальнейшей обработки

Дополнительные сведения о Pig Latin см. в [справочном руководстве по Pig Latin 1][piglatin-manual-1] и [справочном руководстве по Pig Latin 2][piglatin-manual-2].

## <a id="what"></a>Что я буду делать в этом руководстве?
В этом руководстве вы будете анализировать файл журнала Apache (*sample.log*) для выявления разных уровней журнала, таких как INFO, DEBUG, TRACE и т. д. Результаты по итогам занятия будут наглядно представлены на двух рисунках. На первом рисунке приведен фрагмент файла sample.log.

![Whole File Sample][image-hdi-log4j-sample]

Второй рисунок демонстрирует поток данных и преобразование данных по мере того, как вы продвигаетесь дальше по коду сценария Pig.

![HDI.PIG.Data.Transformation][image-hdi-pig-data-transformation]

Задача, которую вам предстоит создать в этом руководстве, будет выполнена в этом же потоке.

## <a id="data"></a>Определение данных для анализа

HDInsight в качестве файловой системы по умолчанию использует хранилище BLOB-объектов Azure для кластеров Hadoop. Во время распределения кластеров несколько файлов-примеров с данными были добавлены в хранилище BLOB-объектов. Вы можете использовать эти файлы-примеры для создания запросов Hive в пределах кластера. При желании вы можете загрузить свой файл в учетную запись хранилища BLOB-объектов для этого кластера. См. раздел [Отправка данных в HDInsight][hdinsight-upload-data] для получения более подробной информации. Дополнительные сведения о том, каким образом хранилище BLOB-объектов Azure используется с HDInsight, см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].

Для доступа к хранилищу BLOB-объектов синтаксис должен быть следующим:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] Кластер HDInsight версии 3.0 поддерживает только синтаксис *wasb://*. Прежний синтаксис *asv://* поддерживается в кластерах HDInsight 2.1 и 1.6, но не поддерживается в кластерах HDInsight 3.0 и не будет поддерживаться в последующих версиях.

Доступ к файлу, хранящемуся в контейнере файловой системы по умолчанию, из HDInsight может также осуществляться с помощью любого из следующих URI (sample.log используется в качестве примера).  Это файл данных, который используется в учебнике):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

Если требуется доступ к этому файлу непосредственно из учетной записи хранения, имя BLOB-объекта для файла имеет следующее значение:

	example/data/sample.log

В статье используется файл-пример *log4j*, который устанавливается вместе с кластерами и размещается в директории *\example\data\sample.log*. FСведения о передаче файлов данных см. в разделе [Передача данных в HDInsight][hdinsight-upload-data].




## <a id="understand"></a> Общие сведения о Pig Latin

В этом разделе будут рассмотрены некоторые отдельные операторы на Pig Latin, а также результаты выполнения этих операторов. В следующем разделе вы будете использовать PowerShell для выполнения операторов на Pig и анализа файл-пример журнала. Отдельные операторы Pig Latin должны выполняться в кластере HDInsight.

1. Запустите протокол удаленного рабочего стола для кластера HDInsight, следуя инструкциям раздела [Соединение с кластерами HDInsight с использованием протокола RDP](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-administer-use-management-portal/#rdp). Войдите в узел кластера и щелкните элемент **Командная строка Hadoop** на рабочем столе.

2. С помощью командной строки перейдите в каталог, в который производилась установка **Pig**. Введите:

		C:\apps\dist\hadoop-<version>> cd %pig_home%\bin

3. В командной строке введите *pig* и нажмите клавишу ВВОД, чтобы перейти к оболочке *grunt*.

		C:\apps\dist\pig-<version>\bin>pig
		...
		grunt>  

4. Введите следующее, чтобы загрузить данные из файла-примера в файловой системе, а после отобразите результаты: 

		grunt> LOGS = LOAD 'wasb:///example/data/sample.log';
		grunt> DUMP LOGS;
	
	Выход аналогичен приведенному ниже:
	
		(2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
		(2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
		(2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
		(2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
		(2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
		(2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
		...

5. Просмотрите каждую строку в файле данных для поиска соответствия с 6 уровням ведения журналов:

		grunt> LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
 
6. Отфильтруйте строки, которые не имеют соответствий, и отобразите результаты. Избавьтесь от пустых строк.

		grunt> FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		grunt> DUMP FILTEREDLEVELS;

	Выход аналогичен приведенному ниже:

		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		...

7. Сгруппируйте все уровни журнала по отдельным строкам и отобразите результат:

		grunt> GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		grunt> DUMP GROUPEDLEVELS;

	Выход аналогичен приведенному ниже:

		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE), ...


8. Для каждой группы вычислите число вхождений каждого уровня журнала и отобразите результаты:

		grunt> FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		grunt> DUMP FREQUENCIES;
 
	Выход аналогичен приведенному ниже:

		(INFO,96)
		(WARN,11)
		(DEBUG,434)
		(ERROR,6)
		(FATAL,2)
		(TRACE,816)

9. Установите сортировку по убывающей частотности и отобразите результаты:

		grunt> RESULT = order FREQUENCIES by COUNT desc;
		grunt> DUMP RESULT;   

	Выход аналогичен приведенному ниже: 

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
	 	(ERROR,6)
		(FATAL,2)

## <a id="powershell"></a>Отправка заданий Pig с использованием PowerShell

В этом разделе приведены инструкции по использованию командлетов PowerShell. Прежде чем приступить к работе с этим разделом, необходимо сначала настроить локальную среду, а затем настроить подключение к Azure. Подробные сведения см. в разделах [Приступая к работе с Azure HDInsight][hdinsight-get-started] и [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell].


**Выполнение Pig Latin с помощью PowerShell**

1. Откройте интегрированную среду сценариев Windows PowerShell. На начальном экране Windows 8 введите **PowerShell_ISE** и нажмите **Windows PowerShell ISE**. См. раздел [Запуск Windows PowerShell в Windows 8 и Windows][powershell-start] для получения более подробной информации.

2. В нижней области выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure. Этот метод добавления подключения подписки имеет срок действия, и после 12 часов вам потребуется снова запустить командлет. 

	> [AZURE.NOTE] Если имеется несколько подписок Azure и должна использоваться подписка, отличная от подписки по умолчанию, воспользуйтесь командлетом  <strong>Select-AzureSubscription</strong> для выбора текущей подписки.
2. В области скрипта скопируйте и вставьте следующий код:

		$clusterName = "<HDInsightClusterName>" 	#Specify the cluster name
		$statusFolder = "/tutorials/usepig/status"	#Specify the folder to dump results

	Если папки статуса, которую вы указали, не существует, сценарий создаст ее самостоятельно. 

3. Добавьте следующие строки в область скрипта. Эти строки определяю строку запроса Pig Latin, а также создают описание задания Pig:

		# Create the Pig job definition
		$0 = '$0';
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;" 
		
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder 

	Можно использовать параметр **-File** чтобы указать файл сценария Pig в HDFS. Переключатель **-StatusFolder** помещает журнал стандартной ошибки и стандартный выходной файл в папку.

4. Добавьте следующие строки для отправки задания Pig:
		
		# Submit the Pig job
		Write-Host "Submit the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

5. Добавьте следующие строки, чтобы дождаться завершения задания Pig:		

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

6. Добавьте следующие строки, чтобы напечатать выход задания Pig:
		
		# Print the standard error and the standard output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

	> [AZURE.NOTE] Один из командлетов Get-AzureHDInsightJobOut закомментирован для сокращения выхода на следующем снимке экрана.   

7. Нажмите клавишу **F5** для запуска сценария:

	![HDI.Pig.PowerShell][image-hdi-pig-powershell]

	Задание Pig вычисляет частоту возникновения различных типов ведения журнала.


## <a id="sdk"></a>Отправка заданий Pig с использованием пакета SDK для HDInsight

Выполните все описанные здесь действия для загрузки Pig с помощью приложения на C#.   Указания по созданию приложения C# для отправки заданий Hadoop см. в разделе [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs].

1. Создание самозаверяющего сертификата, установка его на рабочую станцию и загрузка на вашу подписку Azure. Инструкции см. в разделе [Создание самозаверяющего сертификата](http://go.microsoft.com/fwlink/?LinkId=511138).

2. Создайте консольное приложение Visual Studio и установите пакет программ HDInsight. В меню "Средства" щелкните **Диспетчер пакетов Nuget**, а затем - **Консоль диспетчера пакетов**. После всплытия подсказки введите следующее:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

3. Дважды кликните по Program.cs и вставьте следующий код для загрузки задания Pig. Введите значения для всех переменных.

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
		
		namespace SubmitPigJobs
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
					// Set the variables
					string subscriptionID = "<Azure subscription ID>";
					string certFriendlyName = "<certificate friendly name>";
			
					string clusterName = "<HDInsight cluster name>";
		            string statusFolderName = @"/tutorials/usepig/status";
		
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
		            JobCreationResults mrJobResults = jobClient.CreatePigJob(myJobDefinition);
		
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
		    }
		}

## <a id="nextsteps"></a>Дальнейшие действия

Pig позволяет выполнять анализ данных, однако другие языки, поддерживаемые HDInsight, могут также заинтересовать вас. Hive представляет собой язык запросов, похожий на SQL, который позволяет без труда выполнять запросы данных в HDInsight, а задания MapReduce, написанные на языке Java, позволяют выполнять анализ сложных данных. Дополнительные сведения см. в следующих разделах.


* [Приступая к работе с Azure HDInsight][hdinsight-get-started]
* [Передача данных в HDInsight][hdinsight-upload-data]
* [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs]
* [Использование Hive с HDInsight][hdinsight-use-hive]



[piglatin-manual-1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
[piglatin-manual-2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
[apachepig-home]: http://pig.apache.org/


[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk

[Powershell-install-configure]: ../install-configure-powershell/

[powershell-start]: http://technet.microsoft.com/ru-ru/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png

<!--HONumber=42-->
