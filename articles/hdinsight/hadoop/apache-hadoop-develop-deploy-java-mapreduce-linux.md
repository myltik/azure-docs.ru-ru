---
title: Создание Java MapReduce для Hadoop в Azure HDInsight | Документация Майкрософт
description: Узнайте, как использовать Apache Maven, чтобы создать приложение MapReduce на основе Java, а также запустить его с помощью Hadoop в Azure HDInsight.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: ''
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: Java
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: c245b30a48be73f189dcf48502134cf36e93ba20
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169546"
---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight"></a>Разработка программ MapReduce на Java для Hadoop в HDInsight

Узнайте, как использовать Apache Maven, чтобы создать приложение MapReduce на основе Java, а также запустить его с помощью Hadoop в Azure HDInsight.

> [!NOTE]
> Этот пример в последний раз был протестирован с помощью HDInsight 3.6.

## <a name="prerequisites"></a>Предварительные требования

* Пакет [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 или более поздней версии (или эквивалентный пакет, например OpenJDK).
    
    > [!NOTE]
    > Для HDInsight 3.4 и более ранних версий используйте Java 7. В HDInsight 3.5 и более поздних версиях используется Java 8.

* [Apache Maven](http://maven.apache.org/)

## <a name="configure-development-environment"></a>Настройка среды разработки

Во время установки Java и JDK могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* `JAVA_HOME`. Эта переменная должна указывать на каталог, в который установлена среда выполнения Java (JRE). Например, в системе OS X, Unix или Linux она должна иметь примерно такое значение: `/usr/lib/jvm/java-7-oracle`. В Windows значение будет приблизительно таким: `c:\Program Files (x86)\Java\jre1.7`

* `PATH`. Эта переменная должна содержать следующие пути:
  
  * `JAVA_HOME` или эквивалентный путь;

  * `JAVA_HOME\bin` или эквивалентный путь.

  * Каталог, в который установлено ПО Maven.

## <a name="create-a-maven-project"></a>Создание проекта Maven

1. Из терминального сеанса или командной строки вашей среды разработки измените каталоги на расположение, в котором вы хотите хранить этот проект.

2. Используйте команду `mvn`, которая будет установлена вместе с Maven, для создания шаблона проекта.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > При использовании PowerShell параметры `-D` необходимо заключить в кавычки.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Эта команда создает каталог с именем, указанным в параметре `artifactID` (в этом примере — **wordcountjava**). Этот каталог содержит следующие элементы:

   * `pom.xml` — это [объектная модель проекта (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html), которая содержит сведения и данные конфигурации, использующиеся при сборке проекта;

   * `src` — это каталог, содержащий приложение.

3. Удалите файл `src/test/java/org/apache/hadoop/examples/apptest.java`. Он не используется в этом примере.

## <a name="add-dependencies"></a>Добавление зависимостей

1. Отредактируйте файл `pom.xml` и добавьте в раздел `<dependencies>` следующий код:
   
   ```xml
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-examples</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-client-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
   ```

    Это означает, что требуются библиотеки (перечисленные в параметре &lt;artifactId\>) определенной версии (указанной в параметре &lt;version\>). При компиляции эти зависимости скачиваются из репозитория Maven по умолчанию. Можно воспользоваться [поиском по репозиторию Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) , чтобы получить дополнительную информацию.
   
    `<scope>provided</scope>` сообщает Maven, что эти зависимости не должны быть упакованы с приложением, так как они будут предоставлены кластером HDInsight во время выполнения.

    > [!IMPORTANT]
    > Используемая версия должна соответствовать версии Hadoop в кластере. Дополнительные сведения о версиях см. в статье [Что представляют собой различные компоненты и версии Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md)

2. Добавьте в файл `pom.xml` следующий код. Эти строки должны находиться в файле внутри тегов `<project>...</project>` (например, между тегами `</dependencies>` и `</project>`).

   ```xml
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
            <version>3.6.1</version>
            <configuration>
            <source>1.8</source>
            <target>1.8</target>
            </configuration>
        </plugin>
        </plugins>
    </build>
   ```

    Первый подключаемый модуль настраивает [подключаемый модуль Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/), используемый для создания uberjar (иногда называется fatjar), который содержит требуемые приложению зависимости. Он также предотвращает дублирование лицензий в рамках пакета JAR, которое может вызвать проблемы в некоторых системах.

    Второй подключаемый модуль настраивает целевую версию Java.

    > [!NOTE]
    > Для HDInsight 3.4 и более ранних версий используйте Java 7. В HDInsight 3.5 и более поздних версиях используется Java 8.

3. Сохраните файл `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Создание приложения MapReduce

1. Перейдите к каталогу `wordcountjava/src/main/java/org/apache/hadoop/examples` и переименуйте файл `App.java` на `WordCount.java`.

2. Откройте файл `WordCount.java` в текстовом редакторе и замените его содержимое следующим:
   
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
   
    Обратите внимание, что пакет имеет имя `org.apache.hadoop.examples`, а класс — имя `WordCount`. Эти имена будут использоваться при отправке задания MapReduce.

3. Сохраните файл.

## <a name="build-the-application"></a>Создание приложения

1. Перейдите в каталог `wordcountjava` (если вы еще не сделали это).

2. Выполните следующую команду, чтобы собрать файл JAR, содержащий приложение.

   ```
   mvn clean package
   ```

    Эта команда удаляет остатки предыдущих сборок, скачивает все неустановленные на текущий момент зависимости, а затем собирает и упаковывает приложение.

3. После выполнения команды каталог `wordcountjava/target` будет содержать файл с именем `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > Файл `wordcountjava-1.0-SNAPSHOT.jar` является uberjar, который содержит не только задание WordCount, но и зависимости, необходимые заданию во время выполнения.

## <a id="upload"></a>Загрузка файла JAR

Воспользуйтесь следующей командой, чтобы загрузить файл JAR в головной узел HDInsight:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Эта команда копирует файлы из локальной системы на головной узел. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run"></a>Выполнение задания MapReduce в Hadoop

1. Подключитесь к HDInsight с помощью протокола SSH. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. В сеансе SSH используйте следующую команду для запуска приложения MapReduce:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    Эта команда запускает приложение WordCount MapReduce. Входной файл — `/example/data/gutenberg/davinci.txt`, а выходной каталог — `/example/data/wordcountout`. Файлы ввода и вывода хранятся в хранилище кластера по умолчанию.

3. По завершении задания воспользуйтесь следующей командой, чтобы просмотреть результат:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Должен появиться список слов и чисел со значениями, похожими на приведенные ниже:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Дальнейшие действия

В этом документе объясняется, как разработать задание MapReduce на Java. Чтобы узнать о других методах работы с HDInsight, см. следующие документы.

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

Дополнительную информацию см. также в [Центре разработчика Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

