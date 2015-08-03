<properties 
	pageTitle="Перемещение данных в SQL Server в Azure | Microsoft Azure" 
	description="Перемещение данных в SQL Server в Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="fashah;garye;mohabib;bradsev" />

#Перемещение данных в SQL Server в Azure

В этом документе описано перемещение данных из неструктурированных файлов (CSV или TSV) или из локального SQL Server в SQL Server в Azure. Эта задача является частью процесса обработки аналитических данных и технологии расширенного анализа (ADAPT) в рамках машинного обучения Azure.


<table>

<tr>
<td><b>ИСТОЧНИК</b></td>
<td colspan="2" align="center"><b>НАЗНАЧЕНИЕ</b></td>
</tr>

<tr>
  <td></td>
  <td><b>Виртуальная машина SQL Server в Azure</b></td>
</tr>

<tr>
  <td><b>Неструктурированный файл</b></td>  
  <td>
    1. <a href="#insert-tables-bcp">Служебная программа командной строки для массового копирования (BCP)</a><br>
    2. <a href="#insert-tables-bulkquery">SQL-запрос на массовую вставку</a><br>
    3. <a href="#sql-builtin-utilities">Графические служебные программы, встроенные в SQL Server</a>
  </td>
</tr>
<tr>
  <td><b>Локальный сервер SQL Server</b></td>
  <td>
    1. <a href="#export-flat-file">Экспорт в неструктурированный файл</a><br>
    2. <a href="#sql-migration">Мастер миграции баз данных SQL</a> <br>    
    3. <a href="#sql-backup">Восстановление и резервное копирование базы данных</a> <br>
  </td>
</tr>
</table>

Обратите внимание на то, что в этом документе предполагается выполнение команд SQL в среде SQL Server Management Studio или в обозревателе базы данных Visual Studio.

