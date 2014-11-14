<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="Пример сортировки GraySort 10 ГБ | Azure" metaKeywords="hdinsight, hadoop, hdinsight administration, hdinsight administration azure" description="Узнайте, как выполнить программу сортировки GraySort общего назначения в Hadoop с HDInsight, используя Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Пример сортировки GraySort 10 ГБ" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Пример сортировки GraySort Hadoop 10 ГБ в HDInsight

В этом разделе показано, как выполнить программу сортировки GraySort Hadoop MapReduce общего назначения для Azure HDInsight с использованием Azure PowerShell. GraySort — это измерение производительности сортировки, показателем которой служит скорость (ТБ/мин), достигаемая при сортировке очень больших объемов данных, обычно не менее 100 ТБ.

В этом примере используется небольшой объем данных 10 ГБ, чтобы его можно было выполнить достаточно быстро. Используется также приложение MapReduce, разработанное Оуэном О'Мэлли (Owen O'Malley) и Аруном Мёрти (Arun Murthy) и ставшее в 2009 г. победителем по результатам ежегодного измерения производительности сортировки общего назначения ("daytona") для терабайтных объемов данных, показав скорость 0,578 ТБ/мин (100 ТБ за 173 минуты). Дополнительные сведения об этом и других измерениях производительности сортировки см. на сайте [Sortbenchmark][Sortbenchmark].

В этом примере используются три набора программ MapReduce.

1.  **TeraGen**: программа MapReduce, с помощью которой можно создать строки данных для сортировки.
2.  **TeraSort**: производит выборку входных данных и использует MapReduce для сортировки данных в общем порядке. TeraSort представляет стандартные функции MapReduce, за исключением настраиваемого разделителя, в котором используется отсортированный список выборки ключей N-1, определяющий диапазон ключей для каждой функции reduce. В частности, все ключи, такие как этот пример [i-1] \<= key \< sample[i] , отправляются в функцию reduce i. Это гарантирует, что результат reduce i всегда меньше, чем результат reduce i+1.
3.  **TeraValidate**: программа MapReduce, которая проверяет глобальную сортировку результатов. В выходном каталоге создается одна функция map для каждого файла, и каждая функция map гарантирует, что каждый ключ будет меньше или равен предыдущему. Функция map также создает записи первого и последнего ключей каждого файла, а функция reduce гарантирует, что первый ключ файла i больше последнего ключа файла i-1.Все проблемы указываются в выходных данных функции reduce вместе с неотсортированными ключами.

Формат ввода и вывода, использующийся всеми тремя приложениями, позволяет читать и записывать текстовые файлы в нужном формате. Для репликации выходных данных функции reduce задано значение 1, а не значение по умолчанию 3, поскольку при сравнительном измерении производительности не требуется репликация выходных данных на несколько узлов.

**Вы узнаете:**

-   Как использовать Azure PowerShell для выполнения серии программ MapReduce в Azure HDInsight.
-   Как выглядит программа MapReduce, написанная на языке Java.

**Предварительные требования**:

-   Необходимо иметь учетную запись Azure. Возможности регистрации учетной записи см. на странице [Бесплатное пробное использование Azure][Бесплатное пробное использование Azure].

-   Вы должны были подготовить кластер HDInsight. Сведения по различным способам создания таких кластеров см. в разделе [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight]

-   Необходимо установить Azure PowerShell и настроить эту среду для поддержки вашей учетной записи. Инструкции по выполнению этой задачи см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].

## Содержание

В этом разделе демонстрируется выполнение серии программ MapReduce, входящих в пример, представляется код Java для программы MapReduce, обобщаются полученные знания и кратко описываются некоторые дальнейшие действия. Здесь содержатся следующие разделы:

1.  [Выполнение примера с использованием Azure PowerShell][Выполнение примера с использованием Azure PowerShell]
2.  [Код Java для программы TeraSort MapReduce][Код Java для программы TeraSort MapReduce]
3.  [Сводка][Сводка]
4.  [Дальнейшие действия][Дальнейшие действия]

## <span id="run-sample"></span></a>Выполнение примера с использованием Azure PowerShell

Для примера необходимы три задачи, каждая из которых соответствует одной из программ MapReduce, описанных во введении:

1.  Создание данных для сортировки с помощью задания MapReduce **TeraGen**.
2.  Сортировка данных с помощью задания MapReduceort **TeraSort**.
3.  Подтверждение правильной сортировки данных с помощью задания MapReduce **TeraValidate**.

