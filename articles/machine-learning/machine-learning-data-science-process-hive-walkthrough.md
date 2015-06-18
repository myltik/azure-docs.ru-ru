<properties 
	pageTitle="Обработка данных в облаке Azure в действии: использование кластеров HDInsight Hadoop|Azure" 
	description="Сквозная обработка данных в облаке Azure с использованием кластера HDInsight Hadoop для создания и развертывания модели с помощью общедоступного набора данных." 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="hangzh;bradsev" /> 

                
# Обработка данных в облаке Azure в действии: использование кластеров HDInsight Hadoop

В этом учебнике вы ознакомитесь со схемой сквозной обработки данных в Azure и узнаете, как использовать кластер Azure HDInsight Hadoop, чтобы создать и развернуть модель обработки общедоступного набора данных - в этом случае набора данных [Поездки на такси в Нью-Йорке](http://www.andresmh.com/nyctaxitrips/). 


## <a name="dataset"></a>Описание набора данных "Поездки на такси в Нью-Йорке"

Сведения о поездках на такси в Нью-Йорке заключены в сжатые файлы данных с разделителями-запятыми (CSV) объемом около 20 ГБ (~48 ГБ без сжатия), которые содержат более 173 миллионов записей о поездках и о стоимости каждой из них. Каждая запись о поездке включает в себя сведения о месте и времени посадки и высадки пассажиров, обезличенные данные о номере водительских прав и номер лицензии (уникальный идентификатор) водителя такси. Данные охватывают все поездки за 2013 год и представлены в приведенных ниже двух наборах данных за каждый месяц.

1. В CSV-файлах типа 'trip_data' содержатся сведения о поездках, например количество пассажиров, места посадки и высадки, продолжительность и дальность поездок. Вот несколько примеров таких записей:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. В CSV-файлах типа 'trip_fare' содержатся сведения о плате за каждую поездку, например тип оплаты, стоимость поездки, добавочная стоимость и налог, чаевые и специальные тарифы, а также общая сумма оплаты. Вот несколько примеров таких записей:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Уникальный ключ, объединяющий файлы trip_data и trip_fare, состоит из следующих полей: medallion, hack_licence и pickup_datetime.

Соответственно, в наборе данных представлено по 12 файлов типа 'trip_data' и 'trip_fare'. Числа от 1 до 12, содержащиеся в именах файлов, соответствуют порядковому номеру месяца, в который осуществлялись поездки. 

## <a name="mltasks"></a>Примеры задач прогнозирования
При работе с данными определение типа прогноза, который вам нужен, на основе анализа поможет выяснить задачи, которые нужно будет включить в процесс обработки.
Ниже приведены три примера прогнозирования проблем, с которыми мы будем разбираться в рамках данного руководства и которые сформулированы на основе *tip_amount*.

1. **Двоичная классификация** позволяет прогнозировать оплату чаевых за поездку, т. е. если значение *tip_amount* (сумма чаевых) больше 0 долларов, это положительный пример, а если значение *tip_amount* равно 0 долларов, это отрицательный пример.

2. **Многоклассовая классификация** помогает спрогнозировать диапазон сумм чаевых, оплачиваемых за поездку. Значения *tip_amount* можно разделить на пять позиций или классов:
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Задача регрессии** помогает спрогнозировать сумму чаевых, которая будет уплачена за поездку.  


## <a name="setup"></a>Настройка среды обработки данных Azure

Как видно из руководства [Планирование среды](machine-learning-data-science-plan-your-environment.md), в среде Azure есть несколько вариантов обработки наборов данных "Поездки на такси в Нью-Йорке":

- Работа с данными в больших двоичных объектах Azure с последующим моделированием в среде машинного обучения Azure.
- Загрузка данных в базу данных службы SQL Server с последующим моделированием в среде машинного обучения Azure.
- Загрузка данных в таблицы HDInsight Hive с последующим моделированием в среде машинного обучения Azure.

Исходя из размера набора данных, расположения источника данных и выбранной целевой среды Azure, этот сценарий такой же, как [Сценарий\№7: большой набор данных в локальных файлах, целевые кластеры Azure HDInsight Hadoop](../machine-learning-data-science-plan-sample-scenarios.md#largedbtohive).

Чтобы настроить среду Azure для обработки данных с использованием кластеров Azure HDInsight Hadoop, точнее говоря, таблиц Hive и запросов о хранении и обработке данных, выполните такие действия:

1. [Создайте учетную запись хранения](../storage-whatis-account.md).

2. [Создайте рабочую область машинного обучения Azure](machine-learning-create-workspace.md).

3. [Подготовьте виртуальную машину **Windows** для обработки данных](machine-learning-data-science-setup-virtual-machine.md).

	> [AZURE.NOTE] Образцы скриптов будут загружены в виртуальную машину для обработки данных во время ее настройки. После выполнения сценария, который следует за установкой виртуальной машины, образцы будут сохранены в библиотеке документов виртуальной машины, расположенной в папке *C:\Users<user_name>\Documents\Data Science Scripts*.  Этот образец папок ниже фигурирует под именем **Образец скриптов**. Образцы запросов Hive содержатся в HQL-файлах в папке **Образцы скриптов**. Обратите внимание, что элемент *<user_name>*, указанный в пути к этой папке, - это имя входа на вашу виртуальную машину Windows, а не имя пользователя Azure.

4. [Настройка кластеров Azure HDInsight Hadoop для обработки данных](machine-learning-data-science-customize-hadoop-cluster.md). В рамках этого шага будет создан кластер Azure HDInsight Hadoop с установкой 64-разрядной версии Anaconda Python 2.7 на всех узлах. После создания настраиваемого кластера Hadoop включите удаленный доступ к головному узлу кластера Hadoop на портале Azure, используя процедуру, описываемую в этом разделе настройки.


## <a name="getdata"></a>Получение данных из общедоступных источников

Чтобы получить набор данных [Поездки на такси в Нью-Йорке](http://www.andresmh.com/nyctaxitrips/) из общедоступного расположения, вы можете использовать любой метод из описываемых в статье [Перемещение данных в хранилище больших двоичных объектов Azure и из него](machine-learning-data-science-move-azure-blob.md) для копирования данных на новую виртуальную машину.

Вот как вы можете скопировать данные с помощью AzCopy:

1. Выполните вход на своем компьютере (ВМ).

2. Создайте каталог в диске данных виртуальной машины. (Примечание. Не используйте временный диск данных, который выполняет роль диска данных в виртуальной машине.)

3. Из окна командной строки выполните приведенные ниже команды AzCopy, заменяя <path_to_data_folder> путем к вашей папке данных, созданной в (2).

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxitrip /Dest:<path_to_data_folder> /S

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxifare /Dest:<path_to_data_folder> /S

	Когда программа AzCopy завершит работу, в папке данных должны появиться 24 сжатых файла в формате CSV (12 для trip_data и 12 для trip_fare).

	>[AZURE.NOTE] *Source:https://getgoing.blob.core.windows.net/nyctaxitrip* и *Source:https://getgoing.blob.core.windows.net/nyctaxifare*: эти два адреса указывают на два общедоступных контейнера Azure, используемых, чтобы делиться нераспакованными данными о такси Нью-Йорка с пользователями. Ключ доступа не требуется для чтения в этих двух общедоступных контейнерах Azure. 

## <a name="upload"></a>Отправка данных в контейнер по умолчанию кластера Azure HDInsight Hadoop

Из командной строки или окна Windows PowerShell запустите указанную ниже команду AzCopy, заменяя приведенные ниже параметры значениями, которые вы указали при создании кластера Hadoop. Итак, вот что нужно заменить:

* ***&#60;path_to_data_folder>*** замените именем папки, которую вы создали в предыдущем разделе. 
* ***&#60;storage account name of Hadoop cluster>*** замените именем учетной записи хранения, используемой в вашем кластере.
* ***&#60;default container of Hadoop cluster>*** замените контейнером по умолчанию, который используется в вашем кластере.
* ***&#60;storage account key>*** замените ключом для учетной записи хранения, используемой в вашем кластере.

Обратите внимания, что если элементы ***nyctaxitripraw*** или ***nyctaxifareraw***, приведенные в этой команде, не существуют, то они будут созданы в контейнере. 

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
		
		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

## <a name="#hive-db-tables"></a>Создание базы данных Hive и таблиц, секционированных по месяцам

Чтобы получить доступ к запросам Hive, приведенным в этом руководстве, и выполнить их, войдите в головной узел кластера Hadoop, откройте командную строку Hadoop на рабочем столе головного узла и укажите каталог Hive, введя команду

    cd %hive_home%\bin

Если вам понадобиться дополнительная помощь в связи с этими или другими действиями, см. раздел [Отправка запросов Hive прямо из командной строки Hadoop](machine-learning-data-science-process-hive-tables.md#submit). 

Если, следуя инструкциям раздела [Настройка среды обработки данных Azure](#setup), вы создали виртуальную машину Azure, образцы запросов Hive должны быть уже загружены в вашу виртуальную машину в папку **Образцы скриптов**. Или же пользователи могут извлекать HQL-файлы из [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). 

Чтобы отправить запросы Hive в HQL-файлах, нужно переместить эти файлы в локальный каталог головного узла кластера Hadoop. В приведенных ниже примерах предполагается, что HQL-файлы перемещены в каталог ***C:\temp&#92;*** головного узла.

Ниже приведено содержимое файла ***C:\temp\sample_hive_create_db_and_tables.hql***, с помощью которого создается база данных Hive ***nyctaxidb*** и таблицы ***trip*** и ***fare***.

	create database if not exists nyctaxidb;

	create external table if not exists nyctaxidb.trip
	( 
	    medallion string, 
	    hack_license string,
	    vendor_id string, 
	    rate_code string, 
	    store_and_fwd_flag string, 
	    pickup_datetime string, 
	    dropoff_datetime string, 
	    passenger_count int, 
	    trip_time_in_secs double, 
	    trip_distance double, 
	    pickup_longitude double, 
	    pickup_latitude double, 
	    dropoff_longitude double, 
	    dropoff_latitude double)  
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

	create external table if not exists nyctaxidb.fare 
	( 
	    medallion string, 
	    hack_license string, 
	    vendor_id string, 
	    pickup_datetime string, 
	    payment_type string, 
	    fare_amount double, 
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double)
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Чтобы отправить эти запросы Hive, введите в командную строку Hadoop головного узла приведенную ниже команду.
	
	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"


## <a name="#load-data"></a>Загрузка данных в таблицы по разделам
Чтобы загрузить данные в таблицы Hive, секционированные по месяцам, выполните в командной строке Hadoop приведенные ниже команды.

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Вот содержимое файла *sample_hive_load_data_by_partitions.hql*, которое нужно проверить.

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/fare_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

### <a name="#show-db"></a>Отображение баз данных в кластере HDInsight Hadoop

Чтобы отобразить базы данных, созданные в кластере HDInsight Hadoop в окне командной строки Hadoop, выполните в командной строке Hadoop приведенную ниже команду.

	hive -e "show databases;"

### <a name="#show-tables"></a>Отображение таблиц в базе данных nyctaxidb 
	
Для отображения таблиц в базе данных nyctaxidb выполните в командной строке Hadoop приведенную ниже команду.
	
	hive -e "show tables in nyctaxidb;"
   
## <a name="#explore-hive"></a>Просмотр данных и создание функций в Hive
Задачи, которые связаны с просмотром данных и созданием функций и которые относятся к данным, загруженным в таблицы Hive, вы можете выполнить с помощью запросов Hive. Вот примеры задач, о которых пойдет речь в этом разделе:

- Просмотр 10 верхних записей в обеих таблицах.
- Просмотр распределений данных в нескольких полях и в рамках различных временных окон.
- Проверка качества данных полей широты и долготы.
- Создание меток двоичной и многоклассовой классификации на основе **tip_amount**.
- Создание функций путем вычисления дальности поездки.
- Объединение двух таблиц и извлечение случайных выборок для построения моделей.

После просмотра данных и создания функций в Hive вы будете готовы работать со службой машинного обучения Azure. Вы можете сохранить последний запрос Hive, чтобы извлечь данные и создать их выборку, а затем скопировать запрос прямо в модуль чтения в службе машинного обучения Azure. После этого вы сможете создать модель для этих данных.

### Просмотр: просмотр 10 верхних записей в обеих таблицах

Чтобы понять характер схемы данных и род данных, которые она содержит, пользователи могут извлечь 10 верхних записей из каждой таблицы. Чтобы проверить записи, отправьте из консоли командной строки Hadoop два запроса, приведенных ниже, по отдельности.

Этот запрос нужно отправить, чтобы получить 10 верхних записей в *table _trip_*.

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
    
Этот запрос нужно отправить, чтобы получить 10 верхних записей в *table _fare_*.
	
	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

### Просмотр: количество записей в каждом из 12 разделов

Чтобы ознакомиться с количеством записей в каждом из 12 разделов, выполните в консоли командной строки Hadoop приведенную ниже команду.
	
	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

### Просмотр: распределение поездок по лицензии водителя

В этом примере определяется лицензия водителя, на которой числится более 100 поездок за данный период времени. Преимущество этого запроса - то, что доступ открывается к секционированной таблице, которую обуславливает переменная секции **month**. 

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql"

Здесь для проверки приведено содержимое файла *sample_hive_trip_count_by_medallion.hql*.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100 
	ORDER BY med_count desc;

### Просмотр: распределение поездок по лицензии и водительским правам

Ниже приведен пример группирования по нескольким столбцам таблицы, в этом случае по столбцам лицензии и водительских прав. В консоли командной строки Hadoop выполните приведенную ниже команду.

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql"

Здесь для проверки приведено содержимое файла *sample_hive_trip_count_by_medallion_license.hql*.
	
    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

### Оценка качества данных: проверка записей с неверной долготой и шириной

В этом примере мы проверяем поля широты и долготы на наличие недопустимых значений (градусы, полученные из радиан, должны находиться в диапазоне от -90 до 90) или координат (0, 0).

В консоли командной строки Hadoop выполните приведенную ниже команду.

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

Аргумент *-S* этой команды скрывает окно состояния задач Hive по сопоставлению и уменьшению. Это очень полезно, так как отображение запроса Hive становится более удобочитаемым. 

Здесь для проверки приведено содержимое файла *sample_hive_quality_assessment.hql*.

    	SELECT COUNT(*) FROM nyctaxidb.trip
    	WHERE month=1
    	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'));

### Просмотр: частота поездок с чаевыми и без

В этом примере пользователь узнает о соотношении количества поездок, когда водителю дали на чай, к количеству поездок, когда чаевых не было (пользователь ищет сведения за определенный период времени или, если нужны сведения за полный год, во всех данных набора). Это пример двоичного распределения меток, которое потом будет использовано для двоичного моделирования классификации.

В консоли командной строки Hadoop выполните приведенную ниже команду.

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"

Здесь для проверки приведено содержимое файла *sample_hive_tipped_frequencies.hql*.

    SELECT tipped, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

### Просмотр: частота разных сумм чаевых

В этом примере вычисляется распределение сумм чаевых (пользователь ищет сведения за определенный период времени или, если нужны сведения за полный год, во всех данных набора). Это пример распределения классов метки, которое потом будет использовано для многоклассового моделирования классификации.

В консоли командной строки Hadoop выполните приведенную ниже команду.

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

Здесь для проверки приведено содержимое файла *sample_hive_tip_range_frequencies.hql*.

	SELECT tip_class, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount=0, 0, 
            if(tip_amount>0 and tip_amount<=5, 1, 
            if(tip_amount>5 and tip_amount<=10, 2, 
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

### Просмотр: вычисление расстояния и сравнение с дальностью поездки

В этом примере вычисляется дальность поездки (в милях). В этом примере отображаются только допустимые координаты, так как задействован запрос оценки качества данных, приведенный выше.

В консоли командной строки Hadoop выполните приведенную ниже команду.

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"

Здесь для проверки приведено содержимое файла *sample_hive_trip_direct_distance.hql*.

    set R=3959;
    set pi=radians(180);
	
	insert overwrite directory 'wasb:///queryoutputdir'
    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
        pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
    from nyctaxidb.trip 
    where month=1 
        and pickup_longitude between -90 and -30
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and -30
        and dropoff_latitude between 30 and 90;

После выполнения запроса результаты записываются в большой двоичный объект Azure ***queryoutputdir/000000_0***, который находится в контейнере по умолчанию кластера Hadoop. В Azure Storage Explorer этот большой двоичный объект отображается в контейнере по умолчанию кластера Hadoop (см. рисунок ниже).

![Create workspace][2]

Обратите внимание, что, чтобы получить только большой двоичный объект с указанными буквами в именах, вы можете применить фильтр (обведен красной рамкой). 

Когда запрос выполнен, вы можете с помощью Azure SDK читать результаты обработки запроса, которые выдал большой двоичный объект Azure, в блоке данных Pandas. Таким образом вы сможете и дальше выполнять процессы и просматривать данные. Сведения о том, как читать данные большого двоичного объекта Azure в блоке данных Pandas, см. в статье [Обработка данных большого двоичного объекта Azure в среде обработке данных](machine-learning-data-science-process-data-blob.md). 
	
### Подготовка данных для построения модели

Запрос, приведенный в этом разделе, объединяет таблицы **nyctaxidb.trip** и **nyctaxidb.fare**, создает метку двоичной классификации **tipped** и метку многоклассовой классификации **tip_class**. Этот запрос вы можете скопировать прямо в модуль чтения [Студии машинного обучения Microsoft Azure](https://studio.azureml.net), чтобы данные запроса **Hive** в Azure отправлялись для обработки прямо в этот модуль. Кроме того, этот запрос исключает записи с недопустимыми координатами широты и долготы.

Этот запрос применяет определяемые пользователем признаки, внедренные в Hive, чтобы создавать признаки из записей Hive, в том числе час, неделю года и день недели (1 - это понедельник, 7 - это воскресенье), которые относятся к полю _pickup_datetime_. Полный список определяемых пользователем функций, внедренных в Hive, см. на странице [Руководство по языку для определяемых пользователем функций](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Кроме того, этот запрос понижает качество данных, чтобы с результатами запроса могла работать студия машинного обучения Azure. В студию импортируется только примерно 1 %.

Отправьте запрос, выполнив из консоли командной строки Hadoop приведенную ниже команду.

	hive -f "C:\temp\sample_hive_prepare_for_aml.hql"

Здесь для проверки приведено содержимое файла *sample_hive_prepare_for_aml.hql*.
	
    select 
        t.medallion, 
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        f.payment_type, 
        f.fare_amount, 
        f.surcharge, 
        f.mta_tax, 
        f.tip_amount, 
        f.tolls_amount, 
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
            if(tip_amount>0 and tip_amount<=5,1,
            if(tip_amount>5 and tip_amount<=10,2,
            if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
    from
    (
        select medallion, 
            hack_license,
            vendor_id,
            rate_code,
            store_and_fwd_flag,
            pickup_datetime,
            dropoff_datetime,
            passenger_count,
            trip_time_in_secs,
            trip_distance,
            pickup_longitude,
            pickup_latitude,
            dropoff_longitude,
            dropoff_latitude,
            rand() as sample_key 
        from nyctaxidb.trip
        where pickup_latitude between 30 and 60
            and pickup_longitude between -90 and -60
            and dropoff_latitude between 30 and 60
            and dropoff_longitude between -90 and -60
    )t
    join
    (
        select 
            medallion, 
            hack_license, 
            vendor_id, 
            pickup_datetime, 
            payment_type, 
            fare_amount, 
            surcharge, 
            mta_tax, 
            tip_amount, 
            tolls_amount, 
            total_amount
        from nyctaxidb.fare 
    )f
    on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
    where t.sample_key<=0.01

## <a name="mlmodel"></a>Построение моделей в студии машинного обучения Azure

Теперь вы готовы перейти к построению и развертыванию моделей в [Студии машинного обучения Azure](https://studio.azureml.net). Данные готовы, и вы можете с их помощью решать проблемы прогнозирования, приведенные выше.

1. **Двоичная классификация**: прогнозирование того, получит водитель чаевые или нет.

2. **Многоклассовая классификация**: прогнозирование сумм чаевых за поездку с использованием ранее определенных классов. 

3. **Задача регрессии**: прогнозирование суммы чаевых, которая будет уплачена за поездку.  

Чтобы начать моделирование, войдите в рабочую область Студии машинного обучения Azure. Если вы еще не создали рабочую область для машинного обучения, см. статью [Создание рабочей области машинного обучения Azure](machine-learning-create-workspace.md).

1. Чтобы приступить к работе со Студией машинного обучения Azure, см. статью [Что такое Студия машинного обучения Azure?](machine-learning-what-is-ml-studio.md)

2. Войдите в [Студию машинного обучения Azure](https://studio.azureml.net).

3. На домашней странице Студии вы найдете множество сведений, видео-роликов, учебников, ссылок на модули и другие ресурсы. Дополнительные сведения о машинном обучении Azure см. на сайте [Центр машинного обучения](http://azure.microsoft.com/documentation/services/machine-learning/).

Вот что нужно сделать в типичном учебном эксперименте:

1. Создайте эксперимент **+NEW**.
2. Переместите данные в Студию машинного обучения Azure.
3. Подвергните данные предварительной обработке, преобразуйте их и управляйте ими, если нужно.
4. Создайте функции, если нужно.
5. Разбейте данные на наборы данных для обучения, проверки или тестирования (или создайте отдельные наборы данных для каждой задачи).
6. В зависимости от того, какую учебную проблему нужно решить, выберите алгоритм машинного обучения или несколько таких алгоритмов. Например, двоичная классификация, многоклассовая классификация, регрессия.
7. Обучите модели с помощью учебного набора данных.
8. Оцените проверочный набор данных с помощью обучаемой модели.
9. Чтобы вычислить подходящие метрики для проблемы обучения, оцените модель.
10. Настройте модели и для развертывания выберите лучшую из них.

В этом упражнении мы уже просмотрели и создали данные в Hive (шаги 1-4) и выбрали размер образца, который нужно обработать в Студии машинного обучения Azure. Вот как вы можете построить модель прогнозирования:

1. Отправьте данные в Студию машинного обучения Azure с помощью модуля **чтения**, доступного в разделе **Ввод и вывод данных**. Дополнительные сведения см. на справочной странице по модулю [чтения](http://msdn.microsoft.com/library/dn784775).

	![Create workspace][15]

2. Выберите в качестве **источника данных** **запрос Hive** на панели **Свойства**.

3. Вот как нужно вводить сведения о кластере HDInsight Hadoop. В поле **Имя учетной записи хранения Azure** нужно указать учетную запись, которая используется для создания кластера HDInsight Hadoop, а в поле **Имя контейнера Azure** нужно указать контейнер по умолчанию кластера Hadoop. 

	![Create workspace][11]

4. В поле **Запрос к базе данных Hive** вставьте запрос, который извлекает нужные поля базы данных (в том числе любые вычисляемые поля, такие как метки) и понижает качество данных до желаемого размера.

На рисунке внизу приведен пример эксперимента двоичной классификации, который связан с чтением данных прямо из запроса Hive. Вы можете создавать подобные эксперименты и для проблем многоклассовой классификации и регрессии.

![Create workspace][12]

> [AZURE.IMPORTANT] В запросах на извлечение данных моделирования и создание выборок на их основе, приведенных в предыдущих разделах, **все метки для трех упражнений по моделированию включены в запрос**. Важным этапом в каждом упражнении по моделированию является **исключение** меток, ненужных в двух других проблемах, и любых других **целевых утечек**. Например, при использовании двоичной классификации используйте метку **tipped** и исключите поля **tip_class**, **tip_amount** и **total_amount**. Последние являются целевыми утечками, так как в них подразумевается, что чаевые оплачены.

> В этом эксперименте первый модуль **редактора метаданных** добавляет имена столбца к выходным данным, которые выдал модуль чтения. Модуль редактора метаданных нужен, так как модуль чтения, считывающий данные из запроса Hive, не создает имена столбца для выходных данных. 

> Чтобы исключить ненужные столбцы или целевые утечки (или и то и другое), вы можете использовать модуль **Столбцы проекта** или **Редактор метаданных**. Дополнительные сведения см. на справочных страницах [Столбцы проекта](http://msdn.microsoft.com/library/dn784740) и [Редактор метаданных](http://msdn.microsoft.com/library/dn784761).

## <a name="mldeploy"></a>Развертывание моделей в Студии машинного обучения Azure

Когда ваша модель готова, вы можете развернуть ее как веб-службу прямо из эксперимента. Дополнительные сведения о публикации веб-служб машинного обучения Azure см. в статье [Операции службы API машинного обучения Azure](../machine-learning-overview-of-azure-ml-process.md).

Вот как вы можете развернуть новую веб-службу:

1. Создайте оценивающий эксперимент.
2. Опубликуйте веб-службу.

Чтобы из **готового** обучающего эксперимента создать оценочный эксперимент, на нижней панели действий щелкните **СОЗДАНИЕ ОЦЕНОЧНОГО ЭКСПЕРИМЕНТА**.

![Azure Scoring][13]

Студия машинного обучения Azure попытается создать оценочный эксперимент на основе компонентов обучающего эксперимента. В частности, Студия сделает следующее:

1. Сохранит обучаемую модель и удалит модули обучающей модели.
2. Определит логический **порт ввода**, который должен представлять ожидаемую схему ввода данных.
3. Определит логический **порт вывода**, который должен представлять ожидаемую схему вывода данных для веб-службы.

При создании оценочного эксперимента ознакомьтесь с ним и настройте его должным образом. Обычно, настраивая, заменяют входной набор данных или запрос (или то и другое вместе) таким набором данных или запросом, который исключает поля метки, потому что они не будут доступны при вызове службы. Мы также рекомендуем уменьшать размер входного набора данных или запроса (или того и другого вместе) до нескольких записей, достаточных для того, чтобы обозначить входную схему. Обычно для порта вывода все входные поля исключаются, а в выходные данные включаются только поля **Оцененные метки** и **Оцененные возможности** с помощью модуля **Столбцы проекта**.

Пример оценочного эксперимента приведен в рисунке внизу. Когда вы готовы публиковать, нажмите кнопку **ПУБЛИКАЦЯ ВЕБ-СЛУЖБЫ** на нижней панели действий.

![Create workspace][14]

Итак, с помощью этого учебника вы создали среду обработки данных Azure, работающую с большими общедоступными наборами данных. Вы научились многому, начиная с получения данных, их просмотра, создания признаков при обработке данных и заканчивая обучением моделей и публикацией веб-службы машинного обучения Azure.

## Сведения о лицензии

Доступ к этому учебнику и включенным в него скриптам предоставляет корпорация Майкрософт на основании лицензии свободного ПО. Дополнительные сведения см. в файле LICENSE.txt в каталоге примера кода в GitHub.

## Ссылки

*	[Страница загрузки данных о поездках такси Нью-Йорка (Andrés Monroy)](http://www.andresmh.com/nyctaxitrips/)  
*	[Данные о поездках на такси в Нью-Йорке, находящиеся в свободном доступе согласно Закону о свободе информации и предоставленные Крисом Вонгом](http://chriswhong.com/open-data/foil_nyc_taxi/)   
*	[Статистика и исследования комиссии Нью-Йорка по такси и лимузинам](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!--HONumber=49--> 