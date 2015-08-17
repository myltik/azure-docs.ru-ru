<properties
	pageTitle="Подписка Microsoft Azure, ограничения служб, квоты и ограничения"
	description="В данном разделе приводится перечень наиболее распространенных ограничений, относящихся к подписке Azure и различным службам, квот и ограничений. Сюда входит информация о том, как увеличить лимиты и максимальные значения."
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="jroth"/>

# Подписка Azure, границы, квоты и ограничения службы

## Обзор

Настоящий документ описывает некоторые из наиболее известных ограничений Microsoft Azure. Учтите, что на текущий момент он охватывает не все службы Azure. Со временам эти ограничения будут расширены.

> [AZURE.NOTE]Если требуется сделать ограничение выше значения **ограничения по умолчанию**, то можно [бесплатно отправить запрос в службу поддержки](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Ограничения не могут быть увеличены выше значения **Максимальное ограничение** в таблицах ниже. Если столбца **Максимальное ограничение** нет, то указанный ресурс не имеет настраиваемого ограничения.


### Ограничения и диспетчер ресурсов Azure

Теперь несколько ресурсов можно объединить в одной группе ресурсов Azure. При использовании групп ресурсов ограничения, которые были глобальными, становятся управляемыми на региональном уровне благодаря диспетчеру ресурсов Azure. Подробнее о группах ресурсов Azure см. в разделе [Использование групп ресурсов для управления ресурсами Azure](resource-group-portal.md).

В следующие ограничения добавлена новая таблица, в которой отражены все различия ограничений при использовании диспетчера ресурсов Azure. Например, существует таблица **Ограничения подписки** и таблица **Ограничения подписки — диспетчер ресурсов Azure**. Если ограничение применяется в обоих случаях, оно показано только в первой таблице. Если не указано иное, ограничения глобальны во всех областях.

> [AZURE.NOTE]Важно подчеркнуть, что квоты для ресурсов в группах ресурсов Azure доступны для каждого региона по подписке, а не для каждой подписки, как квоты управления службами. В качестве примера используем квоты ядер ЦП. Если вам необходимо запросить увеличение квоты с поддержкой ядер, следует решить, сколько ядер будут использоваться и в каких регионах, а затем отправить запрос на квоты ядер группы ресурсов Azure core с нужными регионами и количеством. Поэтому, если необходимо использовать 30 ядер в Западной Европе для запуска приложения, следует запросить 30 ядер в Западной Европе. Но других регионах квота ядер не увеличится не будет — только в Западной Европе будет 30 ядер.
<!-- -->
В результате может быть полезно проанализировать, какие квоты группы ресурсов Azure потребуется для вашей рабочей нагрузки в каждом регионе, и запросить эти ресурсы в регионах, где выполняется развертывание. Подробные сведения об определении текущих квот для конкретных регионов см. в разделе [Устранение неполадок развертывания](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues).

## Ограничения подписки

[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

### Ограничения подписки — диспетчер ресурсов Azure

При использовании диспетчера ресурсов Azure и групп ресурсов Azure применяются следующие ограничения. Ограничения, которые не были изменены после выпуска диспетчера ресурсов Azure, ниже не перечислены. Их можно просмотреть в предыдущей таблице.

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


## Ограничения группы ресурсов

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


## Ограничения виртуальных машин

[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


### Ограничения виртуальных машин — диспетчер ресурсов Azure

При использовании диспетчера ресурсов Azure и групп ресурсов Azure применяются следующие ограничения. Ограничения, которые не были изменены после выпуска диспетчера ресурсов Azure, ниже не перечислены. Их можно просмотреть в предыдущей таблице.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


## Ограничения сети

[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]


### Ограничения сети — диспетчер ресурсов Azure

При использовании диспетчера ресурсов Azure и групп ресурсов Azure применяются следующие ограничения. Ограничения, которые не были изменены после выпуска диспетчера ресурсов Azure, ниже не перечислены. Их можно просмотреть в предыдущей таблице.

[AZURE.INCLUDE [azure-virtual-network-limits-azure-resource-manager](../includes/azure-virtual-network-limits-azure-resource-manager.md)]

### Ограничения DNS

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

## Ограничения хранилища

### Ограничения стандартного хранилища

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Дополнительную информацию об ограничениях учетных записей хранения см. в статье [Целевые показатели масштабируемости и производительности хранилища Azure](../articles/storage/storage-scalability-targets.md).


### Ограничения хранилища Premium

[AZURE.INCLUDE [azure-storage-limits-premium-storage](../includes/azure-storage-limits-premium-storage.md)]


### Ограничения хранилища — диспетчер ресурсов Azure

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


## Ограничения облачных служб

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


## Ограничения службы приложений: веб-приложения, мобильные приложения, приложения API, приложения логики

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

## Ограничения планировщика

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

## Ограничения пакета

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]


## Ограничения DocumentDB

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


## Ограничения взаимодействия через мобильные устройства

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


## Ограничение поиска

[AZURE.INCLUDE [azure-search-limits](../includes/azure-search-limits.md)]

Дополнительные сведения об ограничениях поиска Azure см. в разделе [Ограничения](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## Ограничения базы данных SQL

[AZURE.INCLUDE [azure-sql-database-limits](../includes/azure-sql-database-limits.md)]

Дополнительную информацию об ограничениях Базы данных SQL см. в следующих разделах:

 - [Уровни служб баз данных SQL Azure (выпуски)](http://msdn.microsoft.com/library/azure/dn741340.aspx)
 - [Уровни служб и уровни производительности в базе данных SQL Azure](http://msdn.microsoft.com/library/azure/dn741336.aspx)
 - [Квоты пропускной способности базы данных (DTU)](http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs)
 - [Лимиты ресурсов базы данных SQL](sql-database/sql-database-resource-limits.md)

## Ограничения служб мультимедиа

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

## Ограничения мобильных служб

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

## Ограничения служебной шины

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

## Ограничения фабрики данных

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]


## Ограничения Stream Analytics

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

## Лимиты Active Directory

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


## Ограничения Azure RemoteApp

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

## Ограничения системы StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


## Ограничения Operational Insights

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

## Ограничения резервного копирования

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

## Ограничения Site Recovery

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

## Ограничения управления API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

## Ограничения кэша Redis для Azure

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

## Ограничения хранилища ключей

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

## Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

## См. также

[Основные сведения о лимитах Azure и их увеличении](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Размеры виртуальных машин и облачных служб для Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)

<!---HONumber=August15_HO6-->