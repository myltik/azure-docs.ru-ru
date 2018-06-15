---
title: Счетчики производительности для диспетчера карты сегментов
description: Класс ShardMapManager и счетчики производительности для маршрутизации, зависящей от данных
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 9c134ee96f7749529ab665df041cfc51c979acde
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647329"
---
# <a name="performance-counters-for-shard-map-manager"></a>Счетчики производительности для диспетчера карты сегментов
Вы можете сохранять данные о производительности [диспетчера карты сегментов](sql-database-elastic-scale-shard-map-management.md), особенно при использовании [маршрутизации, зависящей от данных](sql-database-elastic-scale-data-dependent-routing.md). Счетчики создаются с помощью методов класса Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Счетчики используются для отслеживания показателей производительности в операциях [маршрутизации, зависящей от данных](sql-database-elastic-scale-data-dependent-routing.md) . Эти счетчики можно найти в системном мониторе в категории "Эластичная база данных: управление сегментами".

**Новая версия** : [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). См. также статью [Обновление приложения для использования новой версии клиентской библиотеки эластичной базы данных](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>предварительным требованиям
* Чтобы пользователь мог создавать категории производительности и счетчики, он должен быть членом локальной группы **Администраторы** на компьютере, где размещается приложение.  
* Чтобы пользователь мог создавать экземпляры счетчика производительности и обновлять показания счетчиков, он должен быть членом группы **Администраторы** или **Пользователи системного монитора**. 

## <a name="create-performance-category-and-counters"></a>Создание счетчиков и категорий производительности
Чтобы создать счетчик, вызовите метод CreatePeformanceCategoryAndCounters класса [ShardMapManagmentFactory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Этот метод может выполнять только администратор: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Для выполнения метода можно также использовать [этот](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) сценарий PowerShell. Этот метод создает следующие счетчики производительности.  

* **Cached mappings**(Кэшированные сопоставления) — количество сопоставлений, кэшируемых для карты сегментов.
* **DDR operations/sec**(Число операций DDR в секунду) — скорость операций маршрутизации, зависящих от данных, для карты сегментов. Этот счетчик обновляется при успешном подключении к целевому сегменту после вызова метода [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx). 
* **Mapping lookup cache hits/sec**(Число попаданий при поиске сопоставлений в кэше в секунду) — скорость успешных операций поиска сопоставлений в кэше для карты сегментов. 
* **Mapping lookup cache misses/sec**(Число промахов при поиске сопоставлений в кэше в секунду) — скорость неуспешных операций поиска сопоставлений в кэше для карты сегментов.
* **Mappings added or updated in cache/sec**(Число добавляемых или обновляемых сопоставлений в кэше в секунду) — скорость добавления или обновления сопоставлений в кэше для карты сегментов. 
* **Mappings removed from cache/sec**(Число удаляемых из кэша сопоставлений в секунду) — скорость удаления сопоставлений из кэша для карты сегментов. 

Счетчики производительности создаются для каждой кэшированной карты сегментов каждого процесса.  

## <a name="notes"></a>Заметки
Создание счетчиков производительности инициируется следующими событиями.  

* Инициализация объекта [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) с [безотложной загрузкой](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), если в ShardMapManager есть карты сегментов. Сюда относятся методы [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) и [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx).
* Успешный поиск карты сегментов (с помощью [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) или [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 
* Успешное создание карты сегментов с помощью CreateShardMap().

Счетчики производительности обновляются при выполнении любых операций кэширования, связанных с картой сегментов или сопоставлениями. Успешное удаление сегментов с помощью метода DeleteShardMap() приводит к удалению экземпляра счетчика производительности.  

## <a name="best-practices"></a>Рекомендации
* Создание категории производительности и счетчиков следует выполнять только один раз, до создания объекта ShardMapManager. При каждом выполнении команды CreatePerformanceCategoryAndCounters() предыдущие значения счетчиков удаляются (данные теряются во всех экземплярах) и создаются новые.  
* Экземпляры счетчиков производительности создаются для каждого процесса. Любой сбой приложения или удаление карты сегментов из кэша приведет к удалению экземпляров счетчиков производительности.  

### <a name="see-also"></a>См. также
[Общие сведения о возможностях эластичных баз данных](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

