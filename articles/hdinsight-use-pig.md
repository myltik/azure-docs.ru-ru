<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="Use Hadoop Pig in HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Pig in HDInsight" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"/>

# Использование Pig с Hadoop в HDInsight

Узнайте, как выполнять задания [Apache Pig][] в HDInsight для анализа файла журнала Apache log4j.

**Предполагаемое время выполнения:** 30 минут

## Содержание

-   [Пример использования Pig][]
-   [Предварительные требования][]
-   [Общие сведения о Pig Latin][]
-   [Отправка заданий Pig с использованием PowerShell][]
-   [Отправка заданий Pig с использованием пакета SDK для HDInsight][]
-   [Дальнейшие действия][]

## <span id="usage"></span></a>Пример использования Pig

Базы данных подходят для управления небольшими наборами данных, для которых допустимы запросы с небольшой задержкой. Однако когда дело доходит до данных большого размера и больших наборов данных, занимающих терабайты, традиционные базы данных SQL не являются идеальным решением. По мере возрастания нагрузки на базы данных и снижения производительности администраторы баз данных должны покупать больше оборудования.

Как правило, все приложения сохраняют ошибки, исключения и другие проблемы с кодом в файл журнала, поэтому администраторы могут оценивать проблемы или создавать определенные показатели на основе данных файла журнала. Эти файлы журнала, как правило, становятся очень большими, так как содержат огромные массивы данных, которые должны быть обработаны и исследованы.

Файлы журналов являются хорошим примером данных большого размера. Работа с данными большого размера становится очень сложной при использовании реляционных баз данных и пакетов для статистики и визуализации. Вследствие больших объемов данных и вычислений, связанных с этими данными, распараллеленное программное обеспечение, работающее на десятках, сотнях и даже тысячах серверов часто становится необходимым для расчета этих данных в приемлемые сроки. Hadoop предоставляет инфраструктуру MapReduce для создания приложений, которые обрабатывают большие объемы структурированных и неструктурированных данных посредством параллельных вычислений в больших компьютерных кластерах, отличаясь при этом большой надежностью и отказоустойчивостью.

[Apache *Pig*][Apache Pig] представляет собой язык написания сценариев для выполнения заданий *MapReduce* в качестве альтернативы для написания кода Java. Язык написания сценариев Pig называется *Pig Latin*. Инструкции Pig Latin соответствуют следующей схеме процессов:

-   **Загрузка**: чтение данных для обработки в файловой системе
-   **Преобразование**: обработка данных
-   **Дамп или сохранение**: вывод данных на экран или сохранение их для дальнейшей обработки

Использование Pig снижает время, необходимое для написания программ сопоставлений и сведения. Это означает, что использование языка Java не требуется и отсутствует потребность в шаблонном коде. Вы также получаете гибкость для объединения кода Java с языком Pig. Многие сложные алгоритмы могут быть написаны с использованием менее чем пяти строчек кода Pig. доступного для понимания человеком.

Визуальное представление задач, которые будут решены по ознакомлению с этой статьей, приведены на следующих двух рисунках. На этих рисунках представлен репрезентативный пример набора данных, что демонстрирует рабочий процесс и преобразования данных по строкам кода сценария Pig. На первом рисунке приведен пример файла log4j:

![Пример всего файла][]

На втором рисунке показано преобразование данных:

![HDI.PIG.Data.Transformation][]

Дополнительные сведения о Pig Latin см. в [справочном руководстве по Pig Latin 1][] и [справочном руководстве по Pig Latin 2][].

## <span id="prerequisites"></span></a>Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

-   Кластер Azure HDInsight. Инструкции см. в разделе [Приступая к работе с Azure HDInsight][] или [Подготовка кластеров HDInsight][]. Для выполнения учебника необходимы следующие данные:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Свойство кластера</th>
    <th align="left">Имя переменной PowerShell</th>
    <th align="left">Значение</th>
    <th align="left">Описание</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Имя кластера HDInsight.</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Кластер HDInsight, на котором будет выполняться данный учебник.</td>
    </tr>
    </tbody>
    </table>

