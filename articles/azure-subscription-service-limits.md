---
title: "Ограничения и квоты подписки Azure | Документация Майкрософт"
description: "В этой статье приводится перечень наиболее распространенных ограничений, относящихся к подписке Azure и различным службам, квот и границ. Сюда входит информация о том, как увеличить лимиты и максимальные значения."
services: 
documentationcenter: 
author: rothja
manager: jeffreyg
editor: 
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: byvinyal
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 13f968b5eeaeb74c6364b9b7c29651657f73e052
ms.contentlocale: ru-ru
ms.lasthandoff: 06/28/2017


---
<a id="azure-subscription-and-service-limits-quotas-and-constraints" class="xliff"></a>

# Подписка Azure, границы, квоты и ограничения службы
В этом документе указаны некоторые из наиболее распространенных ограничений Microsoft Azure, которые иногда называются квотами. Этот документ на текущий момент охватывает не все службы Azure. Со временем список будет расширен и обновлен, чтобы охватить больше платформ.

Дополнительную информацию о ценах Azure см. на странице [Цены Azure](https://azure.microsoft.com/pricing/). Там вы сможете оценить затраты с помощью [калькулятора цен](https://azure.microsoft.com/pricing/calculator/) или просмотрев страницу сведений о ценах для службы (например, для [виртуальных машин Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). Советы по управлению затратами приведены в статье [Предотвращение непредвиденных расходов с помощью функции выставления счетов и управления затратами в Azure](billing/billing-getting-started.md).

> [!NOTE]
> Если ограничение или квоту требуется сделать выше значения **Ограничение по умолчанию**, [бесплатно отправьте запрос в службу поддержки клиентов](azure-supportability/resource-manager-core-quotas-request.md). Ограничения не могут быть увеличены выше значения **Максимальное ограничение**, как показано в таблицах ниже. Если столбца **Максимальное ограничение** нет, то ресурс не имеет настраиваемого ограничения. 
> 
> Бесплатная пробная версия подписки не предусматривает возможность увеличения ограничения и квоты. При наличии бесплатной пробной подписки ее можно обновить до подписки [с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/). Дополнительные сведения см. в статье [Upgrade your free Azure subscription to Pay-As-You-Go](billing/billing-upgrade-azure-subscription.md) (Обновление бесплатной пробной версии Azure до версии с оплатой по мере использования).
> 

<a id="limits-and-the-azure-resource-manager" class="xliff"></a>

## Ограничения и диспетчер ресурсов Azure
Теперь несколько ресурсов можно объединить в одной группе ресурсов Azure. При использовании групп ресурсов ограничения, которые были глобальными, становятся управляемыми на региональном уровне благодаря диспетчеру ресурсов Azure. Дополнительные сведения о группах ресурсов Azure см. в статье [Общие сведения о диспетчере ресурсов Azure](azure-resource-manager/resource-group-overview.md).

В следующие ограничения добавлена новая таблица, в которой отражены все различия ограничений при использовании диспетчера ресурсов Azure. Например, имеется таблица **Ограничения подписки** и таблица **Ограничения подписки — Azure Resource Manager**. Если ограничение применяется в обоих случаях, оно показано только в первой таблице. Если не указано иное, ограничения глобальны во всех областях.

> [!NOTE]
> Важно подчеркнуть, что квоты для ресурсов в группах ресурсов Azure доступны для каждого региона по подписке, а не для каждой подписки, как квоты управления службами. В качестве примера используем квоты ядер ЦП. Если вам необходимо запросить увеличение квоты с поддержкой ядер, следует решить, сколько ядер будут использоваться и в каких регионах, а затем отправить запрос на квоты ядер группы ресурсов Azure core с нужными регионами и количеством. Поэтому, если необходимо использовать 30 ядер в Западной Европе для запуска приложения, следует запросить 30 ядер в Западной Европе. Но других регионах квота ядер не увеличится не будет — только в Западной Европе будет 30 ядер.
> <!-- -->
> В результате может быть полезно проанализировать, какие квоты группы ресурсов Azure потребуется для вашей рабочей нагрузки в каждом регионе, и запросить эти ресурсы в регионах, где выполняется развертывание. Подробные сведения об определении текущих квот для конкретных регионов см. в разделе [Устранение неполадок развертывания](resource-manager-common-deployment-errors.md).
> 
> 

<a id="service-specific-limits" class="xliff"></a>

## Ограничения определенных служб
* [Active Directory](#active-directory-limits)
* [Управление API](#api-management-limits)
* [Служба приложений](#app-service-limits)
* [Шлюз приложений](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Автоматизация](#automation-limits)
* [кэш Azure Redis](#azure-redis-cache-limits)
* [Azure RemoteApp](#azure-remoteapp-limits)
* [Архивация](#backup-limits)
* [Пакетная служба](#batch-limits)
* [Службы BizTalk](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Облачные службы](#cloud-services-limits)
* [Фабрика данных](#data-factory-limits)
* [Аналитика озера данных](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [DNS](#dns-limits)
* [DocumentDB](#documentdb-limits)
* [Концентраторы событий](#event-hubs-limits)
* [Центр Интернета вещей](#iot-hub-limits)
* [хранилище ключей;](#key-vault-limits)
* [Log Analytics или Operational Insights](#log-analytics-limits)
* [Службы мультимедиа](#media-services-limits)
* [Службы мобильного взаимодействия](#mobile-engagement-limits)
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
* [Хранилище](#storage-limits)
* [StorSimple System](#storsimple-system-limits)
* [Анализ потока](#stream-analytics-limits)
* [Подписка](#subscription-limits)
* [Диспетчер трафика](#traffic-manager-limits)
* [Виртуальные машины](#virtual-machines-limits)
* [Наборы для масштабирования виртуальных машин](#virtual-machine-scale-sets-limits)

<a id="subscription-limits" class="xliff"></a>

### Ограничения подписки
<a id="subscription-limits" class="xliff"></a>

#### Ограничения подписки
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

<a id="subscription-limits---azure-resource-manager" class="xliff"></a>

#### Ограничения подписки — Azure Resource Manager
При использовании диспетчера ресурсов Azure и групп ресурсов Azure применяются следующие ограничения. Ограничения, которые не были изменены после выпуска диспетчера ресурсов Azure, ниже не перечислены. Их можно просмотреть в предыдущей таблице.

Сведения о работе с ограничениями на запросы Resource Manager см. в разделе [Throttling Resource Manager requests](resource-manager-request-limits.md) (Регулирование запросов Resource Manager).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

<a id="resource-group-limits" class="xliff"></a>

### Ограничения группы ресурсов
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

<a id="virtual-machines-limits" class="xliff"></a>

### Ограничения виртуальных машин
<a id="virtual-machine-limits" class="xliff"></a>

#### Ограничения виртуальной машины
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

<a id="virtual-machines-limits---azure-resource-manager" class="xliff"></a>

#### Ограничения виртуальных машин — диспетчер ресурсов Azure
При использовании диспетчера ресурсов Azure и групп ресурсов Azure применяются следующие ограничения. Ограничения, которые не были изменены после выпуска диспетчера ресурсов Azure, ниже не перечислены. Их можно просмотреть в предыдущей таблице.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

<a id="virtual-machine-scale-sets-limits" class="xliff"></a>

### Ограничения для масштабируемых наборов виртуальных машин
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

<a id="networking-limits" class="xliff"></a>

### Ограничения сети
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

<a id="networking-limits" class="xliff"></a>

#### Ограничения сети
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

<a id="application-gateway-limits" class="xliff"></a>

#### Ограничения шлюза приложений
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

<a id="network-watcher-limits" class="xliff"></a>

#### Пределы наблюдателя за сетями
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

<a id="traffic-manager-limits" class="xliff"></a>

#### Ограничения диспетчера трафика
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

<a id="dns-limits" class="xliff"></a>

#### Ограничения DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

<a id="storage-limits" class="xliff"></a>

### Ограничения хранилища
Дополнительные сведения об ограничениях учетных записей хранения см. в статье [Целевые показатели по производительности и масштабируемости для хранилища Azure](storage/storage-scalability-targets.md).
<!--like # storage accts --> 
<a id="storage-service-limits" class="xliff"></a>

#### Ограничения службы хранения
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
<a id="virtual-machine-disk-limits" class="xliff"></a>

#### Ограничения для дисков виртуальной машины 
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Дополнительные сведения см. в статье [Размеры виртуальных машин](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

<a id="managed-virtual-machine-disks" class="xliff"></a>

#### Управляемые диски виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

<a id="unmanaged-virtual-machine-disks" class="xliff"></a>

#### Неуправляемые диски виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

<a id="storage-resource-provider-limits" class="xliff"></a>

#### Ограничения поставщика ресурсов хранилища
[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

<a id="cloud-services-limits" class="xliff"></a>

### Ограничения облачных служб
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

<a id="app-service-limits" class="xliff"></a>

### Ограничения службы приложений
Следующие ограничения службы приложения включают ограничения для веб-приложений, мобильных приложений, приложений API и приложений логики.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

<a id="scheduler-limits" class="xliff"></a>

### Ограничения планировщика
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

<a id="batch-limits" class="xliff"></a>

### Ограничения пакета
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

<a id="biztalk-services-limits" class="xliff"></a>

### Ограничения служб BizTalk
В следующей таблице показаны ограничения для служб BizTalk Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

<a id="documentdb-limits" class="xliff"></a>

### Ограничения DocumentDB
DocumentDB — это масштабная база данных, пропускную способность и хранилище которой можно масштабировать в соответствии с требованиями приложения. Если у вас возникнут вопросы по масштабированию DocumentDB, отправьте электронное сообщение по адресу askdocdb@microsoft.com.

<a id="mobile-engagement-limits" class="xliff"></a>

### Пределы для Служб мобильного взаимодействия
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

<a id="search-limits" class="xliff"></a>

### Ограничения поиска
Ценовые категории определяют емкость и ограничения службы поиска. Существуют следующие категории:

* *Бесплатный* : мультитенантная служба, используемая совместно с другими подписчиками Azure и предназначенная для оценки и разработки небольших проектов.
* *Базовый* предоставляет выделенные вычислительные ресурсы для небольших рабочих нагрузок в рабочей среде, поддерживая до трех реплик для обработки запросов с высоким уровнем доступности.
* *Стандартный (S1, S2, S3, S3 с высокой плотностью)* предназначена для больших рабочих нагрузок в рабочей среде. В рамках уровня "Стандартный" есть несколько уровней, поэтому можно выбрать конфигурацию ресурсов, которая лучше всего подходит для вашего профиля рабочей нагрузки.

**Ограничения на одну подписку**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Ограничения на одну службу поиска**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Дополнительные сведения об ограничениях, включая размер документов, количество запросов в секунду, ключи, запросы и ответы, см. в статье [Ограничения поиска Azure](search/search-limits-quotas-capacity.md).

<a id="media-services-limits" class="xliff"></a>

### Ограничения служб мультимедиа
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

<a id="cdn-limits" class="xliff"></a>

### Ограничения CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

<a id="mobile-services-limits" class="xliff"></a>

### Ограничения мобильных служб
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

<a id="monitor-limits" class="xliff"></a>

### Пределы монитора
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

<a id="notification-hub-service-limits" class="xliff"></a>

### Ограничения служб концентратора уведомлений
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

<a id="event-hubs-limits" class="xliff"></a>

### Ограничения концентраторов событий
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

<a id="service-bus-limits" class="xliff"></a>

### Ограничения служебной шины
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

<a id="iot-hub-limits" class="xliff"></a>

### Пределы для Центра Интернета вещей
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

<a id="data-factory-limits" class="xliff"></a>

### Ограничения фабрики данных
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

<a id="data-lake-analytics-limits" class="xliff"></a>

### Ограничения Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

<a id="data-lake-store-limits" class="xliff"></a>

### Ограничения Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

<a id="stream-analytics-limits" class="xliff"></a>

### Ограничения Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

<a id="active-directory-limits" class="xliff"></a>

### Ограничения Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

<a id="azure-remoteapp-limits" class="xliff"></a>

### Ограничения Azure RemoteApp
[!INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

<a id="storsimple-system-limits" class="xliff"></a>

### Ограничения системы StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

<a id="log-analytics-limits" class="xliff"></a>

### Ограничения Log Analytics
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

<a id="backup-limits" class="xliff"></a>

### Ограничения резервного копирования
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

<a id="site-recovery-limits" class="xliff"></a>

### Ограничения Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

<a id="application-insights-limits" class="xliff"></a>

### Ограничения Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

<a id="api-management-limits" class="xliff"></a>

### Ограничения управления API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

<a id="azure-redis-cache-limits" class="xliff"></a>

### Ограничения кэша Redis для Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

<a id="key-vault-limits" class="xliff"></a>

### Ограничения хранилища ключей
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

<a id="multi-factor-authentication" class="xliff"></a>

### Многофакторная идентификация
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

<a id="automation-limits" class="xliff"></a>

### Ограничения автоматизации
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

<a id="sql-database-limits" class="xliff"></a>

### Ограничения базы данных SQL
Ограничения базы данных SQL описаны в разделе [Ограничения ресурсов базы данных SQL](sql-database/sql-database-resource-limits.md).

<a id="see-also" class="xliff"></a>

## Дополнительные материалы
[Основные сведения о лимитах Azure и их увеличении](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Размеры виртуальных машин и облачных служб для Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Размеры для облачных служб](cloud-services/cloud-services-sizes-specs.md)


