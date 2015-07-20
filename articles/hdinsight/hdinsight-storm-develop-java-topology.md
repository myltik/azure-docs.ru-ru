<properties
   pageTitle="Разработка топологий на платформе Java для Apache Storm | Microsoft Azure"
   description="Информация о создании топологии Storm на языке Java путем создания простой топологии для подсчета статистики."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/08/2015"
   ms.author="larryfr"/>

#Разработка топологий на основе Java для базовых приложений подсчета слов с помощью Apache Storm и Maven в HDInsight

Информация о базовом процессе создания топологии на платформе Java для Apache Storm в HDInsight с помощью Maven. Следуя указаниям в этой статье, вы сможете создать с помощью Maven приложение для подсчета слов на языке Java. Хотя инструкции приведены для Eclipse, вы можете использовать любой текстовый редактор по своему выбору.

После выполнения действий, описанных в этом документе, вы получите базовую топологию, которую можно развернуть в Apache Storm в HDInsight.

##Предварительные требования

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">Java Developer Kit (JDK) версии 7</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a> — это система сборки проектов Java.

* Текстовый редактор, такой Блокнот, <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>, <a href="http://www.sublimetext.com/" target="_blank">Sublime Text</a>, <a href="https://atom.io/" target="_blank">Atom.io</a>, <a href="http://brackets.io/" target="_blank">Brackets.io</a>. Либо интегрированная среда разработки (IDE), такая как <a href="https://eclipse.org/" target="_blank">Eclipse</a> (версии Luna или более поздней).

	> [AZURE.NOTE]В редакторе или интегрированной среде разработки могут быть предусмотрены специальные функциональные возможности для работы с Maven, не описанные в настоящем документе. Информацию о возможностях среды редактирования см. в документации продукта, который вы используете.

##Настройка переменных среды

Во время установки Java и JDK могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* **JAVA_HOME** — эта переменная должна указывать на каталог, в который установлена среда выполнения Java (JRE). Например, в дистрибутиве Unix или Linux она должна иметь примерно такое значение: `/usr/lib/jvm/java-7-oracle` В Windows значение будет приблизительно таким: `c:\Program Files (x86)\Java\jre1.7`

* **PATH** — эта переменная должна содержать следующие пути:

	* **JAVA_HOME** или эквивалентный путь;

	* **JAVA_HOME\\bin** или эквивалентный путь.

	* Каталог, в который установлено ПО Maven.

##Создание нового проекта Maven

В командной строке введите следующую команду для создания нового проекта Maven с именем **WordCount**.

	mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

В результате в текущем местоположении будет создан новый каталог с именем **WordCount**, содержащий базовый проект Maven.

Каталог **WordCount** будет содержать следующие элементы:

* **pom.xml** — содержит параметры проекта Maven.

* **src\\main\\java\\com\\microsoft\\example** — содержит код приложения.

* **src\\test\\java\\com\\microsoft\\example** — содержит тесты для приложения. В этом примере тесты создаваться не будут.

###Удаление примера кода

Так как приложение будет создаваться заново, удалите созданные файлы теста и приложения:

*  **src\\test\\java\\com\\microsoft\\example\\AppTest.java**

*  **src\\main\\java\\com\\microsoft\\example\\App.java**

##Добавление зависимостей

Так как это топология Storm, необходимо добавить зависимость из компонентов Storm. Откройте файл **pom.xml** и добавьте следующий код в раздел **&lt;dependencies>**:

	<dependency>
	  <groupId>org.apache.storm</groupId>
	  <artifactId>storm-core</artifactId>
	  <version>0.9.2-incubating</version>
	  <!-- keep storm out of the jar-with-dependencies -->
	  <scope>provided</scope>
	</dependency>

Во время компиляции Maven использует эту информацию для поиска **storm-core** в репозитории Maven. Сначала выполняется поиск в репозитории на локальном компьютере. Если файлы отсутствуют, они будут скачаны из общедоступного репозитория Maven и сохранены в локальном репозитории.