-   Установите и настройте Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][].

**Общие сведения о хранилище HDInsight**

HDInsight использует для хранения данных хранилище BLOB-объектов Azure. Оно называется *WASB* или *Хранилище Azure — BLOB-объекты*. WASB — это реализация HDFS на базе хранилища BLOB-объектов Azure от корпорации Майкрософт. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][].

В процессе подготовки кластера HDInsight в качестве файловой системы по умолчанию устанавливаются учетная запись хранения Azure и конкретный контейнер хранилища BLOB-объектов из этой учетной записи, так же как и в HDFS. Помимо этой учетной записи хранения в процессе подготовки можно добавить дополнительные учетные записи хранения из той же подписки Azure или других подписок Azure. Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров HDInsight][]. Чтобы упростить скрипт PowerShell, используемый в этом учебнике, все файлы хранятся в контейнере файловой системы по умолчанию, расположенном в */tutorials/usepig*. Имя контейнера по умолчанию совпадает с именем кластера HDInsight.
 Синтаксис WASB:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Кластер HDInsight версии 3.0 поддерживает только синтаксис *wasb://*. Прежний синтаксис *asv://* поддерживается в кластерах HDInsight 2.1 и 1.6, но не поддерживается в кластерах HDInsight 3.0 и не будет поддерживаться в последующих версиях..

> [WACOM.NOTE] Путь WASB является виртуальным. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][].

Доступ к файлу, хранящемуся в контейнере файловой системы по умолчанию, из HDInsight может осуществляться с помощью любого из следующих URI (sample.log используется в качестве примера): Это файл данных, который используется в учебнике):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Если требуется доступ к этому файлу непосредственно из учетной записи хранения, имя BLOB-объекта для файла имеет следующее значение:

    example/data/sample.log

В этой статье будет использован образец файла log4j, который входит в состав кластера HDInsight и хранится по пути *\\example\\data\\sample.log* Сведения о передаче файлов данных см. в разделе [Передача данных в HDInsight][].

## <span id="understand"></span></a> Общие сведения о Pig Latin

На этом занятии будут рассмотрены некоторые инструкции Pig Latin, а также результаты выполнения этих инструкций. На следующем занятии вы запустите PowerShell для выполнения инструкций Pig.

1.  Загрузка данных из файловой системы и отображение результатов

        LOGS = LOAD 'wasb:///example/data/sample.log';
        DUMP LOGS;

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

2.  Просмотрите каждую строку в файле данных для поиска соответствия с 6 уровням ведения журналов:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

3.  Отфильтруйте строки, которые не имеют соответствий. Например, пустые строки.

        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        DUMP FILTEREDLEVELS;

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

4.  Сгруппируйте все уровни ведения журналов по отдельным строкам:

        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        DUMP GROUPEDLEVELS;

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

5.  Для каждой группы вычислите число вхождений каждого уровня ведения журнала. Это частоты возникновения каждого уровня ведения журнала.

        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        DUMP FREQUENCIES;

    Выход аналогичен приведенному ниже:

        (INFO,3355)
        (WARN,361)
        (DEBUG,15608)
        (ERROR,181)
        (FATAL,37)
        (TRACE,29950)

6.  Отсортируйте частоты возникновения в порядке убывания:

        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;   

    Выход аналогичен приведенному ниже:

        (TRACE,29950)
        (DEBUG,15608)
        (INFO,3355)
        (WARN,361)
        (ERROR,181)
        (FATAL,37)

## <span id="powershell"></span></a>Отправка заданий Pig с использованием PowerShell

В этом разделе приведены инструкции по использованию командлетов PowerShell. Прежде чем приступить к работе с этим разделом, необходимо сначала настроить локальную среду, а затем настроить подключение к Azure. Подробные сведения см. в разделах [Приступая к работе с Azure HDInsight][] и [Администрирование HDInsight с помощью PowerShell][].

**Выполнение Pig Latin с помощью PowerShell**

