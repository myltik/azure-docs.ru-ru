---
title: "Хранилище данных SQL Azure: руководство по началу работы | Документация Майкрософт"
description: "Руководство по началу работы с хранилищем данных SQL Azure."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/21/2016
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: fe9de0ffad3fe5d4acbf3caf2f08101f6a13daaf
ms.openlocfilehash: 12f500c01671799612b0d1988e0d07bbfda600da


---
# <a name="get-started-with-sql-data-warehouse"></a>Начало работы с хранилищем данных SQL

Руководство по началу работы с хранилищем данных SQL Azure. В этом руководстве изложены основы подготовки и загрузки данных в хранилище данных SQL, а также некоторые базовые сведения о масштабировании, приостановке и настройке. 

**Предполагаемое время выполнения:** 75 минут

## <a name="prerequisites"></a>Предварительные требования


### <a name="sign-up-for-microsoft-azure"></a>Зарегистрируйтесь для Microsoft Azure
Если у вас еще нет учетной записи Microsoft Azure, зарегистрируйте ее, чтобы использовать эту службу. Если у вас уже есть учетная запись, этот шаг можно пропустить. 

1. Перейдите на страницу учетной записи [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/).
2. Создайте бесплатную учетную запись Azure или купите платную.
3. Следуйте указаниям на экране.

### <a name="install-appropriate-sql-client-driver-and-tools"></a>Установка соответствующего клиентского драйвера и средств SQL

Большинство клиентских средств SQL могут подключаться к хранилищу данных SQL Azure с помощью JDBC, ODBC или ADO.NET. Из-за сложности продукта и большого количества функций T-SQL, которые поддерживает хранилище данных SQL, не все клиентские приложения полностью совместимы с хранилищем данных SQL.

Если вы используете операционную систему Windows, рекомендуем использовать либо [Visual Studio] или [SQL Server Management Studio].


