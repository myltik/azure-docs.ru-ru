<properties
   pageTitle="Преобразование существующих баз данных для использования средств эластичной базы данных"
   description="Преобразование сегментированных баз данных для использования средств эластичной базы данных путем создания диспетчера сопоставления сегментов"
   services="sql-database"
   documentationCenter=""
   authors="SilviaDoomra"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/29/2016"
   ms.author="SilviaDoomra"/>

# Преобразование существующих баз данных для использования средств эластичной базы данных

При наличии горизонтально масштабируемого сегментированного решения можно воспользоваться преимуществами средств эластичной базы данных, руководствуясь изложенными в этой статье методами. После преобразования вы сможете воспользоваться [клиентской библиотекой эластичной базы данных](sql-database-elastic-database-client-library.md) и [средством разбиения и слияния](sql-database-elastic-scale-overview-split-and-merge.md).

Эти методы можно реализовать с помощью [клиентской библиотеки .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) или скриптов PowerShell, которые можно найти на странице [Azure SQL DB - Elastic Database tools scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db) (База данных SQL Azure — сценарии для средств эластичной базы данных). В приведенных здесь примерах используются скрипты PowerShell.

Этот процесс состоит из четырех этапов.

1. Подготовка базы данных диспетчера сопоставления сегментов.
2. Создание карты сегментов.
3. Подготовка отдельных сегментов.  
2. Добавление сопоставлений на карту сегментов.

Дополнительные сведения об объекте ShardMapManager см. в статье [Управление размещением сегментов](sql-database-elastic-scale-shard-map-management.md). Общие сведения о средствах эластичных баз данных см. в статье [Общие сведения о возможностях эластичных баз данных](sql-database-elastic-scale-introduction.md).

## Подготовка базы данных диспетчера сопоставления сегментов
В качестве диспетчера сопоставления сегментов можно использовать новую или существующую базу данных. Выполнить данную операцию достаточно всего один раз.

## Шаг 1. Создание диспетчера сопоставления сегментов
Обратите внимание, что базы данных для диспетчера сопоставления сегментов и сегмента должны отличаться.

	# Create a shard map manager. 
	New-ShardMapManager -UserName '<user_name>' 
	-Password '<password>' 
	-SqlServerName '<server_name>' 
	-SqlDatabaseName '<smm_db_name>' 
	#<server_name> and <smm_db_name> are the server name and database name 
	# for the new or existing database that should be used for storing 
	# tenant-database mapping information.

### Извлечение диспетчера сопоставления сегментов

Созданный диспетчер сопоставления сегментов можно извлечь с помощью этого командлета. Этот шаг требуется выполнять при каждом использовании объекта ShardMapManager.

	# Try to get a reference to the Shard Map Manager  
	$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
	-Password '<password>' 
	-SqlServerName '<server_name>' 
	-SqlDatabaseName '<smm_db_name>' 

  
## Шаг 2. Создание карты сегментов

Следует выбрать одну из следующих моделей:

1. По одному клиенту для каждой базы данных. 
2. Несколько клиентов на одну базу данных (два типа):
	3. сопоставление по диапазонам;
	4. сопоставление по спискам.
 

При использовании модели базы данных с одним клиентом следует применить сопоставление по спискам. В модели с одним клиентом каждому клиенту назначается по одной базе данных. Эта модель подходит для разработчиков SaaS, так как она упрощает управление.

![Сопоставление по спискам][1]

В отличие от этой модели, в модели базы данных с несколькими клиентами для одной базы назначается несколько клиентов (группы клиентов можно распределять по нескольким базам данных). Эта модель более приемлема, если объем данных на каждого клиента должен быть небольшим. В этой модели диапазон клиентов назначается для базы данных путем *сопоставления по диапазонам*.
 

![Сопоставление по диапазонам][2]

Вы можете также реализовать модель с несколькими клиентами путем сопоставления по спискам, чтобы назначить несколько клиентов для одной базы данных. Например, база данных DB1 используется для хранения информации о клиенте 1 и 5, а DB2 хранит данные о клиенте 7 и 10.

