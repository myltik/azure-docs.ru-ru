<properties 
	pageTitle="Анализ данных о задержке рейсов с помощью Hadoop в HDInsight | Microsoft Azure" 
	description="Узнайте, как использовать один скрипт Windows PowerShell для подготовки кластера HDInsight, выполнения задания Hive, выполнения задания Sqoop и удаления кластера." 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/12/2015" 
	ms.author="larryfr"/>

#Анализ данных о задержке рейсов с помощью Hive в HDInsight

Узнайте, как анализировать данные о задержке рейсов с помощью Hive в HDInsight под управлением Linux, а затем экспортировать их в Базу данных SQL Azure с помощью Sqoop.

> [AZURE.NOTE]Хотя отдельные части этого документа можно использовать для кластеров HDInsight под управлением Windows (например, Python и Hive), многие действия относятся к кластерам под управлением Linux. Подробнее о действиях для кластеров на основе Windows см. в статье [Анализ данных о задержке рейсов с помощью Hive в HDInsight](hdinsight-analyze-flight-delay-data.md).

###Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Кластер HDInsight__. Действия для создания нового кластера HDInsight см. в разделе [Приступая к работе с Hadoop с Hive в HDInsight на платформе Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

- __База данных SQL Azure__. Вы будете использовать базу данных SQL Azure в качестве конечного хранилища данных. Если у вас еще нет базы данных SQL Azure, см. раздел [Создание и настройка базы данных SQL Azure](../sql-database/sql-database-create-configure.md).

- __Azure CLI__. Если вы еще не установили Azure CLI, см. раздел [Установка и настройка CLI Azure](../xplat-cli-install.md).


##Скачивание данных о рейсах

1. Перейдите в [Бюро транспортной статистики при администрации по исследованиям и инновационным технологиям][rita-website].
2. На странице выберите следующие значения:

	| Имя | Значение | | Фильтр года | 2013 | | Фильтр периода | Январь | | Поля | Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Очистка всех остальных полей |

3. Щелкните элемент **Загрузить**.

##Передача данных

1. Воспользуйтесь следующей командой, чтобы передать ZIP-файл в головной узел кластера HDInsight:

		scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	Замените __FILENAME__ именем ZIP-файла. Замените __USERNAME__ именем для входа SSH для кластера HDInsight. Замените CLUSTERNAME именем кластера HDInsight.
	
	> [AZURE.NOTE]Если для аутентификации входа посредством SSH используется пароль, будет предложено ввести пароль. Если используется открытый ключ, может потребоваться использовать параметр `-i` и указать путь к соответствующему закрытому ключу. Пример: `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. После завершения передачи подключитесь к кластеру с помощью SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Дополнительная информация об использовании SSH с HDInsight на основе Linux приведена в следующих статьях:
	
	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows)
	
3. После установления подключения используйте следующую команду, чтобы распаковать ZIP-файл:

		unzip FILENAME.zip
	
	Она извлечет в CSV-файл, размер которого приблизительно 60 МБ.
	
4. Используйте следующую команду для создания нового каталога в WASB (распределенном хранилище данных, используемом HDInsight) и копирования файла:

	hadoop fs -mkdir -p /tutorials/flightdelays/data hadoop fs -copyFromLocal FILENAME.csv /tutorials/flightdelays/data/FILENAME.csv
	
##Создание и запуск HiveQL

Выполните следующие действия для импорта данных из CSV-файла в таблицу Hive с именем __Delays__.

1. Для создания и редактирования нового файла __flightdelays.hql__ выполните следующее:

		nano flightdelays.hql
		
	Используйте следующее в качестве содержимого этого файла:
	
		DROP TABLE delays_raw;
		-- Creates an external table over the csv file
		CREATE EXTERNAL TABLE delays_raw (
			YEAR string,
			FL_DATE string,
			UNIQUE_CARRIER string,
			CARRIER string,
			FL_NUM string,
			ORIGIN_AIRPORT_ID string,
			ORIGIN string,
			ORIGIN_CITY_NAME string,
			ORIGIN_CITY_NAME_TEMP string,
			ORIGIN_STATE_ABR string,
			DEST_AIRPORT_ID string,
			DEST string,
			DEST_CITY_NAME string,
			DEST_CITY_NAME_TEMP string,
			DEST_STATE_ABR string,
			DEP_DELAY_NEW float,
			ARR_DELAY_NEW float,
			CARRIER_DELAY float,
			WEATHER_DELAY float,
			NAS_DELAY float,
			SECURITY_DELAY float,
			LATE_AIRCRAFT_DELAY float)
		-- The following lines describe the format and location of the file
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE
		LOCATION '/tutorials/flightdelays/data';
		
		-- Drop the delays table if it exists
		DROP TABLE delays;
		-- Create the delays table and populate it with data
		-- pulled in from the CSV file (via the external table defined previously)
		CREATE TABLE delays AS
		SELECT YEAR AS year,
		    FL_DATE AS flight_date,
		    substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
		    substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
		    substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
		    ORIGIN_AIRPORT_ID AS origin_airport_id,
		    substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
		    substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
		    substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
		    DEST_AIRPORT_ID AS dest_airport_id,
		    substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
		    substring(DEST_CITY_NAME,2) AS dest_city_name,
		    substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
		    DEP_DELAY_NEW AS dep_delay_new,
		    ARR_DELAY_NEW AS arr_delay_new,
		    CARRIER_DELAY AS carrier_delay,
		    WEATHER_DELAY AS weather_delay,
		    NAS_DELAY AS nas_delay,
		    SECURITY_DELAY AS security_delay,
		    LATE_AIRCRAFT_DELAY AS late_aircraft_delay
		FROM delays_raw;
		
2. Нажмите клавиши __Ctrl-X__, а затем __Y__ (Да) для сохранения файла.

3. Используйте следующую команду для запуска Hive и выполнения файла __flightdelays.hql__:

		hive -i flightdelays.hql
		
	Файл будет выполнен, а затем отобразится командная строка `hive>`.

4. При появлении командной строки `hive>` используйте следующую команду, чтобы извлечь информацию из импортированных данных о задержке рейсов.

		INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
		SELECT regexp_replace(origin_city_name, '''', ''),
			avg(weather_delay)
		FROM delays
		WHERE weather_delay IS NOT NULL
		GROUP BY origin_city_name;

	Вы получите список городов, рейсы в которые задержаны из-за погодных условий, а также среднее время задержки. Он будет сохранен в `/tutorials/flightdelays/output`. Позже Sqoop считает данные из этого расположения и экспортирует их в базу данных SQL Azure.

##Создание базы данных SQL

Выполните следующие действия для создания базы данных SQL Azure. Она будет использоваться для хранения данных, экспортированных из HDInsight через Sqoop.

1. В среде разработки, где установлен Azure CLI, используйте следующую команду, чтобы создать новую базу данных SQL Azure:

		azure sql server create <adminLogin> <adminPassword> <region>

	Например, `azure sql server create admin password "West US"`.

	По завершении работы команды появится ответ, аналогичный приведенному ниже:

		info:    Executing command sql server create
		+ Creating SQL Server
		data:    Server Name i1qwc540ts
		info:    sql server create command OK

> [AZURE.IMPORTANT]Обратите внимание на имя сервера, возвращаемое этой командой. Это краткое имя созданного сервера базы данных SQL. Полное доменное имя (FQDN) — `<shortname>.database.windows.net`.

2. Используйте следующую команду для создания базы данных **sqooptest** на сервере базы данных SQL:

        azure sql db create [options] <serverName> sqooptest <adminLogin> <adminPassword>

    После ее завершения появится сообщение «ОК».

	> [AZURE.NOTE]Если появится ошибка от отсутствии доступа, необходимо добавить IP-адрес рабочей станции клиента для брандмауэра базы данных SQL с помощью следующей команды:
	>
	> `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

