---
title: "Доступные службы в Azure для государственных организаций | Документация Майкрософт"
description: "Содержит общие сведения о доступных службах в Azure для государственных организаций"
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: liki
ms.assetid: a453a23c-bc0f-4203-9075-0f579dea7e23
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 21c2a0faad87b84058093f02c831b374a644b4b6
ms.lasthandoff: 03/06/2017


---
# <a name="available-services-on-azure-government"></a>Доступные службы в Azure для государственных организаций
В Azure для государственных организаций постоянно увеличивается число доступных служб.  Эти службы развертываются с помощью кода, используемого в общедоступной версии Azure.  В этом разделе перечислены службы, которые в настоящее время доступны в Azure для государственных организаций, а также описаны два основных типа сведений.

* **Различия** — различия из-за возможностей, которые еще не развернуты, или свойств (например, URL-адреса), которые являются уникальными для среды государственных организаций.  
* **Рекомендации** — сведения о реализации для государственных организаций, гарантирующие обеспечение уровня требований для данных.

Дополнительные сведения об этих службах можно найти в общей документации.

Актуальный список служб см. в таблице [Доступность продуктов по регионам](https://azure.microsoft.com/regions/services/). 

В приведенных ниже таблицах службы, указанные как поддерживающие Resource Manager, имеют поставщики ресурсов и могут управляться с помощью PowerShell. Дополнительные сведения о поставщиках Resource Manager, версиях API и схемах см. [здесь](../azure-resource-manager/resource-manager-supported-services.md). Службы, указанные как доступные на портале, могут управляться с помощью [портала Azure для государственных организаций](https://portal.azure.us/). 


## <a name="computedocumentation-government-computemd"></a>[Среда выполнения приложений](documentation-government-compute.md)

| служба | Доступность диспетчера ресурсов | Microsoft Azure |
| --- | --- | --- |
| [Виртуальные машины](documentation-government-compute.md#virtual-machines) | Да | Да |
| Пакетная служба | Да | Да |
| Облачные службы | Да | Да |
| Service Fabric | Да | Да |
| наборы масштабирования виртуальных машин; | Да | Да |


## <a name="networkingdocumentation-government-networkingmd"></a>[Сеть](documentation-government-networking.md)

| служба | Доступность диспетчера ресурсов | Microsoft Azure |
| --- | --- | --- |
| [ExpressRoute](documentation-government-networking.md#expressroute-private-connectivity) | Да | Да |
| Виртуальная сеть | Да | Да |
| [Подсистема балансировки нагрузки](documentation-government-networking.md#support-for-load-balancer) | Да | Да |
| [Диспетчер трафика](documentation-government-networking.md#support-for-traffic-manger) | Да | Да |
| [VPN-шлюз](documentation-government-networking.md#support-for-vpn-gateway) | Да | Да |
| Шлюз приложений | Да | Да |
| ExpressRoute | Да | Да |



## <a name="storagedocumentation-government-services-storagemd"></a>[Хранилище](documentation-government-services-storage.md)

| служба | Доступность диспетчера ресурсов | Microsoft Azure |
| --- | --- | --- |
| [Хранилище — большие двоичные объекты](documentation-government-services-storage.md#azure-storage) | Да | Да |
| [Хранилище — таблицы](documentation-government-services-storage.md#azure-storage) | Да | Да |
| [Хранилище — очереди](documentation-government-services-storage.md#azure-storage) | Да | Да |
| [Хранилище — файлы](documentation-government-services-storage.md#azure-storage) | Да | Да |
| [Хранилище — диски](documentation-government-services-storage.md#azure-storage) | Да | Да |
| [StorSimple](documentation-government-services-storage.md) | Да | Да |
| [Архивация](documentation-government-services-storage.md#azure-storage) | Да | Да |
| [Site Recovery](documentation-government-services-storage.md#azure-storage) | Да | Да |
| [Импорт и экспорт](documentation-government-services-storage.md#azure-storage) | Да | Нет |



## <a name="web--mobiledocumentation-government-services-webandmobilemd"></a>[Интернет и мобильные устройства](documentation-government-services-webandmobile.md)

| служба | Доступность диспетчера ресурсов | Microsoft Azure |
| --- | --- | --- |
| [Служба приложений — веб-приложения](documentation-government-services-webandmobile.md#app-services) | Да | Да |
| [Служба приложений — приложения API](documentation-government-services-webandmobile.md#app-services) | Да | Да |
| [Служба приложений — мобильные приложения](documentation-government-services-webandmobile.md#app-services) | Да | Да |
| Службы мультимедиа | Да | Да |


## <a name="databasesdocumentation-government-services-databasemd"></a>[Базы данных](documentation-government-services-database.md)

| служба | Доступность диспетчера ресурсов | Microsoft Azure |
| --- | --- | --- |
| [База данных SQL](documentation-government-services-database.md#sql-database) | Да | Да |
| Хранилище данных SQL | Да | Да |
| База данных SQL Server Stretch | Да | Да |
| [Кэш Redis](documentation-government-services-database.md#azure-redis-cache) | Да | Да |


## <a name="intelligence--analyticsdocumentation-government-services-intelligenceandanalyticsmd"></a>[Аналитика](documentation-government-services-intelligenceandanalytics.md)

| служба | Доступность диспетчера ресурсов | Microsoft Azure |
| --- | --- | --- |
| [HDInsights](documentation-government-services-intelligenceandanalytics.md#hdinsight) | Да | Да |
| [Power BI Pro](documentation-government-services-intelligenceandanalytics.md#power-bi) | Нет | Нет (портал администрирования Office 365) |


## <a name="internet-of-things-iot"></a>Интернет вещей.

| служба | Доступность диспетчера ресурсов | Microsoft Azure |
| --- | --- | --- |
| Концентраторы событий | Да | Да |
| Центры уведомлений | Нет | Нет (перейти на [устаревший портал](https://manage.windowsazure.us/)) |


## <a name="enterprise-integration"></a>Интеграция Enterprise

| служба | Доступность диспетчера ресурсов | Microsoft Azure |
| --- | --- | --- |
| Служебная шина | Да | Да |
| [StorSimple](documentation-government-services-storage.md) | Да | Да |
| База данных SQL Server Stretch | Да | Да |



## <a name="security--identitydocumentation-government-services-securityandidentitymd"></a>[Безопасность и идентификация](documentation-government-services-securityandidentity.md)

| служба | Доступность диспетчера ресурсов | Microsoft Azure |
| --- | --- | --- |
| Azure Active Directory | Да | Да |
| [хранилище ключей;](documentation-government-services-securityandidentity.md#key-vault) | Да | Нет (ожидается вскоре) |
| Многофакторная идентификация | Да | Да |


## <a name="intelligence--analytics"></a>Аналитика

| служба | Доступность диспетчера ресурсов | Microsoft Azure |
| --- | --- | --- |
| Power BI | Да | Нет |
| HDInsight | Да | Да |



## <a name="monitoring--managementdocumentation-government-services-monitoringandmanagementmd"></a>[Мониторинг и управление](documentation-government-services-monitoringandmanagement.md)

| служба | Доступность диспетчера ресурсов | Microsoft Azure |
| --- | --- | --- |
| [Автоматизация](documentation-government-services-monitoringandmanagement.md#automation) | Да | Да |
| [Архивация](documentation-government-services-backup.md) | Да | Да |
| [Служба Log Analytics](documentation-government-services-monitoringandmanagement.md#log-analytics) | Да | Да |
| [Site Recovery](documentation-government-services-monitoringandmanagement.md#site-recovery) | Да | Да |
| Планировщик | Да | Нет |
| Мониторинг и диагностика | Да | Да |




## <a name="next-steps"></a>Дальнейшие действия
Чтобы получать дополнительные сведения и обновления, подпишитесь на [блог Microsoft Azure для государственных организаций](https://blogs.msdn.microsoft.com/azuregov/).


