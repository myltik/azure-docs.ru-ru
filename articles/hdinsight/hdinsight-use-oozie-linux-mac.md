---
title: Использование рабочих процессов Hadoop Oozie в Azure HDInsight на основе Linux | Документация Майкрософт
description: Использование Hadoop Oozie в HDInsight на основе Linux. Узнайте, как определить рабочий процесс и отправить задание для Oozie.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 8a25507ab076c4eecccea4e8a503d68ff1441ae5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Использование Oozie с Hadoop для определения и запуска рабочих процессов в Azure HDInsight под управлением Linux

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Узнайте, как использовать Apache Oozie с Hadoop в Azure HDInsight. Oozie — это система рабочих процессов и координации, управляющая заданиями Hadoop. Служба Oozie интегрирована со стеком Hadoop и поддерживает следующие задания:

* Apache MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Вы также можете использовать Oozie для планирования системных заданий, например Java-программ и сценариев оболочки.

> [!NOTE]
> Еще один способ определения рабочих процессов с помощью HDInsight — использование фабрики данных Azure. Дополнительные сведения о фабрике данных см. в статье [Преобразование данных в фабрике данных Azure][azure-data-factory-pig-hive].

> [!IMPORTANT]
> Диспетчер Oozie не включен в присоединенном к домену кластере HDInsight.

## <a name="prerequisites"></a>предварительным требованиям

* **Кластер HDInsight**: ознакомьтесь с [началом работы с HDInsight в Linux](/hadoop/apache-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> Для выполнения действий, описанных в этом документе, необходим кластер HDInsight под управлением Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="example-workflow"></a>Пример рабочего процесса

Рабочий процесс, используемый в этой статье, включает два действия. Действия являются определениями задач, таких как запуск Hive, Sqoop, MapReduce или других процессов:

![Схема рабочих процессов][img-workflow-diagram]

1. Действие Hive запускает сценарий HiveQL для извлечения записей из таблицы **hivesampletable**, входящей в состав в HDInsight. Каждая строка данных описывает посещение с определенного мобильного устройства. Формат записи таков:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Сценарий Hive, используемый в этом документе, подсчитывает общее количество посещений для каждой платформы (например, Android или iPhone) и сохраняет результаты в новой таблице Hive.

    Дополнительные сведения о Hive см. в статье [Использование Hive с HDInsight][hdinsight-use-hive].

2. Действие Sqoop экспортирует содержимое новой таблицы Hive в таблицу, созданную в базе данных SQL Azure. Дополнительные сведения о Sqoop см. в статье [Использование Sqoop с Hadoop в HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Сведения о поддерживаемых версиях Oozie в кластерах HDInsight см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?][hdinsight-versions]

## <a name="create-the-working-directory"></a>Создайте рабочий каталог

В Oozie предполагается, что все ресурсы, необходимые для выполнения задания, должны находиться в том же каталоге. В этом примере используется каталог **wasb:///tutorials/useoozie**. Чтобы создать каталог, выполните следующие шаги:

1. Подключитесь к кластеру HDInsight с помощью протокола SSH:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Замените `sshuser` именем пользователя SSH для кластера. Замените `clustername` именем кластера. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Чтобы создать каталог, выполните следующую команду:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]
    > Параметр `-p` означает, что будут созданы все каталоги для пути. В каталоге **data** хранятся данные, используемые сценарием **useooziewf.hql**.

3. Выполните следующую команду, чтобы убедиться, что Oozie может олицетворять учетную запись пользователя:

    ```bash
    sudo adduser username users
    ```

    Замените `username` своим именем пользователя SSH.

    > [!NOTE]
    > Ошибки о том, что пользователь уже является членом группы `users`, можно игнорировать.

## <a name="add-a-database-driver"></a>Добавление драйвера базы данных

Так как этот рабочий процесс использует Sqoop для экспорта данных в базу данных SQL, необходимо предоставить копию драйвера JDBC, используемого для обращения к базе данных SQL. Чтобы скопировать драйвер JDBC в рабочую папку, используйте следующую команду из сеанса SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

> [!NOTE]
> Может отобразиться сообщение о том, что файл уже существует.

Если рабочий процесс использовал другие ресурсы, например JAR-файл, содержащий приложение MapReduce, необходимо добавить и эти ресурсы.

## <a name="define-the-hive-query"></a>Определение запроса Hive

Для создания сценария языка запросов Hive (HiveQL), определяющего запрос, сделайте следующее. Далее в этом документе вы будете использовать запрос в рабочем процессе Oozie.

