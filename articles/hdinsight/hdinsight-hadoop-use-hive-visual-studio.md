<properties
   pageTitle="Выполнение запроса Hive с помощью инструментов Hadoop для Visual Studio | Microsoft Azure"
   description="Узнайте, как использовать Hive с Hadoop в HDInsight с помощью инструментов Visual Studio Hadoop."
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
   ms.date="06/16/2016"
   ms.author="larryfr"/>

#Выполнение запросов Hive с помощью инструментов HDInsight для Visual Studio

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

В этой статье вы узнаете, как отправлять запросы Hive в кластер HDInsight с помощью инструментов HDInsight для Visual Studio.

> [AZURE.NOTE] В этом документе не приводится подробное описание процессов, которые выполняют операторы HiveQL, используемые в примерах. Информацию об операторах HiveQL, используемых в данном примере, см. в статье [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Windows или Linux).

* Одна из следующих версий Visual Studio:

    Visual Studio 2013 Community, Professional, Premium или Ultimate с [обновлением 4](https://www.microsoft.com/download/details.aspx?id=44921);

    Visual Studio 2015 (Community/Enterprise)

- Средства HDInsight для Visual Studio. Пошаговые указания по установке и настройке инструментов Visual Studio Hadoop см. в статье [Приступая к работе с инструментами Hadoop в Visual Studio для HDInsight для выполнения запроса Hive](hdinsight-hadoop-visual-studio-tools-get-started.md).

##<a id="run"></a> Выполнение запросов Hive с помощью инструментов HDInsight для Visual Studio

1. Откройте **Visual Studio** и выберите **Создать** > **Проект** > **HDInsight** > **Приложение Hive**. Введите имя этого проекта.

2. Откройте файл **Script.hql**, созданный в этом проекте, и вставьте следующие операторы HiveQL:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Эти операторы выполняют следующие действия:

    * **DROP TABLE**: удаление таблицы и файла данных, если таблица уже существует.
    * **CREATE EXTERNAL TABLE**: создание новой "внешней" таблицы в Hive. Внешние таблицы хранят только определение самой таблицы в Hive, в то время как данные остаются в исходном расположении.

        > [AZURE.NOTE] Внешние таблицы необходимо использовать в тех случаях, когда ожидается, что исходные данные будут обновляться внешним источником, таким как автоматизированный процесс передачи данных или другой операцией MapReduce, при этом нужно, чтобы запросы Hive использовали самые последние данные.
        >
        > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только определение таблицы.

    * **ROW FORMAT**: инструкции по форматированию данных для Hive. В данном случае поля всех журналов разделены пробелом.
    * **STORED AS TEXTFILE LOCATION**: информация для Hive о расположении хранения данных (каталог example/data) и о их формате (текстовый).
    * **SELECT**: подсчет количества строк, у которых столбец **t4** содержит значение **ERROR**. Эта команда должна вернуть значение **3**, так как данное значение содержат три строки.
    * **INPUT\_\_FILE\_\_NAME LIKE '%.log'** — указывает Hive, что возвратить нужно только данные из файлов с расширением LOG. Это ограничивает поиск файлом sample.log, в котором содержатся данные, и предотвращает возврат данных из других примеров файлов данных, не соответствующих определенной нами схеме.

3. На панели инструментов выберите **кластер HDInsight**, который нужно использовать для этого запроса, а затем выберите **Submit to WebHCat** (Отправить в WebHCat), чтобы выполнить инструкции как задание Hive с помощью WebHCat. Задание также можно отправить с помощью кнопки __Execute via HiveServer2__ (Выполнить через HiveServer2), если сервер HiveServer2 доступен в вашей версии кластера. Отобразится **сводка по заданию Hive** и информация о его выполнении. Воспользуйтесь ссылкой **Обновить**, чтобы обновить информацию о задании. Обновляйте ее до тех пор, пока **Состояние задания** не изменится на **Завершено**.

4. Воспользуйтесь ссылкой **Выходные данные задания**, чтобы просмотреть выходные данные этого задания. В них должен быть текст `[ERROR] 3`, который является значением, возвращенным оператором SELECT.

5. Запросы Hive можно также отправлять без создания проекта. С помощью **обозревателя сервера** разверните узлы **Azure** > **HDInsight**, щелкните правой кнопкой мыши сервер HDInsight, а затем выберите **Написать запрос Hive**.

6. Отобразится документ **temp.hql**, в который нужно добавить следующие операторы HiveQL:

        set hive.execution.engine=tez;
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Эти операторы выполняют следующие действия:

    * **CREATE TABLE IF NOT EXISTS**: создание таблицы, если она до этого не существовала. Так как ключевое слово **EXTERNAL** не использовалось, такая таблица будет внутренней, то есть хранящейся в хранилище данных Hive и полностью управляемой Hive.

        > [AZURE.NOTE] В отличие от **внешних** таблиц, удаление внутренней таблицы приводит к удалению базовых данных.

    * **STORED AS ORC**: хранение данных в формате ORC (Optimized Row Columnar). Это высокооптимизированный и эффективный формат для хранения данных Hive.
    * **INSERT OVERWRITE ... SELECT**: из таблицы **log4jLogs** будут выбраны строки, которые содержат значение **[ERROR]**, а затем данные будут вставлены в таблицу errorLogs.ляет данные в таблицу **errorLogs**.

7. На панели инструментов щелкните **Отправить**, чтобы выполнить задание. Определить, было ли задание выполнено успешно, можно по значению в поле **Состояние задания**.

8. Убедитесь, что задание выполнено и создана новая таблица. Для этого выберите **обозревателем сервера** и разверните **Azure** > **HDInsight** > ваш кластер HDInsight > **Базы данных Hive** и **по умолчанию**. Вы должны увидеть таблицы **errorLogs** и **log4jLogs**.

##<a id="summary"></a>Сводка

Как можно видеть, инструменты HDInsight для Visual Studio позволяют с легкостью выполнять запросы Hive в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Hive в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Дополнительная информация об инструментах HDInsight для Visual Studio:

* [Приступая к работе со средствами HDInsight для Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)


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

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!---HONumber=AcomDC_0622_2016-->