> [AZURE.TIP]Вы также можете воспользоваться [фабрикой данных Azure](https://azure.microsoft.com/ru-ru/services/data-factory/), чтобы создать и назначить канал, который переносит данные в виртуальную машину SQL Server в среде Azure. Дополнительные сведения см. в статье [Копирование данных с помощью фабрики данных Azure – действие копирования](../data-factory/data-factory-copy-activity.md).


## <a name="sqlonazurevm"></a>Перемещение данных в виртуальную машину SQL Server в Azure

В этом разделе описывается процесс перемещения данных в виртуальную машину SQL Server в Azure. Если вы еще не установили виртуальную машину SQL Server, подготовьте новую виртуальную машину SQL Server для расширенного анализа, как описано в статье [Настройка виртуальной машины Azure SQL Server в качестве сервера IPython Notebook для расширенного анализа](machine-learning-data-science-setup-sql-server-virtual-machine.md).

В этом документе описано перемещение данных из следующих источников данных:
  
1. [Из неструктурированных файлов](#filesource_to_sqlonazurevm) 
2. [С локального сервера SQL Server](#sqlonprem_to_sqlonazurevm)


### <a name="filesource_to_sqlonazurevm"></a>Источник файла

Если данные (упорядоченные по строкам и столбцам) хранятся в неструктурированном файле, этот файл можно переместить в виртуальную машину SQL Server в Azure с помощью следующих средств:

1. [Служебная программа командной строки для массового копирования (BCP)](#insert-tables-bcp) 
2. [SQL-запрос на массовую вставку](#insert-tables-bulkquery)
3. [Графические служебные программы, встроенные в SQL Server (импорт или экспорт, службы SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Служебная программа командной строки для массового копирования (BCP)

BCP — это служебная программа командной строки, устанавливаемая вместе с SQL Server, которая предоставляет один из самых быстрых способов перемещения данных. Она работает во всех трех вариантах SQL Server (в локальном SQL Server, SQL Azure и виртуальной машине SQL Server в Azure).

> [AZURE.NOTE]**Где следует размещать данные для программы BCP?** Это не обязательно, но если файлы, содержащие источник данных, расположены на одном компьютере с целевым сервером SQL, это позволяет ускорить перемещение (скорость сети и скорость ввода-вывода на локальном диске). Перемещать неструктурированные файлы, содержащие данные, на компьютер с SQL Server можно с помощью различных инструментов копирования файлов, таких как [AZCopy](../storage-use-azcopy.md) и [обозреватель хранилищ Azure](https://azurestorageexplorer.codeplex.com/), или средств копирования и вставки по протоколу удаленного рабочего стола (RDP) ОС Windows.

1. Убедитесь, что базы данных и таблицы создаются в целевой базе данных SQL Server. В примере ниже показано, как выполнить эту задачу с помощью команд `Create Database` и `Create Table`.

		CREATE DATABASE <database_name>
	
		CREATE TABLE <tablename>
		(
			<columnname1> <datatype> <constraint>,
			<columnname2> <datatype> <constraint>,
			<columnname3> <datatype> <constraint>
		) 
	
2. Создайте файл форматирования с описанием схемы для таблицы, выполнив следующую команду в командной строке компьютера, на котором установлена программа BCP.

	`bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

3. Вставьте данные в базу данных с помощью команды BCP, как показано ниже. Запуск выполняется из командной строки при условии, что на этом компьютере установлен SQL Server:

	`bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Оптимизация операций вставки BCP**. Подробную информацию об оптимизации таких операций вставки см. в статье [Рекомендации по оптимизации массового импорта данных](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx).

#### <a name="insert-tables-bulkquery-parallel"></a>Распараллеливание операций вставки для ускорения перемещения данных

Вы можете ускорить процесс перемещения данных большого размера, выполняя несколько команд BCP одновременно в сценарии PowerShell.

> [AZURE.NOTE]**Прием данных большого размера**.Чтобы оптимизировать загрузку больших и очень больших наборов данных, разбейте таблицы логических и физических баз данных на разделы, используя несколько групп файлов и секционированных таблиц. Дополнительную информацию о создании секционированных таблиц и загрузке в них данных см. в разделе [Секционированные таблицы SQL с параллельной загрузкой](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).


В примере сценария PowerShell ниже показаны параллельные операции вставки при использовании программы BCP.
	
	$NO_OF_PARALLEL_JOBS=2

	 Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
	 # Define what each job does
	   $ScriptBlock = {
		   param($partitionnumber)

		   #Explictly using SQL username password
		   bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

			#Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
			#bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
	  }
	

	# Background processing of all partitions
	for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
	{
	  Write-Debug "Submit loading partition # $i"
	  Start-Job $ScriptBlock -Arg $i	  
	}
	

	# Wait for it all to complete
	While (Get-Job -State "Running")
	{
	  Start-Sleep 10
	  Get-Job
	}
	
	# Getting the information back from the jobs
	Get-Job | Receive-Job
	Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>SQL-запрос на массовую вставку

[SQL-запрос на массовую вставку](https://msdn.microsoft.com/library/ms188365) можно использовать для импорта в базу данных из файлов на основе строк или столбцов (поддерживаемые типы см. [здесь](https://msdn.microsoft.com/library/ms188609)).

Ниже приведены некоторые примеры команд для массовой вставки:

1. Перед импортом проанализируйте данные, задайте все необходимые пользовательские настройки и убедитесь, что в базе данных SQL Server для всех специальных полей, таких как дата, задан одинаковый формат. Ниже показано, как задать формат даты «год-месяц-день» (если в данных содержатся даты в формате «год-месяц-день»):

		SET DATEFORMAT ymd;	
	
2. Импорт данных с помощью операторов массового импорта:

    	BULK INSERT <tablename>
    	FROM	
    	'<datafilename>'
    	WITH 
    	(
		FirstRow=2,
    	FIELDTERMINATOR =',', --this should be column separator in your data
    	ROWTERMINATOR ='\n'   --this should be the row separator in your data
    	)
 	  

### <a name="sql-builtin-utilities"></a>Служебные программы, встроенные в SQL Server

Службы SQL Server Integrations Services (SSIS) можно использовать для импорта данных из неструктурированного файла в виртуальную машину SQL Server в Azure. Службы SSIS доступны в двух средах Studio. Дополнительную информацию см. в статье [Службы Integration Services (SSIS) и среды Studio](https://technet.microsoft.com/library/ms140028.aspx).

- Дополнительную информацию о SQL Server Data Tools см. в статье [Средства Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx).  
- Дополнительную информацию о мастере импорта и экспорта см. в статье [Мастер импорта и экспорта SQL Server](https://msdn.microsoft.com/library/ms141209.aspx).

### <a name="sqlonprem_to_sqlonazurevm"></a>Перемещение данных из локального SQL Server

Данные в локальном SQL Server можно переместить следующими способами:

1. [Экспорт в неструктурированный файл](#export-flat-file) 
2. [Мастер миграции баз данных SQL](#sql-migration)
3. [Восстановление и резервное копирование базы данных](#sql-backup)

Описание каждого из этих способов см. ниже.

#### <a name="export-flat-file"></a>Экспорт в неструктурированный файл

Для массового экспорта данных из локального SQL Server можно использовать разные способы, которые описаны [здесь](https://msdn.microsoft.com/library/ms175937.aspx). В этом документе в качестве примера описано использование программы массового копирования (BCP). После экспорта данных в неструктурированный файл этот файл можно импортировать в другой SQL Server с помощью операции массового импорта.

1. Экспортируйте данные из локального SQL Server в файл с помощью служебной программы BCP, используя следующую команду:

	`bcp dbname..tablename out datafile.tsv -S	servername\sqlinstancename -T -t \t -t \n -c`

2. Создайте базу данных и таблицу в виртуальной машине SQL Server в Azure с помощью команд `create database` и `create table` для схемы таблицы, экспортированной на шаге 1.

3. Создайте файл форматирования для описания схемы таблицы экспортируемых или импортируемых данных. Дополнительную информацию о файле форматирования см. [здесь](https://msdn.microsoft.com/library/ms191516.aspx).

	Создание файла форматирования при запуске служебной программы BCP на компьютере с SQL Server

		bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

	Создание файла форматирования при удаленном запуске BCP для SQL Server

		bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
		
	
4. С помощью любого из методов, описанных в разделе [Перемещение данных из файла источника](#filesource_to_sqlonazurevm), переместите данные неструктурированных файлов в SQL Server.

#### <a name="sql-migration"></a>Мастер миграции баз данных SQL

[Мастер миграции баз данных SQL Server](http://sqlazuremw.codeplex.com/) представляет собой удобный способ перемещения данных между двумя экземплярами SQL Server. Пользователь может сопоставлять схемы данных между источниками и таблицами назначения, выбирать типы столбцов и выполнять другие функции. Этот мастер использует программу массового копирования (BCP). Ниже приведен снимок экрана приветствия мастера миграции баз данных SQL.

![Мастер миграции SQL Server][2]

#### <a name="sql-backup"></a>Восстановление и резервное копирование базы данных

SQL Server поддерживает:

1. [Резервное копирование и восстановление баз данных](https://msdn.microsoft.com/library/ms187048.aspx) (экспорт в локальный файл и экспорт BACPAC-файла в большой двоичный объект) и [приложения уровня данных](https://msdn.microsoft.com/library/ee210546.aspx) (с использованием BACPAC-файла). 
2. Непосредственное создание виртуальных машин SQL Server в Azure с использованием скопированной базы данных или копирование в существующую базу данных SQL Azure. Дополнительную информацию см. в статье [Использование мастера копирования базы данных](https://msdn.microsoft.com/library/ms188664.aspx). 

Ниже приведен снимок экрана параметров резервного копирования и восстановления базы данных в среде SQL Server Management Studio.

![Средство импорта данных из SQL Server][1]


[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png

 

<!---HONumber=July15_HO4-->