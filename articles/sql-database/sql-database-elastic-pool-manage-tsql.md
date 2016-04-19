<properties 
    pageTitle="Создание базы данных Azure SQL или ее перемещение в пул эластичных БД с помощью T-SQL | Microsoft Azure" 
    description="С помощью языка T-SQL можно создать базу данных SQL Azure в пуле эластичных БД либо переместить базу данных в пул или обратно." 
	services="sql-database" 
    documentationCenter="" 
    authors="sidneyh" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="04/11/2016"
    ms.author="sidneyh"/>

# Мониторинг пула эластичных баз данных и управление им с помощью Transact-SQL  

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Для создания и перемещения баз данных в эластичные пулы и обратно используйте команды [Create Database (база данных SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) и [Alter Database (база данных SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx). Прежде чем использовать эти команды, следует создать пул эластичных БД. Эти команды влияют только на базы данных. С помощью команд T-SQL нельзя внести изменения при создании новых пулов и настройке свойств пула (таких как минимальное и максимальное количество eDTU).


> [AZURE.NOTE] Сейчас пулы эластичных баз данных предоставляются в виде предварительной версии, которая доступна только с серверами Базы данных SQL версии 12. Если у вас есть сервер базы данных SQL версии 11, с помощью PowerShell вы можете в один шаг [обновить его до версии 12 и создать пул](sql-database-upgrade-server-portal.md).


## Создание новой базы данных внутри пула эластичных БД
Используйте команду CREATE DATABASE с параметром SERVICE\_OBJECTIVE.

	CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
	-- Create a database named db1 in a pool named S3M100.

Все базы данных в пуле эластичных БД наследуют уровень служб пула эластичных БД ("Базовый", "Стандартный" или "Премиум").


## Перемещение базы данных между пулами эластичных БД
Используйте команду ALTER DATABASE с инструкцией MODIFY и параметром SERVICE\_OBJECTIVE, для которого задано значение ELASTIC\_POOL. Для параметра name задайте имя целевого пула.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
	-- Move the database named db1 to a pool named P1M125  


## Перемещение базы данных в пул эластичных БД 
Используйте команду ALTER DATABASE с инструкцией MODIFY и параметром SERVICE\_OBJECTIVE, для которого задано значение ELASTIC\_POOL. Для параметра name задайте имя целевого пула.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
	-- Move the database named db1 to a pool named S3100.

## Перемещение базы данных из пула эластичных БД
Используйте команду ALTER DATABASE и задайте для параметра SERVICE\_OBJECTIVE уровень производительности (S0, S1 и т. д.).

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
	-- Changes the database into a stand-alone database with the service objective S1.

## Получение списка баз данных в пуле эластичных БД
Чтобы получить список всех баз данных в пуле эластичных БД, используйте [представление sys.database\_service \_objectives](https://msdn.microsoft.com/library/mt712619) Войдите в базу данных master, чтобы отправить запрос на представление.

>[AZURE.NOTE] В настоящее время запрос service\_objective\_column для баз данных в пулах эластичных БД возвращает внутренний маркер строки целевой службы. Она будет заменена строкой ElasticPool.
>

	SELECT d.name, slo.*  
	FROM sys.databases d 
	JOIN sys.database_service_objectives slo  
	ON d.database_id = slo.database_id
	WHERE elastic_pool_name = 'MyElasticPool'; 

## Мониторинг использования ресурсов в пулах эластичных БД

Проанализировать статистику использования ресурсов в пуле эластичных БД на логическом сервере можно в [представлении sys.elastic\_pool \_resource \_stats](https://msdn.microsoft.com/library/mt280062.aspx). Войдите в базу данных master, чтобы отправить запрос на представление.

	SELECT * FROM sys.elastic_pool_resource_stats 
	WHERE elastic_pool_name = 'MyElasticPool'
	ORDER BY end_time DESC;

## Мониторинг использования ресурсов базы данных в пуле эластичных БД
Проанализировать статистику использования ресурсов базы данных в пуле эластичных БД можно в [представлении sys.dm \_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) или [sys.resource \_stats](https://msdn.microsoft.com/library/dn269979.aspx). Этот процесс аналогичен отправке запроса на использование ресурсов для любой отдельной базы данных.

## Задержка операций эластичного пула

- Изменение гарантированного количества eDTU на каждую базу данных или максимального eDTU на каждую базу данных обычно завершается в течение не более 5 минут.
- Изменение ограничения на размер хранилища пула зависит от общего количества памяти, используемой всеми базами данных в пуле. Изменение занимает порядка 90 минут или меньше на каждые 100 ГБ. Например, если общее пространство, используемое всеми базами данных в пуле, равно 200 ГБ, то ожидаемая задержка при изменении eDTU пула или размера хранилища составит 3 часа или менее.

## Дальнейшие действия

После создания эластичного пула вы можете управлять эластичными базами данных в пуле путем создания заданий обработки эластичных БД. Эластичные задания позволяют легко и быстро выполнять сценарии T-SQL в любом количестве баз данных в пуле. Дополнительные сведения можно узнать в статье [Обзор службы заданий эластичной базы данных](sql-database-elastic-jobs-overview.md).

<!---HONumber=AcomDC_0413_2016-->