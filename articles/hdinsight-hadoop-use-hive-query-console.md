<properties
   pageTitle="Использование Hadoop Hive в HDInsight для платформы Azure"
   description="Информация об использовании Hive с помощью консоли веб-запросов HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Выполнение запросов Hive с помощью консоли запросов

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

В этой статье вы узнаете, как выполнять запросы Hive в кластере HDInsight Hadoop из браузера с помощью консоли запросов HDInsight.

> [AZURE.NOTE] Консоль запросов доступна только в кластерах HDInsight на платформе Windows.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер HDInsight Hadoop на платформе Windows

* Современный браузер

##<a id="run"></a> Выполнение запросов Hive с помощью консоли запросов

1. Откройте <a href="https://manage.windowsazure.com" target="_blank">портал управления Azure</a> и выберите кластер HDInsight. В нижней части страницы выберите **Консоль запросов**. При появлении запроса введите имя пользователя и пароль, который использовались при создании кластера.

    > [AZURE.NOTE] Консоль запросов можно также открыть, введя адрес **https://CLUSTERNAME.azurehdinsight.net** в браузере.

2. Среди ссылок в верхней части страницы выберите **Редактор Hive**. Откроется форма, которую можно использовать для ввода операторов HiveQL, которые будут выполняться в кластере HDInsight. 
	
	![the hive editor](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)
	
	Замените текст `Select * from hivesampletable` следующими операторами HiveQL.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Эти операторы выполняют следующие действия:

    * **DROP TABLE** - приводит к удалению таблицы и файла данных в том случае, если таблица уже существует
    * **CREATE EXTERNAL TABLE** - создает новую  'external' таблицу в Hive. Внешние таблицы хранят только описание самой таблицы в Hive, в то время как данные остаются в исходном расположении.

    > [AZURE.NOTE] Внешние таблицы необходимо использовать в тех случаях, когда ожидается, что исходные данные будут обновляться внешним источником, таким как автоматизированный процесс передачи данных или другой операцией MapReduce, при этом нужно, чтобы запросы Hive использовали самые последние данные.
    >
    > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только описание таблицы.

    * **ROW FORMAT** - указывает Hive, как следует форматировать данные. В данном случае поля всех журналов разделены пробелом.
    * **STORED AS TEXTFILE LOCATION** - указывает Hive место, где хранятся данные (например, в каталоге example/data), а также то, что они хранятся в виде текста
    * **SELECT** - подсчитывает количество строк, у которых столбец **t4** содержит значение **[ERROR]**. Эта команда должна вернуть значение **3**, так как таблица содержит три строки с данным значением.

2. Щелкните **Отправить**. В поле **Сеанс задания** в нижней части страницы, должна отображаться подробная информация о задании.

3. После изменения значения поля **Состояние** на **Завершено** выберите **Показать подробности** для задания. На странице подробностей **выходные данные задания** будут содержать `[ERROR]	3`. Чтобы скачать файл, содержащий результат задания, можно нажать кнопку **Скачать** под этим полем.


##<a id="summary"></a>Сводка

Как можно видеть, консоль запросов позволяет с легкостью выполнять запросы Hive в кластере HDInsight, отслеживать состояние задания и получать выходные данные. 

Чтобы получить дополнительную информацию о Hive с помощью консоли запросов, выберите **Приступая к работе** в верхней части консоли запросов, а затем используйте примеры. Каждый пример содержит пошаговые инструкции по анализу данных с помощью Hive, в том числе пояснения к операторами HiveQL, используемым в примере.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Hive в HDInsight.

* [Использование Hive с Hadoop в HDInsight](../hdinsight-use-hive/)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Pig с Hadoop в HDInsight](../hdinsight-use-pig/)

* [Использование MapReduce с Hadoop в HDInsight](../hdinsight-use-mapreduce/)

[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/ pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/ documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=45--> 
