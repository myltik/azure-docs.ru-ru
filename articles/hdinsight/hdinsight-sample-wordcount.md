<properties
	pageTitle="Пример подсчета слов Hadoop MapReduce в HDInsight | Microsoft Azure"
	description="Выполнение примера подсчета слов MapReduce в кластере Hadoop в HDInsight. Программа, написанная на языке Java, подсчитывает вхождения слов в текстовом файле."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2015" 
	ms.author="bradsev"/>

#Выполнение примера подсчета слов MapReduce на Java в кластере Hadoop в HDInsight

В этом учебнике демонстрируется выполнение примера подсчета слов MapReduce в кластере Hadoop в HDInsight. Программа написана на языке Java. Эта программа подсчитывает частоту использования слова в текстовом файле и создает новый текстовый файл, в котором каждое слово приводится с частотой его использования. В качестве анализируемого текстового файла здесь используется выпуск электронной книги проекта "Гутенберг" "The Notebooks of Leonardo Da Vinci" (Записки Леонардо да Винчи).

> [AZURE.NOTE]Для выполнения действий, описанных в этом документе, требуется клиент Windows. Действия по использованию примера подсчета слов в клиенте Linux, OS X или Unix в кластере HDInsight на платформе Linux см. в разделе [Использование MapReduce с Hadoop в HDInsight с помощью SSH](hdinsight-hadoop-use-mapreduce-ssh.md) или [Использование MapReduce с Hadoop в HDInsight с помощью Curl](hdinsight-hadoop-use-mapreduce-curl.md).

**Вы узнаете:**

* Как использовать Azure PowerShell для выполнения программы MapReduce в кластере HDInsight.
* Как писать программы MapReduce на языке Java.


**Предварительные требования**:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Кластер HDInsight**. Инструкции по созданию кластеров см. в разделе [Начало работы с Azure HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight](hdinsight-provision-clusters.md).

- **Рабочая станция с Azure PowerShell**. См. [Установка и использование Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## <a id="run-sample"></a>Выполнение примера с использованием Azure PowerShell</h2>

**Отправка задания MapReduce**

1.	Откройте консоль **Azure PowerShell**. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

3. Задайте две переменные в следующих командах, а затем выполните команды:

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

5. Выполните следующую команду, чтобы создать определение задания MapReduce:

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	> [AZURE.NOTE]Файл *hadoop-examples.jar* предоставляется вместе с кластерами HDInsight версии 2.1. Файл был переименован на *hadoop-mapreduce.jar* в кластерах HDInsight версии 3.0.

	Файл hadoop-mapreduce-examples.jar поставляется с кластером HDInsight. Существует два аргумента для задания MapReduce. Первый из них — имя исходного файла, а второй — путь выходного файла. Исходный файл поставляется с кластером HDInsight, а путь выходного файла будет создан во время выполнения.

6. Выполните следующую команду, чтобы отправить задание MapReduce:

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Помимо определения задания MapReduce вы также указываете имя кластера HDInsight, в котором будет выполняться задание MapReduce.

8. Выполните следующую команду для проверки любых ошибок при выполнении задания MapReduce:

		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

**Получение результатов задания MapReduce**

1. Откройте консоль **Azure PowerShell**.
2. Задайте три переменных в следующих командах, а затем выполните их:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	Учетная запись хранения Azure — та, которую вы создали ранее в этом учебнике. Учетная запись хранения используется для размещения большого двоичного объекта, используемого в кластере HDInsight в качестве файловой системы по умолчанию. Имя контейнера хранилища BLOB-объектов Azure обычно совпадает с именем кластера HDInsight, если при подготовке кластера не указано другое имя.

3. Выполните следующие команды для создания объекта контекста хранилища Azure:

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	**Select-AzureSubscription** указывает активную подписку. Это необходимо, если подписок несколько и по умолчанию установлена не та подписка, которая вам нужна.

4. Чтобы загрузите результаты задания MapReduce из большого двоичного объекта на рабочую станцию, выполните следующую команду:

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Папка */example/data/WordCountOutput* — выходная папка, указанная при запуске задания MapReduce. *part-r-00000* — имя файла по умолчанию для выходных данных задания MapReduce. Этот файл будет загружен в ту же структуру папок в локальной папке. Например, на следующем снимке экрана текущая папка — корневая папка C. Файл будет загружен в папку *C:\\example\\data\\WordCountOutput*.

5. Выполните следующую команду, чтобы напечатать выходной файл задания MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	Задание MapReduce создает файл с именем *part-r-00000*, который содержит слова и их количество. В сценарии используется команда **findstr**, которая создает список всех слов, содержащих *there*.

Результат выполнения сценария WordCount должен появиться в окне командной строки:

![Результаты частоты использования слов получаются их примера счетчика слов Hadoop MapReduce в HDInsight.][image-hdi-sample-wordcount-output]

Обратите внимание, что выходные файлы задания MapReduce являются неизменяемыми. Поэтому при повторном выполнении этого примера потребуется изменить имя выходного файла.

## <a id="java-code"></a>Код Java для программы MapReduce WordCount</h2>



	package org.apache.hadoop.examples;
	import java.io.IOException;
	import java.util.StringTokenizer;
	import org.apache.hadoop.conf.Configuration;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.IntWritable;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapreduce.Job;
	import org.apache.hadoop.mapreduce.Mapper;
	import org.apache.hadoop.mapreduce.Reducer;
	import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
	import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
	import org.apache.hadoop.util.GenericOptionsParser;

	public class WordCount {

  	public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      	}
      }
  	}

  	public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
      }
  	}

  	public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
      System.err.println("Usage: wordcount <in> <out>");
      System.exit(2);
    	}
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  	}
  	}



В этом учебнике вы узнали, как выполнить программу MapReduce, которая подсчитывает частоту использования слов в текстовом файле, в HDInsight с использованием Azure PowerShell.

## <a id="next-steps"></a>Дальнейшие действия</h2>

Учебники по выполнению других примеров и инструкции по использованию заданий Pig, Hive и MapReduce в Azure HDInsight с Azure PowerShell см. в следующих статьях.

* [Начало работы с Azure HDInsight][hdinsight-get-started]
* [Пример: GraySort 10 ГБ][hdinsight-sample-10gb-graysort]
* [Пример: оценка числа пи][hdinsight-sample-pi-estimator]
* [Пример: потоковая передача на C\#][hdinsight-sample-cs-streaming]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Документация по пакету SDK для Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png
 

<!---HONumber=62-->