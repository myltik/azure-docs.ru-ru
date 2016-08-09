<properties
   pageTitle="Обработка событий из концентраторов событий с помощью Storm в HDInsight с использованием Java | Microsoft Azure"
   description="Узнайте, как обрабатывать данные концентраторов событий, используя топологии Java Storm, созданные с помощью Maven."
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="larryfr"/>

# Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)

Служба концентраторов событий Azure позволяет обрабатывать значительные объемы данных из веб-сайтов, приложений и устройств. Воронка службы концентраторов событий упрощает использование Apache Storm в HDInsight для анализа этих данных в режиме реального времени. Вы также можете записывать данные в службу концентраторов событий из Storm с помощью сита службы концентраторов событий.

Из этого учебника вы узнаете, как использовать воронку и сито службы концентраторов событий для чтения и записи данных в топологии Storm на основе Java.

## Предварительные требования

* Кластер Apache Storm в HDInsight. Чтобы создать кластер, ознакомьтесь с одной из следующих статей:

    - [Storm под управлением Linux в кластере HDInsigh](hdinsight-apache-storm-tutorial-get-started-linux.md). Прочтите эту статью, если вы хотите использовать SSH для работы с кластером на клиентах под управлением Linux, Unix, OS X или Windows.

    - [Storm под управлением Windows в кластере HDInsigh](hdinsight-apache-storm-tutorial-get-started.md). Прочтите эту статью, если вы хотите использовать PowerShell для работы с кластером на клиенте под управлением Windows.

    > [AZURE.NOTE] Действия, описанные в этом документе, относятся к использованию Storm в кластере HDInsigh версии 3.3 или более поздней. Эти кластеры предоставляют Storm 0.10.0 и Hadoop 2.7, которые сокращают количество действий, необходимых для работы этого примера.
    >
    > Версию этого примера, работающую со Storm 0.9.3 в кластере HDInsight 3.2, см. в версии [Storm 0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) репозитория примеров.

