<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="Пример счетчика слов на C# для потоковой передачи в HDInsight | Azure" metaKeywords="hadoop, hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run a sample TBD." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The C# streaming wordcount Hadoop sample in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />

# Пример счетчика слов на C# для потоковой передачи в HDInsight
 
Hadoop предоставляет API-интерфейс для MapReduce, позволяющий создавать функции map и reduce на языках, отличных от Java. В этом учебнике показано, как создавать программы MapReduce на C# с использованием интерфейса потоковой передачи Hadoop и как выполнять программы в Azure HDInsight с использованием командлетов Azure PowerShell. 

В приведенном примере процедура map и процедура reduce являются исполняемыми файлами, которые считывают входные данные из [stdin][stdin-stdout-stderr] (построчно) и выводят выходные данные в [stdout][stdin-stdout-stderr]. Программа подсчитывает все слова в тексте.

Если указан исполняемый файл для **процедур map**, то при инициализации процедуры map каждая задача процедуры map запускает исполняемый файл как отдельный процесс. При выполнении задачи процедуры map входные данные преобразуются в строки, а строки подаются в [stdin][stdin-stdout-stderr] процесса. В то же время процедура map собирает связанные со строками выходные данные из stdout процесса и преобразует каждую строку в пару "ключ-значение", которые собираются в качестве выходных данных процедуры map. По умолчанию префикс строки до первого символа табуляции является ключом, а остальная часть строки (исключая символ табуляции) - это значение. Если в строке нет символа табуляции, вся строка рассматривается как ключ, а значение считается нулевым. 

Если указан исполняемый файл для **процедур reduce**, то при инициализации процедуры reduce каждая задача процедуры reduce запускает исполняемый файл как отдельный процесс. При выполнении задачи процедуры reduce входные пары "ключ-значение" преобразовываются в строки, а строки подаются в [stdin][stdin-stdout-stderr] процесса. В то же время процедура reduce собирает связанные со строками выходные данные из [stdout][stdin-stdout-stderr] процесса и преобразует каждую строку в пару "ключ-значение", которые собираются в качестве выходных данных процедуры reduce. По умолчанию префикс строки до первого символа табуляции является ключом, а остальная часть строки (исключая символ табуляции) - это значение. 

Дополнительную информацию об интерфейсе потоковой передачи Hadoop см. в разделе [Потоковая передача Hadoop][hadoop-streaming]. 
 
**Вы узнаете:**	
	
* Как использовать Azure PowerShell для выполнения программы потоковой передачи на C# для анализа данных, содержащихся в файле в HDInsight.		
* Как написать код на C#, использующий интерфейс потоковой передачи Hadoop.


**Предварительные требования**:	

- Необходимо иметь учетную запись Azure. Возможности регистрации учетной записи см. в разделе [Бесплатное пробное использование Azure].(http://azure.microsoft.com/ru-ru/pricing/free-trial/) .

- Вы должны были подготовить кластер HDInsight. Сведения по различным способам создания таких кластеров см. в разделе [Подготовка кластеров HDInsight](../hdinsight-provision-clusters/)

- Необходимо установить Azure PowerShell и настроить эту среду для поддержки вашей учетной записи. Указания по выполнению этой задачи см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].


##Содержание
В этом разделе демонстрируется выполнение примера, предоставляется код Java для программы MapReduce, обобщаются полученные знания и кратко описываются некоторые дальнейшие действия. Здесь содержатся следующие разделы:
	
