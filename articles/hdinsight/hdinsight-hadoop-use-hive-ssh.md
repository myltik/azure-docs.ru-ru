<properties
   pageTitle="Использование Hadoop Hive и SSH в HDInsight | Microsoft Azure"
   description="Узнайте, как использовать протокол SSH для подключения к кластеру Hadoop в HDInsight и отправлять запросы Hive с помощью интерфейса командной строки Hive в интерактивном режиме."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#Использование Hive с Hadoop в HDInsight с применением протокола SSH

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

В этой статье вы узнаете, как использовать протокол SSH для подключения к Hadoop в кластере Azure HDInsight и отправлять запросы Hive с помощью интерфейса командной строки Hive в интерактивном режиме.

> [AZURE.NOTE]Если вы уже знакомы с использованием серверов под управлением Linux Hadoop, но не знакомы с HDInsight, см. раздел [Что необходимо знать о Hadoop в HDInsight на основе Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Hadoop в кластере HDInsight на платформе Linux.

* Клиент SSH. Клиент SSH должен входить в состав Linux, Unix и Mac OS. Пользователи Windows должны загрузить отдельный клиент, например [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Подключение по SSH

Подключитесь с помощью команды SSH, используя полное доменное имя (FQDN) кластера HDInsight. Полное доменное имя — это имя, присвоенное кластеру, с суффиксом **.azurehdinsight.net**. Например, следующая команда позволяет подключиться к кластеру с именем **myhdinsight**:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Если указан ключ сертификата для аутентификации SSH**, возможно, при создании кластера HDInsight потребуется указать расположение закрытого ключа в клиентской системе.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

Если при создании кластера HDInsight **для аутентификации SSH был задан пароль**, при появлении запроса необходимо будет ввести пароль.

Дополнительную информацию об использовании SSH с HDInsight см. в разделе [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

###PuTTY (клиенты под управлением Windows)

Windows не предоставляет встроенный клиент SSH. Рекомендуется использовать **PuTTY**, который можно скачать по адресу: [http://www.chiark.greenend.org.uk/\~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Дополнительные сведения об использовании PuTTY см. в разделе [Использование SSH с Hadoop на основе Linux в HDInsight из Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hive"></a>Использование команды Hive

2. После подключения запустите интерфейс командной строки Hive с помощью следующей команды:

        hive

3. Используя интерфейс командной строки, введите следующие операторы, чтобы создать таблицу с именем **log4jLogs** с помощью примера данных:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Эти операторы выполняют следующие действия:

    * **DROP TABLE**: удаление таблицы и файла данных, если таблица уже существует.
    * **CREATE EXTERNAL TABLE**: создание новой «внешней» таблицы в Hive. Внешние таблицы хранят только определения таблицы в Hive. Данные остаются в исходном расположении.
    * **ROW FORMAT**: инструкции по форматированию данных для Hive. В данном случае поля всех журналов разделены пробелом.
    * **STORED AS TEXTFILE LOCATION**: информация для Hive о расположении хранения данных (каталог example/data) и об их формате (текстовый).
    * **SELECT**: подсчитывает количество строк, в которых столбец **t4** содержит значение **[ERROR]**. Эта команда должна вернуть значение **3**, так как таблица содержит три строки с данным значением.

    > [AZURE.NOTE]Внешние таблицы необходимо использовать в тех случаях, когда ожидается, что исходные данные будут обновляться внешним источником, таким как автоматизированный процесс передачи данных или другой операцией MapReduce, при этом нужно, чтобы запросы Hive использовали самые последние данные.
    >
    > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только определение таблицы.

4. Используйте следующие операторы, чтобы создать новую «внутреннюю» таблицу с именем **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    Эти инструкции выполняют следующие действия.

    * **CREATE TABLE IF NOT EXISTS**: создание таблицы, если она до этого не существовала. Так как ключевое слово **EXTERNAL** не было использовано, данная таблица будет являться «внутренней», то есть храниться в хранилище данных Hive и полностью обслуживаться Hive.
    * **STORED AS ORC**: хранение данных в формате ORC (Optimized Row Columnar). Это высокооптимизированный и эффективный формат для хранения данных Hive.
    * **INSERT OVERWRITE ... SELECT** — выбирает строки из таблицы **log4jLogs**, которые содержат значение **[ERROR]**, а затем вставляет данные в таблицу **errorLogs**.

    Чтобы убедиться в том, что в таблице **errorLogs** сохранены только строки, столбец t4 которых содержит значение **[ERROR]**, воспользуйтесь следующим запросом, чтобы отобразить все строки из таблицы **errorLogs**.

        SELECT * from errorLogs;

    В результате операции должны быть возвращены три строки, в которых столбец t4 содержит значение **[ERROR]**.

    > [AZURE.NOTE]В отличие от внешних таблиц, удаление внутренних таблиц приводит также к удалению данных.

##<a id="summary"></a>Сводка

Как видите, с помощью команды Hive можно с легкостью выполнять интерактивные запросы Hive в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Hive в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

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

[hdinsight-storage]: hdinsight-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!---HONumber=August15_HO8-->