---
title: "MapReduce с Hadoop в HDInsight | Документация Майкрософт"
description: "Узнайте, как запускать задания MapReduce в Hadoop в кластере HDInsight. Мы запустим простую операцию подсчета слов, реализованную как задание MapReduce на Java."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: ab40eb8b53e2e685be52d24ecf2c32b193b12b85


---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Использование MapReduce в Hadoop в HDInsight

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

В этой статье рассказывается о том, как с помощью Curl запускать задания MapReduce в Hadoop в кластере HDInsight. Мы запустим простую операцию подсчета слов, реализованную как задание MapReduce на Java.

## <a name="a-idwhatisawhat-is-mapreduce"></a><a id="whatis"></a>Что такое MapReduce

Hadoop MapReduce — это программная платформа для написания заданий, обрабатывающих большие объемы данных. Входные данные разбиваются на независимые блоки, которые затем обрабатываются параллельно на узлах кластера. Задание MapReduce состоят из двух функций.

* **Mapper**(Модуль сопоставления) — принимает входные данные, анализирует их (обычно с помощью фильтрации и сортировки) и передает кортежи (пары «ключ-значение»).

* **Reducer**(Редуктор) — принимает кортежи, сформированные в модуле сопоставления, и выполняет операцию сводки, которая создает результат меньшего размера, объединяющий данные модуля сопоставления

На следующей диаграмме показан пример задания MapReduce, которое выполняет простую операцию подсчета слов:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

Выходные данные этого задания представляют собой частоту использования каждого слова в тексте, который был проанализирован.

* Процедура map берет каждую строку из входного текста в качестве входных данных и разбивает ее на слова. Она генерирует пару «ключ-значение» каждый раз, когда встречается слово, за которым следует 1. Перед отправкой на обработку редуктором выходные данные сортируются.
* Затем редуктор суммирует эти отдельные счетчики для каждого слова и выдает одну пару «ключ-значение», содержащую слово, за которым следует частота его использования.

Задание MapReduce может быть реализовано на различных языках. Java — это наиболее распространенная реализация, которая используется в данном документе для примера.

### <a name="hadoop-streaming"></a>Потоковая передача Hadoop

Языки или платформы на основе Java или виртуальной машины Java (например, Scalding или Cascading) можно запускать непосредственно как задание MapReduce, точно также как запускается приложение Java. Другие языки и платформы, например C# или Python, или автономные исполняемые файлы, при запуске должны использовать потоковую передачу Hadoop.

Потоковая передача Hadoop взаимодействует с модулем сопоставления и редуктором через STDIN и STDOUT. Модуль сопоставления и редуктор читают данные по одной строке из STDIN и записывают выходные данные в STDOUT. Каждая строка, которая читается или генерируется модулем сопоставления или редуктором, должна иметь формат пары «ключ-значение», разделенных символом табуляции:

    [key]/t[value]

Дополнительные сведения см. в документации по [потоковой передаче Hadoop](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Примеры использования потоковой передачи Hadoop с HDInsight см. в следующих статьях:

* [Разработка заданий Python MapReduce](hdinsight-hadoop-streaming-python.md)

## <a name="a-iddataaabout-the-sample-data"></a><a id="data"></a>Сведения о демонстрационных данных

В этом примере в качестве демонстрационных данных вы будете использовать блокноты Леонардо да Винчи, которые предоставляются в виде текстового документа в кластере HDInsight.

Демонстрационные данные хранятся в хранилище BLOB-объектов Azure, используемом HDInsight в качестве файловой системы по умолчанию для кластеров Hadoop. HDInsight может обращаться к файлам хранилища BLOB-объектов с помощью префикса **wasb** . Поэтому для доступа к файлу sample.log необходимо использовать следующий синтаксис:

    wasbs:///example/data/gutenberg/davinci.txt

Поскольку хранилище BLOB-объектов Azure является хранилищем по умолчанию для HDInsight, вы также можете обратиться к файлу, используя путь **/example/data/gutenberg/davinci.txt**

> [!NOTE]
> Предыдущий синтаксис **wasbs:///** используется для обращения к файлам, которые хранятся в контейнере хранилища по умолчанию для вашего кластера HDInsight. Если вы указали дополнительные учетные записи хранения при подготовке кластера и хотите обращаться к файлам из этих учетных записей, это можно сделать, указав имя контейнера и адрес учетной записи хранения. Например, **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.


## <a name="a-idjobaabout-the-example-mapreduce"></a><a id="job"></a>Сведения о примере MapReduce

Задание MapReduce, используемое в данном примере, находится в файле **wasbs://example/jars/hadoop-mapreduce-examples.jar** и предоставляется с кластером HDInsight. Это задание является примером операции подсчета слов, которая будет выполняться в файле **davinci.txt**.

> [!NOTE]
> В кластерах HDInsight 2.1 файл находится по этому адресу: **wasbs:///example/jars/hadoop-examples.jar**.

Для справки ниже приведен код Java задания MapReduce для счетчика слов:

```java
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
```

Инструкции по написанию собственного задания MapReduce см. в статье [Разработка программ MapReduce на Java для Hadoop в HDInsight на платформе Linux](hdinsight-develop-deploy-java-mapreduce-linux.md).

## <a name="a-idrunarun-the-mapreduce"></a><a id="run"></a>Запуск MapReduce

HDInsight может запускать задания HiveQL с помощью различных методов. Используйте следующую таблицу, чтобы решить, какой метод подходит вам, а затем перейдите по ссылке, чтобы открыть пошаговое руководство.

| **Используется**... | **... чтобы сделать** | ...with этим **кластером операционной системы** | ...из этого **кластера операционной системы** |
|:--- |:--- |:--- |:--- |
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) |Использование команды Hadoop через **SSH** |Linux |Linux, Unix, Mac OS X или Windows |
| [Curl](hdinsight-hadoop-use-mapreduce-curl.md) |Удаленная отправка заданий с помощью **REST** |Windows или Linux |Linux, Unix, Mac OS X или Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) |Удаленная отправка заданий с помощью **Windows PowerShell** |Windows или Linux |Windows |
| [Удаленный рабочий стол](hdinsight-hadoop-use-mapreduce-remote-desktop.md) |Использование команды Hadoop с помощью **удаленного рабочего стола** |Windows |Windows |

> [!IMPORTANT]
> Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Дальнейшие действия

Хотя платформа MapReduce предоставляет мощные диагностические функции, она может быть сложной для освоения. Некоторые платформы на основе Java упрощают определение приложений MapReduce, а такие технологии, как Pig и Hive, упрощают работу с данными в HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Разработка программ MapReduce на Java для HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
* [Разработка программ MapReduce с потоковой передачей Python для HDInsight](hdinsight-hadoop-streaming-python.md)
* [Разработка заданий Scalding MapReduce с Apache Hadoop в HDInsight](hdinsight-hadoop-mapreduce-scalding.md)
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Запуск примеров HDInsight][hdinsight-samples]

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif



<!--HONumber=Feb17_HO3-->