[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-an-azure-sql-data-warehouse"></a>Создание хранилища данных SQL Azure

> [!NOTE]
> Создание хранилища данных SQL может привести к дополнительным расходам.  Дополнительные сведения см. на странице [цен на хранилище данных SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>


### <a name="create-a-sql-data-warehouse"></a>Создание хранилища данных SQL
1. Войдите на [портал Azure](https://portal.azure.com).
2. Последовательно выберите **Создать** > **Базы данных** > **Хранилище данных SQL**.

    ![Колонка "Создать"](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![Выбор хранилища данных](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Укажите сведения о развертывании.

    **Имя базы данных**: выберите любое имя. Если у вас несколько экземпляров хранилища данных SQL, рекомендуем включать в имя такие сведения, такие как регион, среда и т. д., например *mydw-westus-1-test*.

    **Подписка**: ваша подписка Azure.

    **Группа ресурсов**: создайте новую группу ресурсов (или выберите существующую, если планируете использовать хранилище данных SQL Azure с другими службами).
    > [!NOTE]
    > Службы в группе ресурсов должны иметь одинаковый жизненный цикл. Группы ресурсов можно использовать для администрирования ресурсов, например для определения области управления доступом или развертывания по шаблону. Дополнительные сведения о группах ресурсов Azure и рекомендации см. [здесь](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups).
    >

    **Источник**: пустая база данных.

    **Сервер**: выберите сервер, созданный в разделе [Предварительные требования].

    **Параметры сортировки**: оставьте параметры сортировки по умолчанию (SQL_Latin1_General_CP1_CI_AS).

    **Выбор уровня производительности**: рекомендуем использовать стандартное значение 400 DWU.

4. Установите флажок **Закрепить на панели мониторинга**
    ![Закрепить на панели мониторинга](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png).

5. Подождите, пока завершится развертывание хранилища данных SQL Azure. Обычно этот процесс занимает несколько минут. Когда завершится развертывание вашего экземпляра, на портале появится соответствующее уведомление. 

## <a name="connect-to-azure-sql-data-warehouse-through-sql-server-logical-server"></a>Подключение к хранилищу данных SQL Azure с помощью сервера SQL Server (логического сервера)

В этом руководстве для подключения к хранилищу данных SQL используется SQL Server Management Studio. Можно использовать и другие средства для подключения через поддерживаемые соединители: ADO.NET, JDBC, ODBC и PHP. Помните, что функциональные возможности средств, не поддерживаемых корпорацией Майкрософт, могут быть ограничены.


### <a name="get-connection-information"></a>Получение сведений о подключении

Для подключения к хранилищу данных SQL необходимо использовать сервер SQL Server (логический сервер), созданный в разделе [Предварительные требования].

1. Выберите хранилище данных SQL на панели мониторинга или найдите его в списке ресурсов.

    ![Панель мониторинга хранилища данных SQL](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Найдите полное имя логического сервера.

    ![Выбор имени сервера](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Откройте SSMS и с помощью обозревателя объектов подключитесь к этому серверу с помощью учетных данных, созданных в разделе [Предварительные требования]

    ![Подключение с помощью SSMS](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Если все сделано правильно, должно быть установлено подключение к экземпляру сервера SQL Server (логического сервера). Используя учетные данные сервера, можно пройти проверку подлинности для любой базы данных на этом сервере в качестве владельца базы данных. Однако мы рекомендуем создать отдельные имена для входа и пользователей для каждой базы данных. Подробные сведения о создании пользователя см. в разделе [Создание пользователя для хранилища данных SQL](./sql-data-warehouse-get-started-tutorial.md#create-a-user-for-sql-data-warehouse). 

## <a name="create-a-user-for-sql-data-warehouse"></a>Создание пользователя для хранилища данных SQL

### <a name="why-create-a-separate-user"></a>Зачем создавать отдельных пользователей?

Мы создадим нового пользователя для нашего хранилища данных SQL, используя подключение к серверу SQL Server (логическому серверу) с учетными данными сервера из предыдущего шага. Существует две основные причины, почему вам может потребоваться создать отдельного пользователя или имя входа для хранилища данных SQL.

1.  Пользователи вашей организации должны использовать для проверки подлинности отдельную учетную запись. Это позволяет ограничить разрешения, предоставляемые приложениям, и снизить риски вредоносных действий.

2. По умолчанию имя для входа администратора сервера, с помощью которого вы сейчас подключены, использует класс ресурсов небольшого размера. Классы ресурсов позволяют управлять выделением памяти и циклов ЦП для заданного запроса. Для пользователей, использующих класс ресурсов **smallrc**, выделяется меньший объем памяти и предоставляются большие возможности параллелизма. В то же время для пользователей, которым назначен класс ресурсов **xlargerc**, выделяется больший объем памяти, поэтому меньше их запросов могут выполняться параллельно. Для загрузки данных в виде, оптимизированном для сжатия, необходимо, чтобы пользователю, загружающему данные, был назначен класс ресурсов большого размера. Дополнительные сведения о классах ресурсов см. [здесь](./sql-data-warehouse-develop-concurrency.md#resource-classes).

### <a name="creating-a-user-of-a-larger-resource-class"></a>Создание пользователя, относящегося к классу ресурсов большого размера

1. Отправьте запрос к базе данных **master** на вашем сервере.

    ![Создание запроса к базе данных master](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Создание запроса к базе данных master1](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. Создайте имя для входа и пользователя сервера.

    ```sql
    CREATE LOGIN XLRCLOGIN WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

3. Отправив запрос к базе данных хранилища данных SQL, создайте нового пользователя базы данных на основе имени для входа на сервер. 
    ```sql
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

4. Предоставьте пользователю права на управление базой данных.
    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Если имя базы данных содержит дефисы, обязательно заключите его в квадратные скобки! 
    >

5. Добавьте пользователя базы данных в роль класса ресурсов **xlargerc**.
    ```sql
    EXEC sp_addrolemember 'xlargerc', 'LoadingUser';
    ```

6. Войдите в базу данных с помощью новых учетных данных.

    ![Вход с помощью новых учетных данных](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="loading-data"></a>Загрузка данных

### <a name="defining-external-data"></a>Определение внешних данных
1. Создайте главный ключ и определите внешний источник данных.

    ```sql
    CREATE MASTER KEY;

    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
    TYPE = Hadoop
    , LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```


2. Определите форматы внешних файлов.

    С помощью команды ```CREATE EXTERNAL FILE FORMAT``` можно указать формат загружаемых внешних данных. Для данных о маршрутных такси Нью-Йорка мы использовали два формата хранения данных в хранилище BLOB-объектов Azure.

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = ','
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = '|'
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

3.  Создайте схему для формата внешнего файла.

    ```sql
    CREATE SCHEMA ext;
    GO
    ```

4. Создание внешних таблиц Эти таблицы ссылаются на данные, хранящиеся в HDFS или в хранилище BLOB-объектов Azure. 

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
    [DateID] int NOT NULL,
    [Date] datetime NULL,
    [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FirstDayOfMonth] date NULL,
    [LastDayOfMonth] date NULL,
    [FirstDayOfQuarter] date NULL,
    [LastDayOfQuarter] date NULL,
    [FirstDayOfYear] date NULL,
    [LastDayOfYear] date NULL,
    [IsHolidayUSA] bit NULL,
    [IsWeekday] bit NULL,
    [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Date'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
    [GeographyID] int NOT NULL,
    [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Geography'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0 
    )
    ;
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
    [HackneyLicenseID] int NOT NULL,
    [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'HackneyLicense'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
    [MedallionID] int NOT NULL,
    [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Medallion'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Time]
    (
    [TimeID] int NOT NULL,
    [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HourNumber] tinyint NOT NULL,
    [MinuteNumber] tinyint NOT NULL,
    [SecondNumber] tinyint NOT NULL,
    [TimeInSecond] int NOT NULL,
    [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [DayTimeBucketGroupKey] int NOT NULL,
    [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
    LOCATION = 'Time'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
    )
    WITH
    (
    LOCATION = 'Trip2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = compressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
    [DateID] int NOT NULL,
    [GeographyID] int NOT NULL,
    [PrecipitationInches] float NOT NULL,
    [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
    LOCATION = 'Weather2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    ```

### <a name="create-table-as-select-ctas"></a>Инструкция CREATE TABLE AS SELECT (CTAS)

5. Загрузите данные из внешних таблиц в экземпляр хранилища данных SQL. 
    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

    > [!NOTE]
    > Вы загружаете несколько гигабайт данных и сжимаете их в высокопроизводительные кластерные индексы сolumnstore. Выполните запрос динамического административного представления и сделайте перерыв на кофе, пока хранилище данных SQL Azure выполняет объемное задание обработки.
    >

6. Создайте новый запрос и понаблюдайте, как поступают данные, в динамическом административном представлении.

    ```sql
    SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
    on r.request_id = s.request_id
    WHERE
    r.[label] = 'CTAS : Load [dbo].[Date]' OR
    r.[label] = 'CTAS : Load [dbo].[Geography]' OR
    r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
    r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
    r.[label] = 'CTAS : Load [dbo].[Time]' OR
    r.[label] = 'CTAS : Load [dbo].[Weather]' OR
    r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
    r.command,
    s.request_id,
    r.status
    ORDER BY
    nbr_files desc, gb_processed desc;
    ```

7. Просмотрите все запросы в системе.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

8. Посмотрите, как данные упорядоченно загружаются в хранилище данных SQL Azure.

    ![Просмотр загрузки данных](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)



## <a name="querying-data"></a>Выполнение запросов к данным 

### <a name="scan-query-with-scaling"></a>Проверка запроса при масштабировании

Давайте выясним, как масштабирование влияет на скорость выполнения запросов.

Прежде чем начать, давайте уменьшим масштаб нашей операции до 100 DWU, чтобы получить представление о производительности одного вычислительного узла.

1. Перейдите на портал и выберите экземпляр хранилища данных SQL.

2. Выберите пункт "Масштаб" в колонке "Хранилище данных SQL". 

    ![Масштабирование хранилища данных на портале](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Уменьшите производительность 100 DWU и нажмите кнопку "Сохранить".

    ![Масштабирование и сохранение](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Подождите завершения операции масштабирования.

    > [!NOTE]
    > Обратите внимание, что операции масштабирования **завершают** выполняющиеся запросы и не позволяют запустить новые.
    >
    
5. Выполните операцию сканирования данных о поездках, выбрав первый миллион записей для всех столбцов. Если вы хотите побыстрее перейти к следующему шагу, выберите меньшее число строк.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```

Запишите время, затраченное на выполнение этой операции.

6. Увеличьте масштаб экземпляра до 400 DWU. Помните, что каждые 100 DWU — это дополнительный вычислительный узел в хранилище данных SQL Azure.

7. Выполните запрос повторно. Вы заметите существенную разницу. 

> [!NOTE]
> Хранилище данных SQL — это платформа массовой параллельной обработки (MPP). При параллельном выполнении запросов и операций на нескольких узлах используется реальная мощность хранилища данных SQL Azure.
>

### <a name="join-query-with-statistics"></a>Присоединение статистки к запросу

1. Выполните запрос, который объединит таблицу дат с таблицей поездок.

    ```sql
    SELECT TOP (1000000) dt.[DayOfWeek]
    ,tr.[MedallionID]
    ,tr.[HackneyLicenseID]
    ,tr.[PickupTimeID]
    ,tr.[DropoffTimeID]
    ,tr.[PickupGeographyID]
    ,tr.[DropoffGeographyID]
    ,tr.[PickupLatitude]
    ,tr.[PickupLongitude]
    ,tr.[PickupLatLong]
    ,tr.[DropoffLatitude]
    ,tr.[DropoffLongitude]
    ,tr.[DropoffLatLong]
    ,tr.[PassengerCount]
    ,tr.[TripDurationSeconds]
    ,tr.[TripDistanceMiles]
    ,tr.[PaymentType]
    ,tr.[FareAmount]
    ,tr.[SurchargeAmount]
    ,tr.[TaxAmount]
    ,tr.[TipAmount]
    ,tr.[TollsAmount]
    ,tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
    join
    dbo.[Date] as dt
    on tr.DateID = dt.DateID
    ```

    Как можно догадаться, запрос выполняется намного дольше, если данные в случайном порядке распределяются между узлами, особенно в случае объединения, как в этом запросе.

2. Давайте посмотрим, как изменится этот запрос при создании статистики для присоединяемого столбца:

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > Хранилище данных SQL не управляет статистикой автоматически. Статистика важна для производительности запросов, поэтому мы настоятельно рекомендуем создавать и обновлять статистику.
    > 
    > **Статистику рекомендуется вести в столбцах, которые являются частью объединения, используются в предложении WHERE или GROUP BY**.
    >

3. Снова выполните запрос из раздела "Предварительные требования" и понаблюдайте за различиями в производительности. Хотя различия в производительности запроса не будут столь значительными, как при масштабировании, вы должны заметить явное ускорение. 

## <a name="next-steps"></a>Дальнейшие действия

Теперь можно выполнить запрос и изучить данные. Ознакомьтесь с нашими советами и рекомендациями.

Если изучения данных на сегодня достаточно, не забудьте приостановить экземпляр хранилища. В рабочей среде вы можете добиться огромной экономии, приостанавливая и масштабируя хранилище в соответствии с потребностями своего бизнеса.

![Приостановить](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>Полезные ссылки

[Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL]

[Рекомендации по использованию хранилища данных SQL Azure]

[Мониторинг рабочей нагрузки с помощью динамических административных представлений]

[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse] (10 лучших рекомендаций по созданию реляционного хранилища данных большого объема)

[Migrating data to Azure SQL Data Warehouse in practice] (Перенос данных в хранилище данных SQL Azure на практике)


[Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Рекомендации по использованию хранилища данных SQL Azure]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[Мониторинг рабочей нагрузки с помощью динамических административных представлений]: sql-data-warehouse-manage-monitor.md
[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/ (10 лучших рекомендаций по созданию реляционного хранилища данных большого объема)
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/ (Перенос данных в хранилище данных SQL Azure на практике)



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[Предварительные требования]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx



<!--HONumber=Jan17_HO3-->