**Выполнение программы TeraGen**

1.  Откройте Azure PowerShell. Инструкции по открытию окна консоли Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].
2.  Задайте две переменные в следующих командах, а затем выполните команды:

        # Provide the Azure subscription name and the HDInsight cluster name.
        $subscriptionName = "myAzureSubscriptionName"   
        $clusterName = "myClusterName"

3.  Выполните следующую команду, чтобы создать определение задания MapReduce:

        # Create a MapReduce job definition for the TeraGen MapReduce program
        $teragen = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "teragen" -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input" 

    > [WACOM.NOTE] *hadoop-examples.jar* предоставляется вместе с кластерами HDInsight версии 2.1. В кластерах HDInsight версии 3.0 файл был переименован в *hadoop-mapreduce.jar*.

    Аргумент *"-Dmapred.map.tasks=50"* указывает, что для выполнения задания будет создано 50 задач map. Аргумент *100000000* определяет объем создаваемых данных. Последний аргумент */example/data/10GB-sort-input* задает выходной каталог, в котором сохраняются результаты (который содержит входные данные для следующего этапа сортировки).

4.  Выполните следующие команды, чтобы отправить задание, ждать его завершения, а затем напечатать стандартную ошибку:

        # Run the TeraGen MapReduce job.
        # Wait for the job to complete.
        # Print output and standard error file of the MapReduce job
        Select-AzureSubscription $subscriptionName         
        $teragen | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

**Выполнение программы TeraSort**

1.  Откройте Azure PowerShell.
2.  Задайте две переменные в следующих командах, а затем выполните команды:

        # Provide the Azure subscription name and the HDInsight cluster name.
        $subscriptionName = "myAzureSubscriptionName"   
        $clusterName = "myClusterName"

3.  Выполните следующую команду, чтобы определить задание MapReduce:

        # Create a MapReduce job definition for the TeraSort MapReduce program
        $terasort = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "terasort" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output" 

    Аргумент *"-Dmapred.map.tasks=50"* указывает, что для выполнения задания будет создано 50 задач map. Аргумент *100000000* определяет объем создаваемых данных. Последние два аргумента задают входной и выходной каталоги.

4.  Выполните следующую команду, чтобы отправить задание, ждать его завершения, а затем напечатать стандартную ошибку:

        # Run the TeraSort MapReduce job.
        # Wait for the job to complete.
        # Print output and standard error file of the MapReduce job 
        Select-AzureSubscription $subscriptionName        
        $terasort | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

**Выполнение программы TeraValidate**

1.  Откройте Azure PowerShell.
2.  Задайте две переменные в следующих командах, а затем выполните команды:

        # Provide the Azure subscription name and the HDInsight cluster name.
        $subscriptionName = "myAzureSubscriptionName"   
        $clusterName = "myClusterName"

3.  Выполните следующую команду, чтобы определить задание MapReduce:

        #   Create a MapReduce job definition for the TeraValidate MapReduce program
        $teravalidate = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "teravalidate" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate" 

    Аргумент *"-Dmapred.map.tasks=50"* указывает, что для выполнения задания будет создано 50 задач map. Аргумент *"-Dmapred.reduce.tasks=25"* указывает, что для выполнения задания будет создано 25 задач reduce. Последние два аргумента задают входной и выходной каталоги.

4.  Выполните следующие команды, чтобы отправить задание MapReduce, ждать его завершения, а затем напечатать стандартную ошибку:

        # Run the TeraSort MapReduce job.
        # Wait for the job to complete.
        # Print output and standard error file of the MapReduce job 
        Select-AzureSubscription $subscriptionName 
        $teravalidate | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

## <span id="java-code"></span></a> Код Java для программы MapReduce TerraSort