> [AZURE.NOTE]Обратите внимание, что в этом разделе мы добавили строку `<scope>provided</scope>`. Она указывает Maven, что **storm-core** нужно исключить из всех созданных JAR-файлов, так как этот элемент будет предоставлен системой. Это позволяет немного уменьшить размер создаваемых пакетов и обеспечить использование битов **storm-core**, предусмотренных в Storm в кластере HDInsight.

##Конфигурация построения

Подключаемые модули Maven позволяют настроить этапы построения проекта, например способ компиляции проекта или упаковки его в JAR-файл. Откройте **pom.xml** и добавьте следующий код непосредственно над строкой `</project>`.

	<build>
	  <plugins>
	  </plugins>
	</build>

Этот раздел будет использоваться для добавления подключаемых модулей и других параметров конфигурации построения.

###Добавление подключаемых модулей

Для технологий Storm удобен <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">подключаемый модуль Exec Maven</a>, так как он позволяет легко запускать топологию локально в среде разработки. Добавьте следующий код в раздел `<plugins>` файла **pom.xml**, чтобы включить в него подключаемый модуль Exec Maven.

	<plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

Другим полезным подключаемым модулем является <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">Apache Maven Compiler</a>, который используется для изменения параметров компиляции. Он нам нужен в основном для того, чтобы изменить версию Java, используемую Maven для исходного объекта и объекта назначения приложения. Необходима версия 1.7.

Добавьте следующий код в раздел `<plugins>` файла **pom.xml**, чтобы включить в него подключаемый модуль Apache Maven Compiler и указать версию 1.7 для исходного объекта и объекта назначения.

	<plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

##Создание топологии

Топология Storm на платформе Java состоит из трех компонентов, которые необходимо создать или указать как зависимость.

* **Воронки** — чтение данных из внешних источников и отправка потоков данных в топологию.

* **Сита** — выполнение обработки потоков, поступающих из воронок или других сит, и создание одного или нескольких потоков.

* **Топология** — определяет взаимное расположение воронок и сит и предоставляет точку входа для топологии.

###Создание «воронки»

Чтобы снизить требования к настройке внешних источников данных, следующая воронка просто выдает случайные предложения. Это измененная версия воронки, предоставленной в <a href="https://github.com/apache/storm/blob/master/examples/storm-starter/" target="_blank">примере Storm-Starter</a>.

> [AZURE.NOTE]Пример воронки, которая считывает информацию из внешнего источника данных, см. в одном из следующих примеров.
>
> * <a href="https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java" target="_blank">TwitterSampleSpout</a> — пример воронки, считывающей информацию из Twitter.
>
> * <a href="https://github.com/apache/storm/tree/master/external/storm-kafka" target="_blank">Storm-Kafka</a> — воронка, считывающая информацию из Kafka.

Создайте для воронки новый файл с именем **RandomSentenceSpout.java** в каталоге **src\\main\\java\\com\\microsoft\\example** и используйте следующий код в качестве содержимого.

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

Просмотрите комментарии в коде, чтобы понять принцип действия этой воронки.

> [AZURE.NOTE]Хотя в данной топологии используется только одна воронка, в других топологиях их может быть несколько. При этом данные могут поступать в топологию из различных источников.

###Создание «сит»

Сита выполняют обработку данных. В этой топологии у нас их два:

* **SplitSentence** — разделяет предложения, отправленные **RandomSentenceSpout**, на отдельные слова.

* **WordCount** — подсчитывает частоту употребления каждого слова.

> [AZURE.NOTE]Сита могут выполнять буквально любые операции, например вычисление, сохранение, взаимодействие с внешними компонентами, и т. д.

