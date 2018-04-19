---
title: Основные сведения об Apache Hive и HiveQL в Azure HDInsight | Документация Майкрософт
description: Apache Hive — это система хранилища данных для Hadoop. Вы можете запрашивать данные, хранящиеся в Hive, с помощью HiveQL, который напоминает Transact-SQL. В рамках этого руководства вы узнаете, как использовать Hive и HiveQL в Azure HDInsight.
keywords: hiveql, что такое hive, hadoop hiveql, как использовать hive, знакомство с hive, обзор hive
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/26/2018
ms.author: larryfr
ms.openlocfilehash: afd2bc95beb2458ec149824723ec62381b31b2da
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Обзор Apache Hive и HiveQL в Azure HDInsight

[Apache Hive](http://hive.apache.org/) — это система хранилища данных для Hadoop. Hive позволяет обобщать, запрашивать и анализировать данные. Запросы Hive создаются на языке запросов HiveQL, который похож на SQL.

Hive позволяет создавать структуру для преимущественно неструктурированных данных. После определения структуры вы можете использовать HiveQL для запроса этих данных без знания Java или MapReduce.

HDInsight предоставляет несколько типов кластера, которые подходят для конкретных рабочих нагрузок. Для запросов Hive наиболее часто используются следующие типы кластеров:

* __Интерактивный запрос.__ Кластер Hadoop, который оптимизирует время ответа интерактивных запросов с помощью функции [аналитической обработки с низкой задержкой (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP). Дополнительные сведения см. в статье [Использование Interactive Hive с HDInsight (предварительная версия)](../interactive-query/apache-interactive-query-get-started.md).

* __Hadoop:__ кластер Hadoop, который предназначен для рабочих нагрузок пакетной обработки. Дополнительные сведения см. в статье [Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* __Spark:__ Apache Spark содержит встроенные функциональные возможности для работы с Hive. Дополнительные сведения см. в статье [Начало работы. Создание кластера Apache Spark в Azure HDInsight и выполнение интерактивных запросов с помощью SQL Spark](../spark/apache-spark-jupyter-spark-sql.md).

* __HBase:__ HiveQL может использоваться для запроса данных, хранящихся в HBase. Дополнительные сведения см. в статье [Руководство по HBase. Приступая к работе с Apache HBase на Hadoop под управлением Linux в HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md).

## <a name="how-to-use-hive"></a>Как использовать Hive

В следующей таблице показаны различные способы использования Hive в HDInsight:

| **Используйте этот метод**, если требуется: | ...**интерактивные** запросы | ...**пакетная** обработка | ...with этим **кластером операционной системы** | ...из этого **кластера операционной системы** |
|:--- |:---:|:---:|:--- |:--- |
| [Представление Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Для приложений на основе браузера |
| [клиент Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X или Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux или Windows* |Linux, Unix, Mac OS X или Windows |
| [Средства HDInsight для Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ |Linux | Linux, Unix, Mac OS X или Windows |
| [Средства HDInsight для Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux или Windows* |Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux или Windows* |Windows |

> [!IMPORTANT]
> \* Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="hiveql-language-reference"></a>Справочник по языку HiveQL

Справочник по языку HiveQL доступен на странице [LanguageManual (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive и структура данных

Hive поддерживает работу со структурированными и частично структурированными данными. Например, с текстовыми файлами, в которых поля разделяются с помощью определенных знаков. С помощью следующей инструкции HiveQL создается таблица для данных, разделенных пробелами:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Hive также поддерживает пользовательские **сериализаторы/десериализаторы (SerDe)** для сложных или беспорядочно структурированных данных. Дополнительные сведения см. в документе [How to use a custom JSON SerDe with HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx) (Как использовать настраиваемую сериализацию-десериализациюJSON с HDInsight).

Дополнительные сведения о форматах файлов, поддерживаемых Hive, см. в [руководстве по языку (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

### <a name="hive-internal-tables-vs-external-tables"></a>Сравнение внутренних и внешних таблиц Hive

Существует два типа таблиц, которые вы можете создать с помощью Hive:

* __Внутренняя:__ данные хранятся в хранилище данных Hive. Хранилище данных расположено в `/hive/warehouse/`, в хранилище по умолчанию для кластера.

    Используйте внутренние таблицы, если выполняется одно из следующих условий:

    * данные являются временными;
    * вы хотите использовать Hive для управления жизненным циклом таблицы и данных.

* __Внешняя:__ данные хранятся за пределами хранилища данных. Данные могут храниться в любом хранилище, доступном для кластера.

    Используйте внешние таблицы, если выполняется одно из следующих условий:

    * данные также используются за пределами Hive (например, файлы данных обновляются с помощью другого процесса, который не блокирует их);
    * данные должны оставаться в базовом расположении даже после удаления таблицы;
    * вам нужно пользовательское расположение, например нестандартная учетная запись хранилища;
    * программа, отличная от Hive, управляет форматом данных, расположением и т. д.

Дополнительные сведения см. в записи блога [HDInsight: Hive Internal and External Tables Intro][cindygross-hive-tables] (HDInsight: введение во внутренние и внешние таблицы Hive).

## <a name="user-defined-functions-udf"></a>Определяемые пользователем функции (UDF)

Инфраструктура Hive также может быть расширена с помощью **определяемых пользователем функций (UDF)**. UDF позволяет реализовать функции или логику, сложно моделируемые в HiveQL. Примеры использования определяемых пользователем функций с Hive приведены в следующих документах:

* [Работа с определяемыми пользователем функциями Java с использованием Hive в HDInsight](../hadoop/apache-hadoop-hive-java-udf.md)

* [Использование пользовательских функций Python с Hive](../hadoop/python-udf-hdinsight.md)

* [Использование пользовательских функций C# с Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [How to add custom Hive UDFs to HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx) (Как добавить настраиваемые определяемые пользователем функции Hive в HDInsight)

* [Пример определяемой пользователем функции Hive для преобразования форматов даты и времени в метки времени Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Демонстрационные данные

Hive в HDInsight поставляется предварительно загруженным с внутренней таблицей `hivesampletable`. HDInsight также предоставляет пример наборов данных, которые могут использоваться с Hive. Эти наборы данных хранятся в каталогах `/example/data` и `/HdiSamples`. Эти каталоги находятся в хранилище по умолчанию для кластера.

## <a id="job"></a>Пример запроса Hive

Приведенная ниже инструкция HiveQL проецирует столбцы проекта в файл `/example/data/sample.log`.

```hiveql
set hive.execution.engine=tez;
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
    GROUP BY t4;
```

В предыдущем примере операторы HiveQL выполняют следующие действия.

* `set hive.execution.engine=tez;`: задает механизм выполнения с использованием Tez. Использование Tez повышает производительность запросов. Дополнительные сведения о Tez см. в разделе [Использование Apache Tez для повышения производительности](#usetez).

    > [!NOTE]
    > Эта инструкция необходима только при использовании кластера HDInsight под управлением Windows. Tez является подсистемой выполнения по умолчанию для HDInsight под управлением Linux.

* `DROP TABLE`: если таблица уже существует, удалите ее.

* `CREATE EXTERNAL TABLE`: создает новую **внешнюю** таблицу в Hive. Внешние таблицы хранят только определения таблицы в Hive. Данные остаются в исходном расположении и формате.

* `ROW FORMAT`: инструкции по форматированию данных для Hive. В данном случае поля всех журналов разделены пробелом.

* `STORED AS TEXTFILE LOCATION`: указывает Hive расположение хранения данных (каталог `example/data`) и их формат (текст). Данные могут храниться в одном файле или быть распределенными по нескольким файлам в каталоге.

* `SELECT`: подсчитывает количество всех строк, в которых столбец **t4** содержит значение **[ERROR]**. Эта инструкция должна вернуть значение **3**, так как данное значение содержат три строки.

* `INPUT__FILE__NAME LIKE '%.log'`. Hive пытается применить схему ко всем файлам в каталоге. В этом случае каталог содержит файлы, которые не соответствуют схеме. Чтобы исключить лишние данные в результатах, эта инструкция указывает Hive возвращать данные только из файлов, заканчивающихся на .log.

> [!NOTE]
> Внешние таблицы следует использовать, если исходные данные должны обновляться с использованием внешних источников. Например, процессом автоматизированной передачи данных или другой операцией MapReduce.
>
> Удаление внешней таблицы **не** приводит к удалению данных; будет удалено только описание таблицы.

Для создания **внутренней** таблицы вместо внешней используйте следующий запрос HiveQL.

```hiveql
set hive.execution.engine=tez;
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Эти операторы выполняют следующие действия:

* `CREATE TABLE IF NOT EXISTS`: если таблица не существует, создайте ее. Так как ключевое слово **EXTERNAL** не используется, эта инструкция создает внутреннюю таблицу. Таблица хранится в хранилище данных Hive и полностью управляется Hive.

* `STORED AS ORC`: позволяет сохранить данные в формате ORC. Это высокооптимизированный и эффективный формат для хранения данных Hive.

* `INSERT OVERWRITE ... SELECT`: выбирает строки из таблицы **log4jLogs**, содержащей значение **[ERROR]**, а затем вставляет данные в таблицу **errorLogs**.

> [!NOTE]
> В отличие от внешних таблиц, удаление внутренней таблицы приводит к удалению базовых данных.

## <a name="improve-hive-query-performance"></a>Повышение производительности запросов Hive

### <a id="usetez"></a>Apache Tez

[Apache Tez](http://tez.apache.org) — это платформа, которая позволяет повысить производительность приложений, обрабатывающих большие объемы данных (включая Hive). По умолчанию Tez включена для кластеров HDInsight под управлением Linux.

> [!NOTE]
> В настоящее время Tez по умолчанию отключена для кластеров HDInsight под управлением Windows. Необходимо включить эту платформу. Чтобы воспользоваться преимуществами работы с Tez, необходимо установить для запроса Hive следующие значения переменных:
>
> `set hive.execution.engine=tez;`
>
> Tez является подсистемой по умолчанию для кластеров HDInsight под управлением Linux.

Раздел [Документация по работе Hive на Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) содержит дополнительные сведения о реализации этого решения и вариантах настроек.

Для помощи в отладке заданий, запущенных с помощью Tez, HDInsight предоставляет следующие веб-интерфейсы, позволяющие просмотреть сведения о заданиях Tez:

* [Использование представления Ambari Tez в HDInsight на платформе Linux](../hdinsight-debug-ambari-tez-view.md)

* [Использование пользовательского интерфейса Tez в HDInsight на платформе Windows](../hdinsight-debug-tez-ui.md)

### <a name="low-latency-analytical-processing-llap"></a>Аналитическая обработка с низкой задержкой (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (иногда называемая Live Long and Process) — это новая функция в Hive 2.0, которая разрешает кэширование запросов в памяти. LLAP создает запросы Hive гораздо быстрее — в [некоторых случаях в 26 раз быстрее, чем Hive версии 1.x](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight предоставляет LLAP в кластере интерактивных запросов. Дополнительные сведения см. в статье [Использование Interactive Hive с HDInsight (предварительная версия)](../interactive-query/apache-interactive-query-get-started.md).

## <a name="scheduling-hive-queries"></a>Планирование запросов Hive

Есть несколько служб, которые поддерживают запросы Hive в рамках рабочего процесса по расписанию или по требованию.

### <a name="azure-data-factory"></a>Фабрика данных Azure

Фабрика данных Azure позволяет использовать HDInsight как часть конвейера фабрики данных. Дополнительные сведения об использовании Hive из конвейера см. в документе [Преобразование данных с помощью действия Hadoop Hive в фабрике данных Azure](/data-factory/transform-data-using-hadoop-hive.md).

### <a name="hive-jobs-and-sql-server-integration-services"></a>Задания Pig и SQL Server Integration Services

С помощью служб SQL Server Integration Services (SSIS) можно выполнить задание Hive. Пакет дополнительных компонентов Azure для служб SSIS предоставляет следующие компоненты, которые работают с заданиями Hive в HDInsight.

* [Задача Hive в Azure HDInsight][hivetask]

* [Диспетчер подключений по подпискам Azure][connectionmanager]

Дополнительные сведения см. в документации по [пакету функций Azure][ssispack].

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie — это система рабочих процессов и координации, управляющая заданиями Hadoop. Дополнительные сведения см. в статье об [использовании Oozie с Hive для определения и запуска рабочего процесса](../hdinsight-use-oozie-linux-mac.md).

## <a id="nextsteps"></a>Дальнейшие действия

Теперь, когда вы знаете, что такое инфраструктура Hive и как ее использовать с Hadoop в HDInsight, воспользуйтесь следующими ссылками, чтобы изучить другие способы работы с Azure HDInsight.

* [Отправка данных в HDInsight][hdinsight-upload-data]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Использование заданий MapReduce с HDInsight][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
