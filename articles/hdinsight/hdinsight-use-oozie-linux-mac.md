---
title: "Использование рабочих процессов Hadoop Oozie в HDInsight на основе Linux | Документация Майкрософт"
description: "Использование Hadoop Oozie в HDInsight на основе Linux. Вы узнаете, как определить рабочий процесс и отправить задание для Oozie."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 4502ac63428446f61b5876c73ed9a6f5065159cd
ms.lasthandoff: 04/12/2017


---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-hdinsight"></a>Использование Oozie с Hadoop для определения и запуска рабочих процессов в HDInsight под управлением Linux

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Узнайте, как с помощью Apache Oozie определить рабочий процесс, использующий Hive и Sqoop, и запустить рабочий процесс в кластере HDInsight под управлением Linux.

Apache Oozie — это система рабочих процессов и координации, управляющая заданиями Hadoop. Это решение интегрировано со стеком Hadoop и поддерживает задания Hadoop Apache MapReduce, Apache Pig, Apache Hive и Apache Sqoop. Его также можно использовать для планирования системных заданий, например, Java-программ и сценариев оболочки.

> [!NOTE]
> Еще один способ определения рабочих процессов в HDInsight - Azure Data Factory. Дополнительные сведения об использовании действий Pig и Hive см. в статье [Преобразование данных в фабрике данных Azure][azure-data-factory-pig-hive].

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

* **Консоль Azure**: ознакомьтесь с [установкой и настройкой консоли Azure](../cli-install-nodejs.md).

