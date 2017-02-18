---
title: "Руководство по началу работы с хранилищем данных SQL Azure | Документация Майкрософт"
description: "Из этого руководства вы узнаете, как подготовить и загрузить данные в хранилище данных SQL Azure, а также получите основные сведения о масштабировании, приостановке и настройке."
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
ms.date: 01/26/2017
ms.author: elbutter;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2c88c1abd2af7a1ca041cd5003fd1f848e1b311c
ms.openlocfilehash: 12f72e76ee991dfb701637847f2e406cd0f8c449


---
# <a name="get-started-with-sql-data-warehouse"></a>Начало работы с хранилищем данных SQL

Из этого руководства вы узнаете, как подготовить и загрузить данные в хранилище данных SQL Azure, а также получите основные сведения о масштабировании, приостановке и настройке. После завершения работы с документом вы будете уметь использовать запросы и просматривать хранилища данных.

**Предполагаемое время выполнения.**. Работа с этим комплексным руководством и примером кода занимает около 30 минут при условии, что все предварительные требования выполнены. 

## <a name="prerequisites"></a>Предварительные требования

В руководстве предполагается, что вы уже знакомы с основными понятиями хранилища данных SQL. Если это не так, рекомендуем прочитать статью [Что такое хранилище данных SQL](sql-data-warehouse-overview-what-is.md). 

### <a name="sign-up-for-microsoft-azure"></a>Зарегистрируйтесь для Microsoft Azure
Если у вас еще нет учетной записи Microsoft Azure, зарегистрируйте ее, чтобы использовать эту службу. Если у вас уже есть учетная запись, этот шаг можно пропустить. 

1. Перейдите на страницу учетной записи [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/).
2. Создайте бесплатную учетную запись Azure или купите платную.
3. Следуйте указаниям на экране.

### <a name="install-appropriate-sql-client-drivers-and-tools"></a>Установка соответствующих клиентских драйверов и средств SQL

Большинство клиентских средств SQL могут подключаться к хранилищу данных SQL с помощью JDBC, ODBC или ADO.NET. Из-за большого количества функций T-SQL, которые поддерживает хранилище данных SQL, не все клиентские приложения полностью совместимы с хранилищем данных SQL.

Если вы работаете с ОС Windows, рекомендуем использовать либо [Visual Studio], либо [SQL Server Management Studio].

