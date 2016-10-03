<properties
	pageTitle="Пошаговое руководство по масштабируемому анализу данных в озере данных Azure | Microsoft Azure"
	description="В этой статье описано, как выполнять задания по исследованию и двоичной классификации данных на основе набора данных озера данных Azure."  
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
	ms.date="09/19/2016"
	ms.author="bradsev;weig"/>


# Полное пошаговое руководство по масштабируемому анализу данных в озере данных Azure

В этом пошаговом руководстве на примере набора данных о поездках и тарифах такси в Нью-Йорке показано, как использовать озеро данных Azure для выполнения задач по исследованию и двоичной классификации данных, чтобы спрогнозировать вероятность получения чаевых за поездку. Здесь подробно описаны шаги [процесса обработки и анализа данных группы](http://aka.ms/datascienceprocess) — от получения данных для обучения модели и до развертывания веб-службы, которая публикует модель.


### Аналитика озера данных Azure

[Озеро данных Microsoft Azure](https://azure.microsoft.com/solutions/data-lake/) предусматривает все необходимые возможности для аналитиков, упрощающие хранение данных любого размера, формата и скорости обработки и обеспечивающие высокую гибкость и экономичность обработки данных, углубленной аналитики и создания моделей машинного обучения. Плата взимается за задание, и только если данные обработаны фактически. Аналитика озера данных Azure предполагает использование U-SQL, языка, который объединяет декларативную природу SQL с выразительностью C#, чтобы позволить создавать масштабируемые распределенные запросы. Она позволяет обрабатывать неструктурированные данные, применяя схему для чтения, вставки пользовательской логики и определяемых пользователем функций (UDF), и предполагает возможности расширения, обеспечивающие точный контроль над выполнением в масштабе. Дополнительные сведения о принципах разработки U-SQL см. в [записи блога о Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Аналитика озера данных — это также ключевая часть Cortana Analytics Suite. Она поддерживает работу с хранилищем данных SQL Azure, Power BI и фабрикой данных. Это создает всеобъемлющую облачную платформу для работы с большими данными и углубленной аналитики.

Пошаговое руководство начинается с описания необходимых компонентов, а также ресурсов, которые требуются для выполнения задач с использованием Аналитики озера данных, лежащих в основе анализа данных. Затем здесь описаны шаги по обработке данных, выполняемые с использованием U-SQL. В конце статьи показано, как использовать Python и Hive со студией машинного обучения Azure для создания и развертывания прогнозных моделей.


### U-SQL и Visual Studio

В этом пошаговом руководстве рекомендуется использовать Visual Studio, чтобы изменять сценарии U-SQL для обработки набора данных. Сценарии U-SQL, описанные в документе, приводятся в отдельном файле. Процесс включает в себя прием, исследование и выборку данных. Также здесь показывается, как выполнить задание со сценарием U-SQL на портале Azure. Для данных в связанном кластере HDInsight создаются таблицы Hive, которые упрощают сборку и развертывание модели двоичной классификации в Студии машинного обучения Azure.


### Python

В этом пошаговом руководстве также содержится раздел, в котором показано, как создавать и развертывать прогнозную модель с помощью Python и студии машинного обучения Azure. Мы предоставили записную книжку Jupyter со сценариями Python для шагов в этом процессе. Этот Notebook включает в себя код для некоторых дополнительных шагов проектирования признаков и создания моделей, например многоклассовой классификации и регрессии, в дополнение к модели двоичной классификации, описанной здесь. Задача регрессии: спрогнозировать сумму чаевых в зависимости от других признаков.


### Машинное обучение Azure
Студия машинного обучения Azure используется для создания и развертывания прогнозных моделей. Для этого используется два подхода: первый — с помощью сценариев Python, а второй — с использованием таблиц Hive на кластере HDInsight (Hadoop).


### Сценарии

В этом пошаговом руководстве описаны только основные шаги. Можно скачать полный **сценарий U-SQL**, а также **записную книжку Jupyter** из [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).


## Предварительные требования

Прежде чем начать работу по этим разделам, необходимо обеспечить наличие следующего:

- Подписка Azure. Если у вас ее нет, см. раздел [о получении бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- [Рекомендуется] Visual Studio 2013 или 2015. Если вы еще не установили одну из этих версий, можно скачать бесплатный выпуск Community [отсюда](https://www.visualstudio.com/visual-studio-homepage-vs.aspx). Нажмите кнопку **Скачайте Community 2015** в разделе Visual Studio.

>[AZURE.NOTE] Чтобы отправлять запросы озера данных Azure, вместо Visual Studio можно также использовать портал Azure. Указания о том, как сделать это с помощью Visual Studio и на портале, см. в разделе **Обработка данных с использованием U-SQL**.

- Регистрация на предварительную версию озера данных Azure.

>[AZURE.NOTE] Чтобы использовать хранилище озера данных Azure и Аналитику озера данных Azure, необходимо получить утверждение, так как эти службы работают в рамках предварительной версии. Во время создания первого хранилища озера данных Azure или решения Аналитики озера данных Azure отобразится запрос на регистрацию. Чтобы зарегистрироваться, щелкните **Регистрация в предварительной версии**, прочтите соглашение и нажмите кнопку **ОК**. Вот так, к примеру, выглядит станица регистрации хранилища озера данных Azure:

 ![2](./media/machine-learning-data-science-process-data-lake-walkthrough/2-ADLA-preview-signup.PNG)


## Подготовка среды анализа данных для озера данных Azure
Чтобы подготовить среду анализа данных для этого пошагового руководства, создайте следующие ресурсы:

- хранилище озера данных Azure;
- Аналитику озера данных Azure;
- учетную запись хранения BLOB-объектов;
- учетную запись Студии машинного обучения Azure;
- средства озера данных Azure для Visual Studio (рекомендуется).

Этот раздел содержит указания о том, как создать каждый из этих ресурсов. Если для создания модели вы решили вместо Python использовать таблицы Hive с Машинным обучением Azure, то необходимо также подготовить кластер HDInsight (Hadoop). Этот альтернативный способ описан в соответствующем разделе ниже. <br/>
>AZURE.NOTE **Хранилище озера данных Azure** можно создать отдельно или вместе с **аналитикой озера данных Azure** в качестве хранилища по умолчанию. Указания по созданию каждого из этих ресурсов приведены по отдельности ниже, но учетную запись хранения Data Lake не требуется создавать отдельно. <br/>
### Создание хранилища озера данных Azure

Создайте хранилище озера данных Azure на [портале Azure](http://portal.azure.com). Дополнительные сведения см. в статье [Создание кластера HDInsight с хранилищем озера данных с помощью портала Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Обязательно настройте удостоверение кластера AAD в колонке **Источник данных** колонки **Необязательная конфигурация**, показанной здесь.

 ![3](./media/machine-learning-data-science-process-data-lake-walkthrough/3-create-ADLS.PNG)


### Создание учетной записи Аналитики озера данных Azure
Создайте учетную запись аналитики озера данных Azure на [портале Azure](http://portal.azure.com). Дополнительные сведения см. в статье [Учебник. Начало работы с аналитикой озера данных Azure с помощью портала Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4\.](./media/machine-learning-data-science-process-data-lake-walkthrough/4-create-ADLA-new.PNG)


### Создание учетной записи хранения BLOB-объектов Azure
Создайте учетную запись хранилища BLOB-объектов Azure на [портале Azure](http://portal.azure.com). Дополнительные сведения см. в разделе "Создание учетной записи хранения" в статье [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md).
	
 ![5](./media/machine-learning-data-science-process-data-lake-walkthrough/5-Create-Azure-Blob.PNG)


### Настройка учетной записи Студии машинного обучения Azure
Войдите в студию машинного обучения со страницы [Машинного обучения Azure](https://azure.microsoft.com/services/machine-learning/). Нажмите кнопку **Начните прямо сейчас** и выберите Free Workspace ("Бесплатная рабочая область") или Standard Workspace ("Стандартная рабочая область"). После этого в Студии машинного обучения Azure можно будет создавать эксперименты.

### Установка инструментов озера данных Azure [рекомендуется]
Установите инструменты озера данных Azure в зависимости от своей версии Visual Studio со страницы [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (Инструменты озера данных Azure для Visual Studio).

 ![6](./media/machine-learning-data-science-process-data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

После успешного завершения установки откройте Visual Studio. Вы должны увидеть вкладку Data Lake ("Озеро данных") в меню вверху. Ресурсы Azure должны отобразиться на левой панели при входе в учетную запись Azure.

 ![7](./media/machine-learning-data-science-process-data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)


## Набор данных "Поездки такси Нью-Йорка"
Здесь мы использовали общедоступный набор данных [Поездки такси Нью-Йорка](http://www.andresmh.com/nyctaxitrips/). Данные о поездках такси Нью-Йорка содержатся в сжатых CSV-файлах размером около 20 ГБ (примерно 48 ГБ в несжатом виде), которые включают в себя сведения о более 173 млн отдельных поездок и платежах за каждую поездку. В каждой записи о поездке содержатся данные о расположении пунктов посадки и высадки, а также времени, анонимизированный номер лицензии водителя и номер медальона (уникальный идентификатор такси). Данные включают в себя все поездки за 2013 год и предоставляются в виде следующих двух наборов данных за каждый месяц:

 - CSV-файл trip\_data содержит подробную информацию о поездке, например число пассажиров, пункты отправления и назначения, продолжительность поездки и ее расстояние. Вот несколько примеров записей:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count, trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

 - CSV-файл trip\_fare содержит подробную информацию об оплате каждой поездки, такие как тип оплаты, сумма тарифа, надбавка и налоги, чаевые и пошлины, а также общая выплаченная сумма. Вот несколько примеров записей:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Уникальный ключ для соединения trip\_data и trip\_fare состоит из следующих трех полей: medallion, hack\_licence и pickup\_datetime. Необработанные файлы в формате CSV можно получать из общедоступных больших двоичных объектов хранилища Azure. Сценарий U-SQL для этого объединения находится в разделе [Объединение таблиц trip и fare](#join).

## Обработка данных с использованием U-SQL

Задачи обработки данных в этом разделе предусматривают прием, проверку качества, исследование и выборку данных. Здесь также показано, как объединить таблицы trip и fare. В последнем разделе показано, как выполнить задание со сценарием U-SQL на портале Azure. Ниже приведены ссылки на каждый подраздел.

- [Прием данных: чтение из общедоступного большого двоичного объекта](#ingest)
- [Проверка качества данных](#quality)
- [Исследование данных](#explore)
- [Объединение таблиц trip и fare](#join)
- [Выборка данных](#sample)
- [Выполнение заданий U-SQL](#run)

Сценарии U-SQL, описанные в документе, приводятся в отдельном файле. Можно скачать полные **сценарии U-SQL** из [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Чтобы выполнить сценарий U-SQL, откройте Visual Studio, последовательно выберите **Файл --> Создать--> Проект**, щелкните **U-SQL Project** (Проект U-SQL), укажите имя и сохраните проект в папке.

![8](./media/machine-learning-data-science-process-data-lake-walkthrough/8-create-USQL-project.PNG)

>[AZURE.NOTE] Для выполнения сценариев U-SQL вместо Visual Studio можно использовать портал Azure. Можно перейти к ресурсу "Аналитика озера данных Azure" на портале и отправить запросы напрямую, как показано на следующем рисунке.

![9](./media/machine-learning-data-science-process-data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Прием данных: чтение из общедоступного большого двоичного объекта

Данные большого двоичного объекта Azure расположены по адресу **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**, и их можно извлечь, используя **Extractors.Csv()**. В следующих сценариях подставьте имена своих контейнера и учетной записи хранения для container_name@blob\_storage\_account\_name в адресе wasb. Так как у имен файлов одинаковый формат, чтобы выполнить чтение всех 12 файлов поездок, можно использовать **trip\_data\_{*}.csv**.

	///Read in Trip data
	@trip0 =
	    EXTRACT 
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count string,
	    trip_time_in_secs string,
	    trip_distance string,
	    pickup_longitude string,
	    pickup_latitude string,
	    dropoff_longitude string,
	    dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

В первой строке есть заголовки, поэтому их необходимо удалить и изменить типы столбцов на соответствующие. Обработанные данные можно сохранить в хранилище Azure Data Lake по пути **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ или в учетной записи хранения BLOB-объектов Azure по пути **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**.

	// change data types
	@trip =
	    SELECT 
	    medallion,
	    hack_license,
	    vendor_id,
	    rate_code,
	    store_and_fwd_flag,
	    DateTime.Parse(pickup_datetime) AS pickup_datetime,
	    DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
	    Int32.Parse(passenger_count) AS passenger_count,
	    Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
	    Double.Parse(trip_distance) AS trip_distance,
	    (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
	    (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
	    (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
	    (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

	////output data to ADL
	OUTPUT @trip   
	TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
	USING Outputters.Csv(); 

	////Output data to blob
	OUTPUT @trip   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
	USING Outputters.Csv();  

Аналогичным образом можно выполнить чтение наборов данных о тарифах. Щелкните Azure Data Lake Store правой кнопкой мыши и выберите **Портал Azure --> Обозреватель данных** или в среде Visual Studio используйте **проводник**, чтобы просмотреть данные.

 ![10](./media/machine-learning-data-science-process-data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/machine-learning-data-science-process-data-lake-walkthrough/11-data-in-ADL.PNG)


### <a name="quality"></a>Проверка качества данных

После считывания таблиц trip и fare можно проверить качество данных следующим образом. Полученные файлы в формате CSV можно поместить в хранилище BLOB-объектов Azure или хранилище озера данных Azure.

Узнайте количество медальонов и их уникальные номера:

	///check the number of medallions and unique number of medallions
	@trip2 =
	    SELECT
	    medallion,
	    vendor_id,
	    pickup_datetime.Month AS pickup_month
	    FROM @trip;
	
	@ex_1 =
	    SELECT
	    pickup_month, 
	    COUNT(medallion) AS cnt_medallion,
	    COUNT(DISTINCT(medallion)) AS unique_medallion
	    FROM @trip2
	    GROUP BY pickup_month;
	    OUTPUT @ex_1   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
	USING Outputters.Csv(); 

Определите медальоны, которые принадлежат такси, осуществившим более 100 поездок:

	///find those medallions that had more than 100 trips
	@ex_2 =
	    SELECT medallion,
	           COUNT(medallion) AS cnt_medallion
	    FROM @trip2
	    //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
	    GROUP BY medallion
	    HAVING COUNT(medallion) > 100;
	    OUTPUT @ex_2   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
	USING Outputters.Csv(); 

Найдите записи, недопустимые в отношении pickup\_longitude:

	///find those invalid records in terms of pickup_longitude
	@ex_3 =
	    SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
	    FROM @trip
	    WHERE
	    pickup_longitude <- 90 OR pickup_longitude > 90;
	    OUTPUT @ex_3   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
	USING Outputters.Csv(); 

Найдите отсутствующие значения некоторых переменных:

	//check missing values
	@res =
	    SELECT *,
	           (medallion == null? 1 : 0) AS missing_medallion
	    FROM @trip;
	
	@trip_summary6 =
	    SELECT 
	        vendor_id,
	    SUM(missing_medallion) AS medallion_empty, 
	    COUNT(medallion) AS medallion_total,
	    COUNT(DISTINCT(medallion)) AS medallion_total_unique  
	    FROM @res
	    GROUP BY vendor_id;
	OUTPUT @trip_summary6
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
	USING Outputters.Csv();



### <a name="explore"></a>Просмотр данных

Чтобы лучше узнать все аспекты данных, можно провести некоторые исследования.

Получите распределение поездок с чаевыми и без них:

	///tipped vs. not tipped distribution
	@tip_or_not =
	    SELECT *,
	           (tip_amount > 0 ? 1: 0) AS tipped
	    FROM @fare;
	
	@ex_4 =
	    SELECT tipped,
	           COUNT(*) AS tip_freq
	    FROM @tip_or_not
	    GROUP BY tipped;
	    OUTPUT @ex_4   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
	USING Outputters.Csv(); 

Получите распределение сумм чаевых с пороговыми значениями: 0, 5, 10 и 20 долларов.

	//tip class/range distribution
	@tip_class =
	    SELECT *,
	           (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
	    FROM @fare;
	@ex_5 =
	    SELECT tip_class,
	           COUNT(*) AS tip_freq
	    FROM @tip_class
	    GROUP BY tip_class;
	    OUTPUT @ex_5   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
	USING Outputters.Csv(); 

Получите базовые статистические данные о расстоянии поездок:

	// find basic statistics for trip_distance
	@trip_summary4 =
	    SELECT 
	        vendor_id,
	        COUNT(*) AS cnt_row,
	        MIN(trip_distance) AS min_trip_distance,
	        MAX(trip_distance) AS max_trip_distance,
	        AVG(trip_distance) AS avg_trip_distance 
	    FROM @trip
	    GROUP BY vendor_id;
	OUTPUT @trip_summary4
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
	USING Outputters.Csv();

Узнайте процентили расстояния поездок.

	// find percentiles of trip_distance
	@trip_summary3 =
	    SELECT DISTINCT vendor_id AS vendor,
	                    PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
	                    PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
	                    PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
	    FROM @trip;
	   // group by vendor_id;
	OUTPUT @trip_summary3
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
	USING Outputters.Csv(); 


### <a name="join"></a>Объединение таблиц trip и fare

Таблицы trip и fare можно объединить по medallion, hack\_license и pickup\_time.

	//join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

	//// output to blob
	OUTPUT @model_data_full   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
	USING Outputters.Csv(); 

	////output data to ADL
	OUTPUT @model_data_full   
	TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
	USING Outputters.Csv(); 


Вычислите количество записей, среднюю сумму чаевых, дисперсию суммы чаевых и процент поездок, за которые выплатили чаевые, для каждого уровня количества пассажиров.

	// contigency table
	@trip_summary8 =
	    SELECT passenger_count,
	           COUNT(*) AS cnt,
	           AVG(tip_amount) AS avg_tip_amount,
	           VAR(tip_amount) AS var_tip_amount,
	           SUM(tipped) AS cnt_tipped,
	           (float)SUM(tipped)/COUNT(*) AS pct_tipped
	    FROM @model_data_full
	    GROUP BY passenger_count;
	    OUTPUT @trip_summary8
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
	USING Outputters.Csv();


### <a name="sample"></a>Выборка данных

Сначала мы случайным образом выберем 0,1 % данных из объединенной таблицы:

	//random select 1/1000 data for modeling purpose
	@addrownumberres_randomsample =
	SELECT *,
	        ROW_NUMBER() OVER() AS rownum
	FROM @model_data_full;
	
	@model_data_random_sample_1_1000 =
	SELECT *
	FROM @addrownumberres_randomsample
	WHERE rownum % 1000 == 0;
	
	OUTPUT @model_data_random_sample_1_1000   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
	USING Outputters.Csv(); 

Затем мы выполним стратифицированную выборку по двоичной переменной tip\_class:

	//stratified random select 1/1000 data for modeling purpose
	@addrownumberres_stratifiedsample =
	SELECT *,
	        ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
	FROM @model_data_full;
	
	@model_data_stratified_sample_1_1000 =
	SELECT *
	FROM @addrownumberres_stratifiedsample
	WHERE rownum % 1000 == 0;
	//// output to blob
	OUTPUT @model_data_stratified_sample_1_1000   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
	USING Outputters.Csv(); 
	////output data to ADL
	OUTPUT @model_data_stratified_sample_1_1000   
	TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
	USING Outputters.Csv(); 


### <a name="run"></a>Выполнение заданий U-SQL

Завершив редактировать сценарии U-SQL, вы можете отправить их на сервер, используя учетную запись Аналитики озера данных Azure. Выберите вкладку **Data Lake**, щелкните **Отправить задание**, затем выберите свою учетную запись в поле **Analytics Account** (Учетная запись Аналитики), значение параметра **Параллелизм** и нажмите кнопку **Отправить**.

 ![12](./media/machine-learning-data-science-process-data-lake-walkthrough/12-submit-USQL.PNG)

Если задание будет выполнено успешно, его состояние отобразится в Visual Studio для мониторинга. После завершения задания можно даже воспроизвести его выполнение и определить, на каких шагах возникают узкие места, чтобы повысить эффективность работы. Вы также можете перейти на портал Azure, чтобы проверить состояние заданий U-SQL.

 ![13\.](./media/machine-learning-data-science-process-data-lake-walkthrough/13-USQL-running-v2.PNG)


 ![14](./media/machine-learning-data-science-process-data-lake-walkthrough/14-USQL-jobs-portal.PNG)


Теперь можно проверить выходные файлы в хранилище BLOB-объектов или на портале Azure. Данные стратифицированной выборки будут использоваться для моделирования на следующем шаге.

 ![15](./media/machine-learning-data-science-process-data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/machine-learning-data-science-process-data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)


## Создание и развертывание моделей в Машинном обучении Azure

Мы продемонстрируем два доступных варианта извлечения данных в машинное обучение Azure для создания и развертывания моделей.

- Первый вариант предусматривает использование данных выборки, записанных в большой двоичный объект Azure (на шаге **Выборка данных** выше), и Python, чтобы создать и развернуть модели из Машинного обучения Azure.
- Второй вариант предполагает, что вы запрашиваете данные озера данных Azure напрямую с помощью запроса Hive. При выборе такого варианта необходимо создать новый кластер HDInsight или использовать кластер HDInsight, который уже есть. Таблицы Hive кластера должны указывать на данные о такси Нью-Йорка в хранилище озера данных Azure. Мы рассмотрим оба варианта ниже.

## Вариант 1. Использование Python для создания и развертывания моделей машинного обучения

Для создания и развертывания моделей машинного обучения, используя Python, создайте записную книжки Jupyter на локальном компьютере или в студии машинного обучения Azure. Записная книжка Jupyter, которая доступна на сайте [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough), содержит полный код для исследования и визуализации данных, проектирования признаков, моделирования и развертывания. В этой статье показываются шаги только по моделированию и развертыванию.

### Импорт библиотек Python

Чтобы запустить пример Jupyter Notebook или файл сценария Python, необходимо установить следующие пакеты Python. Если вы используете службу Notebook Машинного обучения Azure, эти пакеты уже установлены.

	import pandas as pd
	from pandas import Series, DataFrame
	import numpy as np
	import matplotlib.pyplot as plt
	from time import time
	import pyodbc
	import os
	from azure.storage.blob import BlobService
	import tables
	import time
	import zipfile
	import random
	import sklearn
	from sklearn.linear_model import LogisticRegression
	from sklearn.cross_validation import train_test_split
	from sklearn import metrics
	from __future__ import division
	from sklearn import linear_model
	from azureml import services


### Считывание данных из большого двоичного объекта

- Строка подключения

		CONTAINERNAME = 'test1'
		STORAGEACCOUNTNAME = 'XXXXXXXXX'
		STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
		BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
		blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
	
- Считайте данные в качестве текста:

		t1 = time.time()
		data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
		t2 = time.time()
		print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

 ![17](./media/machine-learning-data-science-process-data-lake-walkthrough/17-python_readin_csv.PNG)
 
- Добавьте имена столбцов и отделите столбцы:

		colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
		'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
		'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
		df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
	


- Измените значения в некоторых столбцах на числовые:

		cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
		'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
		,'tipped','tip_class','rownum']
		for col in cols_2_float:
		    df1[col] = df1[col].astype(float)

### Создание моделей машинного обучения

В этом разделе мы создадим модель двоичной классификации, чтобы спрогнозировать вероятность получения чаевых за поездку. В Jupyter Notebook можно найти две другие модели: многоклассовой классификации и регрессии.

- Сначала необходимо создать фиктивные переменные, которые можно использовать в моделях scikit-learn:

		df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
		df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')

- Создайте кадр данных для моделирования:

		cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
		data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
		
		X = data.iloc[:,1:]
		Y = data.tipped

- Обучите и протестируйте разбиение на 60/40:

		X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)

- Получите логистическую регрессию в обучающем наборе:

		model = LogisticRegression()
		logit_fit = model.fit(X_train, Y_train)
		print ('Coefficients: \n', logit_fit.coef_)
		Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/machine-learning-data-science-process-data-lake-walkthrough/c1-py-logit-coefficient.PNG)

- Оцените тестируемый набор данных:

		Y_test_pred = logit_fit.predict(X_test)

- Выполните вычисление метрик оценки:

		fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
		print fpr_train, tpr_train, thresholds_train
		
		fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
		print fpr_test, tpr_test, thresholds_test
		
		#AUC
		print metrics.auc(fpr_train,tpr_train)
		print metrics.auc(fpr_test,tpr_test)
		
		#Confusion Matrix
		print metrics.confusion_matrix(Y_train,Y_train_pred)
		print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![c2](./media/machine-learning-data-science-process-data-lake-walkthrough/c2-py-logit-evaluation.PNG)


 
### Создание API веб-службы и его использование в Python

После создания модель машинного обучения необходимо ввести в эксплуатацию. Здесь в качестве примера мы используем двоичную логистическую модель. Убедитесь, что на локальном компьютере установлена версия scikit-learn 0.15.1. Если вы используете службу Студии машинного обучения Microsoft Azure, этого делать не нужно.

- Найдите учетные данные своей рабочей области в настройках Студии машинного обучения Microsoft Azure. В Студии машинного обучения Azure последовательно выберите **Параметры** --> **Имя** --> **Authorization Tokens** (Маркеры авторизации).

	![c3](./media/machine-learning-data-science-process-data-lake-walkthrough/c3-workspace-id.PNG)


		workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
		auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

- Создайте веб-службу:

		@services.publish(workspaceid, auth_token) 
		@services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
		@services.returns(int) #0, or 1
		def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
		    inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
		    return logit_fit.predict(inputArray)

- Получите учетные данные веб-службы:

		url = predictNYCTAXI.service.url
		api_key =  predictNYCTAXI.service.api_key
		
		print url
		print api_key

		@services.service(url, api_key)
		@services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
		@services.returns(float)
		def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
		    pass

- Вызовите API веб-службы. После выполнения предыдущего шага нужно будет подождать 5–10 секунд.

		NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/machine-learning-data-science-process-data-lake-walkthrough/c4-call-API.PNG)


## Вариант 2. Создание и развертывание моделей прямо в Машинном обучении Azure

Студия машинного обучения Azure может считывать данные прямо из хранилища озера данных Azure, а затем использоваться для создания и развертывания моделей. Этот подход использует таблицу Hive, которая указывает на хранилище озера данных Azure. Для этого необходимо подготовить отдельный кластер Azure HDInsight, а в нем создать таблицу Hive. В следующих разделах показано, как это сделать.

### Создание кластера HDInsight на платформе Linux

Создайте кластер HDInsight (на платформе Linux) на [портале Azure](http://portal.azure.com). Дополнительные сведения см. в разделе **Создание кластера Azure HDInsight с доступом к хранилищу озера данных Azure** в статье [Создание кластера HDInsight с хранилищем озера данных с помощью портала Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/machine-learning-data-science-process-data-lake-walkthrough/18-create_HDI_cluster.PNG)

### Создание таблицы Hive в HDInsight

Теперь мы создадим таблицы Hive, необходимые для студии машинного обучения Azure в кластере HDInsight, используя данные из хранилища озера данных Azure, сохраненные на предыдущем шаге. Перейдите на только что созданный кластер HDInsight. Последовательно выберите **Параметры** --> **Свойства** --> **Удостоверение кластера AAD** --> **ADLS Access** (Доступ ADLS), убедитесь, что учетная запись Azure Data Lake Store добавлена в список и есть права на чтение, запись и выполнение.

 ![19](./media/machine-learning-data-science-process-data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)


Щелкните **Панель мониторинга** рядом с кнопкой **Параметры**, и откроется новое окно. Щелкните **Представление Hive** в правом верхнем углу страницы, и вы увидите **Редактор запросов**.

 ![20](./media/machine-learning-data-science-process-data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/machine-learning-data-science-process-data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)


Вставьте следующие сценарии Hive, чтобы создать таблицу. Источник данных в Azure Data Lake Store расположен по такому адресу: **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

	CREATE EXTERNAL TABLE nyc_stratified_sample
	(
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count string,
	    trip_time_in_secs string,
	    trip_distance string,
	    pickup_longitude string,
	    pickup_latitude string,
	    dropoff_longitude string,
	    dropoff_latitude string,
	  payment_type string,
	  fare_amount string,
	  surcharge string,
	  mta_tax string,
	  tolls_amount string,
	  total_amount string,
	  tip_amount string,
	  tipped string,
	  tip_class string,
	  rownum string
	  )
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


После выполнения запроса отобразятся аналогичные результаты:

 ![22](./media/machine-learning-data-science-process-data-lake-walkthrough/22-Hive-Query-results.PNG)



### Создание и развертывание моделей в Студии машинного обучения Azure

Теперь все готово для создания и развертывания модели, с помощью которой можно спрогнозировать, будут ли выплачены чаевые за поездку, используя машинное обучение Azure. Данные стратифицированной выборки готовы к использованию в этой задаче двоичной классификации (поездка с чаевыми или без чаевых). Прогнозные модели, использующие многоклассовую классификацию (tip\_class) и регрессию (tip\_amount), также можно создавать и развертывать с помощью студии машинного обучения Azure, но здесь мы показываем только случай с использованием модели двоичной классификации.

1. Загрузите данные в Машинное обучение Azure, используя модуль **Import Data** (Импорт данных), доступный в разделе **Data Input and Output** (Ввод и вывод данных). Дополнительные сведения см. на странице справки [Import Data module](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) (Модуль "Импорт данных").
2. Выберите значение **Hive Query** (Запрос Hive) для параметра **Источник данных** на панели **Свойства**.
3. Вставьте следующий сценарий Hive в редактор **Hive database query** (Запрос к базе данных Hive):

    	select * from nyc_stratified_sample;

4. Введите универсальный код ресурса (URI) кластера HDInsight (его можно найти на портале Azure), учетные данные Hadoop, расположение выходных данных и имя контейнера, ключа или учетной записи хранения Azure.

 ![23](./media/machine-learning-data-science-process-data-lake-walkthrough/23-reader-module-v3.PNG)

На рисунке ниже показан пример эксперимента по двоичной классификации, который связан с чтением данных из таблицы Hive.

 ![24](./media/machine-learning-data-science-process-data-lake-walkthrough/24-AML-exp.PNG)

После создания эксперимента последовательно выберите **Set Up Web Service** (Настройка веб-службы) --> **Predictive Web Service** (Прогнозная веб-служба).

 ![25](./media/machine-learning-data-science-process-data-lake-walkthrough/25-AML-exp-deploy.PNG)

Запустите автоматически созданный оценивающий эксперимент, а по его завершении нажмите кнопку **Deploy Web Service** (Развертывание веб-службы).

 ![26](./media/machine-learning-data-science-process-data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Вскоре отобразится панель мониторинга веб-службы:

 ![27](./media/machine-learning-data-science-process-data-lake-walkthrough/27-AML-web-api.PNG)


## Сводка

После завершения этого пошагового руководства у вас будет создана среда анализа данных для создания всеобъемлющих масштабируемых решений озера данных Azure. Эта среда использовалась для анализа большого набора данных, в отношении которого выполнены ключевые шаги по обработке данных — от получения данных и обучения модели до развертывания модели в качестве веб-службы. Язык U-SQL использовался для обработки, просмотра и выборки данных. Python и Hive использовались со студией машинного обучения Azure для создания и развертывания прогнозных моделей.

## Что дальше?

Схема обучения для [процесса обработки и анализа данных группы (TDSP)](http://aka.ms/datascienceprocess) содержит ссылки на разделы, описывающие каждый шаг в процессе расширенной аналитики. Существует ряд пошаговых руководств, упорядоченных на странице [Пошаговые руководства по процессу обработки и анализа данных](data-science-process-walkthroughs.md), которые демонстрируют, как использовать ресурсы и службы в различных сценариях прогнозной аналитики.

- [Процесс обработки и анализа данных группы на практике: использование хранилища данных SQL](machine-learning-data-science-process-sqldw-walkthrough.md)
- [Процесс обработки и анализа данных группы на практике: использование кластеров HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md)
- [Процесс обработки и анализа данных группы на практике: использование SQL Server](machine-learning-data-science-process-sql-walkthrough.md)
- [Общие сведения об обработке и анализе данных с помощью платформы Spark в Azure HDInsight](machine-learning-data-science-spark-overview.md)

<!---HONumber=AcomDC_0921_2016-->