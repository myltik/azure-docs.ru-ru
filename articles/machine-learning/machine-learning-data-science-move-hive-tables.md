<properties 
	pageTitle="Создание и загрузка данных в таблицы Hive из хранилища больших двоичных объектов | Microsoft Azure" 
	description="Создание таблиц Hive и загрузка в них данных больших двоичных объектов" 
	services="machine-learning,storage" 
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
	ms.date="10/12/2015" 
	ms.author="hangzh;bradsev" />

 
#Создание и загрузка данных в таблицы Hive из хранилища больших двоичных объектов Azure

Это **меню** содержит ссылки на разделы, описывающие прием данных в целевых средах, где они могут храниться и обрабатываться процессом аналитики Cortana (CAP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

 
## Введение
В **этом документе** рассматриваются общие запросы Hive, которые создают таблицы Hive и загружают данные из хранилищ больших двоичных объектов Azure. Здесь также приведены некоторые указания по секционированию таблиц Hive и использованию формата Optimized Row Columnar (ORC) для улучшения производительности запросов.

## Предварительные требования
В этой статье предполагается, что вы:
 
* Создали учетную запись хранения Azure. Инструкции можно найти в статье [Создание учетной записи хранения в Azure](../hdinsight-get-started.md#storage). 
* Подготовили настраиваемый кластер Hadoop с помощью службы HDInsight. Инструкции см. в статье [Настройка кластеров Azure HDInsight Hadoop для расширенного процесса обработки аналитических данных](machine-learning-data-science-customize-hadoop-cluster.md).
* включили удаленный доступ к кластеру, вошли в систему и открыли окно командной строки Hadoop Инструкции можно найти в разделе [Доступ к головному узлу в кластере Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode). 

## Отправка данных в хранилище больших двоичных объектов Azure
Если вы создали виртуальную машину Azure в соответствии с инструкциями в разделе [Настройка виртуальной машины Azure для расширенной аналитики](machine-learning-data-science-setup-virtual-machine.md), этот файл сценария должен быть загружен в каталог *C:\\Users<имя пользователя>\\Documents\\Data Science Scripts* на виртуальной машине. Чтобы запросы Hive были готовы к отправке, достаточно подключить свою схему данных и применить конфигурацию хранилища больших двоичных объектов Azure в соответствующих полях.

Предполагается, что формат данных для таблиц Hive — табличный формат **без сжатия** и что данные отправлены в контейнер по умолчанию (или дополнительный контейнер) учетной записи хранения, которую использует кластер Hadoop.

Если вы хотите научиться использовать _данные о поездках в такси по Нью-Йорку_, сначала необходимо скачать все 24 файла <a href="http://www.andresmh.com/nyctaxitrips/" target="_blank">данных о поездках по Нью-Йорку</a> (12 файлов поездок и 12 файлов тарифов), **распаковать** все CSV-файлы, а затем передать их в контейнер по умолчанию (или соответствующий контейнер) в учетной записи хранения Azure, которая создана с помощью процедуры, описанной в разделе [Настройка кластеров Azure HDInsight Hadoop для обработки аналитических данных и технологии расширенного анализа](machine-learning-data-science-customize-hadoop-cluster.md). Процесс отправки CSV-файла в контейнер по умолчанию в учетной записи хранения описан на этой [странице](machine-learning-data-science-process-hive-walkthrough/#upload).

## <a name="submit"></a>Отправка запросов Hive
Инструменты для отправки запросов Hive:

* командная строка Hadoop на головном узле кластера;
* IPython Notebook;
* редактор Hive;
* сценарии Azure PowerShell.

Запросы Hive похожи на SQL. Пользователям, знакомым с SQL, может оказаться полезной <a href="http://hortonworks.com/wp-content/uploads/downloads/2013/08/Hortonworks.CheatSheet.SQLtoHive.pdf" target="_blank">Памятка по сравнению SQL и Hive</a>.

Отправляя запрос Hive, вы можете указать также место назначения, в которое будут отправлены результаты обработки запроса. Местом назначения может быть экран, локальный файл в головном узле или большой двоичный объект Azure.

### Использование консоли командной строки Hadoop в головном узле кластера Hadoop

Если запрос сложный, отправка запросов Hive прямо из головного узла кластера Hadoop обычно приводит к результатам быстрее, чем отправка с помощью редактора Hive или скриптов Azure PowerShell.

Войдите в головной узел кластера Hadoop, откройте командную строку Hadoop на рабочем столе головного узла и введите команду

    cd %hive_home%\bin

Пользователи могут отправить запросы Hive с помощью консоли командной строки Hadoop тремя способами:

* прямо из командной строки Hadoop;
* с помощью HQL-файлов;
* из командной консоли Hive.

#### Отправка запросов Hive прямо из командной строки Hadoop

Чтобы отправлять простые запросы Hive прямо из командной строки Hadoop, пользователи могут выполнять команды наподобие

	hive -e "<your hive query>;

. Ниже приведен пример, в котором красной рамкой обведена команда, которая отправляет запрос Hive, а зеленой обведены выходные данные обработки этого запроса.

![Создание рабочей области](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png)

#### Отправка запросов Hive в HQL-файлах

Когда запрос Hive сложный и состоит из нескольких строк, редактировать его в командной строке Hadoop или в командной консоли Hive непрактично. Вместо этого лучше с помощью текстового редактора, находящегося в головном узле кластера Hadoop, сохранить запросы Hive в HQL-файле в локальном каталоге головного узла. Затем вы можете отправить запрос Hive в HQL-файле, добавив в команду `hive` аргумент `-f`, как показано ниже.

	`hive -f "<path to the .hql file>"`


#### Отменить вывод состояния хода выполнения запросов Hive на экран

Когда запрос Hive отправлен с помощью консоли командной строки Hadoop, по умолчанию на экране отображается ход выполнения задачи сопоставления или уменьшения. Чтобы скрыть ход выполнения задачи Map/Reduce, можно добавить в командную строку аргумент `-S` (с учетом регистра), как показано ниже.

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Отправка запросов Hive в командной консоли Hive

Вы можете также войти в командную консоль Hive, запустив команду `hive` из командной строки Hadoop, а затем отправить запросы Hive уже из консоли в командной строке **hive>**. Ниже приведен пример.

![Создание рабочей области](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png)

В этом примере красными рамками обведены команды, с помощью которых вы вошли в командную консоль Hive, и запрос Hive, отправленный в командную консоль Hive. Зеленой рамкой выделены выходные данные запроса Hive.

В предыдущих примерах данные обработки запроса Hive выведены прямо на экране. Выходные данные можно также записать в локальный файл на головном узле или в большой двоичный объект Azure. После этого, чтобы глубже проанализировать результаты обработки запросов Hive, вы можете использовать другие инструменты.

#### Вывод результатов запроса Hive в локальный файл

Чтобы вывести результаты запроса Hive в локальный каталог на головном узле, следует отправить запрос Hive из командной строки Hadoop, как показано ниже:

	`hive -e "<hive query>" > <local path in the head node>`


#### Вывод результатов запроса Hive в большой двоичный объект Azure

Результаты запроса Hive можно также выводить в большой двоичный объект Azure в используемом по умолчанию контейнере кластера Hadoop. Вот как выглядит соответствующий запрос Hive:

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

В следующем примере выходные данные запроса Hive записываются в каталог большого двоичного объекта `queryoutputdir` в используемом по умолчанию контейнере кластера Hadoop. Здесь нужно указать только имя каталога, а имя большого двоичного объекта указывать не нужно. Если указать и каталог, и имя большого двоичного объекта, например **wasb:///queryoutputdir/queryoutput.txt*, произойдет ошибка.

![Создание рабочей области](./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png)

Чтобы ознакомиться с результатами обработки запроса Hive в хранилище больших двоичных объектов, нужно с помощью Azure Storage Explorer (или идентичного инструмента) открыть контейнер по умолчанию кластера Hadoop. Если вы хотите получить большой двоичный объект с определенными буквами в именах, вы можете применить фильтр (обведен красной рамкой).

![Создание рабочей области](./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png)

### Использование редактора Hive или команд Azure PowerShell

Вы можете использовать также консоль запросов (редактор Hive). Для этого введите в веб-браузер URL-адрес такого формата:

*имя кластера https://&#60;Hadoop>.azurehdinsight.net/Home/HiveEditor*

. Обратите внимание, что будет предложено ввести учетные данные кластера Hadoop для входа.

Также можно [выполнить запросы Hive с помощью PowerShell](../hdinsight/hdinsight-hadoop-use-hive-powershell.md).


## <a name="create-tables"></a>Создание базы данных и таблиц Hive

Запросы Hive доступны для общего использования в [репозитории GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql), откуда их можно скачать.

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

- **&#60;database name>** — имя базы данных, которую хотят создать пользователи. Если пользователи предпочитают использовать базу данных по умолчанию, запрос *create database...* можно пропустить. 
- **&#60;table name>** — имя таблицы, которую пользователи хотят создать в указанной базе данных. Если пользователи выбирают базу данных по умолчанию, для непосредственного обращения к таблице можно указать имя *&#60;table name>*, не указывая &#60;database name>.
- **&#60;field separator>** — разделитель полей в файле данных, который следует загрузить в таблицу Hive. 
- **&#60;line separator>** — разделитель строк в файле данных. 
- **&#60;storage location>** — хранилище Azure для хранения данных из таблиц Hive. Если пользователи не указывают *LOCATION &#60;storage location>*, база данных и таблицы по умолчанию сохраняются в каталоге *hive/warehouse/* контейнера кластера Hive. Если необходимо указать место хранения, оно должно находиться в пределах контейнера по умолчанию для базы данных и таблиц. Это расположение необходимо указать относительно контейнера кластера по умолчанию в формате *wasb:///&#60;каталог 1>/* или *wasb:///&#60;каталог 1>/&#60;каталог 2>/* и т. д. После выполнения запроса в контейнере по умолчанию будут созданы соответствующие каталоги. 
- **TBLPROPERTIES("skip.header.line.count"="1")**. Если файл данных содержит строку заголовка, пользователям необходимо добавить это свойство **в конец** запроса *create table*. В противном случае строка заголовка будет загружена в таблицу в качестве записи. Если в файле данных нет строки заголовка, в запросе эту конфигурацию можно пропустить. 

## <a name="load-data"></a>Загрузка данных в таблицы Hive
Ниже приведен запрос Hive, который загружает данные в таблицу Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **&#60;path to blob data>**. Если файл BLOB-объекта, который необходимо передать в таблицу Hive, расположен в заданном по умолчанию контейнере кластера HDInsight Hadoop, *&#60;path to blob data>* необходимо указать в формате*'wasb:///&#60;directory in this container>/&#60;blob file name>'*. В дополнительном контейнере кластера Hadoop под управлением службы HDInsight также может быть файл большого двоичного объекта. В этом случае *&#60;path to blob data>* необходимо указать в формате *'wasb://&#60;container name>@&#60;storage account name>.blob.core.windows.net/&#60;blob file name>'*.

	>[AZURE.NOTE]Данные большого двоичного объекта, которые необходимо отправить в таблицу Hive, должны находиться в контейнере по умолчанию или в дополнительном контейнере учетной записи хранения для кластера Hadoop. В противном случае запрос *LOAD DATA* завершится ошибкой доступа к данным.


## <a name="partition-orc"></a>Дополнительные разделы: секционированные таблицы и хранение данных Hive в формате ORC

При большом объеме данных секционирование таблицы полезно для запросов, в рамках которых требуется сканировать только несколько секций таблицы. Например, целесообразно секционировать данные журнала веб-сайта по датам.

Помимо секционирования таблиц Hive данные Hive также удобно хранить в формате ORC. Дополнительные сведения об использовании формата ORC см. в статье <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">Использование ORC-файлов повышает производительность при чтении, записи и обработке данных Hive</a>.

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

При запрашивании секционированных таблиц рекомендуется добавлять условие секционирования в **начало** предложения `where`. Этот прием позволяет значительно повысить эффективность поиска.

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Хранение данных Hive в формате ORC

Пользователи не могут загружать данные непосредственно из хранилища больших двоичных объектов в таблицы Hive в формате ORC. Ниже приведены действия, которые необходимо выполнить, чтобы загрузить данные из больших двоичных объектов Azure в таблицы Hive в формате ORC.

1. Создайте внешнюю таблицу **STORED AS TEXTFILE** и загрузите в нее данные из хранилища BLOB-объектов.

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

	>[AZURE.NOTE]Если в таблице TEXTFILE *&#60;database name>.&#60;external textfile table name>* есть секции, команда `SELECT * FROM <database name>.<external textfile table name>`, выполняемая на ШАГЕ 3, выберет переменную секции в качестве поля в возвращенном наборе данных. Если вставить ее в *&#60;database name>.&#60;ORC table name>*, возникнет ошибка, так как в схеме таблицы *&#60;database name>.&#60;ORC table name>* нет переменной секции в виде поля. В этом случае пользователям нужно указывать поля, которые необходимо вставить в таблицу *&#60;database name>.&#60;ORC table name>*, следующим образом.

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		   SELECT field1, field2, ..., fieldN
		   FROM <database name>.<external textfile table name> 
		   WHERE <partition variable>=<partition value>;

4. Когда все данные будут вставлены в таблицу *&#60;database name>.&#60;ORC table name>*, можно безопасно удалить таблицу *&#60;external textfile table name>*, используя следующий запрос.

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

После выполнения этой процедуры у вас должна быть готовая к использованию таблица с данными в формате ORC.


##Здесь размещаются разделы о настройке

В последнем разделе рассматриваются параметры, настроив которые можно повысить производительность запросов Hive.

<!---HONumber=AcomDC_1125_2015-->