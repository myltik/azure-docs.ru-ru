<properties urlDisplayName="MapReduce with Hadoop in HDInsight" pageTitle="Использование Hadoop MapReduce в HDInsight для платформы Azure" metaKeywords="" description="Вы узнаете, как использовать HDInsight для выполнения простых заданий Hadoop MapReduce." metaCanonical="" services="hdinsight" documentationCenter="" title="Использование Hadoop MapReduce в HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Использование Hadoop MapReduce в HDInsight

Hadoop MapReduce — это программная платформа для создания приложений, обрабатывающих большие объемы данных. В этом учебнике вы с помощью Azure PowerShell на рабочей станции отправите программу MapReduce, которая подсчитывает количество вхождений слова в тексте, в кластер HDInsight. Программа счетчика слов написана на языке Java и поставляется с кластером HDInsight.

**Предварительные требования:**

Перед началом работы с этим учебником необходимо иметь следующее:

-   Кластер HDInsight. Сведения по различным способам создания таких кластеров см. в разделе [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight]

-   Рабочая станция, на которой установлена и настроена среда Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].

**Предполагаемое время выполнения:**30 минут

## В этом учебнике рассматриваются следующие темы:

1.  [Ознакомление со сценарием][Ознакомление со сценарием]
2.  [Выполнение примера с использованием Azure PowerShell][Выполнение примера с использованием Azure PowerShell]
3.  [Код Java для программы счетчика слов MapReduce][Код Java для программы счетчика слов MapReduce]
4.  [Дальнейшие действия][Дальнейшие действия]

## <span id="scenario"></span></a>Ознакомление со сценарием

На следующей схеме показана работа MapReduce для сценария счетчика слов:

![HDI.WordCountDiagram][HDI.WordCountDiagram]

Результат задания MapReduce — это набор пар "ключ-значение". Ключ — строка, определяющая слово, а значение — целое число, определяющее общее количество вхождений слова в тексте. Это делается в два этапа.

-   Процедура map берет каждую строку из входного текста в качестве входных данных и разбивает ее на слова. Она выдает пару "ключ-значение" каждый раз, когда встречается слово, за которым следует 1. Перед отправкой в процедуру reduce выходные данные сортируются.

-   Затем процедура reduce суммирует эти отдельные счетчики для каждого слова и выдает одну пару "ключ-значение", содержащую слово, за которым следует сумма его вхождений.

Для выполнения задания MapReduce требуются следующие элементы:

-   Программа MapReduce. В этом учебнике вы воспользуетесь примером счетчика слов, поставляемым с кластером HDInsight, так что вам не потребуется создавать собственную программу. Этот пример находится в файле */example/jars/hadoop-examples.jar*. В кластерах HDInsight версии 3.0 файл называется *hadoop-mapreduce-examples.jar*. Инструкции по написанию собственного задания MapReduce см. в разделе [Разработка программ MapReduce на Java для HDInsight][Разработка программ MapReduce на Java для HDInsight].
-   Входной файл. В качестве входного файла будет использоваться */example/data/gutenberg/davinci.txt*. Сведения об отправке файлов см. в разделе [Отправка данных в HDInsight][Отправка данных в HDInsight].
-   Папка для выходного файла. В качестве папки для выходного файла будет использоваться */example/data/WordCountOutput*. Система создаст эту папку, если она не существует. Если папка уже есть, задание MapReduce завершится ошибкой. Если нужно выполнить задание MapReduce во второй раз, обязательно удалите выходную папку или укажите другую.

## <span id="run-sample"></span></a>Выполнение примера с использованием Azure PowerShell

1.  Откройте **Azure PowerShell**. Инструкции по открытию окна консоли Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].

2.  Задайте две переменные в следующих командах, а затем выполните команды:

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  Выполните следующую команду и укажите данные учетной записи Azure:

        Add-AzureAccount

4.  Выполните следующие команды, чтобы создать определение задания MapReduce:

        # Define the MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] *hadoop-examples.jar* предоставляется вместе с кластерами HDInsight версии 2.1. В кластерах HDInsight версии 3.0 файл был переименован в *hadoop-mapreduce.jar*.

    Файл hadoop-examples.jar поставляется с дистрибутивом кластера HDInsight. Существует два аргумента для задания MapReduce. Первый из них — имя исходного файла, а второй — путь выходного файла. Исходный файл поставляется с дистрибутивом кластера HDInsight, а путь выходного файла будет создан во время выполнения.

