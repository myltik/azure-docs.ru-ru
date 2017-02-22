---
title: "Управление учетными данными в клиентской библиотеке эластичной базы данных | Документация Майкрософт"
description: "Как установить правильный уровень учетных данных (от администратора до доступа только для чтения) для приложений эластичных баз данных."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 72e0edaf-795e-4856-84a5-6594f735fb7e
ms.service: sql-database
ms.custom: multiple databases
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 16e8c4ba332cbaba86a13d7b815d0561618cb28b


---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Учетные данные для доступа к клиентской библиотеке эластичной базы данных
[Клиентская библиотека эластичной базы данных](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) использует три различных типа учетных данных для доступа к [диспетчеру карты сегментов](sql-database-elastic-scale-shard-map-management.md). В зависимости от выполняемых задач используйте учетные данные с максимально низким уровнем доступа.

* **Учетные данные управления** предназначены для создания диспетчера карты сегментов или операций с ним. (См. [глоссарий](sql-database-elastic-scale-glossary.md).) 
* **Учетные данные для доступа**: обеспечивают доступ к существующему диспетчеру карт сегментов для получения сведений о сегментах.
* **Учетные данные подключения**: предназначены для подключения к сегментам. 

См. также статью [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md). 

## <a name="about-management-credentials"></a>Об учетных данных управления
Учетные данные управления используются для создания объекта [**ShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) в приложениях, работающих с картами сегментов. (Например, см. статьи [Добавление сегмента с использованием средств эластичной базы данных](sql-database-elastic-scale-add-a-shard.md) и [Маршрутизация, зависящая от данных](sql-database-elastic-scale-data-dependent-routing.md).) Пользователь клиентской библиотеки эластичного масштабирования создает пользователей и имена для входа SQL и проверяет, имеют ли они разрешения на чтение и запись для базы данных глобальной карты сегментов, а также для всех баз данных сегментов. Эти учетные данные используются для обновления глобальной карты сегментов и локальных карт сегментов при внесении изменений в карты. Например, используйте учетные данные управления для создания объекта диспетчера карты сегментов с помощью [**GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx): 

    // Obtain a shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmAdminConnectionString, 
            ShardMapManagerLoadPolicy.Lazy 
    ); 

Переменная **smmAdminConnectionString** — это строка подключения с учетными данными управления. Идентификатор пользователя и пароль предоставляют доступ на чтение и запись как к базе данных сопоставления сегментов, так и к отдельным сегментам. Строка подключения для управления также содержит имя сервера и имя базы данных для идентификации базы данных глобальной карты сегментов. Вот типичная строка подключения, используемая в таком случае:

     "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

Не используйте значения вида "username@server"—instead. Вместо этого используйте просто значение "имя_пользователя".  Это обусловлено тем, что учетные данные должны действовать для базы данных диспетчера карт сегментов и отдельных сегментов, которые могут находиться на разных серверах.

## <a name="access-credentials"></a>Учетные данные для доступа
При создании диспетчера карт сегментов в приложении, которое не администрирует эти карты сегментов, используйте учетные данные, дающие разрешения только на чтение глобальной карты сегментов. Данные, полученные из глобальной карты сегментов под этими учетными данными, используются для [зависящей от данных маршрутизации](sql-database-elastic-scale-data-dependent-routing.md) и для заполнения кэша карт сегментов на клиенте. Учетные данные передаются через тот же шаблон вызова **GetSqlShardMapManager** , как показано выше: 

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

Обратите внимание на использование **smmReadOnlyConnectionString**. Для такого доступа применяются другие учетные данные от имени пользователей, **не являющихся администраторами**. Эти учетные данные не должны давать разрешения на запись в глобальную карту сегментов. 

## <a name="connection-credentials"></a>Учетные данные подключения
При использовании метода [**OpenConnectionForKey**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) для доступа к сегменту, связанному с ключом сегментирования, необходимы дополнительные учетные данные. Эти учетные данные должны предоставлять разрешения на доступ только для чтения к таблицам локальных карт сегментов, размещенным в сегменте. Это необходимо для выполнения проверки подключения для маршрутизации на основе данных в сегменте. Этот фрагмент кода разрешает доступ к данным в контексте зависящей от данных маршрутизации. 

    using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
    targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

В этом примере **smmUserConnectionString** содержит строку подключения для учетных данных пользователя. В базах данных SQL Azure обычно используется такая строка подключения для учетных данных пользователя: 

    "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

Как и для учетных данных администратора, не используйте значения вида "username@server". Вместо этого используйте просто «имя_пользователя@сервер».  Кроме того, обратите внимание, что строка подключения не содержит имя сервера и имя базы данных. Их нет, потому что вызов **OpenConnectionForKey** автоматически перенаправит подключение в нужный сегмент на основе ключа. Таким образом, имя сервера и имя базы данных не указываются. 

## <a name="see-also"></a>Дополнительные материалы
[Управление базами данных и именами входа в Базе данных SQL Azure](sql-database-manage-logins.md)

[Защита Базы данных SQL](sql-database-security-overview.md)

[Начало работы с заданиями обработки эластичных баз данных](sql-database-elastic-jobs-getting-started.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]




<!--HONumber=Dec16_HO4-->


