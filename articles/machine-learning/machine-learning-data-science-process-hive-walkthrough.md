<properties
	pageTitle="Процесс обработки и анализа данных группы на практике: использование кластеров Hadoop | Microsoft Azure"
	description="Применение процесса обработки и анализа данных группы в комплексном сценарии, включающем в себя использование кластера HDInsight Hadoop для создания и развертывания модели на основе общедоступного набора данных."
	services="machine-learning,hdinsight"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="hangzh;bradsev" />


# Процесс обработки и анализа данных группы на практике: использование кластеров HDInsight Hadoop

В этом пошаговом руководстве показано комплексное использование [процесса обработки и анализа данных группы (TDSP)](data-science-process-overview.md), где [кластер Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) используется для хранения и просмотра данных из общедоступного набора данных [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) (Поездки такси Нью-Йорка), реконструирования их характеристик и сокращения их выборки. Модели данных создаются с помощью машинного обучения Azure для обработки двоичных и мультиклассовых классификационных и регрессионных прогнозных задач.

Пошаговое руководство, показывающее, как обработать более крупный (1 ТБ) набор данных для подобного сценария с помощью кластеров HDInsight Hadoop для обработки данных, см. в статье [Процесс обработки и анализа данных группы на практике: использование кластеров Azure HDInsight Hadoop с набором данных объемом 1 ТБ](machine-learning-data-science-process-hive-criteo-walkthrough.md).

Кроме того, для выполнения заданий, представленных в этом пошаговом руководстве, с помощью набора данных объемом 1 ТБ, можно использовать iPython Notebook. Пользователям, которые хотят применить этот подход, следует просмотреть раздел [Criteo walkthrough using a Hive ODBC connection](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) (Пошаговое руководство Criteo по использованию подключения Hive ODBC).


## <a name="dataset"></a>Описание набора данных «Поездки такси Нью-Йорка»

Сведения о поездках на такси в Нью-Йорке заключены в сжатые файлы данных с разделителями-запятыми (CSV) объемом около 20 ГБ (~48 ГБ без сжатия), которые содержат более 173 миллионов записей о поездках и о стоимости каждой из них. Каждая запись о поездке содержит расположение пунктов отправления и назначения и время, анонимизированный номер лицензии водителя и номер медальона (уникальный идентификатор такси). Данные включают в себя все поездки за 2013 год и предоставляются в виде следующих двух наборов данных за каждый месяц:

1. В CSV-файлах trip\_data содержатся сведения о поездках, например количество пассажиров, места посадки и высадки, продолжительность и дальность поездок. Вот несколько примеров записей:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. В CSV-файлах trip\_fare содержатся сведения о плате за каждую поездку, например тип оплаты, стоимость поездки, добавочная стоимость и налоги, чаевые и специальные тарифы, а также общая сумма оплаты. Вот несколько примеров записей:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Уникальный ключ для соединения trip\_data и trip\_fare состоит из полей: medallion, hack\_licence и pickup\_datetime.

Чтобы получить все данные, относящиеся к определенной поездке, достаточно выполнить объединение с тремя ключами: «medallion», «hack\_license» и «pickup\_datetime».

Дополнительную информацию о данных мы предоставим ниже, в разделе о сохранении данных в таблицах Hive.

## <a name="mltasks"></a>Примеры задач прогнозирования
При работе с данными определение типа прогноза, который вам нужен, на основе анализа поможет выяснить задачи, которые нужно будет включить в процесс обработки. Ниже приведены три примера проблем прогнозирования, с которыми мы будем разбираться в рамках данного руководства и которые сформулированы на основе *tip\_amount*.

1. **Двоичная классификация**: спрогнозировать, были ли выплачены чаевые за поездку, т. е. значение *tip\_amount*, превышающее 0 $, является позитивным примером, а значение *tip\_amount*, равняющееся 0 $, является негативным примером.

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0

2. **Мультиклассовая классификация**: спрогнозировать диапазон суммы чаевых за поездку. Мы разделяем *tip\_amount* на пять ячеек или классов:

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Задача регрессии**: спрогнозировать сумму чаевых, выплаченных за поездку.


## <a name="setup"></a>Настройка кластера HDInsight Hadoop для расширенной аналитики

>[AZURE.NOTE] Эту задачу обычно выполняет **администратор**.