5.  Выполните следующую команду, чтобы отправить задание MapReduce:

        # Submit the job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Помимо определения задания MapReduce вы также указываете имя кластера HDInsight, в котором нужно выполнить задание MapReduce, и учетные данные. Start-AzureHDInsightJob представляет собой асинхронизированный вызов. Для проверки выполнения задания используйте командлет *Wait-AzureHDInsightJob*.

6.  Выполните следующую команду для проверки выполнения задания MapReduce:

        Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

7.  Выполните следующую команду для проверки любых ошибок при выполнении задания MapReduce:

        # Get the job output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**Получение результатов задания MapReduce**

1.  Откройте **Azure PowerShell**.
2.  Выполните следующую команду, чтобы изменить каталог на c:\\ root:

        cd \

    Каталог Azure Powershell по умолчанию: *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*. По умолчанию у вас нет разрешения на запись для этой папки. Необходимо изменить каталог на C:\\ root или на папку, в которой у вас имеется разрешение на запись.

3.  Задайте три переменных в следующих командах, а затем выполните их:

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name

        $storageAccountName = "<StorageAccountName>"   # Azure storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

        The Azure Storage account is the one you created earlier in the tutorial. The storage account is used to host the Blob container that is used as the default HDInsight cluster file system.  The Blob storage container name usually share the same name as the HDInsight cluster unless you specify a different name when you provision the cluster.

4.  Выполните следующие команды для создания объекта контекста хранилища Azure:

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* используется для задания текущей подписки в случае, если имеется несколько подписок и должна использоваться подписка, отличная от подписки по умолчанию.

5.  Выполните следующую команду, чтобы загрузить результаты задания MapReduce из контейнера BLOB-объектов на рабочую станцию:

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    Папка */example/data/WordCountOutput* — выходная папка, указанная при запуске задания MapReduce. *part-r-00000* — имя файла по умолчанию для выходных данных задания MapReduce. Этот файл будет загружен в ту же структуру папок в локальной папке. Например, на следующем снимке экрана текущая папка — корневая папка C. Файл будет загружен в папку \*C:\\example\\data\\WordCountOutput\*/

6.  Выполните следующую команду, чтобы напечатать выходной файл задания MapReduce:

        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    Задание MapReduce создает файл с именем *part-r-00000*, содержащий слова и их количество. В этом сценарии используется команда findstr для получения списка всех слов, содержащих *"there"*.

Обратите внимание, что выходные файлы задания MapReduce являются неизменяемыми. Поэтому при повторном выполнении этого примера потребуется изменить имя выходного файла.

## <span id="java-code"></span></a>Код Java для программы счетчика слов MapReduce

Ниже приведен исходный код на языке Java для программы счетчика слов MapReduce.

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

## <span id="nextsteps"></span></a>Дальнейшие действия

Технология MapReduce предоставляет комплексные возможности диагностики, однако может быть несколько сложна в освоении. Другие языки, такие как Pig и Hive, предоставляют более простой способ работы с данными, хранящимися в HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

-   [Приступая к работе с Azure HDInsight][Приступая к работе с Azure HDInsight]
-   [Разработка программ MapReduce на Java для HDInsight][Разработка программ MapReduce на Java для HDInsight]
-   [Разработка программ MapReduce потоковой передачи Hadoop на C\# для HDInsight][Разработка программ MapReduce потоковой передачи Hadoop на C\# для HDInsight]
-   [Использование Hive с HDInsight][Использование Hive с HDInsight]
-   [Использование Pig с HDInsight][Использование Pig с HDInsight]
-   [Выполнение примеров HDInsight][Выполнение примеров HDInsight]

  [Подготовка кластеров HDInsight]: ../hdinsight-provision-clusters/
  [Установка и настройка Azure PowerShell]: ../install-and-configure-powershell/
  [Ознакомление со сценарием]: #scenario
  [Выполнение примера с использованием Azure PowerShell]: #run-sample
  [Код Java для программы счетчика слов MapReduce]: #java-code
  [Дальнейшие действия]: #next-steps
  [HDI.WordCountDiagram]: ./media/hdinsight-get-started/HDI.WordCountDiagram.gif
  [Разработка программ MapReduce на Java для HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Отправка данных в HDInsight]: ../hdinsight-upload-data/
  [Приступая к работе с Azure HDInsight]: ../hdinsight-get-started/
  [Разработка программ MapReduce потоковой передачи Hadoop на C\# для HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Использование Hive с HDInsight]: ../hdinsight-use-hive/
  [Использование Pig с HDInsight]: ../hdinsight-use-pig/
  [Выполнение примеров HDInsight]: ../hdinsight-run-samples/