* **Кластер HDInsight**: ознакомьтесь с [началом работы с HDInsight в Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

  > [!IMPORTANT]
  > Для выполнения действий, описанных в этом документе, необходим кластер HDInsight, который использует Linux. Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* **База данных SQL Azure**. Создается в процессе выполнения шагов этого руководства.

## <a name="example-workflow"></a>Пример рабочего процесса

Рабочий процесс, используемый в этой статье, включает два действия. Действия являются определениями задач, таких как запуск Hive, Sqoop, MapReduce или других процессов:

![Схема рабочих процессов][img-workflow-diagram]

1. Действие Hive запускает скрипт HiveQL для извлечения записей из таблицы **hivesampletable** , входящей в состав в HDInsight. Каждая строка данных описывает посещение с определенного мобильного устройства. Формат записи таков:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Скрипт Hive, используемый в данном документе, подсчитывает общее количество посещений для каждой платформы (например, Android или iPhone) и сохраняет результаты в новой таблице Hive.

    Дополнительные сведения о Hive см. в статье [Использование Hive с HDInsight][hdinsight-use-hive].

2. Действие Sqoop экспортирует содержимое новой таблицы Hive в таблицу в базе данных SQL Azure. Дополнительные сведения о Sqoop см. в статье [Использование Sqoop с Hadoop в HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Сведения о поддерживаемых версиях Oozie в кластерах HDInsight см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?][hdinsight-versions]

## <a name="create-the-working-directory"></a>Создайте рабочий каталог

Ресурсы, необходимые для выполнения задания, должны находиться в том же каталоге. В этом примере используется **wasbs:///tutorials/useoozie**. Для создания этого каталога и каталога данных для новой таблицы Hive, которую создаст этот рабочий процесс, воспользуйтесь следующей командой:

```
hdfs dfs -mkdir -p /tutorials/useoozie/data
```

> [!NOTE]
> Каталог `-p` означает, что будут созданы все промежуточные каталоги для указанного пути, если их не существует. В каталоге **data** хранятся данные, используемые скриптом **useooziewf.hql**.

Также можно выполнить следующую команду, которая гарантирует, что при выполнении заданий Hive и Sqoop Oozie сможет работать от имени вашей учетной записи. Замените **USERNAME** на свое имя пользователя:

```
sudo adduser USERNAME users
```

При появлении ошибки о том, что пользователь уже является членом группы пользователей, ее можно просто игнорировать.

## <a name="add-a-database-driver"></a>Добавление драйвера базы данных

Поскольку этот рабочий процесс использует Sqoop для экспорта данных в базу данных SQL, необходимо предоставить копию драйвера JDBC, используемого для обращения к базе данных SQL. Используйте следующую команду для копирования драйвера в рабочий каталог:

```
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

Если рабочий процесс использует другие ресурсы, например JAR-файл, содержащий приложение MapReduce, необходимо также добавить эти ресурсы.

## <a name="define-the-hive-query"></a>Определение запроса Hive

Выполните следующие действия, чтобы создать скрипт HiveQL для определения запроса. Этот запрос будет использоваться в рабочем процессе Oozie далее в этом документе.

1. Подключитесь к кластеру с помощью SSH. Следующая команда является примером использования команды `ssh`. Замените __USERNAME__ именем пользователя для подключения к кластеру с помощью SSH. Замените __CLUSTERNAME__ именем кластера HDInsight.

    ```
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. В сеансе SSH создайте новый файл, используя следующую команду:

    ```
    nano useooziewf.hql
    ```

3. После открытия редактора nano введите следующее в качестве содержимого файла:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    В данном сценарии используются три следующие переменные:

    * **${hiveTableName}** содержит имя создаваемой таблицы;

    * **${hiveDataFolder}** содержит расположения файлов данных для таблицы.

    Файл с определением рабочего процесса (workflow.xml в этом руководстве) передает эти значения в скрипт HiveQL во время выполнения.

4. Нажмите Ctrl-X, чтобы закрыть редактор. При появлении запроса нажмите **Y** для сохранения файла, затем нажмите клавишу **ВВОД**, чтобы использовать имя файла **useooziewf.hql**.
5. Используйте приведенные ниже команды для копирования **useooziewf.hql** в **wasbs:///tutorials/useoozie/useooziewf.hql**.

    ```
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Эти команды сохраняют файл **useooziewf.hql** в учетной записи хранения Azure, связанной с выбранным кластером. Это позволит сохранить файл даже после удаления кластера. Это позволяет сэкономить деньги, удалив неиспользуемые кластеры и при этом сохранив задания и рабочие процессы.

## <a name="define-the-workflow"></a>Определение рабочего процесса

Определения рабочих процессов Oozie записываются в формате hPDL (язык определения процессов XML). Для определения рабочего процесса выполните следующие действия.

1. Для создания и открытия файла выполните следующий запрос:

    ```
    nano workflow.xml
    ```

2. После открытия редактора nano введите следующее в качестве содержимого файла:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>sqljdbc41.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    В рабочем процессе определены два действия:

   * **RunHiveScript**: это действие при запуске, которое запускает сценарий Hive **useooziewf.hql**.

   * **RunSqoopExport**: это действие экспортирует созданные данные из скрипта Hive в базу данных SQL с использованием Sqoop. Это действие будет выполнено, только если действие **RunHiveScript** завершится успешно.

     > [!NOTE]
     > Дополнительные сведения о рабочем процессе Oozie и использовании его действий см. в [документации по Apache Oozie 4.0][apache-oozie-400] (для HDInsight версии 3.0) или [документации по Apache Oozie 3.3.2][apache-oozie-332] (для HDInsight версии 2.1).

     Обратите внимание, что в рабочем процессе есть несколько параметров, таких как `${jobTracker}`, которые заменяются значениями, которые вы укажете в определении задания, как описано далее в этой статье.

     Также обратите внимание на параметр `<archive>sqljdbc4.jar</arcive>` в разделе Sqoop. Этот параметр означает, что этот архив должен стать доступным для Sqoop при выполнении этого действия.

3. Нажмите клавиши Ctrl + X, затем **Y** и **ВВОД**, чтобы сохранить файл.

4. Скопируйте файл **workflow.xml** в каталог **/tutorials/useoozie/workflow.xml** с помощью следующей команды.

    ```
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Создание базы данных

Следуйте инструкциям из [руководства по созданию базы данных SQL](../sql-database/sql-database-get-started.md) , чтобы создать базу данных. При создании задайте для базы данных имя **oozietest** . Запишите также имя, которое использовалось для сервера базы данных, так как оно понадобится при работе со следующим разделом.

### <a name="create-the-table"></a>Создание таблицы

> [!NOTE]
> Существует множество способов подключения к базе данных SQL для создания таблицы. В приведенных ниже действиях используется [FreeTDS](http://www.freetds.org/) из кластера HDInsight.


1. Для установки FreeTDS в кластер HDInsight воспользуйтесь следующей командой:

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. После установки FreeTDS используйте следующую команду для подключения к созданному серверу базы данных SQL:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    Вы получите результат, близкий к приведенному ниже.

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. В командной строке `1>` введите следующее:

    ```
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Если вводится инструкция `GO`, то оцениваются предыдущие инструкции. Эта инструкция создаст новую таблицу с именем **mobiledata**, в которую Sqoop будет записывать данные.

    Используйте следующую команду для проверки создания таблицы:

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    Вы должны увидеть результат, аналогичный приведенному ниже:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    oozietest       dbo     mobiledata      BASE TABLE
    ```

4. Enter `exit` at the `1>` , чтобы выйти из служебной программы tsql.

## <a name="create-the-job-definition"></a>Создание определения задания

Определение задания содержит информацию о местонахождении файла workflow.xml, а также других файлов, используемых рабочим процессом (например, useooziewf.hql). В нем также определяются значения свойств, используемых в рабочем процессе и сопутствующих файлов.

1. Для получения полного адреса WASB хранилища по умолчанию воспользуйтесь следующей командой. Немного позже он будет использован в файле конфигурации.

    ```
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Эта команда возвращает следующую информацию:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Если кластер HDInsight использует службу хранилища Azure в качестве хранилища по умолчанию, содержимое элемента `<value>` начинается с `wasbs://`. Если же используется Azure Data Lake Store, оно начинается с `adl://`.

    Сохраните содержимое элемента `<value>`, которое потребуется нам на следующих шагах.

2. Воспользуйтесь следующей командой для получения полного имени домена головного узла кластера: Оно используется для адреса кластера в JobTracker. Немного позже он будет использован в файле конфигурации.

    ```
    hostname -f
    ```

    Эта команда возвращает следующую информацию:

    ```hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net```

    JobTracker использует порт 8050, поэтому полный адрес для JobTracker выглядит так: `hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8050`.

3. Используйте следующую команду для создания конфигурации определения задания Oozie:

    ```
    nano job.xml
    ```

4. После открытия редактора nano введите следующее в качестве содержимого файла:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>JOBTRACKERADDRESS</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>YourName</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * Замените все вхождения **wasbs://mycontainer@mystorageaccount.blob.core.windows.net** значением, полученным ранее для хранилища по умолчанию.

     > [!WARNING]
     > Если используется путь `wasb`, его необходимо указать полностью. Не сокращайте его до строки `wasb:///`.

   * Замените **JOBTRACKERADDRESS** на адрес JobTracker/ResourceManager, полученный ранее.
   * Замените **YourName** на ваше имя пользователя для кластера HDInsight.
   * Замените **serverName**, **adminLogin** и **adminPassword** соответствующими значениями для своей базы данных SQL Azure.

     Большая часть информации в этом файле используется для заполнения значений, используемых в файлах workflow.xml или ooziewf.hql (например, ${nameNode}.)

     > [!NOTE]
     > Параметр **oozie.wf.application.path** определяет местоположение файла workflow.xml, содержащего рабочий процесс, который запускается этим заданием.

5. Нажмите клавиши Ctrl + X, затем **Y** и **ВВОД**, чтобы сохранить файл.

## <a name="submit-and-manage-the-job"></a>Отправка задания и управление им

Далее используется команда Oozie для отправки рабочих процессов Oozie в кластер и управления ими. Команда Oozie предоставляет удобный интерфейс для [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> При использовании команды Oozie необходимо использовать полное доменное имя для головного узла HDInsight. Это полное доменное имя доступно только из кластера, или если кластер находится в виртуальной сети Azure, с других компьютеров той же сети.


1. Для получения URL-адреса службы Oozie воспользуйтесь следующей командой:

    ```
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Эта команда возвращает следующую информацию:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    Здесь фрагмент `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` обозначает URL-адрес, который используется в команде Oozie.

2. Используйте следующую команду для создания переменной среды для URL-адреса, чтобы не вводить его в каждой команде:

    ```
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Замените URL-адрес полученным ранее.
3. Чтобы отправить задание, воспользуйтесь следующей командой:

    ```
    oozie job -config job.xml -submit
    ```

    Она загружает сведения о задании из **job.xml** и отправляет их Oozie, но не запускает задание.

    После завершения команды она должна вернуть идентификатор задания. Например, `0000005-150622124850154-oozie-oozi-W`. Он используется для управления заданием.

4. Для просмотра состояния задания воспользуйтесь следующей командой. Введите идентификатор задания, возвращенный предыдущей командой:

    ```
    oozie job -info <JOBID>
    ```

    Эта команда возвращает следующую информацию:

    ```
    Job ID : 0000005-150622124850154-oozie-oozi-W
    ------------------------------------------------------------------------------------------------------------------------------------
    Workflow Name : useooziewf
    App Path      : wasbs:///tutorials/useoozie
    Status        : PREP
    Run           : 0
    User          : USERNAME
    Group         : -
    Created       : 2015-06-22 15:06 GMT
    Started       : -
    Last Modified : 2015-06-22 15:06 GMT
    Ended         : -
    CoordAction ID: -
    ------------------------------------------------------------------------------------------------------------------------------------
    ```

    Это задание находится в состоянии `PREP`, что означает, что оно было отправлено, но не было запущено.

5. Используйте следующую команду для запуска задания:

    ```
    oozie job -start JOBID
    ```

    Если вы проверите состояние после этой команды, то увидите состояние выполнения и информацию о действиях для этого задания.

6. После успешного завершения задания с помощью следующих команд можно проверить, что данные были созданы и экспортированы в таблицу базы данных SQL:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    В строке приглашения `1>` введите следующее:

    ```
    SELECT * FROM mobiledata
    GO
    ```

    Эта команда возвращает следующую информацию:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Дополнительные сведения о команде Oozie приведены в разделе [Утилита командной строки Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

Oozie REST API позволяет создавать собственные утилиты для работы с Oozie. Ниже приведена информация об использовании Oozie REST API для HDInsight.

* **URI**: К REST API можно обращаться из-за пределов кластера по адресу: `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Authentication**: Для использования API необходимо пройти аутентификацию с учетной записью HTTP кластера (администратор), указав пароль. Например:

    ```
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Дополнительные сведения об использовании Oozie REST API приведены в разделе [API веб-служб Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Пользовательский веб-интерфейс Oozie

Веб-интерфейс Oozie позволяет получить информацию о состоянии задания Oozie в кластере. Он позволяет просматривать состояние задания, определение задания, конфигурацию задания, диаграммы действий задания и журналы задания. Также можно просмотреть подробную информацию о действиях в рамках задания.

Для доступа к веб-интерфейсу Oozie выполните следующие действия:

1. Создайте туннель SSH для кластера HDInsight. Дополнительные сведения о том, как это сделать, можно узнать в статье [Использование туннелирования SSH для доступа к веб-интерфейсу Ambari, ResourceManager, JobHistory, NameNode, Oozie и другим веб-интерфейсам](hdinsight-linux-ambari-ssh-tunnel.md).

2. После создания туннеля откройте веб-интерфейс Ambari в браузере. Универсальный код ресурса (URI) сайта Ambari — **https://имя_кластера.azurehdinsight.net**. Замените **имя_кластера** именем своего кластера HDInsight под управлением Linux.

3. В левой части страницы выберите **Oozie**, затем **Быстрые ссылки** и, наконец, **Oozie Web UI** (Пользовательский веб-интерфейс Oozie).

    ![изображение меню](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. По умолчанию в веб-интерфейсе Oozie отображаются запущенные задания рабочих процессов. Чтобы просмотреть все задания рабочего процесса, выберите **All Jobs**.

    ![Отображаются все задания](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Чтобы просмотреть дополнительные сведения о задании, выберите это задание.

    ![Job Info](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. На вкладке Job Info можно просмотреть базовую информацию о задании, а также отдельные действия в рамках задания. С помощью вкладок вверху можно просмотреть определение задания, конфигурацию задания, обратиться к журналу задания или просмотреть направленный ациклический граф (DAG) задания.

   * **Журнал задания**: нажмите кнопку **GetLogs** для просмотра всех журналов задания или воспользуйтесь полем **Enter Search Filter** (Введите фильтр поиска) для выбора журналов с помощью фильтра.

       ![Журнал задания](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **JobDAG**: DAG представляет собой графическое представление путей данных рабочего процесса

       ![Направленный ациклический граф задания](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Выбрав одно из действий на вкладке **Job Info** (Сведения о задании), вы увидите информацию об этом действии. Например, выберите действие **RunHiveScript** .

    ![Информация о действии](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Можно просмотреть подробную информацию о действии, включая ссылку на **Console URL**, который можно использовать для просмотра сведений о задании в JobTracker.

## <a name="scheduling-jobs"></a>Планирование заданий

Координатор позволяет указать время начала, окончания и частоту выполнения заданий, чтобы запланировать их на определенное время.

Чтобы задать расписание для рабочего процесса выполните следующие действия:

1. Используйте следующую команду для создания нового файла с именем **coordinator.xml**:

    ```
    nano coordinator.xml
    ```

    Добавьте в этот файл следующее содержимое:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    Обратите внимание на переменные `${...}`. При запуске задания они будут заменены значениями, указанными в определении задания. Используются следующие переменные:

   * **${coordFrequency}**: интервал времени между запуском повторных экземпляров задания.

   * **${coordStart}**: время запуска задания.

   * **${coordEnd}**: время завершения задания.

   * **${coordTimezone}**: задания координатора задаются для конкретного часового пояса без летнего времени (обычно он представляется с помощью UTC). Этот часовой пояс называется "часовым поясом обработки Oozie".

   * **${wfPath}**: путь к файлу workflow.xml.

2. Нажмите клавиши Ctrl + X, затем **Y** и **ВВОД**, чтобы сохранить файл.

3. Чтобы скопировать этот файл в рабочий каталог задания, воспользуйтесь следующей командой:

    ```
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Для изменения файла **job.xml** воспользуйтесь следующей командой:

    ```
    nano job.xml
    ```

    Выполните следующие изменения:

   * Измените `<name>oozie.wf.application.path</name>` на `<name>oozie.coord.application.path</name>`. Эта команда означает, что Oozie следует запустить файл координатора вместо файла рабочего процесса.

   * Добавьте следующую команду, которая устанавливает переменную, используемую в файле coordinator.xml, чтобы указать расположение workflow.xml:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Замените текст `wasbs://mycontainer@mystorageaccount.blob.core.windows` значением, которое используется в других записях файла job.xml.

   * Добавьте следующую команду для определения времени запуска, завершения и интервала запуска для использования в файле coordinator.xml:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-02-07T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-02-09T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Здесь для этого задания устанавливается время начала — 12:00 7 февраля 2017 г., время окончания — 9 февраля 2017 г. и интервал "ежедневно". Интервал задается в минутах, то есть 24 часа x 60 минут = 1440 минут. Наконец, часовой пояс устанавливается в UTC.

5. Нажмите клавиши Ctrl + X, затем **Y** и **ВВОД**, чтобы сохранить файл.

6. Для запуска задания используйте следующую команду:

    ```
    oozie job -config job.xml -run
    ```

    Эта команда отправляет и запускает задание.

7. Если вы зайдете в веб-интерфейс Oozie и выберете вкладку **Coordinator Jobs** (Задания координатора), то увидите следующую информацию:

    ![Вкладка задания координатора](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    Обратите внимание на параметр **Next Materialization** (Следующая материализация), который определяет момент следующего запуска задания.

8. Как и для предыдущих заданий рабочего процесса, вы можете выбрать это задание в веб-интерфейсе и увидеть информацию о нем:

    ![Информация о задании координатора](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    Обратите внимание, что здесь отображается только информация об успешных запусках задания, а не информация об отдельных действиях запланированного рабочего процесса. Для ее просмотра выберите одну из записей **Action** . Отображается такая же информация, как и для предыдущих заданий рабочего процесса.

    ![Информация о действии](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Устранение неполадок

При решении проблем с заданиями Oozie пользовательский интерфейс Oozie очень удобен, так как в нем легко просматривать как журналы Oozie, так и ссылки на журналы JobTracker для заданий MapReduce, таких как запросы Hive. В общем случае действия по решению проблемы должны подчиняться следующему шаблону:

1. Просмотрите информацию о задании в веб-интерфейсе Oozie.

2. Если произошел сбой или ошибка для конкретного действия, выберите действие и посмотрите, не содержится ли в поле **Error Message** дополнительной информации об ошибке.

3. Если известен URL действия, воспользуйтесь им для просмотра дополнительной информации о действии (например, журналов JobTracker).

Ниже приведены конкретные ошибки, которые могут возникнуть, и способы их устранения.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Не удается инициализировать кластер

**Симптомы**. Состояние задания изменяется на **SUSPENDED** (Приостановлено). В подробной информации о задании состояние RunHiveScript отображается как **START_MANUAL** (Ручной запуск). При выборе действия отображается следующее сообщение об ошибке:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Причина**. WASB-адреса, используемые в файле **job.xml**, не содержат контейнер хранилища или имя учетной записи хранения. Формат адреса WASB должен быть следующим `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Решение**: Измените адреса WASB, используемые заданием.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie не разрешено работать от имени &lt;ПОЛЬЗОВАТЕЛЬ>

**Симптомы**. Состояние задания изменяется на **SUSPENDED** (Приостановлено). В подробной информации о задании состояние RunHiveScript отображается как **START_MANUAL** (Ручной запуск). При выборе действия отображается следующее сообщение об ошибке:

    JA002: User: oozie is not allowed to impersonate <USER>

**Причина**: Текущие права доступа не позволяют Oozie работать от имени учетной записи указанного пользователя.

**Решение**. Oozie не разрешено работать от имени пользователей из группы **users**. Для просмотра групп, в которые входит данный пользователь, воспользуйтесь командой `groups USERNAME` . Если пользователь не является членом группы **users** , добавьте его в  группу следующей командой:

    sudo adduser USERNAME users

> [!NOTE]
> Чтобы HDInsight понял, что пользователь добавлен в группу, может потребоваться несколько минут.

### <a name="launcher-error-sqoop"></a>ОШИБКА запуска (Sqoop)

**Симптомы**. Состояние задания изменяется на **KILLED** (Прекращено). В подробной информации о задании состояние RunSqoopExport отображается как **ERROR** (Ошибка). При выборе действия отображается следующее сообщение об ошибке:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Причина**: Sqoop не удалось загрузить драйвер базы данных, необходимый для доступа к базе данных.

**Решение**: При использовании Sqoop из задания Oozie необходимо включить драйвер базы данных в ресурсы, используемые заданием (в файле workflow.xml).

Также необходимо указать архив, содержащий драйвер базы данных, из раздела `<sqoop>...</sqoop>` файла workflow.xml.

Например, для задания в этом документе необходимо выполнить следующие действия:

1. Скопируйте файл sqljdbc4.1.jar в каталог /tutorials/useoozie:

    ```
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Измените файл workflow.xml, добавив следующие строки над `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как задать рабочий процесс Oozie и как запустить задание Oozie. Дополнительные сведения о работе с HDInsight приведены в следующих статьях:

* [Используйте учитывающий время координатор Oozie с Hadoop в HDInsight для определения рабочих процессов и координации заданий][hdinsight-oozie-coordinator-time]
* [Отправка данных для заданий Hadoop в HDInsight][hdinsight-upload-data]
* [Использование Sqoop с Hadoop в HDInsight][hdinsight-use-sqoop]
* [Использование Hive с Hadoop в HDInsight][hdinsight-use-hive]
* [Использование Pig с Hadoop в HDInsight][hdinsight-use-pig]
* [Разработка программ MapReduce на Java для Hadoop в HDInsight на платформе Linux][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