* [Концентратор событий Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* [Oracle Java Developer Kit (JDK) версии 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) или аналогичный, например [OpenJDK](http://openjdk.java.net/).

* [Maven](https://maven.apache.org/download.cgi). Maven — это система сборки проектов Java.

* Текстовый редактор либо интегрированная среда разработки (IDE) Java.

	> [AZURE.NOTE] В редакторе или интегрированной среде разработки могут быть предусмотрены специальные функциональные возможности для работы с Maven, не описанные в настоящем документе. Информацию о возможностях среды редактирования см. в документации продукта, который вы используете.

 * Клиент SSH. Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях:

    - [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* SCP-клиент. Доступен во всех системах Linux, Unix и OS X. В клиентах Windows мы рекомендуем использовать PSCP-клиент, доступный для загрузки на [странице загрузки PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##Общие сведения о примере

Пример [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) включает две топологии:

Топология __com.microsoft.example.EventHubWriter__ записывает случайные данные в концентратор событий Azure. Данные генерируются с помощью воронки и представляют собой случайные идентификатор устройства и значение устройства. Определенное устройство получает команду на передачу идентификатора строки и числового значения.

Топология __com.microsoft.example.EventHubReader__ считывает данные из концентратора событий (данные, записанные топологией EventHubWriter) и сохраняет их в HDFS (в данном случае используется хранилище WASB, так как оно было создано и протестировано для работы с Azure HDInsight) в каталоге /devicedata.

Перед записью в концентратор событий данные преобразуются в формат JSON, а при чтении содержимое файла JSON анализируется и преобразуется в кортежи. Используется следующий формат JSON:

    { "deviceId": "unique identifier", "deviceValue": some value }

Для хранения данных в концентраторе событий используется документ JSON, потому что он содержит информацию о структуре содержимого и позволяет не полагаться на внутренние механизмы форматирования воронки и сита службы концентраторов событий.

###Конфигурация проекта

Файл **POM.xml** содержит сведения о конфигурации проекта Maven. Интересные фрагменты:

####Зависимость EventHubs Storm Spout

    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-eventhubs</artifactId>
      <version>0.10.0</version>
    </dependency>

Этот код добавляет зависимость для пакета storm-eventhubs, который содержит как воронку для чтения данных из концентратора событий, так и сито для записи в него.

> [AZURE.NOTE] Этот пакет доступен только для версии Storm 0.10.0 или более поздней. При использовании Storm 0.9.3 необходимо вручную установить пакет воронки, предоставленный корпорацией Майкрософт. Примеры работы со Storm 0.9.3 см. в ветви [Storm 0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) репозитория примеров.

####Компоненты HdfsBolt и WASB

Для хранения данных в HDFS обычно используется HdfsBolt. Однако в кластерах HDInsight в качестве хранилища данных по умолчанию используется хранилище Azure (WASB), поэтому нам придется загрузить несколько компонентов, которые позволят HdfsBolt распознавать файловую систему WASB.

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
        <version>0.10.0</version>
        </dependency>
    <!--So HdfsBolt knows how to talk to WASB -->
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-azure</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.1</version>
        <exclusions>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
        </exclusions>
    </dependency>

> [AZURE.NOTE] При работе с более ранней версией HDInsight, например версией 3.2, эти компоненты необходимо регистрировать вручную. Примеры этого, а также пользовательские фрагменты кода, необходимые для более ранних кластеров HDInsight, можно найти в ветви [Storm 0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) репозитория примеров.

####maven-compiler-plugin

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

Этот код сообщает Maven, что при сборке проекта необходимо обеспечить совместимость с версией Java 7, которая используется кластерами HDInsight.

####maven-shade-plugin

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

Этот код используется для упаковки решения в JAR-файл, содержащий как код проекта, так и необходимые зависимости. Он также используется для:

* Переименования файлов лицензий для зависимостей. Если не переименовать эти файлы, при запуске в кластерах HDInsight под управлением Windows может возникнуть ошибка.

* Исключения цифровой подписи. Если не исключить цифровую подпись, при запуске в кластере HDInsight может возникнуть ошибка.

* Различные реализации одного и того же интерфейса должны быть объединены в одну запись. Если это не так, вы получите сообщения об ошибках, связанные с тем, что сит Storm-HDFS не понимает, как взаимодействовать с файловой системой WASB.

####exec-maven-plugin

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

Позволяет запускать топологию в локальной среде разработки с помощью следующей команды:

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

Пример: `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

####Раздел resources

    <resources>
      <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
          <include>EventHubs.properties</include>
          <include>core-site.xml</include>
        </includes>
      </resource>
    </resources>

Определяет требуемые для проекта ресурсы:

- **EventHubs.properties**: содержит сведения, используемые для подключения к концентратору событий Azure.
- **core-site.xml**: содержит сведения о хранилище Azure, которое используется кластером HDInsight.

В этих разделах необходимо указать информацию о концентраторе событий и кластере HDInsight.

##Настройка переменных среды

Во время установки Java и JDK на компьютере, где ведется разработка, могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* **JAVA\_HOME** — эта переменная должна указывать на каталог, в который установлена среда выполнения Java (JRE). Например, в дистрибутиве Unix или Linux она должна иметь примерно такое значение: `/usr/lib/jvm/java-7-oracle` В Windows значение будет приблизительно таким: `c:\Program Files (x86)\Java\jre1.7`

* **PATH** — эта переменная должна содержать следующие пути:

	* **JAVA\_HOME** или эквивалентный путь;

	* **JAVA\_HOME\\bin** или эквивалентный путь.

	* Каталог, в который установлено ПО Maven.

## Настройка концентраторов Event Hub

Служба концентраторов событий используется в качестве источника данных для этого примера. Чтобы создать новый концентратор Event Hub, выполните следующие действия.

1. На [классическом портале Azure](https://manage.windowsazure.com) выберите **СОЗДАТЬ** > **Служебная шина** > **Концентратор событий** > **Настраиваемое создание**.

2. В диалоговом окне **Добавление нового концентратора событий**введите **Имя концентратора событий**, выберите **Регион** для создания в нем концентратора и создайте новое пространство имен или выберите существующее. Щелкните **стрелку** для продолжения.

	![страница мастера 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] Для уменьшения задержек и затрат в поле **Расположение** следует выбрать то же расположение, в котором находится сервер Storm в HDInsight.

2. В диалоговом окне **Настройка концентраторов событий** введите значения параметров **Количество разделов** и **Хранение сообщений**. В этом примере числу разделов присвойте значение 10, а хранению сообщений — 1. Запомните количество разделов, поскольку это значение понадобится позже.

	![страница мастера 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. После создания концентратора событий выберите пространство имен, выберите **Концентраторы событий**, а затем выберите концентратор событий, который был создан ранее.

4. Выберите **Настроить** и создайте две новые политики доступа, используя следующую информацию.

	<table>
	<tr><th>Имя</th><th>Разрешения</th></tr>
	<tr><td>Модуль записи</td><td>Отправка</td></tr>
	<tr><td>читатель.</td><td>Прослушивание</td></tr>
	</table>

	После создания разрешений выберите значок **Сохранить** в нижней части страницы. Будут созданы политики совместного доступа, которые будут использоваться для отправки (writer) сообщений этому концентратору событий и их прослушивания (reader).

	![политики](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. После сохранения политик используйте **Генератор общего ключа доступа** в нижней части страницы для получения ключа для политик **writer** и **reader**. Сохраните ключ для дальнейшего использования.

## Загрузка и сборка проекта

1. Загрузите проект [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) с сайта GitHub в виде ZIP-архива либо клонируйте проект локально с помощью [git](https://git-scm.com/).

2. Используйте следующую команду, чтобы собрать и упаковать проект:

        mvn package

    В результате будут загружены необходимые зависимости, а затем проект будет собран и упакован. Выходные данные будут сохранены в файле __EventHubExample 1.0-SNAPSHOT.jar__ в папке __/target__.

## Развертывание топологий

JAR-файл, созданный проектом, содержит две топологии: __com.microsoft.example.EventHubWriter__ и __com.microsoft.example.EventHubReader__. Топологию EventHubWriter нужно запускать первой, так как она записывает в концентратор событий события, которые затем считываются топологией EventHubReader.

###Если используется кластер под управлением Linux

1. Используя SCP, скопируйте JAR-пакет в свой кластер HDInsight. Замените USERNAME именем пользователя SSH для кластера. Замените CLUSTERNAME именем кластера HDInsight.

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Если для учетной записи SSH используется пароль, будет предложено его ввести. Если для учетной записи SSH используется ключ, возможно, вам потребуется использовать параметр `-i`, чтобы указать путь к файлу ключа. Например, `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.

    > [AZURE.NOTE] На рабочих станциях под управлением Windows команды SCP не поддерживаются. Мы рекомендуем использовать PSCP-клиент, доступный для скачивания на [странице загрузки PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    Эта команда скопирует файл в основной каталог пользователя SSH в кластере.

1. После завершения передачи подключитесь к кластеру HDInsight по протоколу SSH. Замените **USERNAME** именем пользователя SSH. Замените **CLUSTERNAME** именем кластера HDInsight.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] Если для учетной записи SSH используется пароль, будет предложено его ввести. Если для учетной записи SSH используется ключ, возможно, вам потребуется использовать параметр `-i`, чтобы указать путь к файлу ключа. В следующем примере выполняется загрузка закрытого ключа из файла `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

    При использовании PuTTY введите `CLUSTERNAME-ssh.azurehdinsight.net` в поле __Имя узла (или IP-адрес)__, а затем щелкните __Открыть__ для подключения. Появится запрос на ввод имени учетной записи SSH.

    > [AZURE.NOTE] Если для учетной записи SSH используется пароль, будет предложено его ввести. Если для учетной записи используется ключ SSH, возможно, вам потребуется выполнить следующие действия, чтобы выбрать его:
    >
    > 1. В разделе **Категория** последовательно разверните **Подключение**, **SSH** и выберите **Аутентификация**.
    > 2. Нажмите кнопку **Обзор** и выберите PPK-файл, содержащий закрытый ключ.
    > 3. Щелкните __Открыть__ для подключения.

2. Запустите топологии, используя следующую команду:

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    Топологии будут запущены и получат понятные имена reader и writer.

3. Подождите пару минут, чтобы топологии могли записать события в концентратор событий и считать их оттуда, а затем убедитесь, что топология EventHubReader сохраняет данные в хранилище HDInsight. Для этого выполните следующую команду:

        hadoop fs -ls /devicedata

    В результате выполнения команды должен быть возвращен список файлов, аналогичный этому:

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

    > [AZURE.NOTE] Для некоторых файлов может отображаться размер 0. Это означает, что они были созданы топологией EventHubReader, но данных в них пока нет.

    Чтобы просмотреть содержимое файлов, используйте следующую команду:

        hadoop fs -text /devicedata/*.txt

    В результате вы увидите данные, аналогичные этим:

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

    В первом столбце указан идентификатор устройства, а во втором — значение устройства.

4. Чтобы остановить топологии, используйте следующие команды:

        storm kill reader
        storm kill writer

###Если используется кластер под управлением Windows

1. Откройте в браузере страницу https://CLUSTERNAME.azurehdinsight.net. При появлении соответствующего запроса введите учетные данные администратора кластера HDInsight. Откроется панель мониторинга Storm.

2. Раскройте список __JAR-файл__, чтобы найти и выбрать файл EventHubExample 1.0-SNAPSHOT.jar в среде сборки.

3. В поле __Имя класса__ введите `com.mirosoft.example.EventHubWriter`.

4. В поле __Дополнительные параметры__ введите `writer`. Нажмите кнопку __Отправить__, чтобы передать JAR-файл и запустить топологию EventHubWriter.

5. После запуска первой топологии используйте эту форму для запуска топологии EventHubReader:

    * __JAR-файл__: выберите ранее переданный файл EventHubExample-1.0-SNAPSHOT.jar.
    * __Имя класса__: введите `com.microsoft.example.EventHubReader`.
    * __Дополнительные параметры__: введите `reader`.

    Нажмите кнопку «Отправить», чтобы запустить топологию EventHubReader.

6. Подождите несколько минут, пока топологии сгенерируют события и сохранят их в хранилище Azure, а затем перейдите на вкладку __Консоль запросов Hadoop__ вверху страницы __Панель мониторинга Storm__.

7. В __консоли запросов__ выберите __Редактор Hive__ и замените код по умолчанию `select * from hivesampletable` на следующий:

        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasbs:///devicedata/';
        select * from devicedata limit 10;

    Щелкните __Выбрать__, чтобы выполнить запрос. В результате будет возвращено 10 строк из данных, записанных топологией EventHubReader в хранилище Azure (WASB). После выполнения запроса вы увидите примерно следующее:

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

8. Выберите __Панель мониторинга Storm__ вверху страницы, а затем выберите __Пользовательский интерфейс Storm__. В __пользовательском интерфейсе Storm__ перейдите по ссылке топологии __reader__, а затем нажмите кнопку __Прервать__, чтобы остановить эту топологию. Повторите процедуру для топологии __writer__.



### Контрольные точки

EventHubSpout периодически передает информацию о своем состоянии на узел Zookeeper, который сохраняет текущее смещение для прочитанных сообщений из очереди. Это позволяет компоненту принимать сообщения при сохраненном смещении в следующих сценариях:

* Работа экземпляра компонента завершена аварийно, и он будет перезапущен.

* Расширение или сжатие путем добавления или удаления узлов кластера.

* Топология удалена и перезапущена **под тем же именем**.

####В кластерах HDInsight под управлением Windows

Вы можете экспортировать и импортировать сохраненные контрольные точки в WASB (хранилище Azure в кластере HDInsight). Сценарии для этого находятся в папке Storm кластера HDInsight: **c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**.

>[AZURE.NOTE] Номер версии в пути может отличаться, так как версия Storm, установленная в кластере, может в будущем измениться.

Сценарии в этом каталоге:

* **stormmeta\_import.cmd**: импорт всех метаданных Storm из контейнера хранилища кластера по умолчанию в Zookeeper.

* **stormmeta\_export.cmd**: экспорт всех метаданных Storm из Zookeeper в контейнер хранилища кластера по умолчанию.

* **stormmeta\_delete.cmd**: удаление всех метаданных Storm из Zookeeper.

Экспорт и импорт позволяют сохранять данные контрольных точек, если понадобится удалить кластер, но нужно будет возобновить обработку из текущего смещения в концентраторе при повторном развертывании нового кластера в Интернет.

> [AZURE.NOTE] Поскольку данные сохраняются в контейнере хранилища по умолчанию, новый кластер **должен** использовать учетную запись хранения и контейнер предыдущего кластера.

## Удаление кластера

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##Устранение неполадок

Если файлы не сохраняются в папке /devicedata (ни с помощью команды `hadoop fs -ls /devicedata`, ни с помощью команды Hive в консоли запросов), найдите ошибки, возвращаемые топологиями, в пользовательском интерфейсе Storm.

Дополнительные сведения об использовании пользовательского интерфейса Storm см. в следующих статьях.

* Если вы используете Storm в кластере HDInsight __под управлением Linux__, см. статью [Развертывание и управление топологиями Apache Storm в HDInsight под управлением Linux](hdinsight-storm-deploy-monitor-topology-linux.md).

* Если вы используете Storm в кластере HDInsight __под управлением Windows__, см. статью [Развертывание и управление топологиями Apache Storm в HDInsight под управлением Windows](hdinsight-storm-deploy-monitor-topology-linux.md).

##Дальнейшие действия

* [Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0727_2016-->