Настроить среду Azure для использования расширенной аналитики, в которой задействуется кластер HDInsight, вы можете в три этапа.

1. [Создание учетной записи хранения](../storage/storage-create-storage-account.md). Эта учетная запись используется для хранения данных в хранилище больших двоичных объектов Azure. Здесь находятся также и данные, используемые в кластерах HDInsight.

2. [Настройка кластеров Azure HDInsight Hadoop для технологии и процесса расширенного анализа](machine-learning-data-science-customize-hadoop-cluster.md) На этом этапе создается кластер Azure HDInsight Hadoop, на всех узлах которого установлена 64-разрядная версия Anaconda Python 2.7. Существует два важных действия, которые нужно не забыть выполнить при настройке кластера HDInsight.

	* Во время создания кластера HDInsight не забудьте связать учетную запись хранения, созданную на шаге 1, с этим кластером. Эта учетная запись хранения используется для доступа к данным, которые обрабатываются в пределах кластера.

	* После создания кластера включите удаленный доступ к головному узлу кластера. Перейдите на вкладку **Конфигурация** и нажмите кнопку **Включить удаленный доступ**. На этом шаге указываются учетные данные пользователя для удаленного входа.

3. [Создание рабочей области машинного обучения Azure](machine-learning-create-workspace.md): эта рабочая область используется для построения моделей машинного обучения. Эта задача решается после начального исследования данных и сокращения выборки с использованием кластера HDInsight.

## <a name="getdata"></a>Получение данных из общедоступного источника

>[AZURE.NOTE] Эту задачу обычно выполняет **администратор**.