[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-a-sql-data-warehouse"></a>Создание хранилища данных SQL

Хранилище данных SQL — это специальный тип базы данных, предназначенный для вычислений массовым параллелизмом. База данных распределяется между несколькими узлами и обрабатывает запросы параллельно. У хранилища данных SQL есть управляющий узел, который координирует действия всех узлов. Сами же узлы для управления данными используют базу данных SQL.  

> [!NOTE]
> Создание хранилища данных SQL может привести к дополнительным расходам.  Дополнительные сведения см. на странице [цен на хранилище данных SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>

### <a name="create-a-data-warehouse"></a>Создание хранилища данных

1. Войдите на [портал Azure](https://portal.azure.com).
2. Последовательно выберите **Создать** > **Базы данных** > **Хранилище данных SQL**.

    ![Колонка "Создать"](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![Выбор хранилища данных](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Укажите сведения о развертывании.

    **Имя базы данных**: выберите любое имя. При наличии нескольких хранилищ данных рекомендуется включать в имена такие сведения, как регион и среда, например *mydw-westus-1-test*.

    **Подписка**: ваша подписка Azure.

    **Группа ресурсов**: создайте группу ресурсов или выберите имеющуюся.
    > [!NOTE]
    > Группы ресурсов можно использовать для администрирования ресурсов, например для определения области управления доступом или развертывания по шаблону. Дополнительные сведения о группах ресурсов Azure и рекомендации см. [здесь](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups).

    **Источник**: пустая база данных.

    **Сервер**: выберите сервер, который мы создали во время прохождения раздела "Предварительные требования".

    **Параметры сортировки**: оставьте параметры сортировки по умолчанию (SQL_Latin1_General_CP1_CI_AS).

    **Выбор уровня производительности**: рекомендуем начать со стандартного значения 400DWU.

4. Установите флажок **Закрепить на панели мониторинга**
    ![Закрепить на панели мониторинга](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png).

5. Подождите, пока завершится развертывание хранилища данных. Обычно этот процесс занимает несколько минут. Портал уведомит вас, когда хранилище данных будет готово к использованию. 

## <a name="connect-to-sql-data-warehouse"></a>Подключение к хранилищу данных SQL

В этом руководстве для подключения к хранилищу данных используется SQL Server Management Studio (SSMS). Подключиться к хранилищу данных SQL можно через следующие поддерживаемые соединители: ADO.NET, JDBC, ODBC и PHP. Помните, что функциональные возможности средств, не поддерживаемых Майкрософт, могут быть ограничены.


### <a name="get-connection-information"></a>Получение сведений о подключении

Подключение к хранилищу данных SQL выполняется через логический сервер SQL Server, созданный в разделе "Предварительные требования".

1. Выберите хранилище данных на панели мониторинга или найдите его в списке ресурсов.

    ![Панель мониторинга хранилища данных SQL](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Найдите полное имя логического сервера SQL Server.

    ![Выбор имени сервера](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Откройте SSMS. С помощью обозревателя объектов подключитесь к этому серверу, используя учетные данные администратора сервера, созданные в разделе "Предварительные требования".

    ![Подключение с помощью SSMS](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Если все сделано правильно, вы должны подключиться к логическому серверу SQL Server. Поскольку вы вошли как администратор сервера, вы можете подключиться к любой базе данных, размещенной на этом сервере, включая базу данных master. 

Существует только одна учетная запись администратора сервера, обладающая наибольшими правами. Следите за тем, чтобы пароль администратора не был известен слишком большому количеству людей в организации. 

Также у вас может быть учетная запись администратора Azure Active Directory. Сведения о ней не входят в объем этой статьи. Если вы хотите узнать больше об использовании аутентификации Azure Active Directory, см. статью [Подключение к Базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

Далее мы рассмотрим создание дополнительных пользователей и имен для входа.


## <a name="create-a-database-user"></a>Создание пользователя базы данных

На этом шаге мы создадим учетную запись пользователя, который будет подключаться к хранилищу данных. Также мы покажем, как предоставить этому пользователю возможность выполнять запросы с большим объемом памяти и ресурсов ЦП.

### <a name="notes-about-resource-classes-for-allocating-resources-to-queries"></a>Примечания о классах ресурсов для выделения ресурсов запросам

- В целях защиты данных не следует выполнять запросы к производственным базам данных от имени администратора сервера. Эта учетная запись обладает наибольшими правами, и ее использование для выполнения операций с данными пользователей представляет риск для ваших данных. Кроме того, поскольку учетная запись администратора сервера предназначена для управления, она выполняет операции с выделением только небольшого объема памяти и ресурсов ЦП. 

- В хранилище данных SQL есть предварительно определенные роли базы данных, называемые классами ресурсов. Они используются для выделения пользователям различных объемов памяти, ресурсов ЦП и слотов параллельной обработки. Каждый пользователь может принадлежать к малому, среднему, большому или очень большому классу ресурсов. Класс ресурсов пользователя определяет ресурсы, которыми располагает пользователь для выполнения запросов и операций загрузки.

- Для оптимизации сжатия данных пользователю, как правило, требуется загрузка с выделением больших и очень больших ресурсов. Дополнительные сведения о классах ресурсов см. [здесь](./sql-data-warehouse-develop-concurrency.md#resource-classes).

### <a name="create-an-account-that-can-control-a-database"></a>Создание учетной записи, позволяющей управлять базой данных

Поскольку вы вошли в качестве администратора сервера, у вас есть разрешения на создание пользователей и имен для входа.

1. Используя SSMS или другой клиент запросов, откройте новый запрос к базе данных **master**.

    ![Создание запроса к базе данных master](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Создание запроса к базе данных master1](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. В окне запроса выполните следующую команду T-SQL, чтобы создать имя для входа MedRCLogin и пользователя с именем LoadingUser. Это имя для входа позволяет подключаться к логическому серверу SQL.

    ```sql
    CREATE LOGIN MedRCLogin WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN MedRCLogin;
    ```

3. Теперь, выполняя запрос к *базе данных хранилища данных SQL*, создайте пользователя базы данных с именем для входа, которое вы создали для доступа к базе данных и выполнения в ней операций.

    ```sql
    CREATE USER LoadingUser FOR LOGIN MedRCLogin;
    ```

4. Предоставьте пользователю базы данных разрешения на управление базой данных с именем NYT. 

    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Если имя базы данных содержит дефисы, обязательно заключите его в квадратные скобки! 
    >

### <a name="give-the-user-medium-resource-allocations"></a>Выделение для пользователя среднего объема ресурсов

1. Выполните следующую команду T-SQL, чтобы сделать пользователя членом класса средних ресурсов, который называется mediumrc. 

    ```sql
    EXEC sp_addrolemember 'mediumrc', 'LoadingUser';
    ```
    > [!NOTE]
    > Дополнительные сведения о параллелизме и классах ресурсов см. [здесь](sql-data-warehouse-develop-concurrency.md#resource-classes). 
    >

2. Подключитесь к логическому серверу с помощью новых учетных данных.

    ![Вход с помощью новых учетных данных](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="load-data-from-azure-blob-storage"></a>Загрузка данных из хранилища BLOB-объектов Azure

Теперь все готово к загрузке данных в хранилище данных. В этом шаге мы покажем, как загрузить данные о такси Нью-Йорка из общедоступного BLOB-объекта хранилища Azure. 

- Распространенный способ загрузки данных в хранилище данных SQL заключается в том, чтобы сначала переместить данные в хранилище BLOB-объектов Azure, а затем загрузить их в хранилище данных. Чтобы упростить понимание процесса загрузки, мы уже разместили данные о такси Нью-Йорка в общедоступном BLOB-объекте хранилища Azure. 

- Сведения о том, как переместить данные в хранилище BLOB-объектов Azure или загрузить их в хранилище данных SQL непосредственно из источника, см. статью [Загрузка данных в хранилище данных Azure SQL](sql-data-warehouse-overview-load.md).


### <a name="define-external-data"></a>Определение внешних данных

1. Создайте главный ключ. Главный ключ создается для каждой базы данных только один раз. 

    ```sql
    CREATE MASTER KEY;
    ```

2. Определите расположение BLOB-объекта Azure, содержащего данные о такси.  

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```

3. Определите форматы внешних файлов.

    С помощью команды ```CREATE EXTERNAL FILE FORMAT``` можно указать формат файлов, содержащих внешние данные. Эти файлы содержат текст, разделенный одним или несколькими символами, которые называются разделителями. В целях демонстрации данные о такси хранятся как в несжатом виде, так и в виде сжатых данных в формате gzip.

    Выполните следующие команды T-SQL, чтобы определить два разных формата: несжатый и сжатый.

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( 
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH ( 
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
            STRING_DELIMITER = '',
        DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        ),
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

4.  Создайте схему для формата внешних файлов. 

    ```sql
    CREATE SCHEMA ext;
    ```
5. Создайте внешние таблицы. Эти таблицы ссылаются на данные, содержащиеся в хранилище BLOB-объектов Azure. Выполните следующие команды T-SQL, чтобы создать несколько внешних таблиц, все из которых будут указывать на BLOB-объект Azure, определенный нами ранее во внешнем источнике данных.

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
        LOCATION = 'Date',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    
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
        LOCATION = 'Geography',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0 
    );
        
    
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'HackneyLicense',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
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
        LOCATION = 'Medallion',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
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
        LOCATION = 'Time',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
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
        LOCATION = 'Trip2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = compressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
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
        LOCATION = 'Weather2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    ```

### Import the data from Azure blob storage.

SQL Data Warehouse supports a key statement called CREATE TABLE AS SELECT (CTAS). This statement creates a new table based on the results of a select statement. The new table has the same columns and data types as the results of the select statement.  This is an elegant way to import data from Azure blob storage into SQL Data Warehouse.

1. Run this script to import your data.

    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    
    CREATE TABLE [dbo].[Geography]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    
    CREATE TABLE [dbo].[Medallion]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    
    CREATE TABLE [dbo].[Time]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    
    CREATE TABLE [dbo].[Weather]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    
    CREATE TABLE [dbo].[Trip]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

2. View your data as it loads.

   You’re loading several GBs of data and compressing it into highly performant clustered columnstore indexes. Run the following query that uses a dynamic management views (DMVs) to show the status of the load. After starting the query, grab a coffee and a snack while SQL Data Warehouse does some heavy lifting.
    
    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM 
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
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
        nbr_files desc, 
        gb_processed desc;
    ```

3. View all system queries.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Enjoy seeing your data nicely loaded into your Azure SQL Data Warehouse.

    ![See Data Loaded](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)


## Improve query performance

There are several ways to improve query performance and to achieve the high-speed performance that SQL Data Warehouse is designed to provide.  

### See the effect of scaling on query performance 

One way to improve query performance is to scale resources by changing the DWU service level for your data warehouse. Each service level costs more, but you can scale back or pause resources at any time. 

In this step, you compare performance at two different DWU settings.

First, let's scale the sizing down to 100 DWU so we can get an idea of how one compute node might perform on its own.

1. Go to the portal and select your SQL Data Warehouse.

2. Select scale in the SQL Data Warehouse blade. 

    ![Scale DW From portal](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Scale down the performance bar to 100 DWU and hit save.

    ![Scale and save](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Wait for your scale operation to finish.

    > [!NOTE]
    > Queries cannot run while changing the scale. Scaling **kills** your currently running queries. You can restart them when the operation is finished.
    >
    
5. Do a scan operation on the trip data, selecting the top million entries for all the columns. If you're eager to move on quickly, feel free to select fewer rows. Take note of the time it takes to run this operation.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```
6. Scale your data warehouse back to 400 DWU. Remember, each 100 DWU is adding another compute node to your Azure SQL Data Warehouse.

7. Run the query again! You should notice a significant difference. 

> [!NOTE]
> Since SQL Data Warehouse uses massively parallel processing. Queries that scan or perform analytic functions on millions of rows experience the true power of
> Azure SQL Data Warehouse.
>

### See the effect of statistics on query performance

1. Run a query that joins the Date table with the Trip table

    ```sql
    SELECT TOP (1000000) 
        dt.[DayOfWeek],
        tr.[MedallionID],
        tr.[HackneyLicenseID],
        tr.[PickupTimeID],
        tr.[DropoffTimeID],
        tr.[PickupGeographyID],
        tr.[DropoffGeographyID],
        tr.[PickupLatitude],
        tr.[PickupLongitude],
        tr.[PickupLatLong],
        tr.[DropoffLatitude],
        tr.[DropoffLongitude],
        tr.[DropoffLatLong],
        tr.[PassengerCount],
        tr.[TripDurationSeconds],
        tr.[TripDistanceMiles],
        tr.[PaymentType],
        tr.[FareAmount],
        tr.[SurchargeAmount],
        tr.[TaxAmount],
        tr.[TipAmount],
        tr.[TollsAmount],
        tr.[TotalAmount],
    FROM [dbo].[Trip] as tr
        JOIN dbo.[Date] as dt
        ON  tr.DateID = dt.DateID
    ```

    This query takes a while because SQL Data Warehouse has to shuffle data before it can perform the join. Joins do not have to shuffle data if they are designed to join data in the same way it is distributed. That's a deeper subject. 

2. Statistics make a difference. 
3. Run this statement to create statistics on the join columns.

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > SQL DW does not automatically manage statistics for you. Statistics are important for query
    > performance and it is highly recommended you create and update statistics.
    > 
    > **You gain the most benefit by having statistics on columns involved in joins, columns
    > used in the WHERE clause and columns found in GROUP BY.**
    >

3. Run the query from Prerequisites again and observe any performance differences. While the differences in query performance will not be as drastic as scaling up, you should notice a  speed-up. 

## Next steps

You're now ready to query and explore. Check out our best practices or tips.

If you're done exploring for the day, make sure to pause your instance! In production, you can experience enormous 
savings by pausing and scaling to meet your business needs.

![Pause](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## Useful readings

[Concurrency and Workload Management][]

[Best practices for Azure SQL Data Warehouse][]

[Query Monitoring][]

[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse][]

[Migrating Data to Azure SQL Data Warehouse][]

[Concurrency and Workload Management]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Best practices for Azure SQL Data Warehouse]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[Query Monitoring]: sql-data-warehouse-manage-monitor.md
[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/
[Migrating Data to Azure SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[Prerequisites]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx



<!--HONumber=Feb17_HO1-->


