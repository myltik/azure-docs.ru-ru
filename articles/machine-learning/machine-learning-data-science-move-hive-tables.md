<properties 
	pageTitle="Создание и загрузка данных в таблицы Hive из хранилища больших двоичных объектов | Microsoft Azure" 
	description="Создание таблиц Hive и загрузка в них данных больших двоичных объектов" 
	services="machine-learning,storage" 
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
	ms.date="05/29/2015" 
	ms.author="hangzh;bradsev" />

 
#Создание и загрузка данных в таблицы Hive из хранилища больших двоичных объектов Azure
 
В этом документе рассматриваются общие запросы Hive, которые создают таблицы Hive и загружают данные из хранилищ больших двоичных объектов Azure. Здесь также приведены некоторые указания по секционированию таблиц Hive и использованию формата Optimized Row Columnar (ORC) для улучшения производительности запросов.


Запросы Hive доступны для совместного использования в [репозитории GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql), где их можно загрузить.

Если вы создали виртуальную машину Azure в соответствии с указаниями в разделе [Настройка виртуальной машины Azure для обработки и анализа данных](machine-learning-data-science-setup-virtual-machine.md), этот файл сценария скачан в каталог *C:\Users<user name>\Documents\Data Science Scripts* в виртуальной машине. Чтобы запросы Hive были готовы к отправке, достаточно подключить свою схему данных и применить конфигурацию хранилища больших двоичных объектов Azure в соответствующих полях.