Если вам нужно получить набор данных [Поездки такси Нью-Йорка](http://www.andresmh.com/nyctaxitrips/) из общедоступного расположения, то, чтобы скопировать их на ваш компьютер, вы можете воспользоваться любым из методов, описанных в разделе [Перемещение данных в хранилище больших двоичных объектов Azure и из него](machine-learning-data-science-move-azure-blob.md).

Здесь рассматривается использование AzCopy для передачи файлов, содержащих данные. Чтобы загрузить и установить AzCopy, следуйте инструкциям в статье [Приступая к работе со служебной программой командной строки AzCopy](../storage/storage-use-azcopy.md).

1. В окне командной строки выполните приведенные ниже команды AzCopy, заменяя *<path\_to\_data\_folder>* путем к нужному месту назначения.


		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

2. По завершении копирования выбранная папка данных будет содержать в общей сложности 24 ZIP-файла. Распакуйте загруженные файлы в тот же каталог на локальном компьютере. Обратите внимание на папку, в которой располагаются распакованные файлы. Далее по тексту мы будем называть эту папку *<path\_to\_unzipped\_data\_files>*.


## <a name="upload"></a>Отправка данных в контейнер по умолчанию кластера Azure HDInsight Hadoop

>[AZURE.NOTE] Эту задачу обычно выполняет **администратор**.

В следующих командах AzCopy замените приведенные ниже параметры фактическими значениями, указанными при создании кластера Hadoop и распаковке файлов данных.

* ***&#60;path\_to\_data\_folder>*** — каталог (вместе с путем) на компьютере, где содержатся распакованные файлы данных.
* ***&#60;storage account name of Hadoop cluster>*** — учетная запись хранения, связанная с вашим кластером HDInsight.
* ***&#60;default container of Hadoop cluster>*** — контейнер по умолчанию, используемый кластером. Обратите внимание, что имя контейнера по умолчанию обычно совпадает с именем самого кластера. Например, если кластер называется abc123.azurehdinsight.net, контейнером по умолчанию будет abc123.
* ***&#60;storage account key>*** — ключ для учетной записи хранения, используемой вашим кластером.

В командной строке или окне Windows PowerShell выполните две приведенные ниже команды AzCopy.

Эта команда передает данные о поездке в каталог ***nyctaxitripraw*** в контейнере по умолчанию кластера Hadoop.

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Эта команда передает данные о тарифе в каталог ***nyctaxifareraw*** в контейнере по умолчанию кластера Hadoop.

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Теперь данные должны находиться в хранилище больших двоичных объектов Azure и быть готовы к использованию в кластере HDInsight.

## <a name="#download-hql-files"></a>Вход в головной узел кластера Hadoop и подготовка к исследовательскому анализу данных

>[AZURE.NOTE] Эту задачу обычно выполняет **администратор**.

Чтобы получить доступ к головному узлу кластера в целях исследовательского анализа данных и сокращения их выборки, выполните процедуру, описанную в разделе [Доступ к головному узлу кластера Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

Для предварительных исследований данных мы в этом пошаговом руководстве в основном используем запросы, написанные на языке запросов [Hive](https://hive.apache.org/) (он похож на язык SQL). Запросы Hive хранятся в HQL-файлах. Затем мы сокращаем выборку этих данных, которые будут использоваться в машинном обучении Azure для построения моделей.

Чтобы подготовить кластер к исследовательскому анализу, мы загрузим HQL-файлы, содержащие соответствующие сценарии Hive, с [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) в локальный каталог (C:\\temp) на головном узле. Чтобы сделать это, откройте **командную строку** на головном узле кластера и введите две приведенные ниже команды.

	set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Эти две команды загрузят все необходимые в этом пошаговом руководстве HQL-файлы в локальный каталог ***C:\\temp&#92;*** головного узла.

## <a name="#hive-db-tables"></a>Создание базы данных Hive и таблиц, секционированных по месяцам

>[AZURE.NOTE] Эту задачу обычно выполняет **администратор**.

Теперь мы готовы создать таблицы Hive для нашего набора данных о такси Нью-Йорка. На головном узле кластера Hadoop откройте ***командную строку Hadoop*** на рабочем столе головного узла и войдите в каталог Hive с помощью команды

    cd %hive_home%\bin

>[AZURE.NOTE] **Выполняйте все команды Hive в этом пошаговом руководстве из указанного выше запроса командной строки bin/directory. Таким образом вы сможете автоматически избежать любых проблем с путем. Термины «командная строка каталога Hive», «командная строка Hive bin/directory» и «командная строка Hadoop» в этом руководстве являются взаимозаменяемыми.**

В командной строке каталога Hive введите приведенную ниже команду в командную строку Hadoop головного узла, чтобы отправить запрос Hive для создания базы данных и таблиц Hive.

	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Ниже приведено содержимое файла ***C:\\temp\\sample\_hive\_create\_db\_and\_tables.hql***, который создает базу данных Hive ***nyctaxidb*** и таблицы Hive ***trip*** и ***fare***.

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

Этот сценарий Hive создает две таблицы:

* в таблице «trip» содержатся подробные сведения о каждой поездке (сведения о водителе, время отправки, расстояние поездки и время);
* в таблице «fare» содержатся подробные сведения о тарифе (сумма тарифа, сумма чаевых, сборы и наценки).

Если вам требуется дополнительная помощь в работе с этими процедурами или вы хотите изучить альтернативы, см. раздел [Отправка запросов Hive прямо из командной строки Hadoop](machine-learning-data-science-process-hive-tables.md#submit).

## <a name="#load-data"></a>Загрузка данных в таблицы Hive по разделам

>[AZURE.NOTE] Эту задачу обычно выполняет **администратор**.

Набор данных о такси Нью-Йорка содержит естественное секционирование по месяцам, которое используется для ускорения обработки и выполнения запросов. Приведенные ниже команды PowerShell (которые вводятся из каталога Hive с помощью **командной строки Hadoop**) загружают данные в таблицы Hive «trip» и «fare», секционированные по месяцам.

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

В файле *sample\_hive\_load\_data\_by\_partitions.hql* содержатся приведенные ниже команды **LOAD**.

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Обратите внимание, что ряд запросов Hive, которые мы используем здесь в процессе исследования, предусматривает поиск только в одном разделе или в небольшом их количестве. Однако эти запросы можно выполнять по всем данным.

### <a name="#show-db"></a>Отображение баз данных в кластере HDInsight Hadoop

Чтобы отобразить базы данных, созданные в кластере HDInsight Hadoop в окне командной строки Hadoop, выполните в командной строке Hadoop приведенную ниже команду.

	hive -e "show databases;"

### <a name="#show-tables"></a>Отображение таблиц Hive в базе данных nyctaxidb

Для отображения таблиц в базе данных nyctaxidb выполните в командной строке Hadoop приведенную ниже команду.

	hive -e "show tables in nyctaxidb;"

Мы можем убедиться, что таблицы секционированы, выполнив приведенную ниже команду.

	hive -e "show partitions nyctaxidb.trip;"

Ожидаемые выходные данные показаны ниже.

	month=1
	month=10
	month=11
	month=12
	month=2
	month=3
	month=4
	month=5
	month=6
	month=7
	month=8
	month=9
	Time taken: 2.075 seconds, Fetched: 12 row(s)

Аналогично мы можем убедиться в том, что таблица fare секционирована, выполнив приведенную ниже команду.

	hive -e "show partitions nyctaxidb.fare;"

Ожидаемые выходные данные показаны ниже.

	month=1
	month=10
	month=11
	month=12
	month=2
	month=3
	month=4
	month=5
	month=6
	month=7
	month=8
	month=9
	Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Исследование данных и проектирование признаков в Hive

>[AZURE.NOTE] Обычно эту задачу выполняет **специалист по обработке и анализу данных**.

Задачи, которые связаны с просмотром данных и созданием функций и которые относятся к данным, загруженным в таблицы Hive, вы можете выполнить с помощью запросов Hive. Вот примеры задач, о которых пойдет речь в этом разделе:

- Просмотр 10 верхних записей в обеих таблицах.
- Просмотрим распределение данных по нескольким полям в различных временных окнах.
- Исследуем качество данных по полям долготы и широты.
- Создадим метки двоичной и мультиклассовой классификации на основе **tip\_amount**.
- Создание функций путем вычисления дальности поездки.

### Исследование: просмотр 10 верхних записей в таблице trip

>[AZURE.NOTE] Обычно эту задачу выполняет **специалист по обработке и анализу данных**.

Чтобы увидеть, как выглядят данные, мы изучим по 10 записей из каждой таблицы. Чтобы просмотреть записи, выполните приведенные ниже два запроса по отдельности в консоли командной строки Hadoop из каталога Hive.

Чтобы получить первые 10 записей в таблице «trip» за первый месяц:

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Чтобы получить первые 10 записей в таблице «fare» за первый месяц:

	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Часто полезно сохранять записи в файл для удобного просмотра. Это достигается небольшим изменением приведенного выше запроса:

	hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### Исследование: просмотр количества записей в каждом из 12 разделов

>[AZURE.NOTE] Обычно эту задачу выполняет **специалист по обработке и анализу данных**.

Интерес представляет варьирование числа поездок в течение календарного года. Группирование по месяцам позволяет увидеть, как выглядит такое распределение поездок.

	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Мы получаем приведенные ниже выходные данные.

	1       14776615
	2       13990176
	3       15749228
	4       15100468
	5       15285049
	6       14385456
	7       13823840
	8       12597109
	9       14107693
	10      15004556
	11      14388451
	12      13971118
	Time taken: 283.406 seconds, Fetched: 12 row(s)

Здесь первый столбец обозначает месяц, а второй содержит количество поездок за этот месяц.

Кроме того, мы можем подсчитать общее число записей в нашем наборе данных о поездках, выполнив следующую команду в командной строке каталога Hive.

	hive -e "select count(*) from nyctaxidb.trip;"

Мы получаем такой результат:

	173179759
	Time taken: 284.017 seconds, Fetched: 1 row(s)

Чтобы проверить количество записей, мы можем с помощью команд, подобных показанным для набора данных о поездках (trip), выполнить запросы Hive в командной строке каталога Hive для набора данных о тарифах (fare).

	hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Мы получаем приведенные ниже выходные данные.

	1       14776615
	2       13990176
	3       15749228
	4       15100468
	5       15285049
	6       14385456
	7       13823840
	8       12597109
	9       14107693
	10      15004556
	11      14388451
	12      13971118
	Time taken: 253.955 seconds, Fetched: 12 row(s)

Обратите внимание, что для обоих наборов данных возвращается в точности одинаковое количество поездок в месяц. Это первое подтверждение правильности загрузки данных.

Подсчет общего числа записей в наборе данных о тарифах можно выполнить с помощью приведенной ниже команды в командной строке каталога Hive.

	hive -e "select count(*) from nyctaxidb.fare;"

Мы получаем такой результат.

	173179759
	Time taken: 186.683 seconds, Fetched: 1 row(s)

Совпадает и общее число записей в обеих таблицах. Это второе подтверждение правильности загрузки данных.

### Просмотр: распределение поездок по параметру medallion

>[AZURE.NOTE] Обычно эту задачу выполняет **специалист по обработке и анализу данных**.

В этом примере определяются медальоны (номера такси) с более чем 100 поездками за заданный период времени. Этот запрос выигрывает от доступа к секционированной таблице, поскольку в нем содержится условие, заданное переменной раздела **month**. Результаты запроса записываются в локальный файл queryoutput.tsv в `C:\temp` на головном узле.

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Вот содержимое файла *sample\_hive\_trip\_count\_by\_medallion.hql* для проверки.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100
	ORDER BY med_count desc;

Медальон в наборе данных о такси Нью-Йорка идентифицирует уникальный автомобиль такси. Мы можем определить, какие автомобили востребованы, запросив, какие из них выполнили более определенного количества поездок за определенный период времени. В следующем примере идентифицируются автомобили, которые выполнили более ста поездок за первые три месяца, и результаты запроса сохраняются в локальном файле C:\\temp\\queryoutput.tsv.

Вот содержимое файла *sample\_hive\_trip\_count\_by\_medallion.hql* для проверки.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100
	ORDER BY med_count desc;

В командной строке каталога Hive выполните приведенную ниже команду.

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### Просмотр: распределение поездок по параметрам medallion и hack\_license

>[AZURE.NOTE] Обычно эту задачу выполняет **специалист по обработке и анализу данных**.

При исследовании набора данных нам часто требуется проверить количество вхождений тех или иных групп значений. В этом разделе приводится пример того, как сделать это для автомобилей и водителей.

Файл *sample\_hive\_trip\_count\_by\_medallion\_license.hql* группирует набор данных о тарифах по параметрам medallion и hack\_license и возвращает количество вхождений каждой из комбинаций. Ниже приведено его содержимое.

    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

Этот запрос возвращает комбинации автомобиля и конкретного водителя, упорядоченных по убыванию количества поездок.

В командной строке каталога Hive выполните приведенную ниже команду.

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Результаты запроса записываются в локальный файл C:\\temp\\queryoutput.tsv.

### Исследование: оценка качества данных через проверку наличия недопустимых записей долготы и широты

>[AZURE.NOTE] Обычно эту задачу выполняет **специалист по обработке и анализу данных**.

Общей целью исследовательского анализа данных является отсеивание недопустимых или неверных записей. Пример в этом разделе определяет, содержатся ли в полях долготы (longitude) и широты (latitude) значения, указывающие на местоположения далеко за пределами региона Нью-Йорка. Так как вероятно, что в таких записях будут содержаться ошибочные значения долготы и широты, нам следует устранить их из любых данных, которые будут использоваться для моделирования.

Вот содержимое файла *sample\_hive\_quality\_assessment.hql* для проверки.

	    SELECT COUNT(*) FROM nyctaxidb.trip
	    WHERE month=1
	    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
	    OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


В командной строке каталога Hive выполните приведенную ниже команду.

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

Аргумент *-S* этой команды скрывает окно состояния задач Hive Map/Reduce. Это очень полезно, так как отображение запроса Hive становится более удобочитаемым.

### Исследования: двоичные классовые распределения чаевых за поездки

**Примечание.** Обычно эту задачу выполняет **специалист по обработке и анализу данных**.

Касательно проблемы двоичной классификации, изложенной в разделе [Примеры задач прогнозирования](machine-learning-data-science-process-hive-walkthrough.md#mltasks), полезно знать, были ли выплачены чаевые за поездку. Распределение чаевых является двоичным:

* чаевые выплачены (класс 1, tip\_amount > $0);
* чаевых нет (класс 0, tip\_amount = $0).

Для этого следует использовать приведенный ниже файл *sample\_hive\_tipped\_frequencies.hql*.

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

В командной строке каталога Hive выполните приведенную ниже команду.

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### Исследование: классовые распределения при мультиклассовой настройке

**Примечание.** Обычно эту задачу выполняет **специалист по обработке и анализу данных**.

Для проблемы мультиклассовой классификации, изложенной в разделе [Примеры задач прогнозирования](machine-learning-data-science-process-hive-walkthrough.md#mltasks), этот набор данных тоже поддается естественной классификации, если нам нужно спрогнозировать сумму выплачиваемых чаевых. Для определения диапазонов чаевых в запросе можно использовать ячейки. Для получения классовых распределений различных диапазонов чаевых мы используем файл *sample\_hive\_tip\_range\_frequencies.hql*. Ниже приведено его содержимое.

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

В консоли командной строки Hadoop выполните приведенную ниже команду.

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### Исследование: вычисление расстояния по прямой между двумя местоположениями, заданными долготой и широтой

**Примечание.** Обычно эту задачу выполняет **специалист по обработке и анализу данных**.

Наличие меры расстояния по прямой позволяет нам находить расхождение между ним и фактическим расстоянием поездки. Мы мотивируем использование этой характеристики тем, что пассажир с меньшей вероятностью выплатит чаевые, если определит, что водитель намеренно вез его намного более длинным маршрутом.

Для просмотра сравнения между фактическим расстоянием поездки и [расстоянием гаверсинуса](http://en.wikipedia.org/wiki/Haversine_formula) между двумя точками долготы и широты (расстоянием по «большому кругу») мы используем доступные в Hive тригонометрические функции (результат см. ниже).

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

В приведенном выше запросе R — это радиус Земли в милях, а число пи (pi) преобразуется в радианы. Обратите внимание, что точки долготы и широты «фильтруются» для удаления значений, расположенных далеко от региона Нью-Йорка.

В данном случае мы запишем результаты в каталог с именем «queryoutputdir». Последовательность команд, приведенных ниже, сначала создает этот выходной каталог, а затем выполняет команду Hive.

В командной строке каталога Hive выполните приведенную ниже команду.

	hdfs dfs -mkdir wasb:///queryoutputdir

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Результаты запроса записываются в 9 больших двоичных объектов Azure с ***queryoutputdir/000000\_0*** по ***queryoutputdir/000008\_0*** в используемом по умолчанию контейнере кластера Hadoop.

Чтобы просмотреть размер отдельных больших двоичных объектов, мы выполняем приведенную ниже команду в командной строке каталога Hive.

	hdfs dfs -ls wasb:///queryoutputdir

Чтобы просмотреть содержимое заданного файла, скажем 000000\_0, мы используем команду Hadoop `copyToLocal` (см. ниже).

	hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

**Предупреждение.** `copyToLocal` может выполняться очень медленно для больших файлов, и использовать его с ними не рекомендуется.

Ключевое преимущество размещения этих данных в большом двоичном объекте Azure заключается в том, что мы можем исследовать данные в Машинном обучении Azure с помощью модуля [Import Data][import-data] \(Импорт данных).


## <a name="#downsample"></a>Сокращение выборки и построение моделей в машинном обучении Azure

**Примечание.** Обычно эту задачу выполняет **специалист по обработке и анализу данных**.

После исследовательской фазы анализа данных мы готовы к сокращению их выборки для построения моделей в машинном обучении Azure. В этом разделе мы покажем, как использовать запрос Hive для сокращения выборки данных, к которым затем будет обращаться модуль [Import Data][import-data] \(Импорт данных) в Машинном обучении Azure.

### Сокращение выборки данных

Эта процедура состоит из двух шагов. Сначала мы соединяем таблицы **nyctaxidb.trip** и **nyctaxidb.fare** по трем ключам, присутствующим во всех записях: medallion, hack\_license и pickup\_datetime. Затем мы создаем метку двоичной классификации **tipped** и метку мультиклассовой классификации **tip\_class**.

Чтобы использовать данные сокращенной выборки непосредственно из модуля [Import Data][import-data] \(Импорт данных) в Машинном обучении Azure, необходимо сохранить результаты приведенного выше запроса во внутренней таблице Hive. В дальнейшем мы создаем внутреннюю таблицу Hive и заполняем ее содержимое объединенными данными с сокращенной выборкой.

Запрос применяет стандартные функции Hive непосредственно для формирования часа дня, недели года, дня недели (1 — понедельник, 7 — воскресенье) из поля pickup\_datetime, а также расстояния по прямой между местоположениями посадки и высадки. Полный список определяемых пользователем функций см. на странице [Руководство по языку для определяемых пользователем функций](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Затем запрос сокращает выборку данных так, чтобы результаты запроса можно было загрузить в Студию машинного обучения Azure. В студию импортируется только около 1 % исходного набора данных.

Ниже приведено содержимое файла *sample\_hive\_prepare\_for\_aml\_full.hql*, подготавливающего данные к построению моделей в машинном обучении Azure.

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
		)
		row format delimited fields terminated by ','
		lines terminated by '\n'
		stored as textfile;

		--- now insert contents of the join into the above internal table

    	insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
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
        t.direct_distance,
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
        select
		medallion,
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
		${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
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

Выполнение этого запроса в командной строке каталога Hive.

	hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Теперь у нас есть внутренняя таблица nyctaxidb.nyctaxi\_downsampled\_dataset, к которой можно получить доступ с помощью модуля [Import Data][import-data] \(Импорт данных) в Машинном обучении Azure. Кроме того, мы можем использовать этот набор данных для построения моделей машинного обучения.

### Использование модуля "Импорт данных" в Машинном обучении Azure для доступа к данным сокращенной выборки

В качестве предварительного условия для выполнения запросов Hive в модуле [Import Data][import-data] \(Импорт данных) Машинного обучения Azure нам понадобится доступ к рабочей области Машинного обучения Azure, а также доступ к учетным данным кластера и связанной с ним учетной записи хранения.

Ниже приведены некоторые сведения о модуле [Import Data][import-data] \(Импорт данных) и входных параметрах.

**URI сервера HCatalog**: если именем кластера является abc123, то это просто: https://abc123.azurehdinsight.net.

**Имя учетной записи пользователя Hadoop**: имя пользователя, выбранное для кластера (**не** имя пользователя удаленного доступа).

**Пароль учетной записи пользователя Hadoop**: пароль, выбранный для кластера (**не** пароль удаленного доступа).

**Расположение выходных данных**: этим расположением выбирается Azure.

**Имя учетной записи хранения Azure**: имя учетной записи хранения по умолчанию, связанной с кластером.

**Имя контейнера Azure**: имя контейнера по умолчанию для кластера, которое обычно совпадает с именем кластера. Для кластера с именем «abc123» это будет просто abc123.

**Важное примечание**. **Любая таблица, к которой нам требуется отправить запрос с помощью модуля [Import Data][import-data] \(Импорт данных) в Машинном обучении Azure, должна быть внутренней таблицей**. Определить, является ли таблица T в базе данных D.db внутренней таблицей, можно приведенным ниже образом.

В командной строке каталога Hive выполните такую команду.

	hdfs dfs -ls wasb:///D.db/T

Если таблица является внутренней и заполнена, ее содержимое должно отобразиться здесь. Другой способ определить, является ли таблица внутренней, — использовать обозреватель хранилищ Azure. С его помощью перейдите к используемому по умолчанию имени контейнера кластера, а затем выполните фильтрацию по имени таблицы. Если таблица и ее содержимое отображаются, это подтверждает, что таблица является внутренней.

Ниже приведен снимок запроса Hive и модуля [Import Data][import-data] \(Импорт данных).

![](./media/machine-learning-data-science-process-hive-walkthrough/1eTYf52.png)

Обратите внимание, что, так как наши данные с сокращенной выборкой располагаются в контейнере по умолчанию, результирующий запрос Hive в машинном обучении Azure будет очень простым — «SELECT * FROM nyctaxidb.nyctaxi\_downsampled\_data».

Теперь набор данных можно использовать в качестве отправной точки для построения моделей машинного обучения.

### <a name="mlmodel"></a>Построение моделей в компоненте машинного обучения Azure

Теперь мы можем перейти к построению и развертыванию модели в [компоненте машинного обучения Azure](https://studio.azureml.net). Данные готовы к использованию для решения задач прогнозирования, определенных выше.

**1. Двоичная классификация**: спрогнозировать, были ли выплачены чаевые за поездку.

**Используемое средство обучения:** двухклассовая логистическая регрессия.

а. Для нашей задачи целевой меткой (меткой класса) будет «tipped» (чаевые выплачены). В нашем исходном наборе данных с сокращенной выборкой есть несколько столбцов, содержащих целевые утечки сведений для данного эксперимента по классификации. В частности: tip\_class, tip\_amount и total\_amount раскрывают информацию о целевой метке, которая недоступна во время тестирования. Мы удаляем эти столбцы из рассмотрения с помощью модуля [Select Columns in Dataset][select-columns] \(Выбор столбцов в наборе данных).

На снимке ниже показан наш эксперимент по прогнозированию выплаты чаевых за данную поездку.

![](./media/machine-learning-data-science-process-hive-walkthrough/QGxRz5A.png)

b. Для этого эксперимента распределение наших целевых меток составляло примерно 1:1.

На снимке ниже показано распределение меток класса чаевых для задачи двоичной классификации.

![](./media/machine-learning-data-science-process-hive-walkthrough/9mM4jlD.png)

В результате мы получаем AUC величиной 0,987, как показано на рисунке ниже.

![](./media/machine-learning-data-science-process-hive-walkthrough/8JDT0F8.png)

**2. Мультиклассовая классификация**: прогнозирование диапазона сумм чаевых за поездку с использованием заранее определенных классов.

**Используемое средство обучения:** мультиклассовая логистическая регрессия.

а. Для этой задачи нашей целевой меткой (меткой класса) будет tip\_class, которая может принимать одно из пяти значений (0, 1, 2, 3, 4). Как и в случае двоичной классификации, у нас есть несколько столбцов с утечкой целевой информации для этого эксперимента. В частности: tipped, tip\_amount и total\_amount раскрывают информацию о целевой метке, которая недоступна во время тестирования. Мы удаляем эти столбцы с помощью модуля [Select Columns in Dataset][select-columns] \(Выбор столбцов в наборе данных).

На снимке ниже показан наш эксперимент по прогнозированию ячейки, в которую вероятнее всего попадет сумма чаевых (класс 0: tip = $0, класс 1 : tip > $0 и tip <= $5, класс 2: tip > $5 и tip <= $10, класс 3: tip > $10 и tip <= $20, класс 4: tip > $20)

![](./media/machine-learning-data-science-process-hive-walkthrough/5ztv0n0.png)

Теперь мы покажем, как выглядит наше фактическое тестовое классовое распределение. Мы видим, что в то время как классы 0 и 1 являются наиболее распространенными, другие классы редки.

![](./media/machine-learning-data-science-process-hive-walkthrough/Vy1FUKa.png)

b. Для этого эксперимента мы используем матрицу неточностей для проверки точности наших прогнозов. Такой способ показан ниже.

![](./media/machine-learning-data-science-process-hive-walkthrough/cxFmErM.png)

Обратите внимание, что при вполне неплохой точности для наиболее распространенных классов модель не особо хорошо «учится» на более редких классах.


**3. Задача регрессии**: спрогнозировать сумму чаевых, выплаченных за поездку.

**Используемое средство обучения:** усиленное дерево принятия решений.

а. Для этой задачи целевой меткой (меткой класса) будет «tip\_amount» (сумма чаевых). Целевые утечки в данном случае: tipped, tip\_class, total\_amount; все эти переменные раскрывают информацию о сумме чаевых, которая обычно недоступна во время тестирования. Мы удаляем эти столбцы с помощью модуля [Select Columns in Dataset][select-columns] \(Выбор столбцов в наборе данных).

На снимке ниже показан наш эксперимент по прогнозированию суммы выплаченных чаевых.

![](./media/machine-learning-data-science-process-hive-walkthrough/11TZWgV.png)

b. Для задач регрессии мы измеряем величины точности нашего прогноза, рассматривая квадратичную ошибку в прогнозах, коэффициент детерминации и тому подобное. Они показаны ниже.

![](./media/machine-learning-data-science-process-hive-walkthrough/Jat9mrz.png)

Мы видим, что коэффициент детерминации равен 0,709, что подразумевает, что коэффициенты нашей модели объясняют примерно 71% дисперсии.

**Важное примечание.** Чтобы узнать больше о машинном обучении Azure, о доступе к машинному обучению и его использовании, см. статью [Что такое машинное обучение](machine-learning-what-is-machine-learning.md). Весьма полезным ресурсом для экспериментов с Машинным обучением Azure является [коллекция Cortana Intelligence](https://gallery.cortanaintelligence.com/). Коллекция охватывает спектр экспериментов и служит подробным введением в диапазон возможностей машинного обучения Azure.

## Сведения о лицензии

Доступ к этому учебнику и включенным в него скриптам предоставляет корпорация Майкрософт на основании лицензии свободного ПО. Дополнительные сведения см. в файле LICENSE.txt в каталоге примеров кода на сайте GitHub.

## Ссылки

•	[Andrés Monroy NYC Taxi Trips Download Page](http://www.andresmh.com/nyctaxitrips/)  
•	[FOILing NYC’s Taxi Trip Data by Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
•	[NYC Taxi and Limousine Commission Research and Statistics](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0921_2016-->