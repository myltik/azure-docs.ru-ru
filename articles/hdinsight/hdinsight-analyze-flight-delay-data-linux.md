---
title: "Анализ данных о задержке рейсов с помощью Hive в HDInsight на основе Linux | Документация Майкрософт"
description: "Узнайте, как анализировать данные о задержке рейсов с помощью Hive в HDInsight под управлением Linux, а затем экспортировать их в Базу данных SQL с помощью Sqoop."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0c23a079-981a-4079-b3f7-ad147b4609e5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 9d20050dada974c0c2a54399e2db7b9a289f7e89
ms.openlocfilehash: 8ca2e34fef825bbc50dd367642bc82d8ba00b6b0


---
# <a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Анализ данных о задержке рейсов с помощью Hive в HDInsight
Узнайте, как анализировать данные о задержке рейсов с помощью Hive в HDInsight под управлением Linux, а затем экспортировать их в Базу данных SQL Azure с помощью Sqoop.

> [!IMPORTANT]
> Для выполнения действий, описанных в этом документе, необходим кластер HDInsight, который использует Linux. Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

### <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

* **Кластер HDInsight**. Действия для создания нового кластера HDInsight см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

* **База данных SQL Azure**. Вы используете базу данных SQL Azure в качестве конечного хранилища данных. Если у вас еще нет базы данных SQL, см. статью [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](../sql-database/sql-database-get-started.md).

* **Интерфейс командной строки Azure**. Если вы еще не установили интерфейс командной строки Azure, см. статью [Установка Azure CLI](../xplat-cli-install.md).

## <a name="download-the-flight-data"></a>Скачивание данных о рейсах

1. Перейдите на страницу [бюро транспортной статистики при администрации по исследованиям и инновационным технологиям][rita-website].

2. На странице выберите следующие значения:
   
   | Имя | Значение |
   | --- | --- |
   | Фильтр года |2013 |
   | Период фильтра |Январь |
   | Поля |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Очистите все остальные поля. |

3. Щелкните элемент **Загрузить**. 

## <a name="upload-the-data"></a>Передача данных

1. Воспользуйтесь следующей командой, чтобы передать ZIP-файл в головной узел кластера HDInsight:
   
    ```
    scp FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
    ```
   
    Замените **FILENAME** именем ZIP-файла. Замените **USERNAME** именем для входа SSH для кластера HDInsight. Замените CLUSTERNAME именем кластера HDInsight.
   
   > [!NOTE]
   > Если для аутентификации входа посредством SSH используется пароль, будет предложено ввести пароль. Если используется открытый ключ, может потребоваться использовать параметр `-i` и указать путь к соответствующему закрытому ключу. Пример: `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. После завершения передачи подключитесь к кластеру с помощью SSH:
   
    ```ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net```
   
    Дополнительная информация об использовании SSH с HDInsight на основе Linux приведена в следующих статьях:
   
   * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

3. После установления подключения используйте следующую команду, чтобы распаковать ZIP-файл:
   
    ```
    unzip FILENAME.zip
    ```
   
    Она извлечет в CSV-файл, размер которого приблизительно 60 МБ.

4. Используйте следующую команду для создания нового каталога в WASB (распределенном хранилище данных, используемом HDInsight) и копирования файла:
   
    ```
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="create-and-run-the-hiveql"></a>Создание и запуск HiveQL
Выполните следующие действия для импорта данных из CSV-файла в таблицу Hive с именем **Delays**.

1. Для создания и редактирования нового файла **flightdelays.hql**выполните следующее:
   
    ```
    nano flightdelays.hql
    ```
   
    Используйте следующее в качестве содержимого этого файла:
   
    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. Нажмите клавиши **Ctrl + X**, а затем **Y** (Да) для сохранения файла.

