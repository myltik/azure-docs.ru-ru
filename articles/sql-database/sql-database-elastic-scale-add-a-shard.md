<properties 
	pageTitle="Добавление сегмента с использованием средств эластичных баз данных | Microsoft Azure" 
	description="Информация о том, как использовать интерфейсы API эластичного масштабирования для добавления новых сегментов в набор сегментов." 
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

# Добавление сегмента с использованием средств эластичных баз данных

## Добавление сегмента для нового диапазона или ключа  

Часто необходимо, чтобы приложение просто добавило в существующую карту сегментов новые сегменты для обработки данных, поступление которых ожидается из новых ключей и диапазонов ключей. Например, приложение с сегментированием по идентификатору клиента должно подготовить новый сегмент для нового клиента или при обработке данных с сегментированием по месяцам требуется создание нового сегмента перед началом следующего месяца.

Если новый диапазон ключей не входит в существующее сопоставление, добавление сегмента и привязка к нему нового ключа или диапазона становится довольно простой задачей.

### Пример: добавление сегмента и его диапазона в существующее сопоставление сегментов
В этом примере используются методы [TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx), [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx), [CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0})) и создается экземпляр класса [ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.). В примере ниже для хранения диапазона [300, 400) создается база данных с именем **sample\_shard\_2** и все необходимые объекты схемы внутри нее.

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


В качестве альтернативы можно использовать PowerShell, чтобы создать новый диспетчер карты сегментов. Пример представлен [здесь](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).
## Добавление сегмента для пустой части существующего диапазона  

В некоторых случаях к этому моменту диапазон может быть уже сопоставлен с сегментом и частично заполнен данными, но теперь последующие данные должны направляться в другой сегмент. Такая ситуация, например, возникает, если сегментация выполняется по диапазону с разбиением на дни и при этом для сегмента уже выделено 50 дней, однако на 24 день понадобилось, чтобы будущие данные поступали в другой сегмент. [Средство разбиения и объединения](sql-database-elastic-scale-overview-split-and-merge.md) эластичных баз данных может выполнить эту операцию. Но если перемещать данные не обязательно (например, данные для диапазона дней [25, 50), т. е. с 25-го дня включительно по 50-й день не включительно, еще не существуют), эту операцию можно выполнить полностью непосредственно с помощью API управления картой сегментов.

### Пример: разбиение диапазона и присвоение пустой части новому добавленному сегменту

В рамках примера были созданы база данных с именем sample\_shard\_2 и все необходимые объекты схемы, содержащиеся в ней.

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Важно**: используйте этот метод, только если вы уверены, что диапазон для обновленного сопоставления пуст. Методы выше не позволяют проверить данные для перемещаемого диапазона, так что лучше включить проверки в код. Если перемещаемый диапазон содержит строки, фактическое распределение данных не будет соответствовать обновленному сопоставлению сегментов. В таких случаях для выполнения операции используйте [средство разбиения и объединения](sql-database-elastic-scale-overview-split-and-merge.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0601_2016--->