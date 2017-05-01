---
title: "Что такое Hive и как использовать HiveQL | Документация Майкрософт"
description: "Дополнительные сведения об инфраструктуре Apache Hive и о ее использовании с Hadoop в HDInsight. Выберите способ выполнения задания Hive и воспользуйтесь HiveQL для анализа примера файла Apache log4j."
keywords: "hiveql, что такое hive"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: db324cc8269a1f23613e5193f5a1dec1d26a62b1
ms.lasthandoff: 04/17/2017


---
# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight"></a>Использование Hive и HiveQL с Hadoop в HDInsight

Узнайте, как использовать Hive с HDInsight. Воспользуйтесь приведенной таблицей, чтобы узнать, как можно использовать Hive с HDInsight.

| **Используйте** , если требуется... | ... **интерактивная** оболочка | ...**пакетная** обработка | ...with этим **кластером операционной системы** | ...из этого **кластера операционной системы** |
|:--- |:---:|:---:|:--- |:--- |
| [Представление Hive](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Для приложений на основе браузера |
| [Команда Beeline](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X или Windows |
| [REST API](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux или Windows |Linux, Unix, Mac OS X или Windows |
| [Консоль запросов](hdinsight-hadoop-use-hive-query-console.md) (HDInsight 3.2 и 3.3) |&nbsp; |✔ |Windows |Для приложений на основе браузера |
| [Средства HDInsight для Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux или Windows |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux или Windows |Windows |
| [Удаленный рабочий стол](hdinsight-hadoop-use-hive-remote-desktop.md) (HDInsight 3.2 и 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Чтобы узнать больше, ознакомьтесь с [нерекомендуемыми версиями HDInsight 3.2 и 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a id="why"></a>Что такое Hive

[Apache Hive](http://hive.apache.org/) — это система хранения данных для Hadoop, которая позволяет обобщать, запрашивать и анализировать данные. Hive позволяет создавать структуру для преимущественно неструктурированных данных. После определения структуры вы можете использовать Hive для создания запроса этих данных без знания Java или MapReduce.

Запросы Hive создаются на языке запросов HiveQL, который похож на SQL. Hive можно использовать для интерактивного исследования данных или создания многократно используемых заданий пакетного задания обработки.

Hive понимает, как работать со структурированными и полуструктурированными данными, например текстовыми файлами, в которых поля разделяются специальными символами. Hive также поддерживает пользовательские **сериализаторы/десериализаторы (SerDe)** для сложных или беспорядочно структурированных данных. Дополнительные сведения см. в документе [How to use a custom JSON SerDe with HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx) (Как использовать настраиваемую сериализацию-десериализациюJSON с HDInsight).

## <a name="user-defined-functions-udf"></a>Определяемые пользователем функции (UDF)

Инфраструктура Hive также может быть расширена с помощью **определяемых пользователем функций (UDF)**. UDF позволяет реализовать функции или логику, сложно моделируемые в HiveQL. Примеры использования определяемых пользователем функций с Hive приведены в следующих документах:

* [Работа с определяемыми пользователем функциями Java с использованием Hive в HDInsight](hdinsight-hadoop-hive-java-udf.md)

* [Использование Python с Hive и Pig в HDInsight](hdinsight-python.md)

* [Использование C# с Hive и Pig в HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Добавление пользовательских UDF Hive в HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Пример UDF для Hive: преобразование форматов даты и времени в метки времени Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>Сравнение внутренних и внешних таблиц Hive

Есть несколько вещей, которые нужно знать о внутренней и внешней таблицах Hive.

* Команда **CREATE TABLE** создает внутреннюю таблицу. Файл данных должен находиться в контейнере по умолчанию.

* Команда **CREATE TABLE** перемещает файл данных в каталог `/hive/warehouse/<TableName>` в хранилище по умолчанию для кластера.

* Команда **CREATE EXTERNAL TABLE** создает внешнюю таблицу. Файл данных может находиться за пределами контейнера по умолчанию.

* Команда **CREATE EXTERNAL TABLE** не перемещает файл данных.

Дополнительные сведения см. в записи блога [HDInsight: Hive Internal and External Tables Intro][cindygross-hive-tables] (HDInsight: введение во внутренние и внешние таблицы Hive).

## <a id="data"></a>Демонстрационные данные

HDInsight предоставляет различные наборы демонстрационных данных, которые хранятся в каталогах `/example/data` и `/HdiSamples`. Эти каталоги находятся в хранилище по умолчанию для кластера. В этом документе используется файл `/example/data/sample.log`. Это файл *log4j*. Каждый журнал в файле состоит из строки полей, содержащей поле `[LOG LEVEL]` для отображения типа и серьезности.

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

В предыдущем примере уровень журнала имеет значение ERROR ("ошибка").

> [!NOTE]
> Вы также можете создать файл log4j с помощью средства ведения журнала [Apache Log4j](http://en.wikipedia.org/wiki/Log4j), а затем отправить этот файл в контейнер больших двоичных объектов. Дополнительные сведения см. в статье [Отправка данных для заданий Hadoop в HDInsight](hdinsight-upload-data.md). Дополнительные сведения о том, каким образом хранилище BLOB-объектов Azure используется с HDInsight, см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Пример запроса Hive

Приведенная ниже инструкция HiveQL проецирует столбцы проекта в файл `/example/data/sample.log`.

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

В предыдущем примере операторы HiveQL выполняют следующие действия.

* **set hive.execution.engine=tez;** — задает использование механизма выполнения Tez. Использование Tez вместо MapReduce позволяет повысить производительность запросов. Дополнительные сведения о Tez см. в разделе [Использование Apache Tez для повышения производительности](#usetez).

    > [!NOTE]
    > Эта инструкция необходима только при использовании кластера HDInsight под управлением Windows. Tez является подсистемой выполнения по умолчанию для HDInsight под управлением Linux.

* **DROP TABLE**: если таблица уже существует, она будет удалена.

* **CREATE EXTERNAL TABLE**: создание **внешней** таблицы в Hive. Внешние таблицы хранят только определения таблицы в Hive. Данные остаются в исходном расположении и формате.

* **ROW FORMAT**: инструкции по форматированию данных для Hive. В данном случае поля всех журналов разделены пробелом.

* **STORED AS TEXTFILE LOCATION**: указывает Hive расположение хранения данных (каталог `example/data`) и их формат (текст). Данные могут храниться в одном файле или быть распределенными по нескольким файлам в каталоге.

* **SELECT**: подсчет количества всех строк, в которых столбец **t4** содержит значение **[ERROR]**. Эта инструкция должна вернуть значение **3**, так как данное значение содержат три строки.

* **INPUT__FILE__NAME LIKE '%.log'** — указывает Hive, что вернуть нужно только данные из файлов с расширением LOG. Эта инструкция ограничивает поиск файлом `sample.log`, который содержит данные.

> [!NOTE]
> Внешние таблицы следует использовать, если исходные данные должны обновляться с использованием внешних источников. Например, процессом автоматизированной передачи данных или другой операцией MapReduce.
>
> Удаление внешней таблицы **не** приводит к удалению данных; будет удалено только описание таблицы.

Для создания **внутренней** таблицы вместо внешней используйте следующий запрос HiveQL.

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Эти операторы выполняют следующие действия:

* **CREATE TABLE IF NOT EXISTS**: создание таблицы, если она не существует. Так как ключевое слово **EXTERNAL** не использовалось, эта инструкция создает внутреннюю таблицу, размещенную в хранилище данных Hive и полностью управляемую Hive.

* **STORED AS ORC**: хранение данных в формате ORC (Optimized Row Columnar). Это высокооптимизированный и эффективный формат для хранения данных Hive.

* **INSERT OVERWRITE ... SELECT**: выбор строк из таблицы **log4jLogs**, содержащей значение **[ERROR]**, и ввод данных в таблицу **errorLogs**.

> [!NOTE]
> В отличие от внешних таблиц, удаление внутренней таблицы приводит к удалению базовых данных.

## <a id="usetez"></a>Использование Apache Tez для повышения производительности

[Apache Tez](http://tez.apache.org) — это платформа, которая позволяет повысить производительность приложений, обрабатывающих большие объемы данных (включая Hive). В последней версии HDInsight выполнение заданий на Tez поддерживается Hive. По умолчанию Tez включена для кластеров HDInsight под управлением Linux.

> [!NOTE]
> В настоящее время Tez по умолчанию отключена для кластеров HDInsight под управлением Windows. Необходимо включить эту платформу. Чтобы воспользоваться преимуществами работы с Tez, необходимо установить для запроса Hive следующие значения переменных:
>
> `set hive.execution.engine=tez;`
>
> Tez является подсистемой по умолчанию для кластеров HDInsight под управлением Linux.

Раздел [Документация по работе Hive на Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) содержит дополнительные сведения о реализации этого решения и вариантах настроек.

Для помощи в отладке заданий, запущенных с помощью Tez, HDInsight предоставляет следующие веб-интерфейсы, позволяющие просмотреть сведения о заданиях Tez:

* [Использование представления Ambari Tez в HDInsight на платформе Linux](hdinsight-debug-ambari-tez-view.md)

* [Использование пользовательского интерфейса Tez в HDInsight на платформе Windows](hdinsight-debug-tez-ui.md)

## <a id="run"></a>Способ выполнения задания HiveQL

HDInsight может выполнять задания HiveQL, используя различные методы. Используйте следующую таблицу, чтобы решить, какой метод подходит вам, а затем перейдите по ссылке к пошаговому руководству.

| **Используйте** , если требуется... | ... **интерактивная** оболочка | ...**пакетная** обработка | ...with этим **кластером операционной системы** | ...из этого **кластера операционной системы** |
|:--- |:---:|:---:|:--- |:--- |
| [Представление Hive](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Для приложений на основе браузера |
| [Команда Beeline](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X или Windows |
| [REST API](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux или Windows |Linux, Unix, Mac OS X или Windows |
| [Консоль запросов](hdinsight-hadoop-use-hive-query-console.md) (HDInsight 3.2 и 3.3) |&nbsp; |✔ |Windows |Для приложений на основе браузера |
| [Средства HDInsight для Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux или Windows |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux или Windows |Windows |
| [Удаленный рабочий стол](hdinsight-hadoop-use-hive-remote-desktop.md) (HDInsight 3.2 и 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Чтобы узнать больше, ознакомьтесь с [нерекомендуемыми версиями HDInsight 3.2 и 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="hive-jobs-and-sql-server-integration-services"></a>Задания Pig и SQL Server Integration Services

С помощью служб SQL Server Integration Services (SSIS) также можно выполнить задание Hive. Пакет дополнительных компонентов Azure для служб SSIS предоставляет следующие компоненты, которые работают с заданиями Hive в HDInsight.

* [Задача Hive в Azure HDInsight][hivetask]

* [Диспетчер подключений по подпискам Azure][connectionmanager]

Узнать больше о пакете дополнительных компонентов Azure для служб SSIS можно [здесь][ssispack].

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
[hdinsight-upload-data]: hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

