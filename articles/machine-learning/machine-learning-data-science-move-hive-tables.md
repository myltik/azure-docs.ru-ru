<properties 
	pageTitle="Создание и загрузка данных в таблицы Hive из хранилища больших двоичных объектов Azure | Azure" 
	description="Создание таблиц Hive и загрузка в них данных больших двоичных объектов" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="hangzh;bradsev" />

 
# Создание и загрузка данных в таблицы Hive из хранилища больших двоичных объектов Azure
 
В этом документе рассматриваются общие запросы Hive, которые создают таблицы Hive и загружают данные из хранилищ больших двоичных объектов Azure. Здесь также приведены некоторые указания по секционированию таблиц Hive и использованию формата Optimized Row Columnar (ORC) для улучшения производительности запросов.

Запросы Hive доступны для совместного использования в [репозитории GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql), где их можно скачать.

Если вы создали виртуальную машину Azure в соответствии с указаниями в разделе [Настройка виртуальной машины Azure для обработки и анализа данных](machine-learning-data-science-setup-virtual-machine.md), этот файл сценария скачан в каталог *C:\Users<user name>\Documents\Data Science Scripts* в виртуальной машине. Чтобы запросы Hive были готовы к отправке, достаточно подключить свою схему данных и применить конфигурацию хранилища больших двоичных объектов Azure в соответствующих полях.

