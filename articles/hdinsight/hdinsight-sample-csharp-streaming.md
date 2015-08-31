<properties
	pageTitle="Пример счетчика слов на C# для потоковой передачи | Microsoft Azure"
	description="Написание программ MapReduce на C# с использованием интерфейса потоковой передачи Hadoop и их запуск в Azure HDInsight с использованием командлетов Azure PowerShell."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="jgao"/>

# Пример счетчика слов MapReduce для потоковой передачи C# в HDInsight

Hadoop предоставляет API-интерфейс для MapReduce, позволяющий создавать функции map и reduce на языках, отличных от Java. В этом учебнике объясняется, как создавать программы MapReduce на C# с использованием интерфейса потоковой передачи Hadoop и как выполнять программы в Azure HDInsight с использованием командлетов Azure PowerShell.

> [AZURE.NOTE]Шаги, описанные в этом учебнике, можно применять только к кластерам HDInsight под управлением Windows. Пример потоковой передачи для HDInsight под управлением Linux см. в статье [Разработка программ потоковой передачи Python для HDInsight](hdinsight-hadoop-streaming-python.md).

В приведенном примере процедура map и процедура reduce являются исполняемыми файлами, которые считывают входные данные из [stdin][stdin-stdout-stderr] (построчно) и выводят выходные данные в [stdout][stdin-stdout-stderr]. Программа подсчитывает все слова в тексте.

Если указан исполняемый файл для **процедур map**, то при инициализации процедуры map каждая задача процедуры map запускает исполняемый файл как отдельный процесс. Модуль сопоставления во время выполнения заданий преобразует входные данные в строки и подает эти строки в процесс [stdin][stdin-stdout-stderr].

В то же время модуль сопоставления собирает выходные данные в виде строк из процесса stdout. Он преобразует каждую строку в пару «ключ-значение», которая собирается в качестве выходных данных модуля сопоставления. По умолчанию префикс строки до первого символа табуляции является ключом, а остальная часть строки (кроме символа табуляции) — это значение. Если в строке нет символа табуляции, вся строка рассматривается как ключ, а значение считается нулевым.

Если указан исполняемый файл для **процедур reduce**, то при инициализации процедуры reduce каждая задача процедуры reduce запускает исполняемый файл как отдельный процесс. Редуктор во время выполнения заданий преобразует входные пары «ключ-значение» в строки, а строки подает в процесс [stdin][stdin-stdout-stderr].

В то же время редуктор собирает выходные данные в виде строк из процесса [stdout][stdin-stdout-stderr]. Он преобразует каждую строку в пару «ключ-значение», которая собирается в качестве выходных данных модуля редукции. По умолчанию префикс строки до первого символа табуляции является ключом, а остальная часть строки (кроме символа табуляции) — это значение.

Дополнительные сведения об интерфейсе потоковой передачи Hadoop см. в разделе [Потоковая передача Hadoop][hadoop-streaming].

**В этом учебнике вы найдете следующую информацию.**

* Использование Azure PowerShell для выполнения программы потоковой передачи на C# для анализа данных, содержащихся в файле в HDInsight.
* Написание кода на C#, который использует интерфейс потоковой передачи Hadoop.


**Предварительные требования**:

Перед началом работы убедитесь, что у вас есть такие компоненты.

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Кластер HDInsight**. Сведения по различным способам создания таких кластеров см. в разделе [Подготовка кластеров HDInsight](hdinsight-provision-clusters.md)
- **Рабочая станция с Azure PowerShell**. См. [Установка и использование Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## <a id="run-sample"></a>Выполнение примера с помощью Azure PowerShell

**Выполнение задания MapReduce**

1.	Откройте **Azure PowerShell**. Инструкции по открытию окна консоли Azure PowerShell см. в статье [Установка и настройка Azure PowerShell][powershell-install-configure].

3. Задайте две переменные в следующих командах, а затем выполните команды:

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


2. Выполните следующую команду, чтобы определить задание MapReduce:

		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe"

	Параметры определяют функции модуля сопоставления и редуктора, а также входной и выходной файлы.

5. Выполните следующие команды, чтобы запустить задание MapReduce. Подождите завершения задания, а затем напечатайте стандартную ошибку:

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
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $\_.Primary } $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	Обратите внимание, что выходные файлы задания MapReduce являются неизменяемыми. Поэтому при повторном выполнении этого примера потребуется изменить имя выходного файла.


## <a id="java-code"></a>Код C# для потоковой передачи Hadoop


В программе MapReduce используются приложение cat.exe как интерфейс сопоставления для потоковой передачи текста на консоль и приложение wc.exe как интерфейс редукции для подсчета количества слов, передаваемых в потоковом режиме из документа. Модуль сопоставления и модуль редукции считывают символы (построчно) из стандартного входного потока (stdin) и записывают данные в стандартный выходной поток (stdout).



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



Код сопоставления в файле cat.cs использует объект [StreamReader][streamreader] для чтения символов входящего потока на консоли. Этот объект записывает потоковые данные в стандартный выходной поток с помощью статического метода [Console.Writeline][console-writeline].


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


Код процедуры reduce в файле wc.cs использует объект [StreamReader][streamreader] для чтения символов из стандартного входного потока, который является выходным потоком процедуры mapper в cat.exe. Поскольку этот объект читает символы с помощью метода [Console.Writeline][console-writeline], он подсчитывает слова, считая пробелы и символы конца строки в конце каждого слова. Общая сумма записывается в стандартный выходной поток с помощью метода [Console.Writeline][console-writeline].


## <a id="summary"></a>Сводка

В этом учебнике вы узнали, как развернуть задание MapReduce в HDInsight с использованием потоковой передачи Hadoop.

## <a id="next-steps"></a>Дальнейшие действия


Учебники по выполнению других примеров и инструкции по использованию заданий Pig, Hive и MapReduce в Azure HDInsight с использованием Azure PowerShell см. в следующих статьях.

* [Начало работы с Azure HDInsight][hdinsight-get-started]
* [Пример: оценка числа пи][hdinsight-sample-pi-estimator]
* [Пример: счетчик слов][hdinsight-sample-wordcount]
* [Пример: GraySort 10 ГБ][hdinsight-sample-10gb-graysort]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Документация по пакету SDK для Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/library/3x292kth(v=vs.110).aspx

[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=August15_HO8-->