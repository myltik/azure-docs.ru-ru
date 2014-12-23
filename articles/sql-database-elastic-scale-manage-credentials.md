<properties title="Managing Elastic Scale Credentials" pageTitle="Управление учетными данными эластичного масштабирования" description="How to set the right level of credentials, admin to read-only, for Elastic Scale apps." metaKeywords="Azure SQL Database, elastic scale, about user credentials in elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Управление учетными данными эластичного масштабирования  

[Клиентские API-интерфейсы эластичного масштабирования](http://go.microsoft.com/?linkid=9862605) используют учетные данные для различных типов операций, например для создания [Диспетчера карт сегментов](http://go.microsoft.com/?linkid=9862595) и работы с ним, обращения к существующему диспетчеру карт сегментов для получения сведений о сегментах, а также подключения к сегментам. Учетные данные для этих типов операций описаны ниже. 


* **Учетные данные управления для доступа к картам сегментов** Учетные данные управления используются при создании экземпляра объекта **ShardMapManager** в приложениях, работающих с картами сегментов. Пользователь API-интерфейсов эластичного масштабирования должен создать соответствующие имена входа и пользователей SQL, убедиться, что они имеют разрешения чтения и записи для базы данных глобальной карты сегментов, а также для всех баз данных сегментов. Эти учетные данные используются для обновления глобальной карты сегментов и локальных карт сегментов при внесении изменений в карты. Например, используйте учетные данные управления для создания экземпляра объекта диспетчера карт сегментов, как показано в следующем коде: 

        // Obtain a shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
        ); 


     Переменная **smmAdminConnectionString** - это строка подключения с учетными данными управления. Идентификатор пользователя и пароль предоставляют доступ на чтение и запись как к базе данных сопоставления сегментов, так и к отдельным сегментам. Строка подключения для управления также содержит имя сервера и имя базы данных для идентификации базы данных глобальной карты сегментов. Вот типичная строка подключения, используемая в таком случае:

        "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" 

* **Учетные данные пользователя для доступа к диспетчеру карт сегментов**  При создании экземпляра диспетчера карт сегментов в приложении, которое не будет администрировать эти карты, используйте учетные данные, дающие разрешения только на чтение глобальной карты сегментов. Данные, полученные из глобальной карты сегментов под эти учетные данные, используются для [маршрутизации на основе данных](./sql-database-elastic-scale-data-dependent-routing.md) и для заполнения кэша карт сегментов на клиенте. Учетные данные передаются через тот же шаблон вызова **GetSqlShardMapManager**, как показано выше: 
 
        // Obtain shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
        );  

Обратите внимание на использование **smmReadOnlyConnectionString**. Для такого доступа применяются другие учетные данные от имени пользователей, **не являющихся администраторами**. Эти учетные данные не должны давать разрешения на запись в глобальной карте сегментов. 

* **Учетные данные пользователя для доступа к пользовательским данным** При использовании метода **OpenConnectionForKey** для доступа к сегменту, связанному с ключом, необходимы дополнительные учетные данные. Эти учетные данные должны предоставлять разрешения на доступ только для чтения к таблицам локальных карт сегментов, размещенным в сегменте. Это необходимо для выполнения проверки подключения для маршрутизации на основе данных в сегменте. В следующем фрагменте кода демонстрируется использование учетных данных пользователя для доступа к пользовательским данным в контексте маршрутизации на основе данных: 
 
        using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
        targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

    В этом примере **smmUserConnectionString** содержит строку подключения для учетных данных пользователя. В базах данных SQL Azure обычно используется такая строка подключения для учетных данных пользователя: 

        "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;"  

    Обратите внимание, что строка подключения не содержит имя сервера и имя базы данных. Их нет, потому что вызов **OpenConnectionForKey** автоматически перенаправит подключение в нужный сегмент на основе ключа. Таким образом, нет нужды указывать имя сервера и имя базы данных. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