3. Используйте следующую команду для запуска Hive и выполнения файла **flightdelays.hql** :
   
    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -f flightdelays.hql
    ```
   
   > [!NOTE]
   > В этом примере используется `localhost`, так как вы подключены к головному узлу кластера HDInsight, на котором выполняется HiveServer2.

4. По завершении выполнения сценария __flightdelays.hql__ используйте следующую команду, чтобы открыть интерактивный сеанс Beeline:
   
    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

5. При появлении командной строки `jdbc:hive2://localhost:10001/>` используйте следующую команду, чтобы извлечь информацию из импортированных данных о задержке рейсов.
   
    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```
   
    Вы получите список городов, рейсы в которые задержаны из-за погодных условий, а также среднее время задержки. Он будет сохранен в `/tutorials/flightdelays/output`. Позже Sqoop считает данные из этого расположения и экспортирует их в базу данных SQL Azure.

6. Чтобы выйти из Beeline, введите `!quit` в командной строке.

## <a name="create-a-sql-database"></a>Создание базы данных SQL

Если у вас уже имеется база данных SQL, необходимо получить имя сервера. Его можно найти на [портале Azure](https://portal.azure.com) , выбрав **Базы данных SQL**и применив фильтр по имени базы данных, которую следует использовать. Имя сервера указано в столбце **СЕРВЕР** .

Если у вас еще нет базы данных SQL, создайте ее, ознакомившись с разделом [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](../sql-database/sql-database-get-started.md) . Необходимо сохранить имя сервера, используемого для базы данных.

## <a name="create-a-sql-database-table"></a>Создание таблицы базы данных SQL

> [!NOTE]
> Существует множество способов подключения к базе данных SQL для создания таблицы. В приведенных ниже действиях используется [FreeTDS](http://www.freetds.org/) из кластера HDInsight.


1. Используйте SSH для подключения к кластеру HDInsight под управлением Linux и выполните следующие действия в сеансе SSH.

2. Используйте следующую команду для установки FreeTDS:
   
    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. После установки используйте следующую команду для подключения к серверу базы данных SQL. Замените **serverName** именем сервера базы данных SQL. Замените **adminLogin** и **adminPassword** именем для входа и паролем для базы данных SQL. Замените **databaseName** именем базы данных.
   
    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```
   
    Должен появиться результат, аналогичный приведенному ниже.
   
    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. В командной строке `1>` введите следующее:
   
    ```
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```
   
    Если вводится инструкция `GO`, то оцениваются предыдущие инструкции. Будет создана таблица **delays** с кластеризованным индексом (требуется для базы данных SQL).
   
    Используйте следующую команду для проверки создания таблицы:
   
    ```
    SELECT * FROM information_schema.tables
    GO
    ```
   
    Выход аналогичен приведенному ниже:
   
    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo     delays      BASE TABLE
    ```

5. В командной строке `exit` at the `1>` , чтобы выйти из служебной программы tsql.

## <a name="export-data-with-sqoop"></a>Экспорт данных с помощью Sqoop

1. Чтобы проверить, видно ли в Sqoop базу данных SQL, используйте следующую команду:
   
    ```
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```
   
    Эта команда должна вывести список баз данных, включая базу данных, в которой вы создали таблицу delays ранее.

2. Для экспорта данных из hivesampletable в таблицу mobiledata используйте следующую команду:
   
    ```
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```
   
    Она дает Sqoop указание подключиться к базе данных SQL, к базе данных с таблицей delays, и экспортировать данные из каталога `/tutorials/flightdelays/output` (где хранятся выходные данные запроса Hive, выполненного ранее) в таблицу delays.

3. После выполнения команды используйте следующую команду для подключения к базе данных с помощью TSQL:
   
    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```
   
    После установления подключения используйте следующие инструкции для проверки экспорта данных в таблицу mobiledata:
   
    ```
    SELECT * FROM delays
    GO
    ```
    
    Вы увидите список данных в таблице. Введите `exit` для выхода из служебной программы tsql.

## <a name="a-idnextstepsa-next-steps"></a><a id="nextsteps"></a> Дальнейшие действия

Теперь вы знаете, как отправить файл в хранилище больших двоичных объектов, как заполнить таблицу Hive, используя данные из хранилища больших двоичных объектов, как выполнять запросы Hive и как использовать Sqoop для экспорта данных из HDFS в базу данных SQL Azure. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][hdinsight-get-started]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Oozie с HDInsight][hdinsight-use-oozie]
* [Использование Sqoop с Hadoop в HDInsight][hdinsight-use-sqoop]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Разработка программ MapReduce на Java для Hadoop в HDInsight на платформе Linux][hdinsight-develop-mapreduce]
* [Разработка программ потоковой передачи на Python для HDInsight][hdinsight-develop-streaming]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx






<!--HONumber=Feb17_HO2-->


