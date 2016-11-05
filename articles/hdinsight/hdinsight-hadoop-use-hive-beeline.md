---
title: Использовать Beeline для работы с Hive в HDInsight (Hadoop) | Microsoft Docs
description: Узнайте, как использовать SSH для подключения к кластеру Hadoop в HDInsight и интерактивно отправлять запросы Hive с помощью Beeline. Beeline — это служебная программа для работы с HiveServer2 через JDBC.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/10/2016
ms.author: larryfr

---
# <a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Использование Hive с Hadoop в HDInsight с применением Beeline
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Из этой статьи вы узнаете, как использовать протокол SSH для подключения к кластеру HDInsight на базе Linux и отправлять запросы Hive с помощью интерфейса командной строки [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) в интерактивном режиме.

> [!NOTE]
> Для подключения к Hive в Beeline используется интерфейс JDBC. Дополнительные сведения об использовании JDBC с Hive см. в статье [Подключение к Hive в Azure HDInsight с помощью драйвера Hive JDBC](hdinsight-connect-hive-jdbc-driver.md).
> 
> 

## <a name="<a-id="prereq"></a>prerequisites"></a><a id="prereq"></a>Предварительные требования
Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Hadoop в кластере HDInsight на платформе Linux.
* Клиент SSH. Клиент SSH должен входить в состав Linux, Unix и Mac OS. Пользователи Windows должны загрузить отдельный клиент, например [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="<a-id="ssh"></a>connect-with-ssh"></a><a id="ssh"></a>Подключение по SSH
Подключитесь с помощью команды SSH, используя полное доменное имя (FQDN) кластера HDInsight. Полное доменное имя — это имя, присвоенное кластеру, с суффиксом **.azurehdinsight.net**. Например, следующая команда позволяет подключиться к кластеру с именем **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Если указан ключ сертификата для аутентификации SSH** , возможно, при создании кластера HDInsight потребуется указать расположение закрытого ключа в клиентской системе.

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**для аутентификации SSH был задан пароль** , при появлении запроса необходимо будет ввести пароль.

Дополнительную информацию об использовании SSH с HDInsight см. в разделе [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="putty-(windows-based-clients)"></a>PuTTY (клиенты под управлением Windows)
Windows не предоставляет встроенный клиент SSH. Рекомендуется использовать **PuTTY**, который можно скачать по адресу: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Дополнительные сведения об использовании PuTTY см. в разделе [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a name="<a-id="beeline"></a>use-the-beeline-command"></a><a id="beeline"></a>Использование команды Beeline
1. После подключения запустите Beeline с помощью такой команды:
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
   
    Клиент Beeline будет запущен, и будет выполнен переход по URL-адресу JDBC. В нашем случае указан адрес `localhost` , так как сервер HiveServer2 работает на обоих головных узлах кластера, а мы запускаем Beeline непосредственно на основном головном узле.
   
    После выполнения команды отобразится командная строка `jdbc:hive2://localhost:10001/>`.
2. Команды Beeline обычно начинаются с символа `!`. Например, `!help` выводит справку. При этом `!` часто можно опускать. Например, `help` также будет работать.
   
    При просмотре справки можно заметить текст `!sql`, который используется для выполнения инструкций HiveQL. Однако эти инструкции настолько распространены, что `!sql`тоже можно опустить. Следующие две инструкции дадут идентичный результат (будут выведены таблицы, доступные в Hive):
   
        !sql show tables;
        show tables;
   
    В новом кластере должна быть указана только одна таблица: **hivesampletable**.
3. Используйте следующую команду, чтобы вывести схему для таблицы hivesampletable:
   
        describe hivesampletable;
   
    Команда возвращает следующую информацию:
   
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
   
    Здесь перечислены столбцы в таблице. Хотя мы можем выполнять отдельные запросы к этим данным, мы создадим новую таблицу, чтобы продемонстрировать загрузку данных в Hive и применение схемы.
4. Введите следующие инструкции, чтобы создать таблицу с именем **log4jLogs** , используя пример данных, предоставляемый с кластером HDInsight:
   
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   
    Эти операторы выполняют следующие действия.
   
   * **DROP TABLE** : удаление таблицы и файла данных, если таблица уже существует.
   * **CREATE EXTERNAL TABLE** : создание новой "внешней" таблицы в Hive. Внешние таблицы хранят только определения таблицы в Hive. Данные остаются в исходном расположении.
   * **ROW FORMAT** : инструкции по форматированию данных для Hive. В данном случае поля всех журналов разделены пробелом.
   * **STORED AS TEXTFILE LOCATION** : информация для Hive о расположении хранения данных (каталог example/data) и об их формате (текстовый).
   * **SELECT** : подсчитывает количество строк, у которых столбец **t4** содержит значение **[ERROR]**. Эта команда должна вернуть значение **3** , так как таблица содержит три строки с данным значением.
   * **INPUT__FILE__NAME LIKE '%.log'** — указывает Hive, что вернуть нужно только данные из файлов с расширением LOG. Как правило, возвращаемые запросом Hive данные с одной и той же схемой хранятся в одной и той же папке. Однако в этом примере файл журнала хранится с другими форматами данных.
     
     > [!NOTE]
     > Внешние таблицы необходимо использовать в тех случаях, когда ожидается, что исходные данные будут обновляться внешним источником, таким как автоматизированный процесс передачи данных или другой операцией MapReduce, при этом нужно, чтобы запросы Hive использовали самые последние данные.
     > 
     > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только определение таблицы.
     > 
     > 
     
     Результат этой команды должен выглядеть примерно так:
     
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
5. Чтобы выйти из Beeline, используйте инструкцию `!quit`.

## <a name="<a-id="file"></a>run-a-hiveql-file"></a><a id="file"></a>Запуск файла HiveQL.
Beeline также можно использовать для запуска файла, содержащего инструкции HiveQL. Чтобы создать файл, а затем запустить его с помощью Beeline, выполните следующие действия.

1. Создайте файл **query.hql**, используя следующую команду:
   
        nano query.hql
2. Когда откроется редактор, добавьте в файл следующее содержимое: Запрос создаст новую таблицу internal с именем **errorLogs**.
   
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   
    Эти операторы выполняют следующие действия.
   
   * **CREATE TABLE IF NOT EXISTS** : создание таблицы, если она до этого не существовала. Так как ключевое слово **EXTERNAL** не было использовано, данная таблица будет являться "внутренней", то есть храниться в хранилище данных Hive и полностью обслуживаться Hive.
   * **STORED AS ORC** : хранение данных в формате ORC (Optimized Row Columnar). Это высокооптимизированный и эффективный формат для хранения данных Hive.
   * **INSERT OVERWRITE ... SELECT**: выбирает строки из таблицы **log4jLogs**, которые содержат значение **[ERROR]**, а затем вставляет данные в таблицу **errorLogs**.
     
     > [!NOTE]
     > В отличие от внешних таблиц, удаление внутренних таблиц приводит также к удалению данных.
     > 
     > 
3. Чтобы сохранить файл, нажмите клавиши **CTRL** + **X**, введите **Y** и нажмите клавишу **ВВОД**.
4. Запустите файл с помощью Beeline, используя следующую команду: Замените **HOSTNAME** полученным ранее именем головного узла, а **PASSWORD** — паролем учетной записи администратора.
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql
   
   > [!NOTE]
   > Параметр `-i` запускает Beeline, выполняет инструкции в файле query.hql и остается в Beeline в командной строке `jdbc:hive2://localhost:10001/>`. Кроме того, вы можете запустить файл с помощью параметра `-f` , который возвращает вас в Bash после обработки файла.
   > 
   > 
5. Чтобы убедиться, что таблица **errorLogs** создана, выполните приведенную ниже инструкцию (она выводит все строки из таблицы **errorLogs**).
   
        SELECT * from errorLogs;
   
    В результате операции должны быть возвращены три строки со значением **[ERROR]** в столбце t4.
   
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>Дополнительные сведения о возможностях подключения Beeline
В инструкциях в этом документе для подключения к HiveServer2 на головном узле кластера используется `localhost`. Хотя можно также использовать имя узла или полное доменное имя головного узла, это потребует выполнить дополнительные действия (для поиска имени узла или полного доменного имени). При использовании Beeline на головном узле достаточно возможностей `localhost`.

Если в кластере имеется граничный узел, на котором установлен компонент Beeline, то для подключения потребуется использовать имя узла или полное доменное имя головного узла.

Если компонент Beeline установлен на клиенте вне кластера, к нему можно подключиться с помощью следующей команды. Замените **CLUSTERNAME** именем кластера HDInsight. Замените **PASSWORD** паролем администратора (имени для входа по протоколу HTTP).

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

Обратите внимание, что параметры и универсальный код ресурса (URI) отличаются от используемых при запуске непосредственно на головном узле или из углового узла в кластере. Причина этого в том, что при подключении к кластеру через Интернет используется общедоступный шлюз, который направляет трафик через порт 443. Кроме того, некоторые другие службы предоставляются через общедоступный шлюз на порту 443, поэтому универсальный код ресурса (URI) отличается от при прямом подключении. При подключении из Интернета необходимо также аутентифицировать сеанс с помощью пароля.

## <a name="<a-id="summary"></a><a-id="nextsteps"></a>next-steps"></a><a id="summary"></a><a id="nextsteps"></a>Дальнейшие действия
Как видите, команда Beeline предоставляет простой способ отправки запросов Hive в кластере HDInsight в интерактивном режиме.

Общая информация о Hive в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Если вы используете Tez с Hive, обратитесь к следующим документам для сведений об отладке:

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


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx




<!--HONumber=Oct16_HO2-->