1. В сеансе SSH создайте файл с именем `useooziewf.hql`, выполнив следующую команду:

    ```bash
    nano useooziewf.hql
    ```

3. Открыв редактор GNU nano, используйте следующий запрос в качестве содержимого файла:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    В данном сценарии используются три следующие переменные:

    * `${hiveTableName}`: содержит имя создаваемой таблицы.

    * `${hiveDataFolder}`: содержит расположения файлов данных для таблицы.

    Файл с определением рабочего процесса (workflow.xml в этом руководстве) передает эти значения в скрипт HiveQL во время выполнения.

4. Нажмите CTRL+X, чтобы закрыть редактор. При появлении запроса выберите `Y` для сохранения файла, введите `useooziewf.hql` как имя файла, а затем нажмите клавишу **ВВОД**.

5. Выполните указанные ниже команды, чтобы скопировать `useooziewf.hql` в `wasb:///tutorials/useoozie/useooziewf.hql`.

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Эта команда сохраняет файл `useooziewf.hql` в HDFS-совместимом хранилище кластера.

## <a name="define-the-workflow"></a>Определение рабочего процесса

Определения рабочего процесса Oozie записываются на языке определения процессов Hadoop (hPDL), который является языком определения процессов XML. Для определения рабочего процесса выполните следующие действия.

1. Для создания и открытия файла выполните следующий запрос:

    ```bash
    nano workflow.xml
    ```

2. Открыв редактор nano, введите следующий код XML в качестве содержимого файла:

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

   * `RunHiveScript`. Это действие запускает сценарий Hive `useooziewf.hql`.

   * `RunSqoopExport`. Это действие экспортирует созданные данные из сценария Hive в базу данных SQL, используя Sqoop. Это действие будет выполнено только после успешного завершения действия `RunHiveScript`.

     В рабочем процессе есть несколько записей, таких как `${jobTracker}`. Вы замените их значениями, используемыми в определении задания. Позже в этом документе вы создадите определение задания.

     Также обратите внимание на параметр `<archive>sqljdbc4.jar</archive>` в разделе Sqoop. Эта запись означает, что этот архив должен стать доступным для Sqoop при выполнении этого действия.

3. Чтобы сохранить файл, нажмите Ctrl+X, введите `Y`, а затем нажмите клавишу **ВВОД**. 

4. Выполните следующую команду, чтобы скопировать файл `workflow.xml` в `/tutorials/useoozie/workflow.xml`.

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Создание базы данных

Чтобы создать базу данных SQL, следуйте инструкциям в документе [Создание базы данных SQL Azure на портале Azure](../sql-database/sql-database-get-started.md). При создании задайте для базы данных имя `oozietest`. Запишите также имя сервера базы данных.

### <a name="create-the-table"></a>Создание таблицы