Предполагается, что формат данных для таблиц Hive — табличный формат **без сжатия** и что данные отправлены в контейнер по умолчанию (или дополнительный контейнер) учетной записи хранения, которую использует кластер Hadoop. Если вы хотите научиться использовать _Данные о поездках в такси по Нью-Йорку_, вам сначала необходимо скачать все 24 файла [данных о поездках по Нью-Йорку](http://www.andresmh.com/nyctaxitrips/) (это 12 файлов поездок и 12 файлов тарифов), **распаковать** CSV-файлы, затем передать их в контейнер по умолчанию (или соответствующий контейнер) учетной записи хранения Azure, которая использовалась в процедуре, описанной в разделе [Настройка кластеров Hadoop под управлением службы Azure HDInsight для обработки аналитических данных и расширенного анализа](machine-learning-data-science-customize-hadoop-cluster.md).

Запросы Hive можно отправлять из окна командной строки Hadoop на головном узле кластера Hadoop. Для этого войдите в головной узел кластера Hadoop, откройте окно командной строки Hadoop и отправьте оттуда запросы Hive. Соответствующие инструкции см. в статье [Отправка запросов Hive в кластеры Hadoop под управлением службы HDInsight при расширенной обработке аналитических данных](machine-learning-data-science-process-hive-tables.md).

Кроме того, можно использовать консоль запросов (редактор Hive). Для этого нужно ввести URL-адрес

имя кластера https://&#60;Hadoop>.azurehdinsight.net/Home/HiveEditor

. Обратите внимание, что будет предложено ввести учетные данные кластера Hadoop для входа. Кроме того, можно [отправить задания Hive с использованием PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).

## Предварительные требования
В этой статье предполагается, что вы:
 
* Создали учетную запись хранения Azure. Инструкции можно найти в статье [Создание учетной записи хранения в Azure](../hdinsight-get-started.md#storage). 
* Подготовили настраиваемый кластер Hadoop с помощью службы HDInsight. Инструкции см. в статье [Настройка кластеров Azure HDInsight Hadoop для расширенного процесса обработки аналитических данных](machine-learning-data-science-customize-hadoop-cluster.md).
* включили удаленный доступ к кластеру, вошли в систему и открыли окно командной строки Hadoop Инструкции можно найти в разделе [Доступ к головному узлу в кластере Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode). 


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

- **&#60;имя базы данных>** — имя базы данных, которую необходимо создать пользователям Если пользователям необходимо использовать базу данных по умолчанию, запрос *create database...* можно пропустить. 
- **&#60;имя таблицы>** — имя таблицы, которую необходимо создать пользователям в указанной базе данных. Если пользователям нужно использовать базу данных по умолчанию, для непосредственного обращения к таблице можно указать имя *&#60;имя таблицы>* без указания &#60;имя базы данных>.
- **&#60;разделитель полей>** — разделитель полей в файле данных, который необходимо загрузить в таблицу Hive. 
- &#60;разделитель строк> — разделитель строк в файле данных. 
- **&#60;место хранения>** — место хранения в Azure для хранения данных таблиц Hive. Если пользователи не указывают *LOCATION &#60;место хранения>*, то база данных и таблицы сохраняются в каталоге *hive/warehouse/* контейнера кластера Hive по умолчанию. Если необходимо указать место хранения, оно должно находиться в пределах контейнера по умолчанию для базы данных и таблиц. Это расположение необходимо указать относительно контейнера кластера по умолчанию в формате *'wasb:///&#60;каталог 1>/'* или *'wasb:///&#60;каталог 1>/&#60;каталог 2>/'* и т.д. После выполнения запроса в контейнере по умолчанию будут созданы соответствующие каталоги. 
- **TBLPROPERTIES("skip.header.line.count"="1")** — если файл данных содержит строку заголовка, пользователям необходимо добавить это свойство **в конец** запроса *create table*. В противном случае строка заголовка будет загружена в таблицу в качестве записи. Если в файле данных нет строки заголовка, в запросе эту конфигурацию можно пропустить. 

## <a name="load-data"></a>Загрузка данных в таблицы Hive
Ниже приведен запрос Hive, который загружает данные в таблицу Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **&#60;путь к данным большого двоичного объекта>** — если файл большего двоичного объекта, который необходимо загрузить в таблицу Hive, расположен в контейнере по умолчанию кластера под управлением службы HDInsight, *&#60;путь к данным большого двоичного объекта>* необходимо указать в формате *'wasb:///&#60;каталог в этом контейнере>/&#60;имя файла большого двоичного объекта>'*. В дополнительном контейнере кластера Hadoop под управлением службы HDInsight также может быть файл большого двоичного объекта. В этом случае *&#60;путь к данным большого двоичного объекта>* должен быть указан в формате *'wasb://&#60;имя контейнера>@&#60;имя учетной записи хранения>.blob.windows.core.net/&#60;имя файла большого двоичного объекта>'*.

	>[AZURE.NOTE]Данные большого двоичного объекта, которые необходимо отправить в таблицу Hive, должны находиться в контейнере по умолчанию или в дополнительном контейнере учетной записи хранения для кластера Hadoop. В противном случае — запрос *ЗАГРУЗКА ДАННЫХ* завершится ошибкой доступа к данным.


## <a name="partition-orc"></a>Дополнительные разделы: секционированные таблицы и хранение данных Hive в формате ORC

При большом объеме данных секционирование таблицы полезно для запросов, в рамках которых требуется сканировать только несколько секций таблицы. Например, целесообразно секционировать данные журнала веб-сайта по датам.

Помимо секционирования таблиц Hive данные Hive также удобно хранить в формате ORC. Дополнительную информацию об использовании формата ORC см. в статье [о том, как использование ORC-файлов улучшает производительность при чтении, записи и обработке данных Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles).

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

2. Создайте внутреннюю таблицу, используя ту же схему и тот же разделитель полей, что и во внешней таблице на шаге 1, и сохраните данные Hive в формате ORC.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Выберите данные во внешней таблице на шаге 1 и вставьте их в таблицу в формате ORC.

		INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

	[AZURE.NOTE]Если в таблице В ВИДЕ ТЕКСТОВОГО ФАЙЛА *&#60;database name>.&#60;external textfile table name>* есть секции, команда `SELECT * FROM <database name>.<external textfile table name>`, выполняемая на ШАГЕ 3, выберет переменную секции в качестве поля в возвращенном наборе данных. Ее вставка в *&#60;database name>.&#60;ORC table name>* приведет к ошибке, поскольку в схеме *&#60;database name>.&#60;ORC table name>* нет переменной секции в виде поля. В этом случае пользователям нужно указать поля, которые необходимо вставить в таблицу *&#60;database name>.&#60;ORC table name>*, следующим образом:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		   SELECT field1, field2, ..., fieldN
		   FROM <database name>.<external textfile table name> 
		   WHERE <partition variable>=<partition value>;

4. После вставки всех данных в таблицу *&#60;database name>.&#60;ORC table name>* можно безопасно удалить таблицу *&#60;external textfile table name>*, используя следующий запрос:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

После выполнения этой процедуры у вас должна быть готовая к использованию таблица с данными в формате ORC.

<!---HONumber=July15_HO4-->