##Создание таблицы базы данных SQL

> [AZURE.NOTE]Существует множество способов подключения к базе данных SQL для создания таблицы. В приведенных ниже действиях используется [FreeTDS](http://www.freetds.org/) из кластера HDInsight.

1. Используйте SSH для подключения к кластеру HDInsight под управлением Linux и выполните следующие действия в сеансе SSH.

3. Используйте следующую команду для установки FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. После установки FreeTDS используйте следующую команду для подключения к созданному серверу базы данных SQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Должен появиться результат, аналогичный приведенному ниже.

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. В командной строке `1>` введите следующее:

        CREATE TABLE [dbo].[delays](
		[origin_city_name] [nvarchar](50) NOT NULL,
		[weather_delay] float,
		CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
		([origin_city_name] ASC))
		GO

    Если вводится инструкция `GO`, то оцениваются предыдущие инструкции. Будет создана новая таблица __delays__ с кластеризованным индексом (требуется для базы данных SQL).

    Используйте следующую команду для проверки создания таблицы:

        SELECT * FROM information_schema.tables
        GO

    Вы должны увидеть результат, аналогичный приведенному ниже:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     delays      BASE TABLE

8. В командной строке `1>` введите `exit`, чтобы выйти из служебной программы tsql.
	
##Экспорт данных с помощью Sqoop

1. Используйте следующую команду для создания ссылки на драйвер JDBC для SQL Server из каталога lib в Sqoop. Этот драйвер используется для обмена данными между Sqoop и базой данных SQL:

		sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /usr/hdp/current/sqoop-client/lib/sqljdbc4.jar

2. Чтобы проверить, видно ли в Sqoop базу данных SQL, используйте следующую команду:

		sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

	Эта команда должна вывести список баз данных, включая созданную ранее базу данных sqooptest.

3. Для экспорта данных из hivesampletable в таблицу mobiledata используйте следующую команду:

		sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasb:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

	Она дает Sqoop указание подключиться к базе данных SQL, базе данных sqooptest и экспортировать данные из wasb:///tutorials/flightdelays/output (где хранятся выходные данные запроса Hive, выполненного ранее) в таблицу delays.

4. После выполнения команды используйте следующую команду для подключения к базе данных с помощью TSQL:

		TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

	После установления подключения используйте следующие инструкции для проверки экспорта данных в таблицу mobiledata:
	
		SELECT * FROM delays
		GO

	Вы увидите список данных в таблице. Введите `exit` для выхода из служебной программы tsql.

##<a id="nextsteps"></a>Дальнейшие действия
Теперь вы знаете, как отправить файл в хранилище больших двоичных объектов, как заполнить таблицу Hive, используя данные из хранилища больших двоичных объектов, как выполнять запросы Hive и как использовать Sqoop для экспорта данных из HDFS в базу данных SQL Azure. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с HDInsight][hdinsight-get-started]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Oozie с HDInsight][hdinsight-use-oozie]
* [Использование Sqoop с HDInsight][hdinsight-use-sqoop]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-mapreduce]
* [Разработка программ потоковой передачи Hadoop для HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 

<!---HONumber=Oct15_HO1-->