Создайте два новых файла, **SplitSentence.java** и **WordCount.Java**, в каталоге **src\\main\\java\\com\\microsoft\\example**. Измените содержимое файлов следующим образом:

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
      //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
          //Get the word contents from the tuple
          String word = tuple.getString(0);
          //Have we counted any already?
          Integer count = counts.get(word);
          if (count == null)
            count = 0;
          //Increment the count and store it
          count++;
          counts.put(word, count);
          //Emit the word and the current count
          collector.emit(new Values(word, count));
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
          declarer.declare(new Fields("word", "count"));
        }
      }

Просмотрите комментарии в коде, чтобы понять принцип действия каждого сита.

###Создание топологии

Топология связывает воронки и сита на диаграмме, определяя порядок обмена данными между компонентами. Она также предоставляет указания по обеспечению параллелизма, которые Storm использует при создании экземпляров компонентов в кластере.

Ниже приведена базовая диаграмма компонентов этой топологии.

![диаграмма, показывающая упорядочение воронок и сит](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

Для реализации топологии создайте новый файл с именем **WordCountTopology.java** в каталоге **src\\main\\java\\com\\microsoft\\example**. Измените содержимое файла следующим образом:

	package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        conf.setDebug(true);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

Просмотрите комментарии в коде, чтобы понять, как определяется топология и как она затем отправляется в кластер.

##Локальная проверка топологии

После сохранения файлов используйте следующую команду для локального тестирования топологии.

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

Во время их выполнения топология будет отображать информацию о запуске. Затем она отображает строки, похожие на эти, как предложения, отправленные из воронки и обработанные ситом.

    15398 [Thread-16-split] INFO  backtype.storm.daemon.executor - Processing received message source: spout:10, stream: default, id: {}, [an apple a day keeps thedoctor away]]
    15398 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [an]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [an]
    15399 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [apple]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [apple]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [a]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.task - Emitting: count default [an, 53]
    15400 [Thread-12-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [a]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [day]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.task - Emitting: count default [apple, 53]
    15401 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [day]
    15401 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [keeps]
    15401 [Thread-12-count] INFO  backtype.storm.daemon.task - Emitting: count default [a, 53]

Как видно из этих выходных данных произошло следующее:

1. Воронка выдает текст an apple a day keeps the doctor away.

2. Сито Split начинает отправлять отдельные слова из этого предложения.

3. Сито Count начинает отправлять каждое слово и данные о частоте отправления этого слова.

Согласно данным, отправленным ситом Count, слово apple было отправлено 53 раза. Значение счетчика будет расти, пока работает топология, так как одни и те же предложения отправляются снова и снова, а счетчик никогда не сбрасывается.

##Trident

Trident — это высокоуровневая абстракция, предоставляемая Storm. Он поддерживает обработку с отслеживанием состояний. Основное преимущество Trident заключается в том, что эта технология гарантирует одноразовую обработку каждого сообщения, которое входит в топологию. Этого сложно добиться в необработанной топологии Java, которая гарантирует, что каждое сообщение будет обработано по крайней мере один раз. Существуют также другие отличия, например встроенные компоненты, которые можно использовать вместо создания сит. На самом деле сита полностью заменяются менее общими компонентами, например фильтрами, проекциями и функциями.

Приложения Trident можно создавать с помощью проектов Maven. Для этого используются те же основные действия, приведенные ранее в этой статье, отличается только код.

Дополнительные сведения о Trident см. в статье <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Обзор API Trident</a> (на английском языке).

Пример приложения Trident см. в статье [Анализ популярных тем в Twitter с помощью Apache Storm в HDInsight](hdinsight-storm-twitter-trending.md) (на английском языке).

##Дальнейшие действия

Вы узнали, как создавать топологии Storm с помощью Java. Теперь ознакомьтесь со следующими статьями.

* [Развертывание топологий Apache Storm в HDInsight и управление ими](hdinsight-storm-deploy-monitor-topology.md)

* [Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Другие примеры топологий Storm см. в разделе [Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=July15_HO2-->