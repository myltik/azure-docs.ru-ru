<properties
	pageTitle="Что такое Hive и как использовать HiveQL | Microsoft Azure"
	description="Дополнительные сведения об инфраструктуре Apache Hive и о ее использовании с Hadoop в HDInsight. Выберите способ выполнения задания Hive и воспользуйтесь HiveQL для анализа примера файла Apache log4j."
	keywords="hiveql, что такое hive"
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
	ms.date="09/19/2016"
	ms.author="larryfr"/>

# Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


В этом учебнике описывается, как использовать Apache Hive с Hadoop в HDInsight, а также как выбрать способ выполнения задания Hive. Вы также узнаете о HiveQL и о том, как выполнить анализ примера файла Apache log4j.

##<a id="why"></a>Что такое инфраструктура Hive и зачем ее использовать?
[Apache Hive](http://hive.apache.org/) — это система хранилища данных для Hadoop, которая позволяет обобщать и анализировать данные, а также обрабатывать запросы с использованием HiveQL (язык запросов, подобный SQL). Hive можно использовать для интерактивного исследования данных или создания многократно используемых заданий пакетного задания обработки.

Hive позволяет создавать структуру для преимущественно неструктурированных данных. После определения структуры вы можете использовать Hive для создания запроса этих данных без знания Java или MapReduce. **HiveQL** (язык запросов Hive) позволяет создавать запросы, используя операторы, подобные операторам T-SQL.

Hive понимает, как работать со структурированными и полуструктурированными данными, например текстовыми файлами, в которых поля разделяются специальными символами. Hive также поддерживает пользовательские **сериализаторы/десериализаторы (SerDe)** для сложных или беспорядочно структурированных данных. Дополнительные сведения см. в разделе [Использование пользовательских JSON SerDe с HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

## Определяемые пользователем функции (UDF)

Инфраструктура Hive также может быть расширена с помощью **определяемых пользователем функций (UDF)**. UDF позволяет реализовать функции или логику, сложно моделируемые в HiveQL. Примеры использования UDF с помощью Hive см. в следующих разделах:

* [Use a Java User Defined Function with Hive (Использование определяемых пользователем функций Java с помощью Hive)](hdinsight-hadoop-hive-java-udf.md)

* [Использование Python с Hive и Pig в HDInsight](hdinsight-python.md)

* [Использование C# с Hive и Pig в HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Добавление пользовательских UDF Hive в HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Пример UDF для Hive: преобразование форматов даты и времени в метки времени Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## Сравнение внутренних и внешних таблиц Hive

Есть несколько вещей, которые нужно знать о внутренней и внешней таблицах Hive.

- Команда **CREATE TABLE** создает внутреннюю таблицу. Файл данных должен находиться в контейнере по умолчанию.
- Команда **CREATE TABLE** перемещает файл данных в папку /hive/warehouse/<имя\_таблицы>.
- Команда **CREATE EXTERNAL TABLE** создает внешнюю таблицу. Файл данных может находиться за пределами контейнера по умолчанию.
- Команда **CREATE EXTERNAL TABLE** не перемещает файл данных.
- Команда **CREATE EXTERNAL TABLE** не разрешает наличие каких-либо папок в LOCATION. Именно по этой причине в учебнике создается копия файла sample.log.

Дополнительные сведения см. в разделе [HDInsight: введение во внутренние и внешние таблицы Hive][cindygross-hive-tables].


##<a id="data"></a>О демонстрационных данных — файле Apache log4j

В этом примере используется пример файла *log4j*, который хранится в контейнере хранилища BLOB-объектов (путь к файлу **/example/data/sample.log**). Каждый журнал в файле состоит из строки полей, содержащей поле `[LOG LEVEL]` для отображения типа и серьезности.

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

В предыдущем примере уровень журнала имеет значение ERROR ("ошибка").

> [AZURE.NOTE] Вы также можете создать файл log4j с помощью средства ведения журнала [Apache Log4j](http://en.wikipedia.org/wiki/Log4j), а затем отправить этот файл в контейнер BLOB-объектов. См. раздел [Отправка данных в HDInsight](hdinsight-upload-data.md) для получения более подробной информации. Дополнительные сведения о том, каким образом хранилище BLOB-объектов Azure используется с HDInsight, см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight](hdinsight-hadoop-use-blob-storage.md).

Демонстрационные данные хранятся в хранилище BLOB-объектов Azure, используемом HDInsight в качестве файловой системы по умолчанию. HDInsight может обращаться к файлам хранилища BLOB-объектов с помощью префикса **wasb**. Поэтому для доступа к файлу sample.log необходимо использовать следующий синтаксис:

	wasbs:///example/data/sample.log

Поскольку хранилище BLOB-объектов Azure является хранилищем по умолчанию для HDInsight, обратиться к файлу также можно из HiveQL, используя путь **/example/data/sample.log**

> [AZURE.NOTE] Синтаксис **wasbs:///** используется для обращения к файлам, которые хранятся в контейнере хранилища по умолчанию для вашего кластера HDInsight. Если вы указали дополнительные учетные записи хранения при подготовке кластера и хотите получить доступ к файлам, хранящимся в них, выберите имя контейнера и адрес учетной записи хранения, например: **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="job"></a>Пример задания: создание столбцов из данных с разделителями

Следующие операторы HiveQL позволяют создать столбцы из данных с разделителями, хранящихся в каталоге **wasbs:///example/data**.

    set hive.execution.engine=tez;
	DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

В предыдущем примере операторы HiveQL выполняют следующие действия.

* __set hive.execution.engine=tez;__ — задает использование механизма выполнения Tez. Использование Tez вместо MapReduce позволяет повысить производительность запросов. Дополнительные сведения о Tez см. в разделе [Использование Apache Tez для повышения производительности](#usetez).

    > [AZURE.NOTE] Эта инструкция необходима только при использовании кластера под управлением Windows. Для HDInsight под управлением Linux Tez является механизмом выполнения по умолчанию.

* **DROP TABLE**: удаление таблицы и файла данных, если таблица уже существует.
* **CREATE EXTERNAL TABLE**: создание новой **внешней** таблицы в Hive. Внешние таблицы хранят только описание таблицы в Hive; данные при этом остаются в исходном расположении и исходном формате.
* **ROW FORMAT**: инструкции по форматированию данных для Hive. В данном случае поля всех журналов разделены пробелом.
* **STORED AS TEXTFILE LOCATION**: информация для Hive о расположении хранения данных (каталог example/data) и о их формате (текстовый). Данные могут храниться в одном файле или быть распределенными по нескольким файлам в каталоге.
* **SELECT**: подсчет количества всех строк, в которых столбец **t4** содержит значение **[ERROR]**. Эта команда должна вернуть значение **3**, так как данное значение содержат три строки.
* **INPUT\_\_FILE\_\_NAME LIKE '%.log'** — указывает Hive, что возвратить нужно только данные из файлов с расширением LOG. Это ограничивает поиск файлом sample.log, в котором содержатся данные, и предотвращает возврат данных из других примеров файлов данных, не соответствующих определенной нами схеме.

> [AZURE.NOTE] Внешние таблицы следует использовать, если ожидается, что исходные данные будут обновляться внешним источником (таким как автоматизированный процесс передачи данных или другой операцией MapReduce), и при этом нужно, чтобы запросы Hive использовали самые последние данные.
>
> Удаление внешней таблицы **не** приводит к удалению данных; будет удалено только описание таблицы.

После создания внешней таблицы, используются следующие операторы для создания **внутренней** таблицы.

    set hive.execution.engine=tez;
	CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
	STORED AS ORC;
	INSERT OVERWRITE TABLE errorLogs
	SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Эти операторы выполняют следующие действия.

* **CREATE TABLE IF NOT EXISTS**: создание таблицы, если она до этого не существовала. Так как ключевое слово **EXTERNAL** не использовалось, такая таблица будет внутренней, то есть хранящейся в хранилище данных Hive и полностью управляемой Hive.
* **STORED AS ORC**: хранение данных в формате ORC (Optimized Row Columnar). Это высокооптимизированный и эффективный формат для хранения данных Hive.
* **INSERT OVERWRITE ... SELECT**: выбор строк из таблицы **log4jLogs**, содержащей значение **[ERROR]**, и ввод данных в таблицу **errorLogs**.

> [AZURE.NOTE] В отличие от внешних таблиц, удаление внутренней таблицы приводит к удалению базовых данных.

##<a id="usetez"></a>Использование Apache Tez для повышения производительности

[Apache Tez](http://tez.apache.org) — это платформа, которая позволяет повысить производительность приложений, обрабатывающих большие объемы данных (включая Hive). В последней версии HDInsight выполнение заданий на Tez поддерживается Hive. По умолчанию Tez включена для кластеров HDInsight под управлением Linux.

> [AZURE.NOTE] В настоящее время Tez по умолчанию отключена для кластеров HDInsight под управлением Windows. Необходимо включить эту платформу. Чтобы воспользоваться преимуществами работы с Tez, необходимо установить для запроса Hive следующие значения переменных:
>
> ```set hive.execution.engine=tez;```
>
>Эти характеристики можно настроить для определенных запросов, поместив их в начале кода вашего запроса. Также можно задействовать эти характеристики по умолчанию для кластера, установив значения конфигурации при создании кластера. За более подробной информацией обращайтесь к разделу [Распределение кластеров HDInsight](hdinsight-provision-clusters.md).

Раздел [Документация по работе Hive на Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) содержит большое количество полезной информации по реализации этого решения и вариантам настроек.

Для помощи в отладке заданий, запущенных с помощью Tez, HDInsight предоставляет следующие веб-интерфейсы, позволяющие просмотреть сведения о заданиях Tez:

* [Использование пользовательского интерфейса Tez в HDInsight на платформе Windows](hdinsight-debug-tez-ui.md)

* [Использование представления Ambari Tez в HDInsight на платформе Linux](hdinsight-debug-ambari-tez-view.md)

##<a id="run"></a>Выбор способа выполнения задания HiveQL

HDInsight может выполнять задания HiveQL, используя различные методы. Используйте следующую таблицу, чтобы решить, какой метод подходит вам, а затем перейдите по ссылке к пошаговому руководству.

| **Используйте**, если требуется... | ...**интерактивная** оболочка | ...**пакетная** обработка | ... с **кластерной операционной системой** | ...из этого **кластера операционной системы** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [Представление Hive](hdinsight-hadoop-use-hive-ambari-view.md) | ✔ | ✔ | Linux | Для приложений на основе браузера |
| [Команда Beeline (из сеанса SSH)](hdinsight-hadoop-use-hive-beeline.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X или Windows |
| [Команда Hive (из сеанса SSH)](hdinsight-hadoop-use-hive-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X или Windows |
| [Curl](hdinsight-hadoop-use-hive-curl.md) | &nbsp; | ✔ | Linux или Windows | Linux, Unix, Mac OS X или Windows |
| [Консоль запросов](hdinsight-hadoop-use-hive-query-console.md) | &nbsp; | ✔ | Windows | Для приложений на основе браузера |
| [Средства HDInsight для Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) | &nbsp; | ✔ | Linux или Windows | Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) | &nbsp; | ✔ | Linux или Windows | Windows |
| [Удаленный рабочий стол](hdinsight-hadoop-use-hive-remote-desktop.md) | ✔ | ✔ | Windows | Windows |

## Выполнение заданий Hive в Azure HDInsight с помощью локальных служб интеграции SQL Server Integration Services

С помощью служб SQL Server Integration Services (SSIS) также можно выполнить задание Hive. Пакет дополнительных компонентов Azure для служб SSIS предоставляет следующие компоненты, которые работают с заданиями Hive в HDInsight.


- [Задача Hive в Azure HDInsight][hivetask]
- [Диспетчер подключений по подпискам Azure][connectionmanager]


Узнать больше о пакете дополнительных компонентов Azure для служб SSIS можно [здесь][ssispack].


##<a id="nextsteps"></a>Дальнейшие действия

Теперь, когда вы знаете, что такое инфраструктура Hive и как ее использовать с Hadoop в HDInsight, воспользуйтесь следующими ссылками, чтобы изучить другие способы работы с Azure HDInsight.


- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование Pig с HDInsight][hdinsight-use-pig]
- [Использование Sqoop с HDInsight](hdinsight-use-sqoop.md)
- [Использование Oozie с HDInsight](hdinsight-use-oozie.md)
- [Использование заданий MapReduce с HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

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

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

<!---HONumber=AcomDC_0921_2016-->