В этом разделе предоставлен для изучения код программы MapReduce TerraSort.

    /**
     * Licensed to the Apache Software Foundation (ASF) under one   
     * or more contributor license agreements.  See the NOTICE file 
     * distributed with this work for additional information    
     * regarding copyright ownership.  The ASF licenses this file   
     * to you under the Apache License, Version 2.0 (the    
     * "License"); you may not use this file except in compliance   
     * with the License.  You may obtain a copy of the License at   
     *  
     *     http://www.apache.org/licenses/LICENSE-2.0   
     *  
     * Unless required by applicable law or agreed to in writing, software  
     * distributed under the License is distributed on an "AS IS" BASIS,    
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
     * See the License for the specific language governing permissions and  
     * limitations under the License.   
     */ 

    package org.apache.hadoop.examples.terasort;

    import java.io.IOException;
    import java.io.PrintStream;
    import java.net.URI;
    import java.util.ArrayList;
    import java.util.List;

    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.filecache.DistributedCache;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.NullWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.Partitioner;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;

    /** 
     * Generates the sampled split points, launches the job,    
     * and waits for it to finish.  
     * <p>
     * To run the program:  
     * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>  
     */ 

    public class TeraSort extends Configured implements Tool {
      private static final Log LOG = LogFactory.getLog(TeraSort.class);

      /**   
       * A partitioner that splits text keys into roughly equal     
       * partitions in a global sorted order.   
       */   

      static class TotalOrderPartitioner implements Partitioner<Text,Text>{
        private TrieNode trie;
        private Text[] splitPoints;

        /**
         * A generic trie node
         */
        static abstract class TrieNode {
          private int level;
          TrieNode(int level) {
            this.level = level;
          }
          abstract int findPartition(Text key);
          abstract void print(PrintStream strm) throws IOException;
          int getLevel() {
            return level;
          }
        }

        /**
         * An inner trie node that contains 256 children based on the next
         * character.
         */
        static class InnerTrieNode extends TrieNode {
          private TrieNode[] child = new TrieNode[256];
          
          InnerTrieNode(int level) {
            super(level);
          }
          int findPartition(Text key) {
            int level = getLevel();
            if (key.getLength() <= level) {
              return child[0].findPartition(key);
            }
            return child[key.getBytes()[level]].findPartition(key);
          }
          void setChild(int idx, TrieNode child) {
            this.child[idx] = child;
          }
          void print(PrintStream strm) throws IOException {
            for(int ch=0; ch < 255; ++ch) {
              for(int i = 0; i < 2*getLevel(); ++i) {
                strm.print(' ');
              }
              strm.print(ch);
              strm.println(" ->");
              if (child[ch] != null) {
                child[ch].print(strm);
              }
            }
          }
        }

        /**
         * A leaf trie node that does string compares to figure out where the given
         * key belongs between lower..upper.
         */
        static class LeafTrieNode extends TrieNode {
          int lower;
          int upper;
          Text[] splitPoints;
          LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
            super(level);
            this.splitPoints = splitPoints;
            this.lower = lower;
            this.upper = upper;
          }
          int findPartition(Text key) {
            for(int i=lower; i<upper; ++i) {
              if (splitPoints[i].compareTo(key) >= 0) {
                return i;
              }
            }
            return upper;
          }
          void print(PrintStream strm) throws IOException {
            for(int i = 0; i < 2*getLevel(); ++i) {
              strm.print(' ');
            }
            strm.print(lower);
            strm.print(", ");
            strm.println(upper);
          }
        }


        /**
         * Read the cut points from the given sequence file.
         * @param fs the file system
         * @param p the path to read
         * @param job the job config
         * @return the strings to split the partitions on
         * @throws IOException
         */
        private static Text[] readPartitions(FileSystem fs, Path p, 
                                             JobConf job) throws IOException {
          SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
          List<Text> parts = new ArrayList<Text>();
          Text key = new Text();
          NullWritable value = NullWritable.get();
          while (reader.next(key, value)) {
            parts.add(key);
            key = new Text();
          }
          reader.close();
          return parts.toArray(new Text[parts.size()]);  
        }

        /**
         * Given a sorted set of cut points, build a trie that will find the correct
         * partition quickly.
         * @param splits the list of cut points
         * @param lower the lower bound of partitions 0..numPartitions-1
         * @param upper the upper bound of partitions 0..numPartitions-1
         * @param prefix the prefix that we have already checked against
         * @param maxDepth the maximum depth we will build a trie for
         * @return the trie node that will divide the splits correctly
         */
        private static TrieNode buildTrie(Text[] splits, int lower, int upper, 
                                          Text prefix, int maxDepth) {
          int depth = prefix.getLength();
          if (depth >= maxDepth || lower == upper) {
            return new LeafTrieNode(depth, splits, lower, upper);
          }
          InnerTrieNode result = new InnerTrieNode(depth);
          Text trial = new Text(prefix);
          // append an extra byte on to the prefix
          trial.append(new byte[1], 0, 1);
          int currentBound = lower;
          for(int ch = 0; ch < 255; ++ch) {
            trial.getBytes()[depth] = (byte) (ch + 1);
            lower = currentBound;
            while (currentBound < upper) {
              if (splits[currentBound].compareTo(trial) >= 0) {
                break;
              }
              currentBound += 1;
            }
            trial.getBytes()[depth] = (byte) ch;
            result.child[ch] = buildTrie(splits, lower, currentBound, trial, 
                                         maxDepth);
          }
          // pick up the rest
          trial.getBytes()[depth] = 127;
          result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                        maxDepth);
          return result;
        }

        public void configure(JobConf job) {
          try {
            FileSystem fs = FileSystem.getLocal(job);
            Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
            splitPoints = readPartitions(fs, partFile, job);
            trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
          } catch (IOException ie) {
            throw new IllegalArgumentException("can't read paritions file", ie);
          }
        }

        public TotalOrderPartitioner() {
        }

        public int getPartition(Text key, Text value, int numPartitions) {
          return trie.findPartition(key);
        }
        
      }
      
      public int run(String[] args) throws Exception {
        LOG.info("starting");
        JobConf job = (JobConf) getConf();
        Path inputDir = new Path(args[0]);
        inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
        Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
        URI partitionUri = new URI(partitionFile.toString() +
                                   "#" + TeraInputFormat.PARTITION_FILENAME);
        TeraInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        job.setJobName("TeraSort");
        job.setJarByClass(TeraSort.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
        job.setInputFormat(TeraInputFormat.class);
        job.setOutputFormat(TeraOutputFormat.class);
        job.setPartitionerClass(TotalOrderPartitioner.class);
        TeraInputFormat.writePartitionFile(job, partitionFile);
        DistributedCache.addCacheFile(partitionUri, job);
        DistributedCache.createSymlink(job);
        job.setInt("dfs.replication", 1);
        TeraOutputFormat.setFinalSync(job, true);
        JobClient.runJob(job);
        LOG.info("done");
        return 0;
      }

      /**
       * @param args
       */

      public static void main(String[] args) throws Exception {
        int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
        System.exit(res);
      }

    }

