---
title: "Использовать Beeline для работы с Hive в HDInsight (Hadoop) | Документация Майкрософт"
description: "Узнайте, как использовать SSH для подключения к кластеру Hadoop в HDInsight и интерактивно отправлять запросы Hive с помощью Beeline. Beeline — это служебная программа для работы с HiveServer2 через JDBC."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/05/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7a1757a7ef2881b9e09389745a8e5aeaea2abf9d
ms.lasthandoff: 04/12/2017


---
# <a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Использование Hive с Hadoop в HDInsight с применением Beeline
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Узнайте, как использовать [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) для выполнения запросов Hive в HDInsight.

Beeline — это программа командной строки, установленная на головных узлах кластера HDInsight. Она использует JDBC для подключения к службе HiveServer2, размещенной на вашем кластере HDInsight. В следующей таблице приведены строки подключения для Beeline.

| Где запускается Beeline | Строка подключения | Другие параметры |
| --- | --- | --- |
| SSH-подключение к головному узлу | `jdbc:hive2://localhost:10001/;transportMode=http` | `-n admin` |
| Граничный узел | `jdbc:hive2://headnodehost:10001/;transportMode=http` | `-n admin` |
| Вне кластера | `jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2` | `-n admin -p password` |

> [!NOTE]
> Замените admin учетной записью для входа на кластер.
>
> Замените password паролем этой учетной записи.
>
> Замените `clustername` на имя вашего кластера HDInsight.

## <a id="prereq"></a>Предварительные требования

