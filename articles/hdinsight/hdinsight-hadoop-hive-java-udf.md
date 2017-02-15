---
title: "Работа с определяемыми пользователем функциями Java с использованием Hive в HDInsight | Документация Майкрософт"
description: "Узнайте, как создать и использовать определяемые пользователем функции Java из Hive в HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 240ba2289ededf7a65c71e2f5007cf3a52ca4f84


---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Работа с определяемыми пользователем функциями Java с использованием Hive в HDInsight
Hive отлично подходит для работы с данными в HDInsight, но в некоторых случаях требуется язык программирования более общего назначения. Hive позволяет создавать определяемые пользовательские функции (UDF) с использованием различных языков программирования. Здесь вы узнаете, как использовать определяемую пользователем функцию Java из Hive.

## <a name="requirements"></a>Требования
* Подписка Azure
* Кластер HDInsight (под управлением Windows или Linux)
  
  > [!NOTE]
  > Большинство шагов, описанных в этой статье, подходят для кластеров обоих типов. Тем не менее шаги по отправке скомпилированных определяемых пользователем функций в кластер и их выполнению относятся только к кластерам под управлением Linux. Здесь также приведены ссылки на дополнительные сведения, которые можно использовать при работе с кластерами под управлением Windows.
  > 
  > 
* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 или более поздней версии (или эквивалент, например OpenJDK).
* [Apache Maven](http://maven.apache.org/)
* Текстовый редактор или Java IDE.
  
  > [!IMPORTANT]
  > Если вы используете сервер HDInsight под управлением Linux, но создаете файлы Python на клиенте Windows, следует использовать редактор, который использует символ LF в качестве конца строки. Если вы не уверены, использует ли редактор символы LF или CRLF, см. раздел [Устранение неполадок](#troubleshooting), в котором описано, как удалять знаки CR с помощью служебных программ в кластере HDInsight.
  > 
  > 

## <a name="create-an-example-udf"></a>Создание примера определяемой пользователем функции
1. Чтобы создать проект Maven, введите следующую команду в командной строке.
   
        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
   > [!NOTE]
   > При использовании PowerShell параметры необходимо заключить в кавычки. Пример: `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.
   > 
   > 
   
    Будет создан каталог **exampleudf**, который будет содержать проект Maven.
2. После создания проекта удалите каталог **exampleudf/src/test**, который создан как часть проекта. Этот каталог не нужен для нашего примера.
3. Откройте файл **exampleudf/pom.xml** и замените текущую запись `<dependencies>` следующей:
   
        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>
   
    Эти записи содержат версию Hadoop и Hive, используемую в кластерах HDInsight 3.3 и 3.4. Сведения о версиях Hadoop и Hive, включенных в HDInsight, можно найти в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md) .
   
    В конце файла добавьте раздел `<build>` перед строкой `</project>`. Этот раздел должен содержать следующее:
   
        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <!-- Keep us from getting a can't overwrite file error -->
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                            </transformer>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                            </transformer>
                        </transformers>
                        <!-- Keep us from getting a bad signature error -->
                        <filters>
                            <filter>
                                <artifact>*:*</artifact>
                                <excludes>
                                    <exclude>META-INF/*.SF</exclude>
                                    <exclude>META-INF/*.DSA</exclude>
                                    <exclude>META-INF/*.RSA</exclude>
                                </excludes>
                            </filter>
                        </filters>
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
            </plugins>
        </build>
   
    Эти записи определяют способ построения проекта. В частности, используемую в проекте версию Java и способ построения uberjar для развертывания в кластер.
   
    После внесения изменений сохраните файл.
4. Переименуйте файл **exampleudf/src/main/java/com/microsoft/examples/App.java** на **ExampleUDF.java**, а затем откройте его в редакторе.
5. Замените содержимое файла **ExampleUDF.java** на следующее и сохраните файл.
   
        package com.microsoft.examples;
   
        import org.apache.hadoop.hive.ql.exec.Description;
        import org.apache.hadoop.hive.ql.exec.UDF;
        import org.apache.hadoop.io.*;
   
        // Description of the UDF
        @Description(
            name="ExampleUDF",
            value="returns a lower case version of the input string.",
            extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
        )
        public class ExampleUDF extends UDF {
            // Accept a string input
            public String evaluate(String input) {
                // If the value is null, return a null
                if(input == null)
                    return null;
                // Lowercase the input string and return it
                return input.toLowerCase();
            }
        }
   
    Этот код реализует определяемую пользователем функцию, которая принимает строковое значение и возвращает строку в нижнем регистре.

## <a name="build-and-install-the-udf"></a>Создание и установка определяемой пользователем функции
1. Выполните следующую команду, чтобы скомпилировать и упаковать определяемую пользователем функцию.
   
        mvn compile package
   
    В результате определяемая пользователем функция будет создана и упакована в файл **exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar**.
2. Чтобы скопировать файл в кластер HDInsight, воспользуйтесь командой `scp` .
   
        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
   
    Замените **myuser** именем учетной записи пользователя SSH для кластера. Замените **mycluster** именем кластера. Если для учетной записи SSH используется пароль, будет предложено его ввести. Если используется сертификат, может потребоваться использовать параметр `-i` , чтобы указать соответствующий файл закрытого ключа.
3. Подключитесь к кластеру с помощью SSH. 
   
        ssh myuser@mycluster-ssh.azurehdinsight.net
   
    Дополнительные сведения об использовании SSH с HDInsight см. в приведенных ниже документах.
   
   * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
4. В сеансе SSH скопируйте JAR-файл в хранилище HDInsight.
   
        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Использование определяемой пользователем функции из Hive
1. В сеансе SSH используйте следующую команду, чтобы запустить клиент Beeline.
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
   
    В этой команде предполагается, что вы использовали для кластера учетную запись входа по умолчанию — **admin** .
2. После появления командной строки `jdbc:hive2://localhost:10001/>` введите следующую команду, чтобы добавить определяемую пользователем функцию в Hive и предоставить ее как функцию.
   
        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
3. Используйте определяемую пользователем функцию, чтобы преобразовать значения, полученные из таблицы, в строки нижнего регистра.
   
        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
   
    В результате выполнения этой команды в таблице будет выбрана платформа устройства (Android, Windows, iOS и т. д.) и отображены строки, преобразованные в нижний регистр. Выходные данные будут выглядеть следующим образом:
   
        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Дальнейшие действия
Другие способы работы с Hive см. в статье об [использовании Hive в HDInsight](hdinsight-use-hive.md).

Дополнительные сведения об определяемых пользователем функциях Hive см. в разделе [Hive Operators and User-Defined Functions](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) (Операторы Hive и определяемые пользователем функции) вики-сайта Hive на сайте apache.org.




<!--HONumber=Nov16_HO3-->


