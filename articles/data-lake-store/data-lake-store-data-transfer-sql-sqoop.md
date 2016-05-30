<properties 
   pageTitle="Копирование данных между хранилищем озера данных и базой данных SQL Azure с помощью Sqoop | Microsoft Azure"
   description="Использование Sqoop для копирования данных между базой данных SQL Azure и хранилищем озера данных" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/11/2016"
   ms.author="nitinme"/>

# Копирование данных между хранилищем озера данных и базой данных SQL Azure с помощью Sqoop

Узнайте, как использовать Apache Sqoop для импорта и экспорта данных между базой данных SQL Azure и хранилищем озера данных.
 

## Что такое Sqoop?

Приложения для работы с большими объемами данных являются естественным выбором для обработки неструктурированных и частично структурированных данных, таких как журналы и файлы. Однако может существовать необходимость обработки структурированных данных, хранимых в реляционных базах данных.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) — это средство, предназначенное для передачи данных между реляционными базами данных и репозиторием больших данных, например хранилищем озера данных. Его можно использовать для импорта данных из системы управления реляционными базами данных (RDBMS), такой как база данных SQL Azure, в хранилище озера данных. Затем данные можно преобразовать и проанализировать с помощью рабочих нагрузок больших данных, а после этого экспортировать их обратно в RDBMS. В этом учебнике в качестве реляционной базы данных для импорта и экспорта используется база данных SQL Azure.
 

## Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Включите свою подписку Azure** для общедоступной предварительной версии хранилища озера данных. См. [инструкции](data-lake-store-get-started-portal.md#signup). 
- **Кластер Azure HDInsight** с доступом к учетной записи хранилища озера данных. См. статью [Создание кластера HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md). В этой статье предполагается, что у вас есть кластер HDInsight на платформе Linux с доступом к хранилищу озера данных.
- **База данных SQL Azure**. Инструкции по созданию базы данных см. в статье [Создание базы данных SQL Azure](../sql-database/sql-database-get-started.md).

## Учитесь быстрее с помощью видео?

[Просмотрите это видно](https://mix.office.com/watch/1butcdjxmu114) об использовании Distcp для копирования данных между большими двоичными объектами службы хранилища Azure и хранилищем озера данных Azure.

## Создание образцов таблиц в базе данных SQL Azure

1. Для начала создайте два образца таблиц в базе данных SQL Azure. Используйте [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) или Visual Studio для подключения к базе данных SQL Azure и выполните приведенные ниже запросы.

	**Создание Table1**

		CREATE TABLE [dbo].[Table1]( 
	    [ID] [int] NOT NULL, 
	    [FName] [nvarchar](50) NOT NULL, 
	    [LName] [nvarchar](50) NOT NULL, 
	 	CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
			( 
		   		[ID] ASC 
			) 
		) ON [PRIMARY] 
		GO

	**Создание Table2**

		CREATE TABLE [dbo].[Table2]( 
	    [ID] [int] NOT NULL, 
	    [FName] [nvarchar](50) NOT NULL, 
	    [LName] [nvarchar](50) NOT NULL, 
	 	CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
			( 
		   		[ID] ASC 
			) 
		) ON [PRIMARY] 
		GO

2. В таблицу **Table1** добавьте несколько примеров данных. Оставьте таблицу **Table2** пустой. Мы будем импортировать данные из таблицы **Table1** в хранилище озера данных. Затем мы будем экспортировать данные из хранилища озера данных в таблицу **Table2**. Выполните следующий фрагмент кода.

		 
		INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## Использование Sqoop из кластера Azure HDInsight с доступом к хранилищу озера данных

В кластере HDInsight уже имеются доступные пакеты Sqoop. Если кластер HDInsight настроен для использования хранилища озера данных в качестве дополнительного хранилища, можно использовать Sqoop (без изменения конфигурации) для импорта и экспорта данных между реляционной базой данных (в данном примере это база данных SQL Azure) и учетной записью хранилища озера данных.

1. В этом учебнике предполагается, что вы создали кластер Linux, поэтому для подключения к нему следует использовать SSH. См. подраздел [Подключение к кластеру HDInsight на основе Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).

2. Проверьте, доступна ли учетная запись хранилища озера данных из кластера. Выполните следующую команду из командной строки SSH:

		
		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

	Она должна вывести список файлов и папок в учетной записи хранилища озера данных.

### Импорт данных из базы данных SQL Azure в хранилище озера данных

3. Перейдите в каталог, где доступны пакеты Sqoop. Как правило, это будет каталог `/usr/hdp/<version>/sqoop/bin`. 

4. Импортируйте данные из таблицы **Table1** в учетную запись хранилища озера данных. Используйте следующий синтаксис:

		
		sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

	Обратите внимание, что заполнитель **sql-database-server-name** представляет имя сервера, на котором запущена база данных SQL Azure. Заполнитель **sql-database-name** представляет реальное имя базы данных.

	Например,

		
		sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. Убедитесь, что данные были переданы в учетную запись хранилища озера данных. Выполните следующую команду:

		
		hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

	Вы должны увидеть следующие выходные данные.

		
		-rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
		-rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
		-rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
		-rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
		-rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

	Каждый файл **part-m-*** соответствует строке в исходной таблице **Table1**. Чтобы проверить, просмотрите содержимое файлов part-m-*.


### Экспорт данных из хранилища озера данных в базу данных SQL Azure

6. Экспортируйте данные из учетной записи хранилища озера данных в пустую таблицу **Table2** в базе данных SQL Azure. Используйте приведенный ниже синтаксис.

		
		sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

	Например,

		
		sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. Убедитесь, что данные были отправлены в таблицу базы данных SQL. Используйте [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) или Visual Studio для подключения к базе данных SQL Azure и выполните приведенный ниже запрос.

		
		SELECT * FROM TABLE2

	Вы должны увидеть следующие выходные данные.

	 	ID  FName   LName
		------------------
		1	Neal	Kell
		2	Lila	Fulton
		3	Erna	Myers
		4	Annette	Simpson

## См. также

- [Copy data from Azure Storage Blobs to Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
- [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
- [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0518_2016-->