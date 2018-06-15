---
title: Часто задаваемые вопросы об эластичном масштабировании SQL Azure | Документация Майкрософт
description: Часто задаваемые вопросы об эластичном масштабировании базы данных SQL Azure.
services: sql-database
documentationcenter: ''
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 94ae9549bb5e09c80703a7db316675bff1272372
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647482"
---
# <a name="elastic-database-tools-faq"></a>Вопросы и ответы по инструментам эластичных баз данных
#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>На каждый сегмент приходится по одному клиенту, но нет ключа сегментирования. Как заполнить ключ сегментирования для сведений схемы?
Объект сведений схемы используется только в сценариях разделения и слияния. Если приложение изначально однотенантное, инструмент разбиения и объединения для него не требуется, поэтому заполнять объект данных схемы не нужно.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>База данных подготовлена, и уже назначен диспетчер Shard Map Manager. Как зарегистрировать эту новую базу данных как сегмент?
См. статью **[Добавление сегмента с использованием средств эластичных баз данных](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Какова стоимость инструментов эластичных баз данных
Плата за использование клиентской библиотеки эластичной базы данных не взимается. Оплачивать нужно только доступ к базам данных SQL Azure, используемым для сегментов и диспетчера сопоставления сегментов, а также для веб-ролей и рабочих ролей, подготовленных для инструмента разбиения и объединения.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Почему мои учетные данные не работают при добавлении сегмента с другого сервера?
Не используйте учетные данные в формате User ID = username@servername. Мы советуем использовать User ID = username.  Кроме того, убедитесь, что у имени пользователя username есть разрешения на доступ к сегменту.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Нужно ли создавать диспетчер Shard Map Manager и заполнять сегменты при каждом запуске приложений?
Нет. Диспетчер карты сегментов (например, **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) создается только один раз.  Во время запуска приложение должно вызывать диспетчер **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)**.  На один домен приложения должен приходиться один такой вызов.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>У меня есть вопросы об использовании инструментов эластичной базы данных. Как найти ответы?
Задайте нам свои вопросы на [форуме, посвященном Базе данных SQL Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>При подключении к базе данных с помощью ключа сегментирования все равно удается запрашивать данные для других ключей сегментирования в том же сегменте.  Так и должно быть?
Интерфейсы API гибкого масштабирования позволяют подключаться к нужным базам данных с помощью ключа сегментирования, но не обеспечивают фильтрацию этих ключей.  Добавьте предложения **WHERE** к своему запросу, чтобы ограничить масштаб до соответствующего ключа сегментирования, если потребуется.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Можно ли использовать разные выпуски базы данных Azure для каждого сегмента моего набора сегментов?
Да. Ваш сегмент является отдельной базой данных и поэтому один сегмент может быть выпуском Premium, тогда как другой — выпуском Standard. Кроме того, выпуск сегмента можно многократно масштабировать во время срока жизни сегмента.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Подготавливает (или удаляет) ли инструмент разбиения и объединения базу данных в процессе разбиения или объединения?
Нет. Для выполнения операций **разбиения** целевая база данных должна существовать в пределах соответствующей схемы и быть зарегистрированной в диспетчере сопоставления сегментов.  Для выполнения операций **объединения** необходимо удалить сегмент из диспетчера сопоставления сегментов, а затем удалить базу данных.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

