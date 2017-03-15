---
title: "Мониторинг и управление Azure для государственных организаций | Документация Майкрософт"
description: "Эта статья содержит сравнительный анализ функциональных возможностей и рекомендации по разработке приложений, которые будут использоваться в Azure для государственных организаций."
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 2/28/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a04b2f26c8ab34fdbfc7412d47292a560466eb44
ms.openlocfilehash: c33d50a900be53a3161ad60bc34e2e819fe62947
ms.lasthandoff: 03/01/2017


---
# <a name="azure-government-monitoring--management"></a>Мониторинг и управление Azure для государственных организаций
В этой статье описаны варианты служб мониторинга и управления, а также представлены рекомендации для среды Azure для государственных организаций.

## <a name="automation"></a>Автоматизация
В Azure для государственных организаций используется общедоступная версия службы автоматизации.

### <a name="variations"></a>Варианты
Приведенные ниже возможности службы автоматизации в настоящее время недоступны в Azure для государственных организаций.

* Создание учетных данных субъекта-службы для аутентификации

Дополнительные сведения см. в [общедоступной документации по службе автоматизации](../automation/automation-intro.md).

## <a name="backup"></a>Архивация
В Azure для государственных организаций используется общедоступная версия службы архивации.

Дополнительные сведения см. в статье [Служба архивации в Azure для государственных организаций](documentation-government-services-backup.md).

## <a name="site-recovery"></a>Site Recovery
В Azure для государственных организаций используется общедоступная версия Site Recovery (ASR).

Дополнительные сведения см. в [общедоступной документации по Site Recovery](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Варианты
Приведенные ниже возможности Site Recovery сейчас недоступны в Azure для государственных организаций:

* хранилища Site Recovery Azure Resource Manager.
* Уведомление по электронной почте

| Site Recovery | Классический | Диспетчер ресурсов |
| --- | --- | --- |
| VMware или физический компьютер  | GA | GA |
| Hyper-V. | GA | GA |
| "Сеть — сеть" | GA | GA |

>[!NOTE]
>Таблица относится к регионам Виргиния и Айова (для обслуживания государственных организаций США).

Приведенные ниже URL-адреса для ASR отличаются в Azure для государственных организаций.

| Azure Public | Azure Government | Примечания |
| --- | --- | --- |
| *.hypervrecoverymanager.windowsazure.com | *.hypervrecoverymanager.windowsazure.us | Доступ к службе Site Recovery. |
| *. backup.windowsazure.com  | *.backup.windowsazure.us | Доступ к Protection Service. |
| *.blob.core.windows.net | *.blob.core.usgovcloudapi.net | Для хранения моментальных снимков виртуальных машин. |
| http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | Для скачивания MySQL. |

## <a name="log-analytics"></a>Служба Log Analytics
В Azure для государственных организаций используется общедоступная версия Log Analytics.

### <a name="variations"></a>Варианты
Приведенные ниже возможности Log Analytics в настоящее время недоступны в Azure для государственных организаций.

* Решения, которые находятся на этапе предварительной версии в Microsoft Azure, включая следующие:
  * Решение для мониторинга сетей.
  * Схема услуги
  * Решение для Office 365.
  * Решение для анализа обновлений Windows 10.
  * решение Application Insights;
  * решение для анализа сетей Azure;
  * решение для анализа службы автоматизации Azure;
  * решение для анализа хранилища ключей.
* Решения и функции, требующие обновления локального программного обеспечения, включая следующие:
  * группы компьютеров из System Center Configuration Manager;
  * решение Surface Hub.
* Функции, которые находятся на этапе предварительной версии в общедоступной версии Azure, включая следующие:
  * экспорт данных в Power BI.
* Метрики и система диагностики Azure.
* Мобильные приложения Operations Management Suite.

URL-адреса для Log Analytics отличаются в Azure для государственных организаций:

| Azure Public | Azure Government | Примечания |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Портал Log Analytics |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[API сборщика данных](../log-analytics/log-analytics-data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |Обмен данными между агентами: [настройка параметров брандмауэра](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |Обмен данными между агентами: [настройка параметров брандмауэра](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |Обмен данными между агентами: [настройка параметров брандмауэра](../log-analytics/log-analytics-proxy-firewall.md) |

Поведение приведенных ниже функций Log Analytics отличается в Azure для государственных организаций:

* Для подключения вашего сервера управления System Center Operations Manager к Log Analytics необходимо скачать и импортировать обновленные пакеты управления.
  + System Center Operations Manager 2016:
    1. Установите [накопительный пакет обновления 2 для System Center Operations Manager 2016](https://support.microsoft.com/help/3209591).
    2. Импортируйте пакеты управления, входящие в состав накопительного пакета обновления 2, в Operations Manager. Сведения о том, как импортировать пакет управления с диска, см. в статье [How to Import an Operations Manager Management Pack](http://technet.microsoft.com/library/hh212691.aspx) (Импорт пакета управления Operations Manager) на веб-сайте Microsoft TechNet.
    3. Чтобы подключить Operations Manager к Log Analytics, выполните действия, описанные в статье [Подключение Operations Manager к Log Analytics](../log-analytics/log-analytics-om-agents.md).
  + System Center Operations Manager 2012 R2 UR3 (или более поздней версии) или System Center Operations Manager 2012 SP1 UR7 (или более поздней версии):
    1. Скачайте и сохраните [обновленные пакеты управления](http://go.microsoft.com/fwlink/?LinkId=828749).
    2. Распакуйте скачанный файл.
    3. Импортируйте пакеты управления в Operations Manager. Сведения о том, как импортировать пакет управления с диска, см. в статье [How to Import an Operations Manager Management Pack](http://technet.microsoft.com/library/hh212691.aspx) (Импорт пакета управления Operations Manager) на веб-сайте Microsoft TechNet.
    4. Чтобы подключить Operations Manager к Log Analytics, выполните действия, описанные в статье [Подключение Operations Manager к Log Analytics](../log-analytics/log-analytics-om-agents.md).
  


### <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
* Можно ли перенести данные из Log Analytics (в Microsoft Azure) в Azure для государственных организаций?
  * Нет. Невозможно переместить данные или рабочую область из Microsoft Azure в Azure для государственных организаций.
* Можно ли переключаться между рабочими областями в Microsoft Azure и Azure для государственных организаций на портале Operations Management Suite Log Analytics?
  * Нет. Для Microsoft Azure и Azure для государственных организаций используются отдельные порталы, не предусматривающие взаимный обмен данными.

Дополнительные сведения см. в [общедоступной документации по Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Дальнейшие действия
Чтобы получать дополнительные сведения и обновления, подпишитесь на <a href="https://blogs.msdn.microsoft.com/azuregov/">блог Microsoft Azure для государственных организаций. </a>

