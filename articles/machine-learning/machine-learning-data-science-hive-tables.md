<properties 
	pageTitle="Создание и загрузка данных в таблицы Hive из хранилища больших двоичных объектов | Microsoft Azure" 
	description="Создание таблиц Hive и загрузка в них данных больших двоичных объектов" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="hangzh;bradsev" />

 
#Создание и загрузка данных в таблицы Hive из хранилища больших двоичных объектов Azure
 
В этом документе рассматриваются общие запросы Hive, которые создают таблицы Hive и загружают данные из хранилищ больших двоичных объектов Azure. Эти запросы Hive доступны для совместного использования [Репозиторий GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql).


Если вы создали виртуальную машину Azure в соответствии с указаниями в разделе «Настройка виртуальной машины Azure для использования сервера IPython Notebook», этот файл сценария загружен в каталог `C:\Users<user name>\Documents\Data Science Scripts` на виртуальной машине. Чтобы подготовить запросы Hive к отправке, необходимо подключить собственную схему данных и применить конфигурацию хранилища больших двоичных объектов Azure в соответствующих полях этих запросов.

Предполагается, что формат данных для таблиц Hive — табличный формат **без сжатия** и что данные отправлены в контейнер по умолчанию или дополнительный контейнер учетной записи хранения, которую использует кластер Hadoop. Если пользователи хотят попрактиковаться на _данных о поездках в такси по Нью-Йорку_, им сначала необходимо [скачать все 24 файла](http://www.andresmh.com/nyctaxitrips/) (12 файлов поездок и 12 файлов тарифов), **распаковать** их в CSV-файлы, а затем отправить эти файлы в контейнер по умолчанию или дополнительный контейнер учетной записи хранения Azure, которая использовалась в процедуре настройки кластера, описанной в разделе [Настройка кластеров Hadoop под управлением службы Azure HDInsight](machine-learning-data-science-customize-hadoop-cluster.html).

Запросы Hive можно отправлять из командной строки Hadoop на головном узле кластера Hadoop. Для этого необходимо:

1. [Включить удаленный доступ к головному узлу кластера Hadoop и войти на головной узел](machine-learning-data-science-customize-hadoop-cluster.md).
2. [Отправить запросы Hive из командной строки Hadoop на головном узле](machine-learning-data-science-hive-queries.md).

Можно также использовать консоль запросов (редактор Hive), введя в браузере URL-адрес https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor (вам будет предложено ввести учетные данные для входа кластера Hadoop), или [отправить задания Hive с помощью PowerShell](hdinsight-submit-hadoop-jobs-programmatically.md).

- [Шаг 1. Создание базы данных и таблиц Hive](#create-tables).
- [Шаг 2. Загрузка данных в таблицы Hive](#load-data).
- [Дополнительные разделы: секционированные таблицы и хранение данных Hive в формате ORC](#partition-orc).

## <a name="create-tables"></a>Создание базы данных и таблиц Hive

    create database if not exists <database name>;
	CREATE EXTERNAL TABLE if not exists <database name>.<table name>
	(
		field1 string, 
		field2 int, 
		field3 float, 
		field4 double, 
		...,
		fieldN string
	) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
	STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Здесь приведены описания полей, необходимых пользователям для подключения, и других конфигураций.

- `<database name>` — имя базы данных, которую необходимо создать пользователям. Если пользователям нужно работать с базой данных по умолчанию, запрос `create database...` можно пропустить. 
- `<table name>` — имя таблицы, которую необходимо создать пользователям в указанной базе данных. Если пользователям нужно использовать базу данных по умолчанию, для непосредственного обращения к таблице можно указать имя `<table name>` без указания `<database name>.`.
- `<field separator>` — разделитель полей в файле данных, который необходимо загрузить в таблицу Hive. 
- `<line separator>` — разделитель строк в файле данных. 
- `<storage location>` — место хранения в Azure для хранения данных таблиц Hive. Если пользователи не зададут значение `LOCATION '<storage location>'`, база данных и таблицы по умолчанию будут сохранены в каталоге `hive/warehouse/` в контейнере по умолчанию кластера Hive. Если необходимо указать место хранения, оно должно находиться в пределах контейнера по умолчанию для базы данных и таблиц. В этом случае расположение указывается относительно контейнера кластера по умолчанию в формате `'wasb:///<directory 1>/'` или `'wasb:///<directory 1>/<directory 2>/'` и т.д. После выполнения запроса в контейнере по умолчанию будут созданы соответствующие каталоги. 
- `TBLPROPERTIES("skip.header.line.count"="1")` — если файл данных содержит строку заголовка, пользователям необходимо добавить это свойство в **конец** запроса `create table`. В противном случае строка заголовка будет загружена в таблицу в качестве записи. Если в файле данных нет строки заголовка, в запросе эту конфигурацию можно пропустить. 

## <a name="load-data"></a>Загрузка данных в таблицы Hive

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<path to blob data>` — если файл большого двоичного объекта, который необходимо загрузить в таблицу Hive, расположен в контейнере по умолчанию кластера Hadoop под управлением службы HDInsight, `<path to blob data>` необходимо указать в формате `'wasb:///<directory in this container>/<blob file name>'`. В дополнительном контейнере кластера HDInsight Hadoop также может быть файл большого двоичного объекта. В этом случае `<path to blob data>` необходимо указать в формате `'wasb://<container name>@<storage account name>.blob.windows.core.net/<blob file name>'`.

> [AZURE.NOTE]Данные большого двоичного объекта, которые необходимо отправить в таблицу Hive, должны находиться в контейнере по умолчанию или в дополнительном контейнере учетной записи хранения для кластера Hadoop. В противном случае запрос `LOAD DATA` завершится ошибкой из-за того, что он не сможет получить доступ к данным.


## <a name="partition-orc"></a>Дополнительные разделы: секционированные таблицы и хранение данных Hive в формате ORC

При большом объеме данных секционирование таблицы будет полезным для запросов, в рамках которых требуется сканировать только несколько секций таблицы. Например, целесообразно секционировать данные журнала веб-сайта по датам.

Кроме того, помимо секционирования таблицы, также полезно хранить данные Hive в формате ORC. ORC расшифровывается как Optimized Row Columnar (формат столбцов с оптимизацией по строкам). Дополнительную информацию см. в статье о том, _[как использование ORC-файлов улучшает производительность при чтении, записи и обработке данных Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles)._

### Секционированная таблица

Запросы для создания секционированной таблицы и загрузки в нее данных выглядят следующим образом:

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

При выполнении запроса на создание секционированной таблицы рекомендуется добавить условие секционирования в **начало** предложения `where`, чтобы значительно повысить эффективность поиска.

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Хранение данных Hive в формате ORC

Пользователи не могут напрямую загружать данные больших двоичных объектов в таблицы Hive в формате ORC. Ниже приведены действия, которые необходимо выполнить, чтобы загрузить данные из больших двоичных объектов Azure в таблицы Hive в формате ORC.

1. Создайте внешнюю таблицу **В ВИДЕ ТЕКСТОВОГО ФАЙЛА** и загрузите в нее данные из хранилища больших двоичных объектов.

		CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
		(
			field1 string,
			field2 int,
			...
			fieldN date
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
			lines terminated by '<line separator>' STORED AS TEXTFILE 
			LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

		LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. Создайте внутреннюю таблицу, используя ту же схему и тот же разделитель полей, что и для внешней таблицы на шаге 1. Затем сохраните данные Hive в формате ORC.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Выберите данные во внешней таблице на шаге 1 и вставьте их в таблицу в формате ORC.

		INSERT OVERWRITE TABLE <database name>.<ORC table name> SELECT * FROM <database name>.<external textfile table name>;

> [AZURE.NOTE]Если в таблице В ВИДЕ ТЕКСТОВОГО ФАЙЛА `<database name>.<external textfile table name>` есть секции, команда `SELECT * FROM <database name>.<external textfile table name>`, выполняемая на ШАГЕ 3, выберет переменную секции в качестве поля в возвращенном наборе данных. Попытка вставить эту переменную в таблицу `<database name>.<ORC table name>` завершится ошибкой, так как в таблице `<database name>.<ORC table name>` в качестве поля в схеме таблицы нет переменной секции. В этом случае пользователям нужно указать поля, которые необходимо вставить в таблицу `<database name>.<ORC table name>`, следующим образом:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. Безопасно удалить таблицу `<external textfile table name>` после вставки всех данных в таблицу `<database name>.<ORC table name>` можно при помощи следующего запроса:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Теперь у нас есть готовая к использованию таблица, которая содержит данные в формате ORC.
 

<!---HONumber=July15_HO4-->