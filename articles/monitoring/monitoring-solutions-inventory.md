---
title: Информация о сборе данных для решений по управлению в Azure | Документация Майкрософт
description: Решения по управлению в Azure — это собой коллекция правил логики, визуализации и получения данных, которые предоставляют метрики, связанные с определенной проблемной областью.  Эта статья содержит список решений по управлению, предлагаемых корпорацией Майкрософт, и сведения о методах и частоте сбора данных в этих решениях.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: bwren
ms.openlocfilehash: cab92187c6c36dc4670e202b15b35db8ad510239
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Информация о сборе данных для решений по управлению в Azure
Эта статья содержит список [решений по управлению](monitoring-solutions.md), предлагаемых корпорацией Майкрософт, со ссылками на подробную документацию по ним.  Также вы здесь найдете сведения о методах и частоте сбора данных в Log Analytics.  Сведения в этой статье помогут вам оценить доступные решения и изучить потоки данных и требования к подключению для разных решений по управлению. 

## <a name="list-of-management-solutions"></a>Список решений по управлению

В следующей таблице перечислены [решения по управлению](monitoring-solutions.md), предоставленных корпорацией Майкрософт для Azure. Отметка в столбце означает, что конкретное решение использует соответствующий метод для сбора данных в Log Analytics.  Те решения, для которых не отмечен ни один столбец, записывают данные в Log Analytics напрямую из другой службы Azure. Ссылки, предложенные для каждого из решений, ведут на подробную документацию, где вы можете получить дополнительную информацию.

Ниже перечислены столбцы этой таблицы с пояснениями.

- **Microsoft Monitoring Agent** используется в Windows и Linux для запуска пакета управления из SCOM и решений по управлению из Azure. В этой конфигурации агент подключается напрямую к Log Analytics, не используя группу управления Operations Manager. 
- **Operations Manager** — это точно такой же агент, как Microsoft Monitoring Agent. Но в этой конфигурации он [подключается к группе управления Operations Manager](../log-analytics/log-analytics-om-agents.md), которая в свою очередь подключена к Log Analytics. 
-  **Хранилище Azure** означает, что решение собирает данные через учетную запись хранения Azure. 
- **Нужен ли Operations Manager?** Подключение к группе управления Operations Manager является обязательным условием для сбора данных с помощью этого решения по управлению. 
- **Данные агента Operations Manager отправляются через группу управления**. Если агент [подключен к группе управления SCOM](../log-analytics/log-analytics-om-agents.md), то он отправляет данные в Log Analytics с сервера управления. В этой конфигурации агенту не требуется прямое подключение к службе Log Analytics. Если в этом поле нет отметки, все данные отправляются от агента напрямую в Log Analytics даже в том случае, если агент подключен к группе управления SCOM. Это означает, что ему требуется возможность взаимодействовать с Log Analytics через [шлюз OMS](../log-analytics/log-analytics-oms-gateway.md).
- **Частота сбора** обозначает частоту сбора данных в этом решении по управлению. 



