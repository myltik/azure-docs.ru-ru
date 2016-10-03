<properties
	pageTitle="Процесс обработки и анализа данных группы на практике: использование хранилища данных SQL | Microsoft Azure"
	description="Расширенный процесс аналитики и технологии в действии"  
	services="machine-learning"
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
	ms.date="06/24/2016"
	ms.author="bradsev;hangzh;weig"/>


# Процесс обработки и анализа данных группы на практике: использование хранилища данных SQL

В этом руководстве описаны шаги по созданию и развертыванию модели машинного обучения с использованием хранилища данных SQL для общедоступного набора данных [Поездки такси Нью-Йорка](http://www.andresmh.com/nyctaxitrips/). Модель двоичной классификации позволяет спрогнозировать получение чаевых за поездку. Кроме того, здесь рассматриваются модели многоклассовой классификации и регрессии, которые помогают спрогнозировать распространение сумм чаевых, оплачиваемых за поездку.

Ниже представлена процедура рабочего процесса [обработки и анализа данных группы (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/). Здесь мы покажем, как настроить среду обработки и анализа данных, загрузить данные в хранилище SQL, а также изучить данные с помощью хранилища данных SQL и IPython Notebook и спроектировать признаки в модель. Затем мы рассмотрим, как создать и развернуть модель в службе машинного обучения Azure.


## <a name="dataset"></a>Набор данных "Поездки такси Нью-Йорка"

Данные о поездках такси Нью-Йорка содержатся в сжатых CSV-файлах размером около 20 ГБ (примерно 48 ГБ в несжатом виде), которые включают в себя сведения о более 173 млн отдельных поездок и платежах за каждую поездку. В каждой записи о поездке содержатся данные о расположении пунктов посадки и высадки, а также времени, анонимизированный номер лицензии водителя и номер медальона (уникальный идентификатор такси). Данные включают в себя все поездки за 2013 год и предоставляются в виде следующих двух наборов данных за каждый месяц:

1. CSV-файл **trip\_data.csv** содержит подробную информацию о поездке, например число пассажиров, пункты отправления и назначения, продолжительность поездки и ее расстояние. Вот несколько примеров записей:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. CSV-файл **trip\_fare** содержит подробную информацию об оплате каждой поездки, такую как тип оплаты, сумма тарифа, надбавка и налоги, чаевые и пошлины, а также общая выплаченная сумма. Вот несколько примеров записей:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

**Уникальный ключ** для соединения trip\_data и trip\_fare состоит из следующих трех полей:

- medallion,
- hack\_license и
- pickup\_datetime.

## <a name="mltasks"></a>Определение трех типов задач прогнозирования

На основе *tip\_amount* мы сформулировали три проблемы прогнозирования, чтобы проиллюстрировать три типа задач моделирования.

1. **Двоичная классификация**: спрогнозировать, были ли выплачены чаевые за поездку, т. е. значение *tip\_amount*, превышающее 0 $, является позитивным примером, а значение *tip\_amount*, равняющееся 0 $, является негативным примером.

2. **Мультиклассовая классификация**: спрогнозировать диапазон суммы чаевых за поездку. Мы разделяем *tip\_amount* на пять ячеек или классов:

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Задача регрессии**: спрогнозировать сумму чаевых, выплаченных за поездку.


## <a name="setup"></a>Настройка среды обработки и анализа данных Azure для расширенной аналитики

Чтобы настроить среду обработки и анализа данных в Azure, выполните следующие шаги.

**Создайте собственную учетную запись хранилища BLOB-объектов Azure**

- Во время подготовки своего хранилища BLOB-объектов Azure выберите географическое расположение как можно ближе к **юго-центральной части США**, где будут храниться данные о такси Нью-Йорка. С помощью AzCopy данные будут скопированы из контейнера общедоступного хранилища BLOB-объектов в контейнер в вашей учетной записи хранения. Чем ближе хранилище BLOB-объектов Azure к юго-центральной части США, тем быстрее будет выполнена эта задача (шаг 4).
- Чтобы создать учетную запись хранения Azure, выполните шаги, описанные в статье [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md). Убедитесь, что вы записали значения данных учетной записи хранения, так как они потребуются позже в этом пошаговом руководстве.

  - **Имя учетной записи хранения**
  - **Ключ учетной записи хранения**
  - **Имя контейнера** (контейнер в хранилище BLOB-объектов Azure, где будут храниться данные)

**Подготовьте экземпляр хранилища данных SQL Azure.** Во время его подготовки следуйте инструкциям в статье [Создание хранилища данных SQL](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Убедитесь, что записали учетные данные хранилища данных SQL, так как они потребуются на следующих шагах:

  - **имя сервера**: <имя сервера>.database.windows.net;
  - **имя (базы данных) хранилища данных SQL;**
  - **Имя пользователя**
  - **Пароль**

**Установите Visual Studio 2015 и SQL Server Data Tools.** Инструкции можно найти в статье [Установка Visual Studio 2015 и SSDT для хранилища данных SQL](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Установите подключение к своему хранилищу данных SQL Azure с помощью Visual Studio.** Инструкции приведены в шагах 1 и 2 статьи [Connect to Azure SQL Data Warehouse with Visual Studio](../sql-data-warehouse/sql-data-warehouse-connect-overview.md) (Подключение к хранилищу данных SQL Azure с помощью Visual Studio).

>[AZURE.NOTE] Выполните следующий SQL-запрос в базе данных, созданной в хранилище данных SQL (вместо запроса, указанного на шаге 3 раздела о подключении), чтобы **создать главный ключ**.

	BEGIN TRY
	       --Try to create the master key
	    CREATE MASTER KEY
	END TRY
	BEGIN CATCH
	       --If the master key exists, do nothing
	END CATCH;

**Создайте рабочую область Машинного обучения Azure, используя подписку Azure.** Инструкции можно найти в статье [Создание рабочей области машинного обучения Azure](machine-learning-create-workspace.md).

## <a name="getdata"></a>Загрузка данных в хранилище данных SQL

Откройте командную консоль Windows PowerShell. Выполните следующие команды PowerShell, чтобы скачать файлы примеров сценариев SQL, доступные на сайте Github, в локальный каталог, указанный с помощью параметра *-DestDir*. Значение параметра *-DestDir* можно изменить и указать любой другой локальный каталог. Если *-DestDir* отсутствует, он будет создан в рамках сценария PowerShell.

>[AZURE.NOTE] При выполнении следующего сценария PowerShell может потребоваться **запуск от имени администратора**, если для создания каталога *DestDir* или записи в него нужны права администратора.

	$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
	$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
	$wc = New-Object System.Net.WebClient
	$wc.DownloadFile($source, $ps1_dest)
	.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

После успешного выполнения текущий рабочий каталог изменится на *-DestDir*. Должен отобразиться экран, аналогичный показанному ниже:

![][19]

В каталоге *-DestDir* выполните следующий сценарий PowerShell в режиме администратора:

	./SQLDW_Data_Import.ps1

При первом выполнении сценария PowerShell будет предложено ввести данные хранилища данных SQL Azure и учетной записи хранилища BLOB-объектов Azure. После первого завершения этого сценария PowerShell введенные учетные данные будут записаны в файл конфигурации SQLDW.conf в используемом рабочем каталоге. При последующем запуске этого файла сценария PowerShell можно будет считать все необходимые параметры из файла конфигурации. Если некоторые параметры нужно изменить, их можно ввести на экране, когда отобразится запрос. Для этого нужно удалить файл конфигурации и ввести значения параметров по запросу. Кроме того, значения параметров можно изменить в файле SQLDW.conf в каталоге *-DestDir*.

>[AZURE.NOTE] Чтобы избежать конфликтов имени схемы с именами, которые уже есть в хранилище данных SQL Azure, при каждом запуске во время чтения параметров из файла SQLDW.conf к имени схемы добавляется случайное число из 3 цифр. Затем это имя используется по умолчанию. Сценарий PowerShell может запросить имя схемы. Имя указывается по усмотрению пользователя.

Запуск этого файла **сценария PowerShell** предусматривает выполнение следующих задач:

- **Скачивание и установка AzCopy**, если она еще не установлена.

		$AzCopy_path = SearchAzCopy
    	if ($AzCopy_path -eq $null){
       		Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
        	InstallAzCopy
        	$AzCopy_path = SearchAzCopy
    	}
			$env_path = $env:Path
			for ($i=0; $i -lt $AzCopy_path.count; $i++){
				if ($AzCopy_path.count -eq 1){
					$AzCopy_path_i = $AzCopy_path
				} else {
					$AzCopy_path_i = $AzCopy_path[$i]
				}
				if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
					Write-Host $AzCopy_path_i 'not in system path, add it...'
					[Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
					$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
					$env_path = $env:Path
				}

- **Копирование данных в учетную запись частного хранилища BLOB-объектов** из общедоступного BLOB-объекта с помощью AzCopy.

		Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
		$start_time = Get-Date
		AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
		$end_time = Get-Date
    	$time_span = $end_time - $start_time
    	$total_seconds = [math]::Round($time_span.TotalSeconds,2)
    	Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
    	Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"


- **Загрузка данных с помощью Polybase (путем выполнения LoadDataToSQLDW.sql) в хранилище данных SQL Azure** из вашей учетной записи частного хранилища BLOB-объектов c помощью следующих команд.

	- Создание схемы

			EXEC (''CREATE SCHEMA {schemaname};'');

	- Создание учетных данных для определенной базы данных

			CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
			WITH IDENTITY = ''asbkey'' ,
			Secret = ''{StorageAccountKey}''

	- Создание внешнего источника данных для BLOB-объекта хранилища Azure

			CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
			WITH
			(
    			TYPE = HADOOP,
    			LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
    			CREDENTIAL = {KeyAlias}
			)
			;

			CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
			WITH
			(
    			TYPE = HADOOP,
    			LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
    			CREDENTIAL = {KeyAlias}
			)
			;

	- Создание формата внешнего файла для CSV-файла. Данные не сжимаются, а поля разделяются символом вертикальной черты.

			CREATE EXTERNAL FILE FORMAT {csv_file_format}
			WITH
			(   
    			FORMAT_TYPE = DELIMITEDTEXT,
    			FORMAT_OPTIONS  
    			(
        			FIELD_TERMINATOR ='','',
        			USE_TYPE_DEFAULT = TRUE
    			)
			)
			;

	- Создание внешних таблиц (trip и fare) для хранения набора данных такси Нью-Йорка в хранилище BLOB-объектов Azure.

			CREATE EXTERNAL TABLE {external_nyctaxi_fare}
			(
				medallion varchar(50) not null,
				hack_license varchar(50) not null,
				vendor_id char(3),
				pickup_datetime datetime not null,
				payment_type char(3),
				fare_amount float,
				surcharge float,
				mta_tax float,
				tip_amount float,
				tolls_amount float,
				total_amount float
			)
			with (
    			LOCATION    = ''/nyctaxifare/'',
    			DATA_SOURCE = {nyctaxi_fare_storage},
    			FILE_FORMAT = {csv_file_format},
				REJECT_TYPE = VALUE,
				REJECT_VALUE = 12     
			)  


			CREATE EXTERNAL TABLE {external_nyctaxi_trip}
			(
       			medallion varchar(50) not null,
       			hack_license varchar(50)  not null,
       			vendor_id char(3),
       			rate_code char(3),
       			store_and_fwd_flag char(3),
       			pickup_datetime datetime  not null,
       			dropoff_datetime datetime,
       			passenger_count int,
       			trip_time_in_secs bigint,
       			trip_distance float,
       			pickup_longitude varchar(30),
       			pickup_latitude varchar(30),
       			dropoff_longitude varchar(30),
       			dropoff_latitude varchar(30)
			)
			with (
    			LOCATION    = ''/nyctaxitrip/'',
    			DATA_SOURCE = {nyctaxi_trip_storage},
    			FILE_FORMAT = {csv_file_format},
    			REJECT_TYPE = VALUE,
				REJECT_VALUE = 12         
			)

	- Загрузка данных из внешних таблиц хранилища BLOB-объектов Azure в хранилище данных SQL.

			CREATE TABLE {schemaname}.{nyctaxi_fare}
			WITH
			(   
    			CLUSTERED COLUMNSTORE INDEX,
				DISTRIBUTION = HASH(medallion)
			)
			AS
			SELECT *
			FROM   {external_nyctaxi_fare}
			;

			CREATE TABLE {schemaname}.{nyctaxi_trip}
			WITH
			(   
    			CLUSTERED COLUMNSTORE INDEX,
				DISTRIBUTION = HASH(medallion)
			)
			AS
			SELECT *
			FROM   {external_nyctaxi_trip}
			;

	- Создание примера таблицы данных (NYCTaxi\_Sample) и вставка данных из него. Предполагает выбор SQL-запросов на таблицы trip и fare. (Этот пример таблицы необходимо будет использовать при выполнении некоторых шагов этого руководства.)

			CREATE TABLE {schemaname}.{nyctaxi_sample}
			WITH
			(   
    			CLUSTERED COLUMNSTORE INDEX,
				DISTRIBUTION = HASH(medallion)
			)
			AS
			(
	    		SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
				tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
				tip_class = CASE
						WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
	    		FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
    			WHERE datepart("mi",t.pickup_datetime) = 1
				AND t.medallion = f.medallion
    			AND   t.hack_license = f.hack_license
    			AND   t.pickup_datetime = f.pickup_datetime
    			AND   pickup_longitude <> ''0''
        		AND   dropoff_longitude <> ''0''
			)
			;

Географическое расположение учетных записей хранения влияет на время загрузки.

>[AZURE.NOTE] От географического расположения учетной записи частного хранилища BLOB-объектов зависит длительность копирования данных из учетной записи общедоступного хранилища в учетную запись частного хранилища. Оно может длиться дольше 15 минут. Загрузка данных из учетной записи хранения в хранилище данных SQL Azure может занимать больше 20 минут.

Необходимо решить, какие действия предпринимать, если исходные и конечные файлы совпадают.

>[AZURE.NOTE] Если CSV-файлы, копируемые из общедоступного хранилища BLOB-объектов в учетную запись частного хранилища BLOB-объектов, уже существуют в учетной записи частного хранилища BLOB-объектов, AzCopy выведет запрос на их перезапись. Если перезаписывать их не нужно, по запросу системы введите **n**. Чтобы перезаписать **все** файлы, по запросу системы введите **a**. Чтобы перезаписать отдельные CSV-файлы, по запросу системы введите **y**.

![График № 21][21]

Можно использовать собственные данные. Если данные находятся на локальном компьютере в работающем приложении, AzCopy можно использовать для загрузки локальных данных в частное хранилище BLOB-объектов Azure. Для этого в команде AzCopy в файле сценария PowerShell необходимо лишь изменить расположение **источника** `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, указав для него локальный каталог, содержащий данные.


>[AZURE.TIP] Если данные уже размещены в частном хранилище BLOB-объектов Azure в работающем приложении, шаг AzCopy в сценарии PowerShell можно пропустить и напрямую загрузить данные в хранилище данных SQL Azure. В сценарий потребуется внести дополнительные изменения, чтобы настроить его согласно формату данных.


Этот сценарий Powershell также предусматривает использование сведений хранилища данных SQL Azure в файлах примеров исследований данных SQLDW\_Explorations.sql, SQLDW\_Explorations.ipynb и SQLDW\_Explorations\_Scripts.py. Таким образом эти три файла можно использовать сразу же по завершении сценария PowerShell.

После успешного выполнения вы увидите экран, аналогичный этому:

![][20]

## <a name="dbexplore"></a>Изучение данных и проектирование признаков в хранилище данных SQL Azure

В этом разделе мы исследуем данные и создадим признаки, используя SQL-запросы для хранилища данных SQL Azure с помощью **средств работы с данными Visual Studio**. Все SQL-запросы, используемые в этом разделе, можно найти в примере сценария *SQLDW\_Explorations.sql*. Этот файл скачан в локальный каталог во время выполнения сценария PowerShell. Кроме того, его также можно взять на веб-сайте [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Но в файл с Github не включены данные хранилища SQL Azure.

Подключитесь к хранилищу данных SQL Azure с помощью Visual Studio, используя имя входа и пароль хранилища данных SQL, и откройте **обозреватель объектов SQL** для подтверждения импорта базы данных и таблиц. Получите файл *SQLDW\_Explorations.sql*.

>[AZURE.NOTE] Чтобы открыть редактор запросов хранилища параллельных данных (PDW), используйте команду **New Query**, выбрав PDW в **обозревателе объектов SQL**. PDW не поддерживает стандартный редактор SQL-запросов.

В этом разделе мы выполним следующие задачи по исследованию данных и созданию признаков.

- Просмотрим распределение данных по нескольким полям в различных временных окнах.
- Исследуем качество данных по полям долготы и широты.
- Создадим метки двоичной и мультиклассовой классификации на основе **tip\_amount**.
- Создадим характеристики и вычислим/сравним расстояния поездок.
- Соединим две таблицы и извлечем случайную выборку, которая послужит основой для построения моделей.

### Проверка импорта данных

Эти запросы обеспечивают быструю проверку числа строк и столбцов в таблицах, заполненных ранее с помощью параллельного массового импорта Polybase.

	-- Report number of rows in table <nyctaxi_trip> without table scan
	SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

	-- Report number of columns in table <nyctaxi_trip>
	SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Выходные данные.** Вы должны получить 173 179 759 строк и 14 столбцов.

### Просмотр: Распределение поездок по параметру medallion

В этом примере запроса определяются медальоны (номера) такси, которые осуществили больше 100 поездок за заданный период времени. Запрос будет использовать секционированный доступ к таблице, поскольку он обусловлен схемой секционирования **pickup\_datetime**. При запросе к полному набору данных также будет задействовано сканирование секционированной таблицы и/или индекса.

	SELECT medallion, COUNT(*)
	FROM <schemaname>.<nyctaxi_fare>
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	GROUP BY medallion
	HAVING COUNT(*) > 100

**Выходные данные.** По результатам запроса возвращается таблица, строки которой содержат 13 369 медальонов (номеров такси) и количество поездок, осуществленных ими за 2013 год. В последнем столбце отображается счетчик поездок.

### Просмотр: распределение поездок по параметрам medallion и hack\_license

В этом примере определяются медальоны (номера) такси и номера hack\_license (лицензий) водителей, которые осуществили больше 100 поездок за заданный период времени.

	SELECT medallion, hack_license, COUNT(*)
	FROM <schemaname>.<nyctaxi_fare>
	WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
	GROUP BY medallion, hack_license
	HAVING COUNT(*) > 100

**Выходные данные.** По результатам запроса возвращается таблица, в 13 369 строках которой перечислены 13 369 автомобилей (водителей), осуществивших в 2013 году более 100 поездок. В последнем столбце отображается счетчик поездок.

### Оценка качества данных: проверка записей с неправильными значениями долготы и/или широты

В этом примере анализируется, содержится ли в каких-либо полях долготы и/или широты неверное значение (количество градусов должно быть в пределах от -90 до 90) или значение координат (0, 0).

	SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Выходные данные.** По результатам запроса возвращаются 837 467 поездок с недопустимыми значениями долготы и (или) широты.

### Изучение: распределение поездок с чаевыми и без чаевых

В этом примере пользователь узнает о соотношении количества поездок, когда водителю дали на чай, к количеству поездок, когда чаевых не было (пользователь ищет сведения за определенный период времени или, если нужны сведения за полный год, во всех данных набора). Это распределение отражает распределение двоичных меток, которые в дальнейшем будут использоваться для моделирования двоичной классификации.

	SELECT tipped, COUNT(*) AS tip_freq FROM (
	  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
	  FROM <schemaname>.<nyctaxi_fare>
	  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tipped

**Выходные данные.** По результатам запроса возвращаются следующие результаты по частотности чаевых за 2013 год: 90 447 622 поездок с чаевыми и 82 264 709 — без чаевых.

### Изучение: распределение классов и диапазонов чаевых

В этом примере вычисляется распределение диапазонов чаевых за заданный период времени (или по полному набору данных в случае охвата целого года). Это распределение классов меток, которое в дальнейшем будет использоваться для моделирования мультиклассовой классификации.

	SELECT tip_class, COUNT(*) AS tip_freq FROM (
		SELECT CASE
			WHEN (tip_amount = 0) THEN 0
			WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
			WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
			WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
			ELSE 4
		END AS tip_class
	FROM <schemaname>.<nyctaxi_fare>
	WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tip_class

**Выходные данные:**

|tip\_class | tip\_freq |
| --------- | -------|
|1 | 82230915 |
|2 | 6198803 |
|3 | 1932223 |
|0 | 82264625 |
|4 | 85765 |

### Изучение: вычисление и сравнение расстояния поездок

В этом примере значения долготы и широты начальных и конечных пунктов поездок преобразуются в географические точки SQL, вычисляется расстояние поездки по разности географических точек и возвращается случайная выборка результатов для сравнения. В примере результаты ограничиваются только допустимыми координатами с помощью запроса оценки качества данных, описанного ранее.

	/****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
	SET ANSI_NULLS ON
	GO

	SET QUOTED_IDENTIFIER ON
	GO

	IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
	  DROP FUNCTION fnCalculateDistance
	GO

	-- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
	CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

	RETURNS float
	AS
	BEGIN
	  	DECLARE @distance decimal(28, 10)
  		-- Convert to radians
  		SET @Lat1 = @Lat1 / 57.2958
  		SET @Long1 = @Long1 / 57.2958
  		SET @Lat2 = @Lat2 / 57.2958
  		SET @Long2 = @Long2 / 57.2958
  		-- Calculate distance
  		SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
  		--Convert to miles
  		IF @distance <> 0
  		BEGIN
    		SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
  		END
  		RETURN @distance
	END
	GO

	SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
	dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
	FROM <schemaname>.<nyctaxi_trip>
	WHERE datepart("mi",pickup_datetime)=1
	AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND pickup_longitude != '0' AND dropoff_longitude != '0'

### Проектирование признаков с помощью функций SQL

Иногда функции SQL могут быть полезны при проектировании признаков. В этом пошаговом руководстве мы определили функцию SQL для расчета прямого расстояния между расположениями посадки и высадки. Описанные ниже сценарии SQL можно выполнять в **средствах Visual Studio для работы с данными**.

Вот сценарий SQL для определения функции расстояния:

	SET ANSI_NULLS ON
	GO

	SET QUOTED_IDENTIFIER ON
	GO

	IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
	  DROP FUNCTION fnCalculateDistance
	GO

	-- User-defined function calculate the direct distance between two geographical coordinates.
	CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

	RETURNS float
	AS
	BEGIN
	  	DECLARE @distance decimal(28, 10)
  		-- Convert to radians
  		SET @Lat1 = @Lat1 / 57.2958
  		SET @Long1 = @Long1 / 57.2958
  		SET @Lat2 = @Lat2 / 57.2958
  		SET @Long2 = @Long2 / 57.2958
  		-- Calculate distance
  		SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
  		--Convert to miles
  		IF @distance <> 0
  		BEGIN
    		SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
  		END
  		RETURN @distance
	END
	GO

Вот пример вызова этой функции в SQL-запросе для создания признаков:

	-- Sample query to call the function to create features
	SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
	dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
	FROM <schemaname>.<nyctaxi_trip>
	WHERE datepart("mi",pickup_datetime)=1
	AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Выходные данные.** По результатам запроса формируется таблица (из 2 803 538 строк) с координатами посадки и высадки пассажиров и дальностью поездок в милях. Вот результаты для первых трех строк:

||pickup\_latitude | pickup\_longitude | dropoff\_latitude |dropoff\_longitude | DirectDistance |
|---| --------- | -------|-------| --------- | -------|
|1 | 40,731804 | –74,001083 | 40,736622 | –73,988953 | 0,7169601222 |
|2 | 40,715794 | –74,010635 | 40,725338 | –74,00399 | 0,7448343721 |
|3 | 40,761456 | –73,999886 | 40,766544 | –73,988228 | 0,7037227967 |



### Подготовка данных для построения модели

Следующий запрос соединяет таблицы **nyctaxi\_trip** и **nyctaxi\_fare**, создает метку двоичной классификации **tipped**, метку многоклассовой классификации **tip\_class**, а также извлекает выборку из полного соединенного набора данных. Выборка предполагает получение подмножества поездок на основе данных времени посадок. Этот запрос можно скопировать, а затем вставить непосредственно в модуль [Импорт данных][import-data] [Студии машинного обучения Azure](https://studio.azureml.net) для прямого приема данных из экземпляра базы данных SQL в Azure. Запрос исключает записи с неверными (0, 0) координатами.

	SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, 	f.total_amount, f.tip_amount,
	    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
	    CASE WHEN (tip_amount = 0) THEN 0
	        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
	        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
	        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
	        ELSE 4
	    END AS tip_class
	FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
	WHERE datepart("mi",t.pickup_datetime) = 1
	AND   t.medallion = f.medallion
	AND   t.hack_license = f.hack_license
	AND   t.pickup_datetime = f.pickup_datetime
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Когда вы будете готовы перейти к машинному обучению Azure, вы можете:

1. Сохранить окончательный запрос SQL для извлечения и выборки данных, скопировать и вставить этот запрос непосредственно в модуль [Импорт данных][import-data] в Машинном обучении Azure или
2. Сохранить выбранные и спроектированные данные, которые планируется использовать для построения модели в новой таблице хранилища данных SQL, и использовать новую таблицу в модуле [Импорт данных][import-data] в Машинном обучении Azure. Это уже сделано на одном из предыдущих шагов сценария PowerShell. Данные можно считать прямо из этой таблицы в модуле "Импорт данных".


## <a name="ipnb"></a>Просмотр данных и проектирование характеристик в IPython Notebook

В этом разделе мы изучим данные и спроектируем признаки, используя как Python, так и SQL-запросы, для созданного ранее хранилища данных SQL. Пример файла IPython Notebook с именем **SQLDW\_Explorations.ipynb** и файла сценария Python **SQLDW\_Explorations\_Scripts.py** скачаны в локальный каталог. Они также доступны на веб-сайте [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). В сценариях Python эти два файла идентичны. Файл сценария Python предоставляется, если нет сервера IPython Notebook. Эти два примера файлов Python предназначены для использования с **Python 2.7**.

Необходимые данные хранилища данных SQL Azure в примере IPython Notebook и файле сценария Python, скачанные на локальный компьютер, уже включены во время выполнения сценария PowerShell. Они выполняются без изменений.

Если ранее вы уже настроили рабочую область Машинного обучения Azure, пример IPython Notebook можно напрямую загрузить в службу IPython Notebook Машинного обучения Azure и там же запустить. Ниже приведены шаги по передаче в службу IPython Notebook Машинного обучения Azure.

1. Войдите в рабочую область Машинного обучения Azure, щелкните "Студия" в верхней части, а затем — NOTEBOOKS в левой части веб-страницы.

	![График № 22][22]

2. Щелкните вкладку "СОЗДАТЬ" в левом верхнем углу веб-страницы и выберите плитку Python 2. Затем введите имя для Notebook и щелкните значок галочки для создания пустого IPython Notebook.

	![График № 23][23]

3. Щелкните символ Jupyter в левом верхнем углу нового IPython Notebook.

	![График № 24][24]

4. Перетащите пример IPython Notebook на страницу **дерева** службы IPython Notebook машинного обучения Azure и нажмите кнопку **Отправить**. После этого пример IPython Notebook будет отправлен в службу IPython Notebook Машинного обучения Azure.

	![График № 25][25]

Чтобы запустить пример IPython Notebook или файл сценария Python, необходимо установить следующие пакеты Python. Если вы используете службу IPython Notebook Машинного обучения Azure, эти пакеты уже установлены.

	- pandas
	- numpy
	- matplotlib
	- pyodbc
	- PyTables

Во время создания расширенных аналитических решений с использованием данных больших объемов с помощью Машинного обучения Azure рекомендуется придерживаться такой последовательности действий:

- Считайте небольшую выборку данных во фрейм данных, размещенный в памяти.
- Выполните несколько визуализаций и просмотров с использованием выборки данных.
- Поэкспериментируйте с проектированием характеристик с использованием выборки данных.
- При изучении данных большого объема, манипуляциях с данными и проектировании признаков используйте Python, чтобы напрямую передавать SQL-запросы в хранилище данных SQL.
- Определите размер выборки, подходящий для создания модели машинного обучения Azure.

Ниже приведены несколько примеров изучения данных, визуализации данных и проектирования признаков. Дополнительные исследования данных можно найти в примере IPython Notebook и файле сценария Python.

### Инициализация учетных данных базы данных

Инициализируйте параметры подключения к базе данных в следующих переменных:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### Создание подключения к базе данных

Вот строка, которая создает подключение к базе данных.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### Сообщение числа строк и столбцов в таблице <nyctaxi\_trip>

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions
		WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
	''', conn)

	print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns
		WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
	''', conn)

	print 'Total number of columns = %d' % ncols.iloc[0,0]

- Общее число строк = 173179759
- Общее число столбцов = 14

### Сообщение числа строк и столбцов в таблице <nyctaxi\_fare>

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions
		WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
	''', conn)

	print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns
		WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
	''', conn)

	print 'Total number of columns = %d' % ncols.iloc[0,0]

- Общее число строк = 173179759
- Общее количество столбцов — 11.

### Считывание небольшой выборки данных из хранилища данных SQL

    t0 = time.time()

	query = '''
		SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
			f.tolls_amount, f.total_amount, f.tip_amount
		FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
		WHERE datepart("mi",t.pickup_datetime) = 1
		AND   t.medallion = f.medallion
		AND   t.hack_license = f.hack_license
		AND   t.pickup_datetime = f.pickup_datetime
	'''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Время чтения таблицы выборки — 14,096 495 секунды. Количество полученных строк и столбцов — 1000 и 21.

### Описательная статистика

Теперь все готово для изучения данных выборки. Начнем с рассмотрения описательной статистики для **trip\_distance** (или любых других выбранных полей).

    df1['trip_distance'].describe()

### Визуализация: пример блочной диаграммы

Далее рассмотрим блочную диаграмму расстояний поездок для визуализации квантилей.

    df1.boxplot(column='trip_distance',return_type='dict')

![График #1][1]

### Визуализация: пример графика распределения

Это график, на котором показано распределение и гистограмма для примеров расстояний выборки.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![График #2][2]

### Визуализация: гистограммы и линейные графики

В этом примере мы сегментируем расстояния поездок на пять ячеек и визуализируем результаты сегментирования.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Мы можем изобразить описанное выше распределение по ячейкам в виде гистограммы или линейного графика, выполнив

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![График #3][3]

и

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![График #4][4]

### Визуализация: примеры точечных диаграмм

Мы покажем точечную диаграмму между параметрами **trip\_time\_in\_secs** и **trip\_distance**, чтобы просмотреть, есть ли в этом случае корреляция

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![График #6][6]

Аналогично мы можем проверить связь между **rate\_code** и** trip\_distance**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![График #8][8]


### Изучение выборки данных с помощью SQL-запросов в IPython Notebook

В этом разделе мы рассмотрим распределение данных с использованием выборки, которая сохранена в созданной ранее таблице. Обратите внимание, что для подобных исследований можно использовать исходные таблицы.

#### Исследование: сообщение количества строк и столбцов в таблице выборки

	nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
	print 'Number of rows in sample = %d' % nrows.iloc[0,0]

	ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
	print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### Исследование: распределение поездок с чаевыми и без чаевых

	query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

	pd.read_sql(query, conn)

#### Изучение: распределение классов чаевых

	query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
	'''

	tip_class_dist = pd.read_sql(query, conn)

#### Исследование: построение диаграммы классового распределения чаевых

	tip_class_dist['tip_freq'].plot(kind='bar')

![График № 26][26]


#### Исследование: ежедневное распределение поездок

    query = '''
		SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
		FROM <schemaname>.<nyctaxi_sample>
		GROUP BY CONVERT(date, dropoff_datetime)
	'''

    pd.read_sql(query,conn)

#### Исследование: распределение поездок по параметру medallion

    query = '''
		SELECT medallion,count(*) AS c
		FROM <schemaname>.<nyctaxi_sample>
		GROUP BY medallion
	'''

	pd.read_sql(query,conn)

#### Изучение: распределение поездок по медальону и номеру лицензии водителя

	query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
	pd.read_sql(query,conn)


#### Просмотр: распределение поездок по времени

	query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
	pd.read_sql(query,conn)

#### Изучение: распределение поездок по расстоянию

	query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
	pd.read_sql(query,conn)

#### Изучение: распределение поездок по типу оплаты

	query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
	pd.read_sql(query,conn)

#### Проверка конечной формы таблицы с признаками

    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Построение моделей в компоненте машинного обучения Azure

Теперь мы готовы перейти к построению и развертыванию модели в [машинном обучении Azure](https://studio.azureml.net). Данные готовы для любой из задач прогнозирования, определенных ранее:

1. **Двоичная классификация**: спрогнозировать, были ли выплачены чаевые за поездку.

2. **Мультиклассовая классификация**: спрогнозировать диапазон выплаченных чаевых в соответствии с ранее определенными классами.

3. **Задача регрессии**: спрогнозировать сумму чаевых, выплаченных за поездку.



Чтобы начать упражнение по моделированию, войдите в рабочую область **Машинного обучения Azure**. Если вы еще не создали рабочую область машинного обучения, см. статью [Создание рабочей области Azure ML](machine-learning-create-workspace.md).

1. Для начала работы с машинным обучением Azure см. статью [Что такое Студия машинного обучения Azure?](machine-learning-what-is-ml-studio.md)

2. Войдите в [Студию машинного обучения Azure](https://studio.azureml.net).

3. На главной странице Студии содержится множество информации, видеороликов, учебников, ссылок на справочник модулей и другие ресурсы. Дополнительные сведения о Машинном обучении Azure см. в [центре документации по Машинному обучению Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

Типичный обучающий эксперимент предусматривает следующие шаги:

1. Создать **+НОВЫЙ** эксперимент.
2. Получить данные в Машинном обучении Azure.
3. Предварительно обработать, преобразовать данные и выполнить необходимые манипуляции с ними.
4. Создать необходимые характеристики.
5. Разбить данные на учебные/проверочные/тестовые наборы данных (или иметь отдельные наборы данных для каждой из этих целей).
6. Выбрать один или несколько алгоритмов машинного обучения в зависимости от решаемой задачи обучения. Например, двоичная классификация, мультиклассовая классификация, регрессия.
7. Обучить одну или несколько моделей с помощью учебного набора данных.
8. Оценить проверочный набор данных с помощью обученных моделей.
9. Вычислить модель (модели) для расчета соответствующих показателей для задачи по обучению.
10. Провести тонкую настройку моделей и выбрать лучшую из них для развертывания.

В этом упражнении мы уже изучили и спроектировали данные в хранилище данных SQL и определились с размером выборки для приема в Машинном обучении Azure. Вот процедура, позволяющая создать одну или несколько моделей прогнозирования:

1. Загрузите данные в машинное обучение Azure, используя модуль [Импорт данных][import-data], доступный в разделе **Data Input and Output** (Ввод и вывод данных). Дополнительные сведения см. на странице справки [Import Data module][import-data] \(Модуль "Импорт данных").

	![Модуль "Импорт данных" в Машинном обучении Azure][17]

2. Выберите **База данных SQL Azure** в поле **Источник данных** на панели **Свойства**.

3. Введите DNS-имя базы данных в поле **Имя сервера базы данных**. Формат: `tcp:<your_virtual_machine_DNS_name>,1433`

4. Введите **Имя базы данных** в соответствующее поле.

5. Введите *имя пользователя SQL* в поле **Server user account name** (Имя учетной записи пользователя сервера) и *пароль* в поле **Server user account password** (Пароль учетной записи пользователя сервера).

6. Установите флажок **Accept any server certificate** (Принимать любой сертификат сервера).

7. В текстовой области **Запрос к базе данных** вставьте запрос, который извлекает необходимые поля базы данных (включая возможные вычисляемые поля, например метки) и уменьшает размер выборки данных до требуемого.

На рисунке ниже показан пример эксперимента по двоичной классификации, в ходе которого происходит чтение данных прямо из базы данных хранилища данных SQL (не забудьте заменить имена таблиц nyctaxi\_trip и nyctaxi\_fare именами схемы и таблиц, использованными в пошаговом руководстве). Подобные эксперименты можно сконструировать для задач мультиклассовой классификации и регрессии.

![Чат Машинного обучения Azure][10]

> [AZURE.IMPORTANT] В примерах запросов на извлечение и выборку данных для моделирования, указанных в предыдущих разделах, **все метки для трех упражнений по моделированию включены в запрос**. Важным (обязательным) шагом каждого из упражнений по моделированию является **исключение** ненужных меток для двух остальных задач, а также любых **целевых утечек**. Например, при двоичной классификации используйте метку **tipped** и исключите поля **tip\_class**, **tip\_amount** и **total\_amount**. Последние являются целевыми утечками, поскольку подразумевают, что чаевые выплачены.
>
> Чтобы исключить все ненужные столбцы или целевые утечки, можно воспользоваться модулем [Select Columns in Dataset][select-columns] \(Выбор столбцов в наборе данных) или [Edit Metadata][edit-metadata] \(Изменение метаданных). Дополнительные сведения см. на страницах справки [Select Columns in Dataset][select-columns] \(Выбор столбцов в наборе данных) и [Edit Metadata][edit-metadata] \(Изменение метаданных).

## <a name="mldeploy"></a>Развертывание моделей в службе Машинного обучения Azure

Когда модель готова, ее можно легко развернуть в виде веб-службы непосредственно из эксперимента. Дополнительную информацию о развертывании веб-служб Azure ML см. в статье [Развертывание веб-службы машинного обучения Azure](machine-learning-publish-a-machine-learning-web-service.md).

Чтобы развернуть новую веб-службу, необходимо выполнить такие действия.

1. Создать эксперимент по количественной оценке.
2. Развернуть веб-службу.

Чтобы создать эксперимент по количественной оценке на основе учебного эксперимента со статусом **Завершено**, щелкните **СОЗДАТЬ ЭКСПЕРИМЕНТ ПО КОЛИЧЕСТВЕННОЙ ОЦЕНКЕ** на нижней панели действий.

![Система оценок Azure][18]

Служба машинного обучения Azure попытается создать эксперимент по количественной оценке на основе компонентов учебного эксперимента. В частности, она:

1. Сохранит обученную модель и удалит модули обучения модели.
2. Идентифицирует логический **порт ввода** для представления ожидаемой схемы входных данных.
3. Идентифицирует логический **порт вывода** для представления ожидаемой схемы вывода веб-службы.

При создании оценочного эксперимента ознакомьтесь с ним и настройте его должным образом. Типичной корректировкой является замена входного набора данных и/или запроса таким, который исключает поля меток, поскольку они не будут доступны при вызове службы. Также рекомендуется уменьшить размер входного набора данных и/или запроса до нескольких записей, которых достаточно для обозначения входной схемы. Для порта вывода зачастую исключаются все входные поля и в вывод включаются только **Оцененные метки** и **Оцененные вероятности** с помощью модуля [Select Columns in Dataset][select-columns] \(Выбор столбцов в наборе данных).

Пример оценивающего эксперимента показан на рисунке ниже. Когда все будет готово к развертыванию, нажмите кнопку **ОПУБЛИКОВАТЬ ВЕБ-СЛУЖБУ** на нижней панели действий.

![Публикация Машинного обучения Azure][11]


## Сводка
Итак, в этом пошаговом руководстве мы создали среду обработки и анализа данных, работали с большим общедоступным набором данных на протяжении всего процесса обработки и анализа данных группы от получения данных до обучения модели и развертывания веб-службы машинного обучения Azure.

### Сведения о лицензии

Этот образец пошагового руководства и сопровождающие его сценарии и файлы IPython Notebook предоставлены корпорацией Майкрософт на условиях лицензии MIT. Дополнительные сведения см. в файле LICENSE.txt в каталоге примеров кода на сайте GitHub.

## Ссылки

•	[Andrés Monroy NYC Taxi Trips Download Page](http://www.andresmh.com/nyctaxitrips/) 
•	[FOILing NYC’s Taxi Trip Data by Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)
•	[NYC Taxi and Limousine Commission Research and Statistics](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sqldw-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sqldw-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlscoring.png
[19]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_load_data.png
[21]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/machine-learning-data-science-process-sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0921_2016-->