1.  Откройте интегрированную среду сценариев Windows PowerShell (на начальном экране Windows 8 введите **PowerShell\_ISE** и затем щелкните элемент **Интегрированная среда сценариев Windows PowerShell**. См. раздел [Запуск Windows PowerShell в Windows 8 и Windows][]).
2.  В нижней области выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

    Появится запрос на ввод учетных данных учетной записи Azure. Этот метод добавления подключения подписки имеет срок действия, и после 12 часов вам потребуется снова запустить командлет.

    > [WACOM.NOTE] Если имеется несколько подписок Azure и должна использоваться подписка, отличная от подписки по умолчанию, воспользуйтесь командлетом **Select-AzureSubscription** для выбора текущей подписки.

3.  В области скрипта скопируйте и вставьте следующий код:

        $clusterName = "<HDInsightClusterName>" 
        $statusFolder = "/tutorials/usepig/status"

4.  Задайте переменную $clusterName.

5.  Добавьте следующие строки в область скрипта. Эти строки определяю строку запроса Pig Latin, а также создают описание задания Pig:

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

    Можно использовать параметр -File, чтобы указать файл сценария Pig в HDFS. Переключатель StatusFolder помещает журнал стандартной ошибки и стандартный выходной файл в папку.

6.  Добавьте следующие строки для отправки задания Pig:

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

7.  Добавьте следующие строки, чтобы дождаться завершения задания Pig:

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8.  Добавьте следующие строки, чтобы напечатать выход задания Pig:

        # Print the standard error and the standard output of the Pig job.
        #Write-Host "Display the standard error log ..." -ForegroundColor Green
        #Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] Один из командлетов Get-AzureHDInsightJobOut закомментирован для сокращения выхода на следующем снимке экрана.

9.  Нажмите клавишу **F5** для запуска скрипта:
    ![HDI.Pig.PowerShell][]

    Задание Pig вычисляет частоту возникновения различных типов ведения журнала.

## <span id="sdk"></span></a>Отправка заданий Pig с использованием пакета SDK для HDInsight

Ниже приводится пример отправки задания Pig с использованием пакета SDK для HDInsight .NET. Указания по созданию приложения C# для отправки заданий Hadoop см. в разделе: [Отправка заданий Hadoop программными средствами][].

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

## <span id="nextsteps"></span></a>Дальнейшие действия

Pig позволяет выполнять анализ данных, однако другие языки, поддерживаемые HDInsight, могут также заинтересовать вас. Hive представляет собой язык запросов, похожий на SQL, который позволяет без труда выполнять запросы данных в HDInsight, а задания MapReduce, написанные на языке Java, позволяют выполнять анализ сложных данных. Дополнительные сведения см. в следующих разделах.

-   [Приступая к работе с Azure HDInsight][]
-   [Отправка данных в HDInsight][Передача данных в HDInsight]
-   [Отправка заданий Hadoop программными средствами][]
-   [Использование Hive с HDInsight][]

  [Apache Pig]: http://pig.apache.org/
  [Пример использования Pig]: #usage
  [Предварительные требования]: #prerequisites
  [Общие сведения о Pig Latin]: #understand
  [Отправка заданий Pig с использованием PowerShell]: #powershell
  [Отправка заданий Pig с использованием пакета SDK для HDInsight]: #sdk
  [Дальнейшие действия]: #nextsteps
  [Пример всего файла]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [справочном руководстве по Pig Latin 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [справочном руководстве по Pig Latin 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [Приступая к работе с Azure HDInsight]: ../hdinsight-get-started/
  [Подготовка кластеров HDInsight]: ../hdinsight-provision-clusters/
  [Установка и настройка Azure PowerShell]: ../install-configure-powershell/
  [Использование хранилища BLOB-объектов Azure с HDInsight]: ../hdinsight-use-blob-storage/
  [Передача данных в HDInsight]: ../hdinsight-upload-data/
  [Администрирование HDInsight с помощью PowerShell]: ../hdinsight-administer-use-powershell/
  [Запуск Windows PowerShell в Windows 8 и Windows]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [Отправка заданий Hadoop программными средствами]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [Использование Hive с HDInsight]: ../hdinsight-use-hive/