![Несколько клиентов для одной базы данных][3]


## Шаг 2, вариант 1. Создание карты сегментов для сопоставления по спискам
Создайте карту сегментов, используя объект ShardMapManager.

	# $ShardMapManager is the shard map manager object. 
	$ShardMap = New-ListShardMap -KeyType $([int]) 
	-ListShardMapName 'ListShardMap' 
	-ShardMapManager $ShardMapManager 
 
 
## Шаг 2, вариант 2. Создание карты сегментов для сопоставления по диапазонам

Обратите внимание, что для использования этого шаблона сопоставления потребуются непрерывные диапазоны значений идентификаторов клиентов. Кроме того, можно не включать все диапазоны и просто пропустить один из них при создании базы данных.

	# $ShardMapManager is the shard map manager object 
	# 'RangeShardMap' is the unique identifier for the range shard map.  
	$ShardMap = New-RangeShardMap 
	-KeyType $([int]) 
	-RangeShardMapName 'RangeShardMap' 
	-ShardMapManager $ShardMapManager 

## Шаг 2, вариант 3. Сопоставления по спискам для одной базы данных
Для настройки этого шаблона также требуется создать карту списков, как показано в разделе "Шаг 2, вариант 1".

## Шаг 3. Подготовка отдельных сегментов

Добавьте каждый сегмент (база данных) в диспетчер сопоставления сегментов. Таким образом отдельные базы данных будут подготовлены для хранения сведений о сопоставлении. Подготовьте так каждый сегмент.
	 
	Add-Shard 
	-ShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>'
	# The $ShardMap is the shard map created in step 2.
 

## Шаг 4. Добавление сопоставлений

Добавление сопоставлений зависит от вида созданной карты сегментов. Если создана карта списков, нужно добавить сопоставления по спискам. Если создана карта диапазонов, нужно добавить сопоставления по диапазонам.

### Шаг 4, вариант 1. Сопоставление данных для сопоставления по спискам

Сопоставьте данные, добавив сопоставление по спискам для каждого клиента.

	# Create the mappings and associate it with the new shards 
	Add-ListMapping 
	-KeyType $([int]) 
	-ListPoint '<tenant_id>' 
	-ListShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>' 

### Шаг 4, вариант 2. Сопоставление данных для сопоставления по диапазонам

Добавьте сопоставления по диапазонам для всех связанных диапазонов идентификаторов клиентов и баз данных:

	# Create the mappings and associate it with the new shards 
	Add-RangeMapping 
	-KeyType $([int]) 
	-RangeHigh '5' 
	-RangeLow '1' 
	-RangeShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>' 


### Шаг 4, вариант 3. Сопоставление данных для нескольких клиентов в одной базе данных

Для каждого клиента следует выполнить функцию Add-ListMapping (вариант 1 выше).


## Проверка сопоставлений

Сведения о существующих сегментах и сопоставлениях, связанных с ними, можно запросить с помощью следующих команд:

	# List the shards and mappings 
	Get-Shards -ShardMap $ShardMap 
	Get-Mappings -ShardMap $ShardMap 

## Сводка

После завершения настройки можно начать работу с клиентской библиотекой эластичной базы данных. Кроме того, можно воспользоваться [маршрутизацией, зависящей от данных](sql-database-elastic-scale-data-dependent-routing.md), и [формированием запросов по нескольким сегментам](sql-database-elastic-scale-multishard-querying.md).

## Дальнейшие действия


Скачайте скрипты PowerShell на странице [Azure SQL DB-Elastic Database tools scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db) (База данных SQL Azure — сценарии для средств эластичной базы данных).

Средства можно также найти на сайте GitHub по адресу [Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Используйте средство разбиения и слияния для перемещения данных из модели с несколькими клиентами в модель с одним клиентом и наоборот. См. статью о [средстве разбиения и слияния](sql-database-elastic-scale-get-started.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 

<!---HONumber=AcomDC_0330_2016-->