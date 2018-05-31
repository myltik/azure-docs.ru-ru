---
title: Ограничения и квоты подписки Azure | Документация Майкрософт
description: В этой статье приводится перечень наиболее распространенных ограничений, относящихся к подписке Azure и различным службам, квот и границ. Сюда входит информация о том, как увеличить лимиты и максимальные значения.
services: ''
documentationcenter: ''
author: rothja
manager: jeffreyg
editor: ''
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: byvinyal
ms.openlocfilehash: 8a8482fa93ca902746e4fc8ef6d67b274a926bdc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939966"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Подписка Azure, границы, квоты и ограничения службы
В этом документе указаны некоторые из наиболее распространенных ограничений Microsoft Azure, которые иногда называются квотами. Этот документ на текущий момент охватывает не все службы Azure. Со временем список будет расширен и обновлен, чтобы охватить больше платформ.

Дополнительную информацию о ценах Azure см. на странице [Цены Azure](https://azure.microsoft.com/pricing/). Там вы сможете оценить затраты с помощью [калькулятора цен](https://azure.microsoft.com/pricing/calculator/) или просмотрев страницу сведений о ценах для службы (например, для [виртуальных машин Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). Советы по управлению затратами приведены в статье [Предотвращение непредвиденных расходов с помощью функции выставления счетов и управления затратами в Azure](billing/billing-getting-started.md).

> [!NOTE]
> Если ограничение или квоту требуется сделать выше значения **Ограничение по умолчанию**, [бесплатно отправьте запрос в службу поддержки клиентов](azure-resource-manager/resource-manager-quota-errors.md). Ограничения не могут быть увеличены выше значения **Максимальное ограничение**, как показано в таблицах ниже. Если столбца **Максимальное ограничение** нет, то ресурс не имеет настраиваемого ограничения.
>
> [Бесплатная пробная версия подписки](https://azure.microsoft.com/offers/ms-azr-0044p) не предусматривает возможность увеличения ограничения и квоты. При наличии [бесплатной пробной версии подписки](https://azure.microsoft.com/offers/ms-azr-0044p) ее можно обновить до подписки [с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/). Дополнительные сведения см. в статье [Upgrade your Free Trial or Microsoft Imagine Azure subscription to Pay-As-You-Go](billing/billing-upgrade-azure-subscription.md) (Обновление бесплатной пробной версии подписки или подписки Microsoft Imagine Azure до подписки с оплатой по мере использования) и [Вопросы и ответы о бесплатной учетной записи Azure](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-the-azure-resource-manager"></a>Ограничения и диспетчер ресурсов Azure
Теперь несколько ресурсов можно объединить в одной группе ресурсов Azure. При использовании групп ресурсов ограничения, которые были глобальными, становятся управляемыми на региональном уровне благодаря диспетчеру ресурсов Azure. Дополнительные сведения о группах ресурсов Azure см. в статье [Общие сведения о диспетчере ресурсов Azure](azure-resource-manager/resource-group-overview.md).

В следующие ограничения добавлена новая таблица, в которой отражены все различия ограничений при использовании диспетчера ресурсов Azure. Например, имеется таблица **Ограничения подписки** и таблица **Ограничения подписки — Azure Resource Manager**. Если ограничение применяется в обоих случаях, оно показано только в первой таблице. Если не указано иное, ограничения глобальны во всех областях.

> [!NOTE]
> Важно подчеркнуть, что квоты для ресурсов в группах ресурсов Azure доступны для каждого региона по подписке, а не для каждой подписки, как квоты управления службами. В качестве примера используем квоты виртуальных процессоров. Если вам необходимо запросить увеличение квоты с поддержкой виртуальных процессоров, определите, сколько виртуальных процессоров будут использоваться и в каких регионах, а затем отправить запрос на квоты виртуальных процессоров группы ресурсов Azure с нужными регионами и количеством. Если необходимо использовать 30 виртуальных процессоров в Западной Европе для запуска приложения, запросите 30 виртуальных процессоров в Западной Европе. Но в других регионах квота виртуальных процессоров не увеличится. Только в Западной Европе будет 30 виртуальных процессоров.
> <!-- -->
> В результате может быть полезно проанализировать, какие квоты группы ресурсов Azure потребуется для вашей рабочей нагрузки в каждом регионе, и запросить эти ресурсы в регионах, где выполняется развертывание. Подробные сведения об определении текущих квот для конкретных регионов см. в разделе [Устранение неполадок развертывания](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Ограничения определенных служб
* [Active Directory](#active-directory-limits)
* [Управление API](#api-management-limits)
* [Служба приложений](#app-service-limits)
* [Шлюз приложений](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Автоматизация](#automation-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Сетка событий Azure](#azure-event-grid-limits)
* [служба "Карты Azure"](#azure-maps-limits);
* [кэш Azure Redis](#azure-redis-cache-limits)
* [Архивация](#backup-limits)
* [Пакетная служба](#batch-limits)
* [Службы BizTalk](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Облачные службы](#cloud-services-limits)
* [Экземпляры контейнеров](#container-instances-limits)
* [Реестр контейнеров](#container-registry-limits)
* [Служба Kubernetes](#container-service-aks-limits)
* [Фабрика данных](#data-factory-limits)
* [Аналитика озера данных](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [DNS](#dns-limits)
* [Концентраторы событий](#event-hubs-limits)
* [Центр Интернета вещей](#iot-hub-limits)
* [Служба подготовки устройств для Центра Интернета вещей](#iot-hub-device-provisioning-service-limits)
* [хранилище ключей;](#key-vault-limits)
* [Служба Log Analytics](#log-analytics-limits)
* [Службы мультимедиа](#media-services-limits)
* [Службы мобильного взаимодействия;](#mobile-engagement-limits)
* [Мобильные службы](#mobile-services-limits)
* [Мониторинг](#monitor-limits)
* [Многофакторная идентификация](#multi-factor-authentication)
* [Сеть](#networking-limits)
* [Наблюдатель за сетями](#network-watcher-limits)
* [Служба концентратора уведомлений](#notification-hub-service-limits)
* [Группа ресурсов](#resource-group-limits)
* [Планировщик](#scheduler-limits)
* [Поиск](#search-limits)
* [Служебная шина](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [База данных SQL](#sql-database-limits)
* [Хранилище данных SQL](#sql-data-warehouse-limits)
* [Хранилище](#storage-limits)
* [StorSimple System](#storsimple-system-limits)
* [Анализ потока](#stream-analytics-limits)
* [Подписка](#subscription-limits)
* [Диспетчер трафика](#traffic-manager-limits)
* [Виртуальные машины](#virtual-machines-limits)
* [Наборы для масштабирования виртуальных машин](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Ограничения подписки
#### <a name="subscription-limits"></a>Ограничения подписки
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Ограничения подписки — Azure Resource Manager
При использовании диспетчера ресурсов Azure и групп ресурсов Azure применяются следующие ограничения. Ограничения, которые не были изменены после выпуска диспетчера ресурсов Azure, ниже не перечислены. Их можно просмотреть в предыдущей таблице.

Сведения о работе с ограничениями на запросы Resource Manager см. в разделе [Throttling Resource Manager requests](resource-manager-request-limits.md) (Регулирование запросов Resource Manager).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Ограничения группы ресурсов
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Ограничения виртуальных машин
#### <a name="virtual-machine-limits"></a>Ограничения виртуальной машины
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Ограничения виртуальных машин — диспетчер ресурсов Azure
При использовании диспетчера ресурсов Azure и групп ресурсов Azure применяются следующие ограничения. Ограничения, которые не были изменены после выпуска диспетчера ресурсов Azure, ниже не перечислены. Их можно просмотреть в предыдущей таблице.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Ограничения для масштабируемых наборов виртуальных машин
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Ограничения для экземпляров контейнеров Azure
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Ограничения реестра контейнера
В следующей таблице приведены сведения о возможностях и ограничениях [уровней обслуживания](./container-registry/container-registry-skus.md) "Базовый", "Стандартный" и "Премиум".

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="kubernetes-service-limits"></a>Ограничения службы Kubernetes
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>Ограничения сети
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Ограничения сети
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Ограничения шлюза приложений
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Пределы наблюдателя за сетями
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Ограничения диспетчера трафика
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Ограничения DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Ограничения хранилища
Дополнительные сведения об ограничениях учетных записей хранения см. в статье [Целевые показатели по производительности и масштабируемости для хранилища Azure](storage/common/storage-scalability-targets.md).

<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Ограничения хранилища BLOB-объектов Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Ограничения службы "Файлы Azure"
Дополнительные сведения об ограничениях службы "Файлы Azure" см. в статье [Azure Files scalability and performance targets](storage/files/storage-files-scale-targets.md) (Целевые показатели по производительности и масштабируемости для службы "Файлы Azure").

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Ограничения службы "Синхронизация файлов Azure"
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Ограничения для службы Azure "Хранилище очередей"
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Ограничения для службы Azure "Хранилище таблиц"
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Ограничения для дисков виртуальной машины
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Дополнительные сведения см. в статье [Размеры виртуальных машин](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Управляемые диски виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Неуправляемые диски виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>Ограничения облачных служб
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Ограничения службы приложений
Следующие ограничения службы приложения включают ограничения для веб-приложений, мобильных приложений, приложений API и приложений логики.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Ограничения планировщика
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Ограничения пакета
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Ограничения служб BizTalk
В следующей таблице показаны ограничения для служб BizTalk Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Ограничения Azure Cosmos DB
Azure Cosmos DB — это глобальная база данных, пропускную способность и хранилище которой можно масштабировать в соответствии с требованиями приложения. Если у вас возникнут вопросы по масштабированию Azure Cosmos DB, отправьте электронное сообщение по адресу askcosmosdb@microsoft.com.

### <a name="mobile-engagement-limits"></a>Ограничения Служб мобильного взаимодействия
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Ограничения поиска
Ценовые категории определяют емкость и ограничения службы поиска. Существуют следующие категории:

* *Бесплатный* : мультитенантная служба, используемая совместно с другими подписчиками Azure и предназначенная для оценки и разработки небольших проектов.
* *Базовый* предоставляет выделенные вычислительные ресурсы для небольших рабочих нагрузок в рабочей среде, поддерживая до трех реплик для обработки запросов с высоким уровнем доступности.
* *Стандартный (S1, S2, S3, S3 с высокой плотностью)* предназначена для больших рабочих нагрузок в рабочей среде. В рамках уровня "Стандартный" есть несколько уровней, поэтому можно выбрать конфигурацию ресурсов, которая лучше всего подходит для вашего профиля рабочей нагрузки.

**Ограничения на одну подписку**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Ограничения на одну службу поиска**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Дополнительные сведения об ограничениях, включая размер документов, количество запросов в секунду, ключи, запросы и ответы, см. в статье [Ограничения поиска Azure](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Ограничения служб мультимедиа
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Ограничения CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Ограничения мобильных служб
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Пределы монитора
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Ограничения служб концентратора уведомлений
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Ограничения концентраторов событий
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Ограничения служебной шины
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Пределы для Центра Интернета вещей
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Ограничения службы подготовки устройств для Центра Интернета вещей
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Ограничения фабрики данных
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Ограничения Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Ограничения Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Ограничения Database Migration Service
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Ограничения Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Ограничения Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Ограничения сетки событий Azure
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Ограничения службы "Карты Azure"
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="storsimple-system-limits"></a>Ограничения системы StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Ограничения Log Analytics
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Ограничения резервного копирования
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Ограничения Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Ограничения Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Ограничения управления API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Ограничения кэша Redis для Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Ограничения хранилища ключей
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Многофакторная идентификация
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Ограничения автоматизации
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Ограничения базы данных SQL
Ограничения базы данных SQL описаны в разделе [Ограничения ресурсов базы данных SQL](sql-database/sql-database-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Ограничения хранилища данных SQL
Сведения об ограничениях ресурсов хранилища данных SQL см. в [этой статье](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>См. также
[Основные сведения о лимитах Azure и их увеличении](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Размеры виртуальных машин и облачных служб для Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Размеры для облачных служб](cloud-services/cloud-services-sizes-specs.md)