Предполагается, что формат данных для таблиц Hive - табличный формат **без сжатия** и что данные отправлены в контейнер по умолчанию (или дополнительный контейнер) учетной записи хранения, которую использует кластер Hadoop. Если пользователям необходимо научиться использовать "Данные о поездках в такси по Нью-Йорку", им сначала необходимо [скачать все 24 файла](http://www.andresmh.com/nyctaxitrips/) (это 12 файлов поездок и 12 файлов тарифов), **распаковать** CSV-файлы, затем передать их в контейнер по умолчанию (или соответствующий контейнер) учетной записи хранения Azure, которая использовалась в процедуре, описанной в разделе [Настройка кластеров Hadoop под управлением службы Azure HDInsight для обработки и анализа данных](machine-learning-data-science-customize-hadoop-cluster.md). 

Запросы Hive можно отправлять из окна командной строки Hadoop на головном узле кластера Hadoop. Для этого войдите в головной узел кластера Hadoop, откройте окно командной строки Hadoop и отправьте оттуда запросы Hive. Указания о том, как это сделать, см. в статье [Отправка запросов Hive в кластеры Hadoop под управлением службы HDInsight при обработке и анализе данных в облаке](machine-learning-data-science-process-hive-tables.md).

Кроме того, можно использовать консоль запросов (редактор Hive). Для этого нужно ввести URL-адрес 

https://&#60;Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor

в веб-браузере. Обратите внимание, что для входа потребуется ввести учетные данные кластера Hadoop. Или же вы можете [отправить задания Hive с использованием PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell). 

## Предварительные требования
В этой статье предполагается, что вы:
 
* создали учетную запись хранения Azure (указания см. в разделе [Создание учетной записи хранения Azure](../hdinsight-get-started.md#storage)); 
* подготовили настраиваемый кластер Hadoop в службе HDInsight  Если вам нужны инструкции, см. статью [Настройка кластеров Azure HDInsight Hadoop для обработки данных](machine-learning-data-science-customize-hadoop-cluster.md).
* включили удаленный доступ к кластеру, вошли в систему и открыли окно командной строки Hadoop (указания см. в разделе [Доступ к головному узлу в кластере Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess)). 


## <a name="create-tables"></a>Создание базы данных и таблиц Hive
Ниже приведен запрос Hive, который создает таблицу Hive.

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

- `<database name>` - имя базы данных, которую необходимо создать пользователям. Если пользователям необходимо использовать базу данных по умолчанию, запрос `create database...` можно пропустить. 
- `<table name>` - имя таблицы, которую необходимо создать пользователям в указанной базе данных. Если пользователям нужно использовать базу данных по умолчанию, для непосредственного обращения к таблице можно указать имя `<table name>` без `<database name>`.
- `<field separator>` - разделитель полей в файле данных, который необходимо загрузить в таблицу Hive. 
- `<line separator>` - разделитель строк в файле данных. 
- `<storage location>` - место хранения в Azure для хранения данных таблиц Hive. Если пользователи не зададут значение `LOCATION '<storage location>'`, база данных и таблицы по умолчанию будут сохранены в каталоге `hive/warehouse/` в контейнере по умолчанию кластера Hive. Если необходимо указать место хранения, оно должно находиться в пределах контейнера по умолчанию для базы данных и таблиц. Это расположение необходимо указать с контейнером кластера по умолчанию, в пределах которого оно будет находиться, в формате `wasb:///<каталог_1>/` или `wasb:///<каталог_1>/<каталог_2>/` и т. д. После выполнения запроса в контейнере по умолчанию будут созданы соответствующие каталоги. 
- `TBLPROPERTIES("skip.header.line.count"="1")` - если файл данных содержит строку заголовка, пользователям необходимо добавить это свойство в **конец** запроса `create table`. В противном случае строка заголовка будет загружена в таблицу в качестве записи. Если в файле данных нет строки заголовка, в запросе эту конфигурацию можно пропустить. 

## <a name="load-data"></a>Загрузка данных в таблицы Hive
Ниже приведен запрос Hive, который загружает данные в таблицу Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<path to blob data>` - Если файл большего двоичного объекта, который необходимо загрузить в таблицу Hive, расположен в контейнере по умолчанию кластера под управлением службы HDInsight, `<path to blob data>` необходимо указать в формате `wasb:///<каталог_в_этом_контейнере>/<имя_файла_большего_двоичного_объекта>`. В дополнительном контейнере кластера Hadoop под управлением службы HDInsight также может быть файл большого двоичного объекта. В этом случае `<path to blob data>` необходимо указать в формате `wasb://<имя_контейнера>@<имя_учетной_записи_хранения>.blob.windows.core.net/<имя_файла_большого_двоичного_объекта>`.

>[AZURE.NOTE] Данные большого двоичного объекта, которые необходимо отправить в таблицу Hive, должны находиться в контейнере по умолчанию или в дополнительном контейнере учетной записи хранения для кластера Hadoop. В противном случае запрос `LOAD DATa` завершится ошибкой из-за того, что он не сможет получить доступ к данным. 


## <a name="partition-orc"></a>Дополнительные разделы: секционированные таблицы и хранение данных Hive в формате ORC

При большом объеме данных секционирование таблицы полезно для запросов, в рамках которых требуется сканировать только несколько секций таблицы. Например, целесообразно секционировать данные журнала веб-сайта по датам. 

Помимо секционирования таблиц Hive данные Hive также удобно хранить в формате ORC. Дополнительную информацию об использовании формата ORC см. в статье о том, как [использование ORC-файлов улучшает производительность при чтении, записи и обработке данных Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles).

### Секционированная таблица
Ниже приведен запрос Hive, который создает секционированную таблицу и загружает в нее данные.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

При запрашивании секционированных таблиц рекомендуется добавить условие секционирования в **начало** предложения `where`, чтобы значительно повысить эффективность поиска. 

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Хранение данных Hive в формате ORC

Пользователи не могут загружать данные непосредственно из хранилища больших двоичных объектов в таблицы Hive в формате ORC. Ниже приведены действия, которые необходимо выполнить, чтобы загрузить данные из больших двоичных объектов Azure в таблицы Hive в формате ORC. 

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

2. Создайте внутреннюю таблицу, используя ту же схему и тот же разделитель полей, что и во внешней таблице на шаге 1, и сохраните данные Hive в формате ORC.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Выберите данные во внешней таблице на шаге 1 и вставьте их в таблицу в формате ORC.

		INSERT OVERWRITE TABLE <database name>.<ORC table name> SELECT * FROM <database name>.<external textfile table name>;

[AZURE.NOTE] Если в таблице В ВИДЕ ТЕКСТОВОГО ФАЙЛА `<database name>.<external textfile table name>` есть секции, команда `SELECT * FROM <database name>.<external textfile table name>`, выполняемая на ШАГЕ 3, выберет переменную секции в качестве поля в возвращенном наборе данных. Попытка вставить эту переменную в таблицу `<database name>.<ORC table name>` завершится ошибкой, так как в таблице `<database name>.<ORC table name>` в качестве поля в схеме таблицы нет переменной секции. В этом случае пользователям нужно указать поля, которые необходимо вставить в таблицу `<database name>.<ORC table name>`, следующим образом:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. Если безопасно, удалите `<external textfile table name>`, используя следующий запрос после того, как все данные были вставлены в таблицу `<database name>.<ORC table name>`:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

После выполнения этой процедуры у вас должна быть готовая к использованию таблица с данными в формате ORC. 

<!--HONumber=49--> 