| **Решение по управлению** | **Платформа** | **Microsoft Monitoring Agent** | **Агент Operations Manager** | **Служба хранилища Azure** | **Нужен ли Operations Manager?** | **Данные агента Operations Manager отправляются через группу управления** | **Частота сбора** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Анализ журнала действий](../log-analytics/log-analytics-activity.md) | Таблицы Azure | | | | | | при уведомлении |
| [Оценка AD](../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 дней |
| [Состояние репликации AD](../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 дней |
| [Работоспособность агентов](../operations-management-suite/oms-solution-agenthealth.md) | Windows и Linux | &#8226; | &#8226; | | | &#8226; | 1 минута |
| [Управление оповещениями](../log-analytics/log-analytics-solution-alert-management.md) (Nagios) |Linux |&#8226; | | | | |При получении |
| [Управление оповещениями](../log-analytics/log-analytics-solution-alert-management.md) (Zabbix) |Linux |&#8226; | | | | |1 минута |
| [Управление оповещениями](../log-analytics/log-analytics-solution-alert-management.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 минуты |
| [Azure Site Recovery](../site-recovery/site-recovery-overview.md) | Таблицы Azure | | | | | | Недоступно |
| [Соединитель Application Insights (предварительная версия)](../log-analytics/log-analytics-app-insights-connector.md) | Таблицы Azure | | | |  |  | при уведомлении |
| [Гибридная рабочая роль службы автоматизации](../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Недоступно |
| [Анализ шлюзов приложений Azure](../log-analytics/log-analytics-azure-networking-analytics.md) | Таблицы Azure |  |  |  |  |  | при уведомлении |
| **Решение по управлению** | **Платформа** | **Microsoft Monitoring Agent** | **Агент Operations Manager** | **Служба хранилища Azure** | **Нужен ли Operations Manager?** | **Данные агента Operations Manager отправляются через группу управления** | **Частота сбора** |
| [Анализ групп безопасности сети Azure](../log-analytics/log-analytics-azure-networking-analytics.md) | Таблицы Azure |  |  |  |  |  | при уведомлении |
| [Службы анализа SQL Azure (предварительная версия)](../log-analytics/log-analytics-azure-sql.md) | Windows | | | | | | 1 минута |
| [Архивация](../backup/backup-introduction-to-azure-backup.md) | Таблицы Azure |  |  |  |  |  | Недоступно |
| [Емкость и производительность (предварительная версия)](../log-analytics/log-analytics-capacity.md) |Windows |&#8226; |&#8226; | | |&#8226; |При получении |
| [Отслеживание изменений](../log-analytics/log-analytics-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |Ежечасно |
| [Отслеживание изменений](../log-analytics/log-analytics-change-tracking.md) |Linux |&#8226; | | | | |Ежечасно |
| [Контейнеры](../log-analytics/log-analytics-containers.md) | Windows и Linux | &#8226; | &#8226; |  |  |  | 3 минуты |
| [Анализ хранилища ключей](../log-analytics/log-analytics-azure-key-vault.md) |Windows | | | | | |при уведомлении |
| [Оценка вредоносных программ](../log-analytics/log-analytics-malware.md) |Windows |&#8226; |&#8226; | | |&#8226; |Ежечасно |
| [Монитор производительности сети](../log-analytics/log-analytics-network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Подтверждения TCP выполняются каждые 5 секунд, данные отправляются каждые 3 минуты |
| [Аналитика Office 365 (предварительная версия)](../operations-management-suite/oms-solution-office-365.md) |Windows | | | | | |при уведомлении |
| **Решение по управлению** | **Платформа** | **Microsoft Monitoring Agent** | **Агент Operations Manager** | **Служба хранилища Azure** | **Нужен ли Operations Manager?** | **Данные агента Operations Manager отправляются через группу управления** | **Частота сбора** |
| [Безопасность и аудит](../operations-management-suite/oms-security-getting-started.md) (системный журнал) | Linux | &#8226; | | |  |  | При получении |
| [Безопасность и аудит](../operations-management-suite/oms-security-getting-started.md) (журналы событий безопасности) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | При получении |
| [Безопасность и аудит](../operations-management-suite/oms-security-getting-started.md) (журналы брандмауэра) |Windows |&#8226; |&#8226; |  |  |  |При получении |
| [Анализ Service Fabric (предварительная версия)](../log-analytics/log-analytics-service-fabric.md) |Windows | | |&#8226; | | |5 мин |
| [Схема услуги](../operations-management-suite/operations-management-suite-service-map.md) | Windows и Linux | &#8226; | &#8226; |  |  |  | 15 секунд |
| [Оценка SQL](../log-analytics/log-analytics-sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 дней |
| [SurfaceHub](../log-analytics/log-analytics-surface-hubs.md) |Windows |&#8226; | | | | |При получении |
| [Оценка System Center Operations Manager (предварительная версия)](../log-analytics/log-analytics-scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7 дней |
| [Управление обновлениями](../operations-management-suite/oms-solution-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |не меньше 2 раз в день и через 15 минут после установки обновления |
| [Готовность к обновлению](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 дня |
| [Мониторинг VMware (предварительная версия)](../log-analytics/log-analytics-vmware.md) | Linux | &#8226; |  |  |  |  | 3 минуты |
| [Wire Data 2.0 (предварительная версия)](../log-analytics/log-analytics-wire-data.md) |Windows (2012 R2/8.1 или более поздней версии) |&#8226; |&#8226; | | | | 1 минута |




## <a name="next-steps"></a>Дополнительная информация
* Узнайте, как [создавать запросы](../log-analytics/log-analytics-log-searches.md) для анализа данных, собранных решением по управлению.
