<properties
	pageTitle="Пример подсчета слов Hadoop MapReduce в HDInsight | Microsoft Azure"
	description="Выполнение примера подсчета слов MapReduce в кластере Hadoop в HDInsight. Программа, написанная на языке Java, подсчитывает вхождения слов в текстовом файле."
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
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="jgao"/>

#Запуск программы подсчета слов MapReduce в кластере Hadoop в HDInsight

Узнайте, как запустить программу MapReduce в кластере Hadoop в HDInsight с помощью Azure PowerShell. Эта программа, написанная на языке Java, подсчитывает частоту использования слова в текстовом файле и создает новый текстовый файл, в котором каждое слово приводится с частотой его использования.

Данная программа устанавливаются в кластерах. В качестве анализируемого текстового файла здесь используется выпуск электронной книги проекта «Гутенберг» The Notebooks of Leonardo Da Vinci (Записки Леонардо да Винчи).

> [AZURE.NOTE]Для выполнения действий, описанных в этом документе, требуется клиент Windows. Действия по использованию примера подсчета слов в клиенте Linux, OS X или Unix в кластере HDInsight на платформе Linux см. в разделе [Использование MapReduce с Hadoop в HDInsight с помощью SSH](hdinsight-hadoop-use-mapreduce-ssh.md) или [Использование MapReduce с Hadoop в HDInsight с помощью Curl](hdinsight-hadoop-use-mapreduce-curl.md).

**Другие статьи по этой теме:**

* [Начало работы с Azure HDInsight][hdinsight-get-started]
* [Разработка программ MapReduce на Java для Hadoop в HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
* [Отправка заданий Hadoop в HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Пример: GraySort 10 ГБ][hdinsight-sample-10gb-graysort]
* [Пример: оценка числа пи][hdinsight-sample-pi-estimator]
* [Пример: потоковая передача на C#][hdinsight-sample-cs-streaming]

**Предварительные требования**:

- **Кластер HDInsight**. Инструкции по созданию кластеров см. в разделе [Начало работы с Azure HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight](hdinsight-provision-clusters.md).
- **Рабочая станция с Azure PowerShell**. См. [Установка и использование Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## Выполнение примера с использованием Azure PowerShell

**Отправка задания MapReduce**

1. Откройте **интегрированную среду сценариев Windows PowerShell**. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].
2. Вставьте следующий сценарий PowerShell:

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
		
		Select-AzureRmSubscription $subscriptionName
		
		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "wordcount" `
									-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
		
		# Submit the job and wait for job completion
		$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
		$wordCountJob = Start-AzureRmHDInsightJob `
							-ResourceGroupName $resourceGroupName `
							-ClusterName $clusterName `
							-HttpCredential $cred `
							-JobDefinition $wordCountJobDefinition 
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-JobId $wordCountJob.JobId 
		
		# Get the job output
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-DefaultStorageAccountName $defaultStorageAccount `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultContainer $defaultStorageContainer  `
			-JobId $wordCountJob.JobId `
			-DisplayOutputType StandardError

3. Задайте 3 первые переменные и выполните сценарий.
		
**Получение результатов задания MapReduce**

1. Откройте **интегрированную среду сценариев Windows PowerShell**. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].
2. Вставьте следующий сценарий PowerShell:

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Get the cluster properties
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		# Download the job output to the workstation
		$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
		Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
		
		# Display the output file
		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	Задание MapReduce создает файл с именем *part-r-00000*, который содержит слова и их количество. В сценарии используется команда **findstr**, которая создает список всех слов, содержащих *there*.

Результат выполнения сценария WordCount должен появиться в окне командной строки:

![Результаты частоты использования слов получаются их примера счетчика слов Hadoop MapReduce в HDInsight.][image-hdi-sample-wordcount-output]

Обратите внимание, что выходные файлы задания MapReduce являются неизменяемыми. Поэтому при повторном выполнении этого примера потребуется изменить имя выходного файла.

##Исходный код Java

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

## Дальнейшие действия

* [Начало работы с Azure HDInsight][hdinsight-get-started]
* [Разработка программ MapReduce на Java для Hadoop в HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
* [Отправка заданий Hadoop в HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Пример: GraySort 10 ГБ][hdinsight-sample-10gb-graysort]
* [Пример: оценка числа пи][hdinsight-sample-pi-estimator]
* [Пример: потоковая передача на C#][hdinsight-sample-cs-streaming]

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png

<!---HONumber=Oct15_HO4-->