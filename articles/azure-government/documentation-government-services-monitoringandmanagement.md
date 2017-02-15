---
title: "Мониторинг и управление Azure для государственных организаций | Документация Майкрософт"
description: "Эта статья содержит сравнительный анализ функциональных возможностей и рекомендации по разработке приложений, которые будут использоваться в Azure для государственных организаций."
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/31/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 3e460dd2aaf51ef2ba9840513d236ce313320c80
ms.openlocfilehash: 39a87bdbef7c532f9e50f1e71e9c12797bc9a33c


---
# <a name="azure-government-monitoring--management"></a>Мониторинг и управление Azure для государственных организаций
В этой статье описаны варианты служб мониторинга и управления, а также представлены рекомендации для среды Azure для государственных организаций.

## <a name="automation"></a>Автоматизация
В Azure для государственных организаций используется общедоступная версия службы автоматизации.

### <a name="variations"></a>Варианты
Приведенные ниже возможности службы автоматизации в настоящее время недоступны в Azure для государственных организаций.

* Создание учетных данных участника-службы для аутентификации.

Дополнительные сведения см. в [общедоступной документации по службе автоматизации](../automation/automation-intro.md).

## <a name="backup"></a>Архивация
В Azure для государственных организаций используется общедоступная версия службы архивации.

Дополнительные сведения см. в статье [Служба архивации в Azure для государственных организаций](documentation-government-services-backup.md).

### <a name="variations"></a>Варианты
Приведенные ниже возможности службы архивации сейчас недоступны в Azure для государственных организаций:

* хранилища Azure Resource Manager;
* управление с помощью портала Azure (поддерживается классический портал Azure).

URL-адреса для службы архивации отличаются в Azure для государственных организаций.

## <a name="site-recovery"></a>Site Recovery
В Azure для государственных организаций используется общедоступная версия Site Recovery (ASR).

Дополнительные сведения см. в [общедоступной документации по Site Recovery](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Варианты
Приведенные ниже возможности Site Recovery сейчас недоступны в Azure для государственных организаций:

* хранилища Site Recovery Azure Resource Manager.

| Site Recovery | Классический | Диспетчер ресурсов | 
| --- | --- | --- |
| VMware или физический компьютер  | GA | Запланировано |
| Hyper-V. | GA | Запланировано |
| "Сеть — сеть" | GA | Запланировано |

Примечания. Таблица относится к VA и Iowa. 

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
  * решение для мониторинга зависимости приложений;
  * Решение для Office 365.
  * Решение для анализа обновлений Windows 10.
  * решение Application Insights;
  * решение для анализа сетей Azure;
  * решение для анализа службы автоматизации Azure;
  * решение для анализа хранилища ключей.
* Решения и функции, требующие обновления локального программного обеспечения, включая следующие:
  * интеграция с System Center Operations Manager 2016 (поддерживаются более ранние версии Operations Manager);
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

* Агент Windows для Azure для государственных организаций нужно скачать с [портала Log Analytics](https://oms.microsoft.us).
* Для подключения вашего сервера управления System Center Operations Manager к Log Analytics необходимо скачать и импортировать обновленные пакеты управления.
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

## <a name="site-recovery"></a>Site Recovery
В Azure для государственных организаций используется общедоступная версия Site Recovery.

Дополнительные сведения см. в [общедоступной документации по Site Recovery](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Варианты
Приведенные ниже возможности Site Recovery сейчас недоступны в Azure для государственных организаций:

* хранилища Site Recovery Azure Resource Manager.

## <a name="next-steps"></a>Дальнейшие действия
Чтобы получать дополнительные сведения и обновления, подпишитесь на <a href="https://blogs.msdn.microsoft.com/azuregov/">блог Microsoft Azure для государственных организаций. </a>



<!--HONumber=Dec16_HO2-->