1. [Выполнение примера с использованием Azure PowerShell](#run-sample)	
2. [Код C# для потоковой передачи Hadoop](#java-code)
3. [Сводка](#summary)	
4. [Дальнейшие действия](#next-steps)	

<h2><a id="run-sample"></a>Выполнение примера с использованием Azure PowerShell</h2>

**Выполнение задания MapReduce**

1.	Откройте **Azure PowerShell**. Указания по открытию окна консоли Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

3. Задайте две переменные в следующих командах, а затем выполните команды:
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


2. Выполните следующую команду, чтобы определить задание MapReduce:
 
		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

	Параметры задают функции сопоставления и редукции, а также входной и выходной файлы.
                 
5. Выполните следующие команды, чтобы запустить задание MapReduce, ждать завершения задания, а затем напечатать стандартную ошибку:

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

6. Выполните следующие команды для отображения результатов подсчета слов:

		$subscriptionName = "<SubscriptionName>"   
		$storageAccountName = "<StorageAccountName>" 
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
              
		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $_.Primary }
      $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
 
		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force 

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	Обратите внимание, что выходные файлы задания MapReduce являются неизменяемыми. Поэтому при повторном выполнении этого примера потребуется изменить имя выходного файла.
	
<h2><a id="java-code"></a>Код C# для потоковой передачи Hadoop</h2>

В программе MapReduce используются приложение cat.exe как интерфейс процедуры map для потоковой передачи текста на консоль и приложение wc.exe как интерфейс процедуры reduce для подсчета количества слов, передаваемых в потоковом режиме из документа. Модуль сопоставления и модуль редукции считывают символы (построчно) из стандартного входного потока (stdin) и записывают данные в стандартный выходной поток (stdout). 



	// The source code for the cat.exe (Mapper). 
	 
	using System;
	using System.IO;
	
	namespace cat
	{
	    class cat
	    {
	        static void Main(string[] args)
	        {
	            if (args.Length > 0)
	            {
	                Console.SetIn(new StreamReader(args[0])); 
	            }
	
	            string line;
	            while ((line = Console.ReadLine()) != null) 
	            {
	                Console.WriteLine(line);
	            }
	        }
	    }
	}

 

Код процедуры map в файле cat.cs использует объект StreamReader для чтения символов входящего потока на консоли, который, в свою очередь, с помощью статического метода Console.Writeline записывает потоковые данные в стандартный выходной поток.


	// The source code for wc.exe (Reducer) is:
	
	using System;
	using System.IO;
	using System.Linq;
	
	namespace wc
	{
	    class wc
	    {
	        static void Main(string[] args)
	        {
	            string line;
	            var count = 0;
	
	            if (args.Length > 0){
	                Console.SetIn(new StreamReader(args[0]));
	            }
	
	            while ((line = Console.ReadLine()) != null) {
	                count += line.Count(cr => (cr == ' ' || cr == '\n'));
	            }
	            Console.WriteLine(count);
	        }
	    }
	}


Код процедуры reduce в файле wc.cs использует объект [StreamReader][streamreader] для чтения символов из стандартного входного потока, который является выходным потоком процедуры map в cat.exe. Поскольку для чтения символов используется метод [Console.Writeline][console-writeline], подсчет слов осуществляется так: подсчитываются символы пробела и конца строки в конце каждого слова, а затем суммарное значение записывается в стандартный выходной поток с помощью метода [Console.Writeline][console-writeline]. 

<h2><a id="summary"></a>Сводка</h2>

В этом учебнике вы увидели, как развернуть задание MapReduce в HDInsight с использованием потоковой передачи Hadoop.

<h2><a id="next-steps"></a>Дальнейшие действия</h2>

Учебники по выполнению других примеров и инструкции по использованию заданий Pig, Hive и MapReduce в Azure HDInsight с использованием Azure PowerShell см. в следующих разделах:

* [Приступая к работе с Azure HDInsight.][hdinsight-get-started]
* [Пример: оценка числа пи][hdinsight-sample-pi-estimator]
* [Пример: счетчик слов][hdinsight-sample-wordcount]
* [Пример: сортировка GraySort 10 ГБ][hdinsight-sample-10gb-graysort]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Документация по пакету SDK для Azure для HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/ru-ru/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/ru-ru/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/ru-ru/library/3x292kth(v=vs.110).aspx

[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/



<!--HONumber=35.1-->
