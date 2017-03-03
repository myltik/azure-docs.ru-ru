---
title: "Обработка событий из концентраторов событий с помощью Storm в HDInsight с использованием Java | Документация Майкрософт"
description: "Узнайте, как обрабатывать данные концентраторов событий, используя топологии Java Storm, созданные с помощью Maven."
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: 85f56e223210f14615f7e4e1c87e35111b238aac
ms.lasthandoff: 02/21/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)

Узнайте, как использовать концентраторы событий Azure со Storm в HDInsight. В этом примере используются компоненты на основе Java для чтения и записи данных в концентраторах событий Azure.

Служба концентраторов событий Azure позволяет обрабатывать значительные объемы данных из веб-сайтов, приложений и устройств. Элемент spout концентратора событий упрощает использование Apache Storm в HDInsight для анализа этих данных в режиме реального времени. Вы также можете записывать данные в службу концентраторов событий из Storm с помощью сита службы концентраторов событий.

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Storm в HDInsight версии 3.5. Дополнительные сведения см. в статье [Руководство по Apache Storm в HDInsight: начало работы с анализом больших объемов данных в HDInsight с помощью примеров Storm Starter](hdinsight-apache-storm-tutorial-get-started-linux.md).
    
    > [!IMPORTANT]
    > Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* [Концентратор событий Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* [Oracle Java Developer Kit (JDK) версии 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) или аналогичный, например [OpenJDK](http://openjdk.java.net/).

* [Maven](https://maven.apache.org/download.cgi) — это система сборки проектов Java.

* Текстовый редактор либо интегрированная среда разработки (IDE).
  
  > [!NOTE]
  > В редакторе или интегрированной среде разработки могут быть предусмотрены специальные функциональные возможности для работы с Maven, не описанные в настоящем документе. Информацию о возможностях среды редактирования см. в документации продукта, который вы используете.
  
  * Клиент SSH. Дополнительные сведения см. в одном из следующих документов:
    
    * [Использование SSH с HDInsight (Hadoop) на платформе Windows, Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

    * [Использование SSH с HDInsight (Hadoop) в PuTTY на базе Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

* SCP-клиент. Команда `scp` доступна во всех системах Linux, Unix и OS X. Linux, Unix и OS X (включая Bash в Windows 10). Для систем Windows, которые не содержат команду `scp`, мы рекомендуем использовать PSCP. Средство PSCP доступно на [странице для скачивания PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="understanding-the-example"></a>Общие сведения о примере

Пример [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) включает две топологии:

**com.microsoft.example.EventHubWriter** записывает случайные данные в концентратор событий Azure. Данные генерируются с помощью воронки и представляют собой случайные идентификатор устройства и значение устройства. Определенное устройство получает команду на передачу идентификатора строки и числового значения.

**com.microsoft.example.EventHubReader** считывает данные из концентратора событий и сохраняет их в хранилище кластера по умолчанию в каталоге /devicedata.

Перед записью в концентратор событий данные преобразуются в формат JSON, а при чтении содержимое файла JSON анализируется и преобразуется в кортежи. Используется следующий формат JSON:

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>Конфигурация проекта

Файл **POM.xml** содержит сведения о конфигурации проекта Maven. Интересные фрагменты:

#### <a name="the-eventhubs-storm-spout-dependency"></a>Зависимость EventHubs Storm Spout

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

Этот XML-файл определяет зависимость для пакета eventhubs, который содержит как элемент spout для чтения данных из концентратора событий, так и элемент bolt для записи в него.

> [!NOTE]
> Этот пакет устанавливается далее в этом документе.

#### <a name="the-hdfsbolt-and-wasb-components"></a>Компоненты HdfsBolt и WASB

Для хранения данных в HDFS обычно используется HdfsBolt. Однако в кластерах HDInsight в качестве хранилища данных по умолчанию используется хранилище Azure (WASB), поэтому нам придется загрузить несколько компонентов, которые позволят HdfsBolt распознавать файловую систему WASB.

```xml
<!--HdfsBolt stuff -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-hdfs</artifactId>
    <exclusions>
    <exclusion>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
    </exclusion>
    <exclusion>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
    </exclusion>
    </exclusions>
    <version>${storm.version}</version>
</dependency>
<!--So HdfsBolt knows how to talk to WASB -->
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-client</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-hdfs</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-azure</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>${hadoop.version}</version>
    <exclusions>
    <exclusion>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
    </exclusion>
    </exclusions>
</dependency>
```

> [!NOTE]
> При работе с более ранней версией HDInsight, например версией 3.2, эти компоненты необходимо регистрировать вручную. Примеры работы доступны в ветви [Storm&0;.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) репозитория примеров.

#### <a name="the-maven-compiler-plugin"></a>maven-compiler-plugin

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.3.2</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

Этот подключаемый модуль настраивает проект для создания выходных данных для Java 8, которые использует HDInsight 3.5.

#### <a name="the-maven-shade-plugin"></a>maven-shade-plugin

```xml
<!-- build an uber jar -->
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-shade-plugin</artifactId>
<version>2.3</version>
<configuration>
    <transformers>
    <!-- Keep us from getting a can't overwrite file error -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
    <!-- Keep us from getting errors when trying to use WASB from the storm-hdfs bolt -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
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
```

Этот подключаемый модуль настраивает решение, чтобы упаковать выходные данные в файл типа uber jar. JAR-файл содержит код проекта и требуемые зависимости. Он также используется для:

* Переименования файлов лицензии для зависимостей.
* Исключения групп безопасности и подписей.
* Различные реализации одного и того же интерфейса должны быть объединены в одну запись.

Эти параметры конфигурации предотвращают ошибки во время выполнения.

#### <a name="the-exec-maven-plugin"></a>exec-maven-plugin

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.2.1</version>
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
```

Эти параметры конфигурации позволяют легко запускать топологию локально в среде разработки. Топологию можно запустить локально, используя следующий синтаксис:

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

Например, `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

#### <a name="the-resources-section"></a>Раздел resources

```xml
<resources>
    <resource>
    <directory>${basedir}/conf</directory>
    <filtering>false</filtering>
    <includes>
        <include>EventHubs.properties</include>
    </includes>
    </resource>
</resources>
```

Эта конфигурация определяет включенные в проект ресурсы, не относящиеся к Java. Например, файл **EventHubs.properties** содержит сведения, используемые для подключения к концентратору событий Azure.

## <a name="configure-environment-variables"></a>Настройка переменных среды

Во время установки Java и JDK на компьютере, где ведется разработка, могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* **JAVA_HOME** — эта переменная должна указывать на каталог, в который установлена среда выполнения Java (JRE). Например, в дистрибутиве Unix или Linux она должна иметь примерно такое значение: `/usr/lib/jvm/java-7-oracle` В Windows значение будет приблизительно таким: `c:\Program Files (x86)\Java\jre1.7`
* **PATH** — эта переменная должна содержать следующие пути:
  
  * **JAVA_HOME** или эквивалентный путь;
  * **JAVA_HOME\bin** или эквивалентный путь.
  * Каталог, в который установлено ПО Maven.

## <a name="download-and-register-the-eventhub-components"></a>Скачивание и регистрация компонентов концентратора событий

1. Скачайте `storm-eventhubs-1.0.2-jar-with-dependencies.jar` по адресу [https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar](https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar). Этот файл содержит компонент элементов spout и bolt для записи и чтения из концентраторов событий.

2. Для регистрации компонентов в локальном репозитории Maven используйте следующую команду:
    
        mvn install:install-file -Dfile=storm-eventhubs-1.0.2-jar-with-dependencies.jar -DgroupId=com.microsoft -DartifactId=eventhubs -Dversion=1.0.2 -Dpackaging=jar
    
    Измените параметр `-Dfile=`, чтобы указать местоположение скачанного файла.

    При компиляции он будет установлен в репозиторий Maven с помощью этих команд.

## <a name="configure-event-hub"></a>Настройка концентраторов Event Hub

Служба концентраторов событий используется в качестве источника данных для этого примера. Чтобы создать концентратор событий, выполните следующие действия.

1. На [классическом портале Azure](https://manage.windowsazure.com) выберите **Создать** > **Служебная шина** > **Концентратор событий** > **Настраиваемое создание**.

2. В диалоговом окне **Добавить новый концентратор событий** введите **имя концентратора событий**. Выберите **регион** для его создания и создайте пространство имен или выберите имеющееся. Затем щелкните **стрелку**, чтобы продолжить.
   
    ![страница мастера 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)
   
   > [!NOTE]
   > Чтобы сократить задержки и затраты, в поле **Расположение** следует выбрать то же расположение, в котором находится сервер Storm в HDInsight.

3. В диалоговом окне **Настроить концентратор событий** введите значения параметров **Количество разделов** и **Хранение сообщений**. В этом примере числу разделов присвойте значение 10, а хранению сообщений — 1. Запомните количество разделов, так как позже вам понадобится это значение.
   
    ![страница мастера 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

4. После создания концентратора событий выберите пространство имен, выберите **Концентраторы событий**, а затем выберите концентратор событий, который был создан ранее.
5. Выберите **Настроить** и создайте две политики доступа, используя следующие сведения.
   
    <table>
    <tr><th>Имя</th><th>Разрешения</th></tr>
    <tr><td>Модуль записи</td><td>Отправка</td></tr>
    <tr><td>читатель.</td><td>Прослушивание</td></tr>
    </table>
   
    После создания разрешений выберите значок **Сохранить** в нижней части страницы. Эти политики общего доступа используются для чтения и записи в концентратор событий.
   
    ![политики](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

6. После сохранения политик используйте **генератор общего ключа доступа** в нижней части страницы для получения ключа для политик **writer** и **reader**. Сохраните эти значения.

## <a name="download-and-build-the-project"></a>Загрузка и сборка проекта

1. Скачайте проект [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) с сайта GitHub в виде ZIP-файла либо клонируйте проект локально с помощью [git](https://git-scm.com/).

2. Используйте следующую команду, чтобы собрать и упаковать проект:
   
        mvn package
   
    Эта команда скачивает необходимые зависимости, а также создает и упаковывает проект. Выходные данные сохраняются в файл **EventHubExample-1.0-SNAPSHOT.jar** в каталоге **/target**.

## <a name="deploy-the-topologies"></a>Развертывание топологий

JAR-файл, созданный проектом, содержит две топологии: **com.microsoft.example.EventHubWriter** и **com.microsoft.example.EventHubReader**. Топологию EventHubWriter нужно запускать первой, так как она записывает в концентратор событий события, которые затем считываются топологией EventHubReader.

1. Используя SCP, скопируйте JAR-пакет в свой кластер HDInsight. Замените USERNAME именем пользователя SSH для кластера. Замените CLUSTERNAME именем кластера HDInsight.
   
        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.
   
    Если для учетной записи SSH используется пароль, вам потребуется его ввести. Если для учетной записи SSH используется ключ, возможно, вам потребуется использовать параметр `-i` , чтобы указать путь к файлу ключа. Пример: `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.
   
   > [!NOTE]
   > На рабочих станциях под управлением Windows команды SCP не поддерживаются. Мы рекомендуем использовать PSCP-клиент, доступный для скачивания на [странице загрузки PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
   
    Эта команда скопирует файл в основной каталог пользователя SSH в кластере.

2. После завершения передачи подключитесь к кластеру HDInsight по протоколу SSH. Замените **USERNAME** именем пользователя SSH. Замените **CLUSTERNAME** именем кластера HDInsight.
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
   > [!NOTE]
   > Если для учетной записи SSH используется пароль, вам потребуется его ввести. Если для учетной записи SSH используется ключ, возможно, вам потребуется использовать параметр `-i` , чтобы указать путь к файлу ключа. В следующем примере выполняется загрузка закрытого ключа из файла `~/.ssh/id_rsa`:
   > 
   > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    При использовании PuTTY введите `CLUSTERNAME-ssh.azurehdinsight.net` в поле **Имя узла (или IP-адрес)**, а затем щелкните **Открыть** для подключения. Появится запрос на ввод имени учетной записи SSH.
   
   > [!NOTE]
   > Если для учетной записи SSH используется пароль, вам потребуется его ввести. Если для учетной записи используется ключ SSH, возможно, вам потребуется выполнить следующие действия, чтобы выбрать его:
   > 
   > 1. В разделе **Категория** последовательно разверните **Подключение**, **SSH** и выберите **Проверка подлинности**.
   > 2. Нажмите кнопку **Обзор** и выберите PPK-файл, содержащий закрытый ключ.
   > 3. Щелкните **Открыть** для подключения.

3. Запустите топологии, используя следующую команду:
   
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader
   
    Эти команды запускают топологии с понятными именами reader и writer.

4. Подождите одну минуту, пока топологии создадут данные. Чтобы убедиться, что данные записываются в хранилище HDInsight, используйте следующую команду:
   
        hadoop fs -ls /devicedata
   
    Эта команда возвращает список файлов, аналогичных следующим:
   
        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt
   
   > [!NOTE]
   > Для некоторых файлов может отображаться размер 0. Это означает, что они были созданы топологией EventHubReader, но данных в них пока нет.
   
    Чтобы просмотреть содержимое файлов, используйте следующую команду:
   
        hadoop fs -text /devicedata/*.txt
   
    Эта команда возвращает данные в следующем формате:
   
        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574
   
    В первом столбце указан идентификатор устройства, а во втором — значение устройства.

5. Чтобы остановить топологии, используйте следующие команды:
   
        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если файлы отсутствуют в каталоге /devicedata, найдите возвращенные топологиями ошибки с помощью пользовательского интерфейса Storm.

Дополнительные сведения об использовании пользовательского интерфейса Storm см. в следующих статьях.

* Если вы используете Storm в кластере HDInsight **под управлением Linux**, ознакомьтесь со статьей [Развертывание топологий Apache Storm в HDInsight под управлением Linux и управление ими](hdinsight-storm-deploy-monitor-topology-linux.md).

* Если вы используете Storm в кластере HDInsight **под управлением Windows**, ознакомьтесь со статьей [Развертывание топологий Apache Storm в HDInsight под управлением Windows и управление ими](hdinsight-storm-deploy-monitor-topology-linux.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md)