## <span id="summary"></span></a>Сводка

В этом примере показано, как выполнить серию заданий MapReduce с использованием HDInsight Azure, когда выходные данные для одного задания становятся входными данными для следующего задания в серии.

## <span id="next-steps"></span></a>Дальнейшие действия

Учебники по выполнению других примеров и инструкции по использованию заданий Pig, Hive и MapReduce в Azure HDInsight с использованием Azure PowerShell см. в следующих разделах:

-   [Приступая к работе с Azure HDInsight][Приступая к работе с Azure HDInsight]
-   [Пример: оценка числа пи][Пример: оценка числа пи]
-   [Пример: счетчик слов][Пример: счетчик слов]
-   [Пример: потоковая передача на C#][Пример: потоковая передача на C#]
-   [Использование Pig с HDInsight][Использование Pig с HDInsight]
-   [Использование Hive с HDInsight][Использование Hive с HDInsight]
-   [Документация по пакету Azure HDInsight SDK][Документация по пакету Azure HDInsight SDK]

  [Sortbenchmark]: http://sortbenchmark.org/
  [Бесплатное пробное использование Azure]: http://azure.microsoft.com/ru-ru/pricing/free-trial/
  [Подготовка кластеров HDInsight]: ../hdinsight-provision-clusters/
  [Установка и настройка Azure PowerShell]: ../install-configure-powershell/
  [Выполнение примера с использованием Azure PowerShell]: #run-sample
  [Код Java для программы TeraSort MapReduce]: #java-code
  [Сводка]: #summary
  [Дальнейшие действия]: #next-steps
  [Приступая к работе с Azure HDInsight]: ../hdinsight-get-started/
  [Пример: оценка числа пи]: ../hdinsight-sample-pi-estimator/
  [Пример: счетчик слов]: ../hdinsight-sample-wordcount/
  [Пример: потоковая передача на C#]: ../hdinsight-sample-csharp-streaming/
  [Использование Pig с HDInsight]: ../hdinsight-use-pig/
  [Использование Hive с HDInsight]: ../hdinsight-use-hive/
  [Документация по пакету Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx
