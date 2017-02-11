---
title: "Разработка программ MapReduce на Java для HDInsight на основе Linux | Документация Майкрософт"
description: "Ознакомьтесь с разработкой программ MapReduce на Java, а также с процессом их развертывания на HDInsight на основе Linux."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 93990e342f6bd8fcfe9781bcb021aabfd33e8572
ms.openlocfilehash: a63cedc57b863d03f65b99d4c2490d5e8aec25b5


---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight-linux"></a>Разработка программ MapReduce на Java для Hadoop в HDInsight на платформе Linux
Этот документ содержит пошаговые инструкции по использованию Apache Maven для создания приложения MapReduce, его развертывания и запуска в кластере HDInsight Hadoop под управлением Linux.

## <a name="a-nameprerequisitesaprerequisites"></a><a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 или более поздней версии (или эквивалент, например OpenJDK).
* [Apache Maven](http://maven.apache.org/)
* **Подписка Azure**
* **Интерфейс командной строки Azure**

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

## <a name="configure-environment-variables"></a>Настройка переменных среды
Во время установки Java и JDK могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* **JAVA_HOME** — эта переменная должна указывать на каталог, в который установлена среда выполнения Java (JRE). Например, в системе OS X, Unix или Linux она должна иметь примерно такое значение: `/usr/lib/jvm/java-7-oracle` В Windows значение будет приблизительно таким: `c:\Program Files (x86)\Java\jre1.7`
* **PATH** — эта переменная должна содержать следующие пути:
  
  * **JAVA_HOME** или эквивалентный путь;
  * **JAVA_HOME\bin** или эквивалентный путь.
  * Каталог, в который установлено ПО Maven.

## <a name="create-a-new-maven-project"></a>Создание нового проекта Maven
1. Из терминального сеанса или командной строки вашей среды разработки измените каталоги на расположение, в котором вы хотите хранить этот проект.
2. Используйте команду **mvn** , которая будет установлена вместе с Maven, для создания шаблона проекта.
   
        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
    При этом в текущем каталоге будет создан новый каталог с именем, указанным в параметре **artifactID** (в данном примере это **wordcountjava**). В этом каталоге будут находиться следующие элементы:
   
   * **pom.xml** — это [модель объекта проекта (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) , которая содержит информацию и подробности конфигурации, использующиеся при сборке проекта;
   * **src** — каталог, содержащий каталог **main/java/org/apache/hadoop/examples**, в котором будет разрабатываться приложение.
3. Удалите файл **src/test/java/org/apache/hadoop/examples/apptest.java** , так как он не используется в этом примере.

## <a name="add-dependencies"></a>Добавление зависимостей
1. Отредактируйте файл **pom.xml** и добавьте в раздел `<dependencies>` следующий код:
   
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-mapreduce-client-common</artifactId>
            <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
   
    Этот код сообщает Maven, что для проекта требуются библиотеки (перечисленные в параметре &lt;artifactId\>) определенной версии (указанной в параметре &lt;version\>). При компиляции он будет загружено из репозитория Maven по умолчанию. Можно воспользоваться [поиском по репозиторию Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) , чтобы получить дополнительную информацию.
   
    `<scope>provided</scope>` сообщает Maven, что эти зависимости не должны быть упакованы с приложением, так как они будут предоставлены кластером HDInsight во время выполнения.
2. Добавьте в файл **pom.xml** следующий код: Эти строки должны находиться в файле внутри тегов `<project>...</project>`; например между `</dependencies>` и `</project>`.
   
        <build>
            <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
                </configuration>
                <executions>
                <execution>
                    <phase>package</phase>
                      <goals>
                      <goal>shade</goal>
                      </goals>
                </execution>
                </executions>
              </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
            </plugins>
        </build>
   
    Первый подключаемый модуль настраивает [подключаемый модуль Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/), используемый для создания uberjar (иногда называется fatjar), который содержит требуемые приложению зависимости. Он также предотвращает дублирование лицензий в рамках пакета JAR, которое может вызвать проблемы в некоторых системах.
   
    Второй подключаемый модуль настраивает компилятор Maven, который используется для задания версии Java, необходимой для этого приложения. Версия должна совпадать с версией, используемой в кластере HDInsight.
3. Сохраните файл **pom.xml** .

## <a name="create-the-mapreduce-application"></a>Создание приложения MapReduce
1. Перейдите в каталог **wordcountjava/src/main/java/org/apache/hadoop/examples** и переименуйте файл **App.java** в **WordCount.java**.
2. Откройте файл **WordCount.java** в текстовом редакторе и замените его содержимое на следующее:
   
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
   
    Обратите внимание, что пакет имеет имя **org.apache.hadoop.examples**, а класс — имя **WordCount**. Эти имена будут использоваться при отправке задания MapReduce.
3. Сохраните файл.

## <a name="build-the-application"></a>Создание приложения
1. Перейдите в каталог **wordcountjava** (если вы еще не сделали это).
2. Выполните следующую команду, чтобы собрать файл JAR, содержащий приложение.
   
        mvn clean package
   
    При этом будут удалены остатки предыдущих сборок, загружены все неустановленные на текущий момент зависимости, затем будет произведена сборка и создание пакета приложения.
3. После завершения выполнения команды в каталог **wordcountjava/target** будет содержать файл с именем **wordcountjava-1.0-SNAPSHOT.jar**.
   
   > [!NOTE]
   > Файл **Wordcountjava-1.0-SNAPSHOT.jar** является uberjar, который содержит не только задание WordCount, но и зависимости, необходимые заданию во время выполнения.
   > 
   > 

## <a name="a-iduploadaupload-the-jar"></a><a id="upload"></a>Загрузка файла JAR
Воспользуйтесь следующей командой, чтобы загрузить файл JAR в головной узел HDInsight:

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

В результате файлы будут скопированы из локальной системы на головной узел.

> [!NOTE]
> Если для защиты учетной записи SSH использовался пароль, будет предложено ввести пароль. Если использовался ключ SSH, возможно, нужно будет использовать параметр `-i` и указать путь к закрытому ключу. Например, `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.
> 
> 

## <a name="a-namerunarun-the-mapreduce-job"></a><a name="run"></a>Выполнение задания MapReduce
1. Подключитесь к HDInsight через SSH, как описано в следующих статьях.
   
   * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
2. В сеансе SSH используйте следующую команду для запуска приложения MapReduce:
   
        yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/wordcountout
   
    Она будет использовать приложение WordCount MapReduce, чтобы подсчитать слова в файле davinci.txt и сохранить результаты в **wasbs:///example/data/wordcountout**. Файлы ввода и вывода хранятся в хранилище кластера по умолчанию.
3. После завершения задания просмотрите результаты с помощью следующей команды.
   
        hdfs dfs -cat wasbs:///example/data/wordcountout/*
   
    Должен появиться список слов и чисел со значениями, похожими на приведенные ниже:
   
        zeal    1
        zelus   1
        zenith  2

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Дальнейшие действия
В этом документе объясняется, как разработать задание MapReduce на Java. Чтобы узнать о других методах работы с HDInsight, см. следующие документы.

* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

Дополнительную информацию см. также в [Центре разработчика Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx




<!--HONumber=Feb17_HO2-->