> [!NOTE]
> Существует множество способов подключения к базе данных SQL для создания таблицы. В приведенных ниже действиях используется [FreeTDS](http://www.freetds.org/) из кластера HDInsight.


1. Для установки FreeTDS в кластер HDInsight воспользуйтесь следующей командой:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. После установки FreeTDS используйте следующую команду для подключения к созданному серверу базы данных SQL:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    Выводятся сведения следующего вида:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. В командной строке `1>` введите следующее:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Если вводится инструкция `GO`, то оцениваются предыдущие инструкции. С помощью этих инструкций создается таблица **mobiledata**, используемая рабочим процессом.

    Используйте следующие команды для проверки создания таблицы:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Выводятся сведения следующего вида:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

4. Чтобы выйти из служебной программы tsql, введите `exit` в командной строке `1>`.

## <a name="create-the-job-definition"></a>Создание определения задания

Определение задания содержит информацию о местонахождении файла workflow.xml, а также о том, где можно найти другие файлы, используемые рабочим процессом, например `useooziewf.hql`. Кроме того, в нем определяются значения свойств, используемых в рабочем процессе, и сопутствующих файлов.

1. Для получения полного адреса хранилища по умолчанию, воспользуйтесь указанной ниже командой. Этот адрес используется в файле конфигурации, который вы создадите на следующем шаге.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Эта команда возвращает информацию, как в следующем коде XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Если кластер HDInsight использует службу хранилища Azure в качестве хранилища по умолчанию, содержимое элемента `<value>` начинается с `wasb://`. Если же используется Azure Data Lake Store, оно начинается с `adl://`.

    Сохраните содержимое элемента `<value>`, которое потребуется нам на следующих шагах.

2. Используйте следующую команду для создания конфигурации определения задания Oozie:

    ```bash
    nano job.xml
    ```

3. Открыв редактор nano, используйте следующий код XML в качестве содержимого файла:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
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
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
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
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * Замените все экземпляры `wasb://mycontainer@mystorageaccount.blob.core.windows.net` значением, полученным ранее для хранилища по умолчанию.

     > [!WARNING]
     > Если используется путь `wasb`, его необходимо указать полностью. Не сокращайте его до строки `wasb:///`.

   * Замените `YourName` своим именем пользователя для кластера HDInsight.
   * Замените `serverName`, `adminLogin` и `adminPassword` данными для базы данных SQL.

     Большая часть информации в этом файле используется для заполнения значений, используемых в файлах workflow.xml или ooziewf.hql, таких как `${nameNode}`.

     > [!NOTE]
     > Запись `oozie.wf.application.path` определяет, где можно найти файл workflow.xml. Этот файл содержит рабочий процесс, запущенный этим заданием.

5. Чтобы сохранить файл, нажмите Ctrl+X, введите `Y`, а затем нажмите клавишу **ВВОД**.

## <a name="submit-and-manage-the-job"></a>Отправка задания и управление им

Далее используется команда Oozie для отправки рабочих процессов Oozie в кластер и управления ими. Команда Oozie предоставляет удобный интерфейс для [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> При использовании команды Oozie необходимо использовать полное доменное имя для головного узла HDInsight. Это полное доменное имя доступно только из кластера или, если кластер находится в виртуальной сети Azure, с других компьютеров той же сети.


1. Для получения URL-адреса службы Oozie, воспользуйтесь следующей командой:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Эта команда возвращает информацию, как в следующем коде XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    Здесь фрагмент `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` обозначает URL-адрес, который используется в команде Oozie.

2. Используйте следующую команду для создания переменной среды для URL-адреса, чтобы не вводить его в каждой команде:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Замените URL-адрес полученным ранее.
3. Чтобы отправить задание, воспользуйтесь следующей командой:

    ```bash
    oozie job -config job.xml -submit
    ```

    Она загружает сведения о задании из `job.xml` и отправляет их Oozie, но не запускает задание.

    После завершения команда должна вернуть идентификатор задания, например `0000005-150622124850154-oozie-oozi-W`. Этот идентификатор используется для управления заданием.

4. Чтобы просмотреть состояние задания, используйте следующую команду:

    ```bash
    oozie job -info <JOBID>
    ```

    > [!NOTE]
    > Замените `<JOBID>` идентификатором, возвращенным на предыдущем шаге.

    Эта команда возвращает следующую информацию:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
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

    Это задание находится в состоянии `PREP`, Это состояние указывает на то, что задание было создано, но не запущено.

5. Для запуска задания выполните следующую команду:

    ```bash
    oozie job -start JOBID
    ```

    > [!NOTE]
    > Замените `<JOBID>` идентификатором, возвращенным ранее.

    Если вы проверите состояние после этой команды, то увидите состояние выполнения и информацию о действиях для этого задания.

6. После успешного завершения задания с помощью следующих команд можно удостовериться, что данные были созданы и экспортированы в таблицу базы данных SQL:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    В командной строке `1>` введите следующий запрос.

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    При этом возвращается следующая информация:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Дополнительные сведения о команде Oozie см. на [странице, посвященной программе командной строки Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

Oozie REST API позволяет создавать собственные утилиты для работы с Oozie. Ниже приведена информация об использовании Oozie REST API для HDInsight:

* **URI.** Вы можете получить доступ к REST API из-за пределов кластера по адресу `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Аутентификация.** Для аутентификации используйте API, учетную запись кластера HTTP (администратор) и пароль. Например: 

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Дополнительные сведения об использовании Oozie REST API см. в статье о [API веб-служб Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>веб-интерфейс Oozie

Веб-интерфейс Oozie позволяет получить информацию о состоянии задания Oozie в кластере. С помощью веб-интерфейса вы можете просмотреть следующую информацию:

   * Состояние задания
   * определение задания;
   * Параметр Configuration
   * диаграмму действий задания;
   * журналы задания.

Кроме того, можно просмотреть подробную информацию о действиях в рамках задания.

Для доступа к веб-интерфейсу Oozie сделайте следующее:

1. Создайте туннель SSH для кластера HDInsight. Дополнительные сведения см. в статье [Использование туннелирования SSH с для доступа к веб-интерфейсу Ambari, JobHistory, NameNode, Oozie и другим веб-интерфейсам](hdinsight-linux-ambari-ssh-tunnel.md).

2. После создания туннеля откройте веб-интерфейс Ambari в браузере. Универсальный код ресурса (URI) для сайта Ambari — `https://CLUSTERNAME.azurehdinsight.net`. Замените `CLUSTERNAME` именем своего кластера HDInsight под управлением Linux.

3. В левой части страницы выберите **Oozie** > **Быстрые ссылки** > **Oozie Web UI** (Веб-интерфейс Oozie).

    ![Изображение меню](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. По умолчанию в веб-интерфейсе Oozie отображаются запущенные задания рабочих процессов. Чтобы просмотреть все задания рабочего процесса, выберите **All Jobs** (Все задания).

    ![Отображаются все задания](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Чтобы просмотреть дополнительные сведения о задании, выберите это задание.

    ![Job Info](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. На вкладке **Job Info** (Сведения о задании) можно просмотреть базовую информацию о задании, а также отдельные действия в рамках задания. С помощью вкладок вверху можно просмотреть **определение задания**, **конфигурацию задания**, обратиться к **журналу задания** или просмотреть направленный ациклический граф (DAG) задания в разделе **Job DAG** (Направленный ациклический граф задания).

   * **Job Log** (Журнал задания). Нажмите кнопку **Get Logs** (Получить журналы) для просмотра всех журналов задания или воспользуйтесь полем **Enter Search Filter** (Введите фильтр поиска) для выбора журналов с помощью фильтра.

       ![Журнал задания](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **Job DAG** (Направленный ациклический граф задания). DAG представляет собой графическое отображение путей данных рабочего процесса.

       ![Направленный ациклический граф задания](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Выбрав одно из действий на вкладке **Job Info** (Сведения о задании), вы увидите информацию об этом действии. Например, выберите действие **RunSqoopExport**.

    ![Информация о действии](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Вы можете просмотреть подробную информацию о действии, например ссылку на **URL-адрес консоли**. Используйте эту ссылку для просмотра сведений о задании в средстве отслеживания заданий.

## <a name="schedule-jobs"></a>Запланированные задания

Вы можете использовать координатор, чтобы указать время начала, окончания и частоту выполнения заданий. Чтобы задать расписание для рабочего процесса, выполните следующие действия:

1. Выполните следующую команду для создания файла с именем **coordinator.xml**:

    ```bash
    nano coordinator.xml
    ```

    Используйте следующий код XML в качестве содержимого файла:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]
    > При запуске задания переменные `${...}` будут заменены значениями, указанными в определении задания. Используются следующие переменные:
    >
    > * `${coordFrequency}`: интервал времени между запуском повторных экземпляров задания.
    > * `${coordStart}`: время запуска задания.
    > * `${coordEnd}`: время завершения задания.
    > * `${coordTimezone}`: задания координатора задаются для конкретного часового пояса без летнего времени (обычно представляется в виде времени в формате UTC). Этот часовой пояс называется *часовым поясом обработки Oozie.*
    > * `${wfPath}`: путь к файлу workflow.xml.

2. Чтобы сохранить файл, нажмите Ctrl+X, введите `Y`, а затем нажмите клавишу **ВВОД**.

3. Чтобы скопировать этот файл в рабочий каталог задания, воспользуйтесь следующей командой:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Чтобы создать файл `job.xml`, используйте следующую команду:

    ```
    nano job.xml
    ```

    Выполните следующие изменения:

   * Чтобы служба Oozie запускала файл координатора вместо файла рабочего процесса, измените `<name>oozie.wf.application.path</name>` на `<name>oozie.coord.application.path</name>`.

   * Чтобы задать переменную `workflowPath`, используемую координатором, добавьте следующий код XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Замените текст `wasb://mycontainer@mystorageaccount.blob.core.windows` значением, которое используется в других записях файла job.xml.

   * Чтобы определить начало, окончание и частоту выполнения для координатора, добавьте следующий код XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-05-12T12:00Z</value>
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

       Приведенные выше значения задают время начала — 12:00 часов 10 мая 2017 года и время окончания — 12 мая 2017 года. Интервал запуска этого задания — "ежедневно". Интервал задается в минутах, то есть 24 часа x 60 минут = 1440 минут. И наконец, часовой пояс устанавливается в формате UTC.

5. Чтобы сохранить файл, нажмите Ctrl+X, введите `Y`, а затем нажмите клавишу **ВВОД**.

6. Для запуска задания используйте следующую команду:

    ```
    oozie job -config job.xml -run
    ```

    Эта команда отправляет и запускает задание.

7. Если вы зайдете в веб-интерфейс Oozie и выберете вкладку **Coordinator Jobs** (Задания координатора), то увидите похожую информацию:

    ![Вкладка Coordinator Jobs (Задания координатора)](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    Запись **Next Materialization** (Следующая материализация) определяет момент следующего запуска задания.

8. Как и для предыдущих заданий рабочего процесса, если вы выберете это задание в веб-интерфейсе, то увидите информацию о нем:

    ![Информация о задании координатора](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]
    > На этом изображении показаны только сведения об успешных запусках задания, а не сведения об отдельных действиях запланированного рабочего процесса. Для просмотра отдельных действий выберите одну из записей **Action** (Действие).

    ![Информация о действии](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Устранение неполадок

С помощью пользовательского интерфейса Oozie можно просмотреть журналы Oozie. Пользовательский интерфейс Oozie также содержит ссылки на журналы JobTracker для заданий MapReduce, запущенных рабочим процессом. Действия по решению проблемы должны подчиняться следующему шаблону:

   1. Просмотрите информацию о задании в веб-интерфейсе Oozie.

   2. Если произошел сбой или ошибка для конкретного действия, выберите действие и посмотрите, не содержится ли в поле **Error Message** дополнительной информации об ошибке.

   3. Если известен URL-адрес действия, воспользуйтесь им для просмотра дополнительной информации о действии (например, журналов JobTracker).

Ниже приведены конкретные ошибки, которые могут возникнуть, и способы их устранения.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Не удается инициализировать кластер

**Симптомы**. Состояние задания изменяется на **SUSPENDED** (Приостановлено). В подробной информации о задании `RunHiveScript` состояние отображается как **START_MANUAL**. При выборе действия отображается следующее сообщение об ошибке:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Причина.** Адреса хранилища BLOB-объектов Azure, используемые в файле **job.xml**, не содержат контейнер хранилища или имя учетной записи хранения. Адрес хранилища BLOB-объектов должен иметь формат `wasb://containername@storageaccountname.blob.core.windows.net`.

**Решение.** Измените адреса хранилища BLOB-объектов, используемые в задании.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie не разрешено работать от имени &lt;ПОЛЬЗОВАТЕЛЬ>

**Симптомы**. Состояние задания изменяется на **SUSPENDED** (Приостановлено). В подробной информации о задании `RunHiveScript` состояние отображается как **START_MANUAL**. Если выбрать действие, отобразится следующее сообщение об ошибке:

    JA002: User: oozie is not allowed to impersonate <USER>

**Причина.** Текущие права доступа не позволяют Oozie работать от имени учетной записи указанного пользователя.

**Решение.** Oozie может работать от имени пользователей из группы **users**. Для просмотра групп, в которые входит данный пользователь, воспользуйтесь командой `groups USERNAME` . Если пользователь не является членом группы **users**, добавьте его в группу следующей командой:

    sudo adduser USERNAME users

> [!NOTE]
> Чтобы служба HDInsight поняла, что пользователь добавлен в группу, может потребоваться несколько минут.

### <a name="launcher-error-sqoop"></a>ОШИБКА запуска (Sqoop)

**Симптомы**. Состояние задания изменяется на **KILLED** (Прекращено). В подробной информации о задании `RunSqoopExport` состояние отображается как **ERROR**. Если выбрать действие, отобразится следующее сообщение об ошибке:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Причина**: Sqoop не удалось загрузить драйвер базы данных, необходимый для доступа к базе данных.

**Решение.** При использовании Sqoop из задания Oozie необходимо включить драйвер базы данных в ресурсы, используемые заданием, такие как workflow.xml. Кроме того, укажите архив, содержащий драйвер базы данных, из раздела `<sqoop>...</sqoop>` файла workflow.xml.

Например, для задания в этом документе необходимо выполнить следующие действия:

1. Скопируйте файл `sqljdbc4.1.jar` в каталог **/tutorials/useoozie**:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Измените файл `workflow.xml`, чтобы добавить следующий код XML в новую строку выше `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как задать рабочий процесс Oozie и как запустить задание Oozie. Дополнительные сведения о работе с HDInsight приведены в следующих статьях:

* [Используйте учитывающий время координатор Oozie с Hadoop в HDInsight для определения рабочих процессов и координации заданий][hdinsight-oozie-coordinator-time]
* [Отправка данных для заданий Hadoop в HDInsight][hdinsight-upload-data]
* [Использование Sqoop с Hadoop в HDInsight][hdinsight-use-sqoop]
* [Использование Hive с Hadoop в HDInsight][hdinsight-use-hive]
* [Использование Pig с Hadoop в HDInsight][hdinsight-use-pig]
* [Разработка программ MapReduce на Java для Hadoop в HDInsight на платформе Linux][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
