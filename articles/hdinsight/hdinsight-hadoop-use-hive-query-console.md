<properties
   pageTitle="Использование Hadoop Hive в консоли запросов в HDInsight | Microsoft Azure"
   description="Узнайте, как выполнять запросы Hive в кластере HDInsight Hadoop из браузера с помощью консоли запросов HDInsight для Интернета."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/20/2016"
   ms.author="larryfr"/>

# Выполнение запросов Hive с помощью консоли запросов

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

В этой статье вы узнаете, как выполнять запросы Hive в кластере HDInsight Hadoop из браузера с помощью консоли запросов HDInsight.

> [AZURE.IMPORTANT] Консоль запросов HDInsight доступна только в кластерах HDInsight на платформе Windows. Если вы используете кластер HDInsight под управлением Linux, см. статью [Использование представления Hive с Hadoop в HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).


##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер HDInsight Hadoop на платформе Windows

* Современный браузер

##<a id="run"></a> Выполнение запросов Hive с помощью консоли запросов

1. Откройте веб-браузер и перейдите по адресу __https://CLUSTERNAME.azurehdinsight.net__, где __CLUSTERNAME\_\_ — это имя вашего кластера HDInsight. При появлении соответствующего запроса введите имя пользователя и пароль, использованные при создании кластера.


2. Среди ссылок в верхней части страницы выберите **Редактор Hive**. Откроется форма, которую можно использовать для ввода операторов HiveQL, которые будут выполняться в кластере HDInsight.

	![Редактор Hive](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)

	Замените текст `Select * from hivesampletable` следующими операторами HiveQL:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Эти операторы выполняют следующие действия.

    * **DROP TABLE**: удаление таблицы и файла данных, если таблица уже существует.
    * **CREATE EXTERNAL TABLE**: создание новой «внешней» таблицы в Hive. Внешние таблицы хранят только описание самой таблицы в Hive, в то время как данные остаются в исходном расположении.

    > [AZURE.NOTE] Внешние таблицы необходимо использовать в тех случаях, когда ожидается, что исходные данные будут обновляться внешним источником, таким как автоматизированный процесс передачи данных или другой операцией MapReduce, при этом нужно, чтобы запросы Hive использовали самые последние данные.
    >
    > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только определение таблицы.

    * **ROW FORMAT**: инструкции по форматированию данных для Hive. В данном случае поля всех журналов разделены пробелом.
    * **STORED AS TEXTFILE LOCATION**: информация для Hive о расположении хранения данных (каталог example/data) и об их формате (текстовый).
    * **SELECT**: подсчет количества строк, у которых столбец **t4** содержит значение **ERROR**. Эта команда должна вернуть значение **3**, так как данное значение содержат три строки.
    * **INPUT\_\_FILE\_\_NAME LIKE '%.log'** — указывает Hive, что возвратить нужно только данные из файлов с расширением LOG. Это ограничивает поиск файлом sample.log, в котором содержатся данные, и предотвращает возврат данных из других примеров файлов данных, не соответствующих определенной нами схеме.

2. Нажмите кнопку **Submit** (Отправить). В поле **Сеанс задания** в нижней части страницы должна отображаться подробная информация о задании.

3. После изменения значения поля **Состояние** на **Завершено** выберите **Показать подробности** для задания. На странице подробностей **выходные данные задания** будут содержать `[ERROR]	3`. Чтобы скачать файл, содержащий выходные данные задания, можно нажать кнопку **Скачать** под этим полем.


##<a id="summary"></a>Сводка

Как можно видеть, консоль запросов позволяет с легкостью выполнять запросы Hive в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

Чтобы получить дополнительную информацию об использовании консоли запросов Hive, выберите **Приступая к работе** в верхней части консоли запросов, а затем воспользуйтесь предоставленными примерами. Каждый пример содержит пошаговые инструкции по анализу данных с помощью Hive, в том числе пояснения к операторами HiveQL, используемым в примере.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Hive в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Если вы используете Tez с Hive, обратитесь к следующим документам для сведений об отладке:

* [Использование пользовательского интерфейса Tez в HDInsight на платформе Windows](hdinsight-debug-tez-ui.md)

* [Использование представления Ambari Tez в HDInsight на платформе Linux](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

<!---HONumber=AcomDC_0921_2016-->