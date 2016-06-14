<properties 
	pageTitle="Управление учетными данными в клиентской библиотеке эластичной базы данных | Microsoft Azure" 
	description="Как установить правильный уровень учетных данных (от администратора до доступа только для чтения) для приложений эластичных баз данных." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="ddove" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2016" 
	ms.author="ddove"/>

# Учетные данные для доступа к клиентской библиотеке эластичной базы данных

[Клиентская библиотека эластичной базы данных](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) использует три различных типа учетных данных для доступа к [диспетчеру сопоставления сегментов](sql-database-elastic-scale-shard-map-management.md). В зависимости от выполняемых задач используйте учетные данные с как можно более низким уровнем доступа.

* **Учетные данные управления**: предназначены для создания диспетчера карт сегментов или операций с ним. (См. [глоссарий](sql-database-elastic-scale-glossary.md).) 
* **Учетные данные для доступа**: обеспечивают доступ к существующему диспетчеру карт сегментов для получения сведений о сегментах.
* **Учетные данные подключения**: предназначены для подключения к сегментам. 

Также см. раздел [Управление базами данных и учетными записями в Базе данных SQL Azure](sql-database-manage-logins.md).
 
## Об учетных данных управления

Учетные данные управления используются для создания объекта [**ShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) в приложениях, работающих с картами сегментов. (Например, см. разделы [Добавление сегмента с использованием инструментов эластичной базы данных](sql-database-elastic-scale-add-a-shard.md) и [Маршрутизация на основе данных](sql-database-elastic-scale-data-dependent-routing.md).) Пользователь клиентской библиотеки эластичного масштабирования создает пользователей и имена для входа SQL и проверяет, имеют ли они разрешения чтения и записи для базы данных глобальной карты сегментов, а также для всех баз данных сегментов. Эти учетные данные используются для обновления глобальной карты сегментов и локальных карт сегментов при внесении изменений в карты. Например, используйте учетные данные управления для создания объекта диспетчера карт сегментов с помощью [**GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx):

	// Obtain a shard map manager. 
	ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
	        smmAdminConnectionString, 
	        ShardMapManagerLoadPolicy.Lazy 
	); 

Переменная **smmAdminConnectionString** — это строка подключения с учетными данными управления. Идентификатор пользователя и пароль предоставляют доступ на чтение и запись как к базе данных сопоставления сегментов, так и к отдельным сегментам. Строка подключения для управления также содержит имя сервера и имя базы данных для идентификации базы данных глобальной карты сегментов. Вот типичная строка подключения, используемая в таком случае:

	 "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

Не используйте значения вида «имя\_пользователя@сервер». Вместо этого используйте просто значение «имя\_пользователя». Это обусловлено тем, что учетные данные должны действовать для базы данных диспетчера карт сегментов и отдельных сегментов, которые могут находиться на разных серверах.

## Учетные данные для доступа
  
При создании диспетчера карт сегментов в приложении, которое не администрирует эти карты сегментов, используйте учетные данные, дающие разрешения только на чтение глобальной карты сегментов. Данные, полученные из глобальной карты сегментов под этими учетными данными, используются для [зависящей от данных маршрутизации](sql-database-elastic-scale-data-dependent-routing.md) и для заполнения кэша карт сегментов на клиенте. Учетные данные передаются через тот же шаблон вызова **GetSqlShardMapManager**, как показано выше:

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

Обратите внимание на использование **smmReadOnlyConnectionString**. Для такого доступа применяются другие учетные данные от имени пользователей, **не являющихся администраторами**. Эти учетные данные не должны давать разрешения на запись в глобальную карту сегментов.

## Учетные данные подключения 

При использовании метода [**OpenConnectionForKey**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) для доступа к сегменту, связанному с ключом сегментирования, необходимы дополнительные учетные данные. Эти учетные данные должны предоставлять разрешения на доступ только для чтения к таблицам локальных карт сегментов, размещенным в сегменте. Это необходимо для выполнения проверки подключения для маршрутизации на основе данных в сегменте. Этот фрагмент кода разрешает доступ к данным в контексте зависящей от данных маршрутизации.
 
	using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
	targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

В этом примере **smmUserConnectionString** содержит строку подключения для учетных данных пользователя. В базах данных SQL Azure обычно используется такая строка подключения для учетных данных пользователя:

	"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

Как и для учетных данных администратора, не используйте значения вида «имя\_пользователя@сервер». Вместо этого используйте просто «имя\_пользователя@сервер». Кроме того, обратите внимание, что строка подключения не содержит имя сервера и имя базы данных. Их нет, потому что вызов **OpenConnectionForKey** автоматически перенаправит подключение в нужный сегмент на основе ключа. Таким образом, имя сервера и имя базы данных не указываются.

## См. также
[Управление базами данных и именами входа в Базе данных SQL Azure](sql-database-manage-logins.md)

[Защита Базы данных SQL](sql-database-security.md)

[Начало работы с заданиями обработки эластичных баз данных](sql-database-elastic-jobs-getting-started.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0601_2016-->