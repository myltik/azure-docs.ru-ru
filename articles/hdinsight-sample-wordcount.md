<properties 
	pageTitle="Запуск примера подсчета слов Hadoop MapReduce в HDInsight | Azure" 
	description="Запуск примера подсчета слов MapReduce в кластере Hadoop в HDInsight. Программа, написанная на языке Java, подсчитывает вхождения слов в текстовом файле." 
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
	ms.date="11/10/2014" 
	ms.author="bradsev"/>

#Запуск примера подсчета слов MapReduce в кластере Hadoop в HDInsight
 
В этом учебнике демонстрируется запуск примера подсчета слов MapReduce в кластере Hadoop в HDInsight. Эта программа, написанная на языке Java, подсчитывает количество вхождений слова в текстовом файле и создает новый текстовый файл, в котором каждое слово приводится с частотой его вхождений. В качестве анализируемого текстового файла здесь используется выпуск электронной книги проекта "Гутенберг" "The Notebooks of Leonardo Da Vinci" (Записки Леонардо да Винчи).

 
**Вы узнаете:**
		
* Как использовать Azure PowerShell для выполнения программы MapReduce в кластере HDInsight.
* Как писать программы MapReduce на языке Java.


**Предварительные требования**:	

- Необходимо иметь учетную запись Azure. Возможности регистрации учетной записи см. на странице [Бесплатное пробное использование Azure](http://azure.microsoft.com/ru-ru/pricing/free-trial/).

- Вы должны были подготовить кластер HDInsight. Инструкции по различным способам создания таких кластеров см. в разделе [Приступая к работе с Azure HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight](../hdinsight-provision-clusters/)

- Необходимо установить Azure PowerShell и настроить эту среду для поддержки вашей учетной записи. Инструкции по выполнению этой задачи см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure]

## Содержание	
В этом разделе демонстрируется выполнение примера, предоставляется код Java для программы MapReduce, обобщаются полученные знания и кратко описываются некоторые дальнейшие действия. Здесь содержатся следующие разделы:
	
1. [Выполнение примера с использованием Azure PowerShell](#run-sample)	
2. [Код Java для программы WordCount MapReduce](#java-code)
3. [Сводка](#summary)	
4. [Дальнейшие действия](#next-steps)	

<h2><a id="run-sample"></a>Выполнение примера с использованием Azure PowerShell</h2> 

**Отправка задания MapReduce**

1.	Откройте **Azure PowerShell**. Инструкции по открытию окна консоли Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

2. Задайте две переменные в следующих командах, а затем выполните команды:
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
		
3. Выполните следующую команду, чтобы создать определение задания MapReduce:

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

	> [AZURE.NOTE] *hadoop-examples.jar* предоставляется вместе с кластерами HDInsight версии 2.1. В кластерах HDInsight версии 3.0 файл был переименован в *hadoop-mapreduce.jar*.
	
	Файл hadoop-examples.jar поставляется с кластером HDInsight. Существует два аргумента для задания MapReduce. Первый из них - имя исходного файла, а второй - путь выходного файла. Исходный файл поставляется с кластером HDInsight, а путь выходного файла будет создан во время выполнения.

4. Выполните следующую команду, чтобы отправить задание MapReduce:

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Помимо определения задания MapReduce вы также указываете имя кластера HDInsight, в котором нужно выполнить задание MapReduce.

5. Выполните следующую команду для проверки любых ошибок при выполнении задания MapReduce:	
	
		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
		
**Получение результатов задания MapReduce**

1. Откройте **Azure PowerShell**.
2. Задайте три переменных в следующих командах, а затем выполните их:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	Учетная запись хранения Azure - та, которую вы создали ранее в этом учебнике. Эта учетная запись хранения используется для размещения контейнера BLOB-объектов, используемого в качестве файловой системы по умолчанию кластера HDInsight.  Имя контейнера хранилища BLOB-объектов обычно совпадает с именем кластера HDInsight, если при подготовке кластера не указано другое имя.

3. Выполните следующие команды для создания объекта контекста хранилища Azure:
		
		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	 *Select-AzureSubscription* используется для задания текущей подписки в случае, если имеется несколько подписок и должна использоваться подписка, отличная от подписки по умолчанию. 

4. Выполните следующую команду, чтобы загрузить результаты задания MapReduce из контейнера BLOB-объектов на рабочую станцию:

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Папка */example/data/WordCountOutput* - выходная папка, указанная при запуске задания MapReduce. *part-r-00000* - имя файла по умолчанию для выходных данных задания MapReduce.  Этот файл будет загружен в ту же структуру папок в локальной папке. Например, на следующем снимке экрана текущая папка - корневая папка диска C.  Файл будет загружен в папку *C:\example\data\WordCountOutput*. 

5. Выполните следующую команду, чтобы напечатать выходной файл задания MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	Задание MapReduce создает файл с именем *part-r-00000*, содержащий слова и их количество.  В этом сценарии используется команда findstr для получения списка всех слов, содержащих *"there"*.

Результат выполнения сценария WordCount должен появиться в окне cmd:

![Word frequency results in PowerShell from the Hadoop MapReduce word count example in HDInsight.][image-hdi-sample-wordcount-output]

Обратите внимание, что выходные файлы задания MapReduce являются неизменяемыми. Поэтому при повторном выполнении этого примера потребуется изменить имя выходного файла.

<h2><a id="java-code"></a>Код Java для программы MapReduce WordCount</h2>



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



<h2><a id="summary"></a>Сводка</h2>

В этом учебнике вы узнали, как выполнить программу MapReduce, которая подсчитывает вхождения слов в текстовом файле, в HDInsight с использованием Azure PowerShell.

<h2><a id="next-steps"></a>Дальнейшие действия</h2>

Учебники по выполнению других примеров и инструкции по использованию заданий Pig, Hive и MapReduce в Azure HDInsight с использованием Azure PowerShell см. в следующих разделах:

* [Приступая к работе с Azure HDInsight][hdinsight-get-started]
* [Пример: сортировка GraySort 10 ГБ][hdinsight-sample-10gb-graysort]
* [Пример: оценка числа пи][hdinsight-sample-pi-estimator]
* [Пример: потоковая передача на C#][hdinsight-sample-cs-streaming]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Документация по пакету Azure HDInsight SDK][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx

[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-cs-streaming]: ../hdinsight-sample-csharp-streaming/


[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
 
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png


<!--HONumber=42-->
