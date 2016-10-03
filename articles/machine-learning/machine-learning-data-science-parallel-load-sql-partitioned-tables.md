<properties 
	pageTitle="Параллельный массовый импорт данных с использованием таблиц секционирования SQL | Microsoft Azure" 
	description="Параллельный массовый импорт данных с использованием таблиц секционирования SQL" 
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
	ms.author="bradsev" />

# Параллельный массовый импорт данных с использованием таблиц секционирования SQL

В этом документе описывается создание секционированных таблиц для быстрого параллельного массового импорта данных в Базу данных SQL Server. При загрузке и передаче массивов "больших данных" в базу данных SQL с помощью _секционированных таблиц и представлений_ можно оптимизировать импорт информации в эту базу, а также выполнение последующих запросов.


## Создание новой базы данных и набора групп файлов

- [Создайте базу данных](https://technet.microsoft.com/library/ms176061.aspx) (если она еще не существует).
- Добавьте группы файлов базы данных в базу данных, которая будет содержать секционированные физические файлы.

  Примечание. Это можно сделать с помощью команды [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx) (при создании базы) или команды [ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx) (если база данных уже существует).

- При необходимости добавьте один или несколько файлов в каждую группу файлов базы данных.

 > [AZURE.NOTE] Укажите целевую группу файлов, которая будет содержать данные для этого секционирования, и имена физических файлов базы данных, в которых будут храниться данные группы файлов.
 
В следующем примере создается новая база данных с тремя группами файлов в отличие от основных групп и групп журналов, содержащих по одному физическому файлу. Файлы базы данных создаются в папке данных SQL Server по умолчанию, настроенной в экземпляре SQL Server. Дополнительную информацию о расположении файлов по умолчанию см. в статье [Расположение файлов для экземпляра по умолчанию и именованных экземпляров SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);
    
    EXECUTE ('
    	CREATE DATABASE <database_name>
     	ON  PRIMARY 
    	( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
     	FILEGROUP [filegroup_1] 
    	( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
     	FILEGROUP [filegroup_2] 
    	( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
     	FILEGROUP [filegroup_3] 
    	( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
     	LOG ON 
    	( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
    
## Создание секционированной таблицы

Создайте секционированные таблицы в соответствии со схемой данных, сопоставленной с группами файлов базы данных, созданных на предыдущем шаге. При массовом импорте данных в секционированные таблицы записи будут распределены между группами файлов в соответствии со схемой секционирования, как описано ниже.

**Чтобы создать таблицу секционирования:**

- [Создайте функцию секционирования](https://msdn.microsoft.com/library/ms187802.aspx), которая определяет диапазон значений или границ для каждой отдельной таблицы секционирования, чтобы, например, ограничить секции по месяцам (поле\_даты\_и\_времени) в 2013 году:

	    CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
	    AS RANGE RIGHT FOR VALUES (
	    	'20130201', '20130301', '20130401',
	    	'20130501', '20130601', '20130701', '20130801',
	    	'20130901', '20131001', '20131101', '20131201' )

- [Создайте схему секционирования](https://msdn.microsoft.com/library/ms179854.aspx), в которой каждый диапазон секционирования в функции секционирования сопоставляется с физической группой файлов, например:

	    CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
	    PARTITION <DatetimeFieldPFN> TO (
	    <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
	    <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
	    <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )

  Совет. Чтобы проверить диапазоны на практике в каждой секции в соответствии с функцией и схемой, выполните следующий запрос:

	    SELECT psch.name as PartitionScheme,
	    	prng.value AS ParitionValue,
	    	prng.boundary_id AS BoundaryID
	    FROM sys.partition_functions AS pfun
	    INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
	    INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
	    WHERE pfun.name = <DatetimeFieldPFN>

- [Создайте секционированные таблицы](https://msdn.microsoft.com/library/ms174979.aspx) в соответствии со схемой данных и укажите схему секционирования и поле ограничений, используемые для секционирования таблицы, например:

	    CREATE TABLE <table_name> ( [include schema definition here] )
	    ON <TablePScheme>(<partition_field>)

Дополнительную информацию см. в статье [Создание секционированных таблиц и индексов](https://msdn.microsoft.com/library/ms188730.aspx).


## Массовый импорт данных для каждой отдельной таблицы секционирования

- Можно использовать BCP, BULK INSERT или другие средства, например [мастер миграции SQL Server](http://sqlazuremw.codeplex.com/). В приведенном ниже примере используется метод BCP.

- [Измените базу данных](https://msdn.microsoft.com/library/bb522682.aspx), заменив схему ведения журнала транзакций на BULK\_LOGGED, что позволит свести к минимуму нагрузку ведения журнала, например:

	    ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED

- Чтобы ускорить загрузку данных, запустите параллельные операции массового импорта. Советы по ускорению импорта массивов «больших данных» в базы SQL Server см. в статье [Загрузка данных емкостью 1 ТБ менее чем за час](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

В следующем сценарии PowerShell приведен пример параллельной загрузки данных с использованием BCP.

    # Set database name, input data directory, and output log directory
	# This example loads comma-separated input data files
	# The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
	# Assumes the input data files include a header line. Loading starts at line number 2.

	$dbname = "<database_name>"
	$indir  = "<path_to_data_files>"
	$logdir = "<path_to_log_directory>"

	# Select authentication mode
    $sqlauth = 0
    
    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>
       
	# Set table name to be loaded, basename of input data files, input format file, and number of partitions
	$tbname = "<table_name>"
	$basename = "<base_input_data_filename_no_extension>"
	$fmtfile = "<full_path_to_format_file>"
   
    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir
      
    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "" + $basename + "_" + $num + ".csv") -o ($logdir + "" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }
    
    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "" + $basename + "_" + $num + ".csv") -o ($logdir + "" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }
    
    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }
    
    Get-Job
    
    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## Создание индексов для оптимизации производительности запросов и объединений

- В случае извлечения данных для моделирования из нескольких таблиц создайте индексы для ключей объединений, чтобы повысить производительность объединений.

- [Создайте индексы](https://technet.microsoft.com/library/ms188783.aspx) (кластеризованные или некластеризованные) для одной и той же целевой группы файлов каждой секции, например:

	    CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
	    ON <TablePScheme>(<partition)field>)
или

	    CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
	    ON <TablePScheme>(<partition)field>)

 > [AZURE.NOTE] Вы можете создать индексы перед массовым импортом данных. Однако это приведет к замедлению загрузки данных.


## Расширенный процесс аналитики и технологии в действии: пример

Полноценный пошаговый пример применения процесса аналитики Кортаны с использованием общедоступного набора данных см. в разделе [Процесс аналитики Кортаны в действии: использование SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
 

<!---HONumber=AcomDC_0921_2016-->