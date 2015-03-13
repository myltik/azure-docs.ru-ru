<properties
   pageTitle="Использование Hive в HDInsight | Azure"
   description="Информация об использовании Hive с HDInsight с помощью Visual Studio."
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

#Выполнение запросов Hive с помощью инструментов HDInsight для Visual Studio

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

В этой статье вы узнаете, как можно удаленно отправлять запросы Hive в кластер HDInsight с помощью средств HDInsight для Visual Studio.

> [AZURE.NOTE] В этом документе не приводится подробное описание процессов, которые выполняют операторы HiveQL, используемые в примерах. Информацию об инструкциях HiveQL, используемых в данном примере, см. в разделе <a href="../hdinsight-use-hive/" target="_blank">"Использование Hive с Hadoop в HDInsight"</a>.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Windows или Linux).

* Visual Studio 2012 <a href="http://www.microsoft.com/ru-ru/download/details.aspx?id=39305" target="_blank">с обновлением 4</a>, Visual Studio 2013 <a href="http://www.microsoft.com/ru-ru/download/details.aspx?id=43721" target="_blank">с обновлением 3</a> или <a href="http://www.microsoft.com/ru-ru/download/details.aspx?id=43722" target="_blank">Visual Studio Express 2013.</a>

##<a id="run"></a> Выполнение запросов Hive с помощью инструментов HDInsight для Visual Studio

1. Откройте **Visual Studio** и выберите **Создать**, **Проект**, **HDInsight** и **Приложение Hive**. Введите имя этого проекта.

2. Откройте файл **Script.hql**, созданный в этом проекте, и вставьте следующие операторы HiveQL.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Эти операторы выполняют следующие действия.

    * **DROP TABLE** - приводит к удалению таблицы и файла данных в том случае, если таблица уже существует
    * **CREATE EXTERNAL TABLE** - создает новую  'external' таблицу в Hive. Внешние таблицы хранят только описание самой таблицы в Hive, в то время как данные остаются в исходном расположении.

        > [AZURE.NOTE] Внешние таблицы необходимо использовать в тех случаях, когда ожидается, что исходные данные будут обновляться внешним источником, таким как автоматизированный процесс передачи данных или другой операцией MapReduce, при этом нужно, чтобы запросы Hive использовали самые последние данные.
        >
        > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только описание таблицы.

    * **ROW FORMAT** - указывает Hive, как следует форматировать данные. В данном случае поля всех журналов разделены пробелом.
    * **STORED AS TEXTFILE LOCATION** - указывает Hive место, где хранятся данные (например, в каталоге example/data), а также то, что они хранятся в виде текста
    * **SELECT** - подсчитывает количество строк, у которых столбец **t4** содержит значение **[ERROR]**. Эта команда должна вернуть значение **3**, так как таблица содержит три строки с данным значением.

3. На панели инструментов выберите **кластер HDInsight**, который нужно использовать для этого запроса, а затем выберите **Отправить**, чтобы выполнить операторы как задание Hive. Отобразится **сводка по заданию Hive** и информация о его выполнении. Воспользуйтесь ссылкой **Обновить**, чтобы обновить информацию о задании. Обновляйте ее до тех пор, пока **Состояние задания** не изменится на **Завершено**.

4. Воспользуйтесь ссылкой **Выходные данные задания**, чтобы просмотреть выходные данные этого задания. В них должен содержаться текст `[ERROR] 3`, который является значением, возвращенным оператором SELECT.

5. Запросы Hive можно также отправлять без создания проекта. С помощью **обозревателя серверов** разверните узлы **Azure**, **HDInsight**, щелкните правой кнопкой мыши сервер HDInsight, а затем выберите **Написать запрос Hive**.

6. Отобразится документ **temp.hql**, в который нужно добавить следующие операторы HiveQL:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    Данная операция выполняет следующие действия:

    * **CREATE TABLE IF NOT EXISTS** - будет создана таблица, если она до этого не существовала. Так как ключевое слово **EXTERNAL** не было использовано, данная таблица будет являться "внутренней", то есть храниться в хранилище данных Hive и полностью обслуживаться Hive.

        > [AZURE.NOTE] В отличие от **ВНЕШНИХ**, удаление внутренних таблиц приводит также к удалению данных.

    * **STORED AS ORC** - данные будут храниться в формате Optimized Row Columnar (ORC). Это высокооптимизированный и эффективный формат для хранения данных Hive.
    * **INSERT OVERWRITE ... SELECT** - из таблицы **log4jLogs** будут выбраны строки, которые содержат значение **[ERROR]**, а затем данные будут вставлены в таблицу **errorLogs**.

7. На панели инструментов выберите в раскрывающемся списке **Отправить** для выполнения задания. Определить, было ли задание выполнено успешно, можно по значению в поле **Состояние задания**.

8. Убедитесь, что задание выполнено и создана новая таблица. Для этого воспользуйтесь **обозревателем серверов** и разверните **Azure**, **HDInsight**, кластер HDInsight, **Базы данных Hive** и **по умолчанию**. Вы должны увидеть таблицы **errorLogs** и **log4jLogs**.

##<a id="summary"></a>Сводка

Как можно видеть, средства HDInsight для Visual Studio позволяют с легкостью выполнять запросы Hive в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Hive в HDInsight.

* [Использование Hive с Hadoop в HDInsight](../hdinsight-use-hive/)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Pig с Hadoop в HDInsight](../hdinsight-use-pig/)

* [Использование MapReduce с Hadoop в HDInsight](../hdinsight-use-mapreduce/)

Дополнительная информация о средствах HDInsight для Visual Studio.

* [Приступая к работе со средствами HDInsight для Visual Studio](../hdinsight-hadoop-visual-studio-tools-get-started/)


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