* Hadoop в кластере HDInsight на платформе Linux.

  > [!IMPORTANT]
  > Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. Чтобы узнать больше, ознакомьтесь с [нерекомендуемыми версиями HDInsight 3.2 и 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* Клиент SSH. Дополнительные сведения об использовании SSH см. в разделе [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="ssh"></a>Подключение по SSH

Подключитесь к кластеру по протоколу SSH, используя приведенную ниже команду.

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

Замените `sshuser` именем учетной записи SSH для кластера. Замените `myhdinsight` на имя вашего кластера HDInsight.

Если при создании кластера HDInsight **для аутентификации SSH был задан пароль**, при появлении запроса необходимо будет его ввести.

**Если указан ключ сертификата для аутентификации SSH** , возможно, при создании кластера HDInsight потребуется указать расположение закрытого ключа в клиентской системе.

    ssh -i ~/.ssh/mykey.key ssh@myhdinsight-ssh.azurehdinsight.net

Дополнительные сведения об использовании протокола SSH с HDInsight см. в разделе [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Использование команды Beeline

1. В сеансе SSH используйте следующую команду, чтобы запустить Beeline.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Эта команда запускает клиент Beeline и подключает его к HiveServer2 на головном узле кластера. Параметр `-n` используется для указания учетной записи для входа на кластер. Имя для входа по умолчанию — `admin`. Если во время создания кластера было указано другое имя, используйте его вместо `admin`.

    После выполнения команды отобразится командная строка `jdbc:hive2://localhost:10001/>`.

2. Команды Beeline начинаются со знака `!`. Например, `!help` выводит справку. Однако в некоторых командах `!` можно опустить. Например, `help` также работает.

    Для выполнения инструкций HiveQL используется `!sql`. Однако эти инструкции настолько распространены, что `!sql`тоже можно опустить. Приведенные ниже две инструкции эквивалентны.

    ```hiveql
    !sql show tables;
    show tables;
    ```

    В новом кластере отображена только одна таблица, **hivesampletable**.

3. Используйте следующую команду, чтобы отобразить схему для таблицы hivesampletable.

    ```bash
    describe hivesampletable;
    ```

    Эта команда возвращает приведенные ниже сведения.

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Они описывают столбцы в таблице. Хотя мы можем выполнять отдельные запросы к этим данным, мы создадим новую таблицу, чтобы продемонстрировать загрузку данных в Hive и применение схемы.

4. Введите следующие инструкции, чтобы создать таблицу **log4jLogs**, используя демонстрационные данные, предоставляемые с кластером HDInsight.

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Эти операторы выполняют следующие действия:

    * **DROP TABLE** — если таблица уже существует, она будет удалена.

    * **CREATE EXTERNAL TABLE** — создание **внешней** таблицы в Hive. Внешние таблицы хранят только определения таблицы в Hive. Данные остаются в исходном расположении.

    * **ROW FORMAT** — настройка форматирования данных. В данном случае поля всех журналов разделены пробелом.

    * **STORED AS TEXTFILE LOCATION** — указание расположения для хранения данных и их формат.

    * **SELECT** : подсчитывает количество строк, у которых столбец **t4** содержит значение **[ERROR]**. Этот запрос должен вернуть значение **3**, так как таблица содержит три строки с данным значением.

    * **INPUT__FILE__NAME LIKE '%.log'** — файл демонстрационных данных хранится вместе с другими файлами. Эта инструкция ограничивает запрос данными, хранящимся в файлах с расширением log.

  > [!NOTE]
  > Внешние таблицы следует использовать, если исходные данные должны обновляться с использованием внешних источников. Например, процессом автоматизированной передачи данных или другой операцией MapReduce.
  >
  > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только определение таблицы.

    После выполнения этой команды вы должны увидеть текст, аналогичный приведенному ниже.

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

5. Чтобы выйти из Beeline, используйте инструкцию `!exit`.

## <a id="file"></a>Запуск файла HiveQL.

Чтобы создать файл, а затем запустить его с помощью Beeline, выполните следующие действия.

1. Создайте файл **query.hql**, используя следующую команду.

    ```bash
    nano query.hql
    ```

2. В качестве содержимого файла добавьте следующий текст: Этот запрос создает "внутреннюю" таблицу **errorLogs**.

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Эти операторы выполняют следующие действия:

    * **CREATE TABLE IF NOT EXISTS** — создание таблицы, если ее не существует. Так как не используется ключевое слово **EXTERNAL**, эта инструкция создает внутреннюю таблицу. Внутренние таблицы хранятся в хранилище данных Hive и полностью управляются Hive.
    * **STORED AS ORC** : хранение данных в формате ORC (Optimized Row Columnar). Это высокооптимизированный и эффективный формат для хранения данных Hive.
    * **INSERT OVERWRITE ... SELECT**: выбирает строки из таблицы **log4jLogs**, которые содержат значение **[ERROR]**, а затем вставляет данные в таблицу **errorLogs**.

    > [!NOTE]
    > В отличие от внешних таблиц, удаление внутренней таблицы приводит к удалению базовых данных.

3. Чтобы сохранить файл, нажмите клавиши **CTRL**+**+X**, введите **Y** и нажмите клавишу **ВВОД**.

4. Запустите файл с помощью Beeline, используя следующую команду: Замените **HOSTNAME** полученным ранее именем головного узла, а **PASSWORD** — паролем учетной записи администратора.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql
    ```

    > [!NOTE]
    > Параметр `-i` запускает Beeline и выполняет инструкции в файле query.hql. После выполнения запроса отобразится командная строка `jdbc:hive2://localhost:10001/>`. Можно также выполнить файл с помощью параметра `-f`, который завершает работу Beeline после завершения выполнения запроса.

5. Чтобы убедиться, что таблица **errorLogs** создана, выполните приведенную ниже инструкцию (она выводит все строки из таблицы **errorLogs**).

    ```hiveql
    SELECT * from errorLogs;
    ```

    В результате операции должны быть возвращены три строки со значением **[ERROR]** в столбце t4.

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="edge-nodes"></a>Граничные узлы

Если кластер содержит граничный узел, рекомендуется всегда использовать его вместо головного узла при подключении по протоколу SSH. Чтобы запустить Beeline с помощью SSH-подключения к граничному узлу, используйте следующую команду.

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -n admin
```

## <a name="remote-clients"></a>Удаленные клиенты

Если программа Beeline установлена локально или используется посредством образа Docker, например [sutoiku или beeline](https://hub.docker.com/r/sutoiku/beeline/), необходимо использовать следующие параметры.

* __Строка подключения__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`.

* __Имя для входа на кластер__:`-n admin`.

* __Пароль для входа на кластер__: `-p 'password'`.

Замените `clustername` в строке подключения именем кластера HDInsight.

Замените `admin` именем для входа на кластер, а `password` — паролем этого имени для входа.

## <a id="summary"></a><a id="nextsteps"></a>Дальнейшие действия

Дополнительные общие сведения об использовании Hadoop в HDInsight см. в следующем документе:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительные сведения о способах работы с Hadoop в HDInsight см. в следующих документах:

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Если вы используете Tez с Hive, ознакомьтесь со следующими документами:

* [Использование пользовательского интерфейса Tez в HDInsight на платформе Windows](hdinsight-debug-tez-ui.md)
* [Использование представления Ambari Tez в HDInsight на платформе Linux](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

