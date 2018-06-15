---
title: Что такое служба Log Analytics в Azure? | Документация Майкрософт
description: Log Analytics — это служба в Azure, которая помогает собирать и анализировать операционные данные, формируемые ресурсами в облачных и локальных средах.  В этой статье приводится краткий обзор различных компонентов службы Log Analytics и ссылки на подробные материалы.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: bwren
ms.openlocfilehash: b951d41dab4d349a8d648e7eaa7e23b73ced2ced
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29939048"
---
# <a name="what-is-azure-log-analytics"></a>Что такое служба Log Analytics в Azure?
Служба Log Analytics играет ключевую роль в управлении Azure. Она собирает данные телеметрии и другие данные из разнообразных источников и предоставляет язык запросов и механизм аналитики, который позволяет анализировать работу приложений и ресурсов.  Вы можете напрямую взаимодействовать с данными Log Analytics с помощью поиска по журналам и представлений. Кроме того, вы можете воспользоваться средствами анализа в других службах Azure, которые хранят свои данные в Log Analytics, например Application Insights или центр безопасности Azure.  

Для службы Log Analytics необходима минимальная конфигурация. Она уже интегрирована с другими службами Azure.  Вам просто нужно создать рабочую область для включения коллекции.  Затем установите агенты на виртуальных машинах, чтобы добавить их в рабочую область, и включите решения по управлению, которые содержат логику, предоставляя дополнительные сведения о различных приложениях.  Обычно типы данных либо предопределены, либо автоматически создаются по мере сбора данных.


## <a name="role-in-monitoring"></a>Роль в мониторинге

Описание служб мониторинга в Azure см. в статье [Мониторинг приложений и ресурсов в Azure](../monitoring-and-diagnostics/monitoring-overview.md).  Log Analytics играет ключевую роль, объединяя данные мониторинга из разных источников и предоставляя эффективный язык запросов для консолидации и анализа.  

Однако служба Log Analytics не ограничивается только мониторингом ресурсов Azure.  Она может собирать данные из ресурсов, находящихся в локальном расположении или в других облаках, для создания гибридной среды мониторинга. Кроме того, она может напрямую подключаться к System Center Operations Manager для сбора телеметрии имеющихся агентов.  Средства анализа в Log Analytics, такие как поиск по журналам, представления и решения по управлению, обрабатывают все собранные данные, предоставляя возможность централизованного анализа всей среды.



## <a name="data-collection"></a>Сбор данных
Log Analytics собирает данные из различных источников.  После сбора данные объединяются в отдельные таблицы для каждого типа данных, что позволяет анализировать все данные независимо от исходного источника.

Методы сбора данных в Log Analytics включают следующее:

- Настройка Azure Monitor для копирования метрик и журналов, собранных из ресурсов Azure.
- Агенты на виртуальных машинах [Windows](log-analytics-windows-agent.md) и [Linux](log-analytics-linux-agents.md) отправляют телеметрию из гостевой ОС и приложений в Log Analytics в соответствии с настроенными [источниками данных](log-analytics-data-sources.md).  
- Подключение [группы управления System Center Operations Manager](log-analytics-om-agents.md) к Log Analytics для сбора данных из ее агентов.
- Службы Azure, такие как [Application Insights](https://docs.microsoft.com/azure/application-insights/) и [центр безопасности Azure](https://docs.microsoft.com/azure/security-center/), хранят свои данные непосредственно в Log Analytics без какой-либо конфигурации.
- Запись данных из командной строки PowerShell или [Runbook службы автоматизации Azure](../automation/automation-runbook-types.md) с помощью командлетов Log Analytics.
- При наличии особых требований вы можете использовать [API сборщика данных HTTP](log-analytics-data-collector-api.md), чтобы записывать данные в Log Analytics из любого клиента REST API.


![Компоненты службы Log Analytics](media/log-analytics-overview/collecting-data.png)

## <a name="add-functionality-with-management-solutions"></a>Добавление возможностей с помощью решений по управлению
[Решения по управлению](log-analytics-add-solutions.md) предоставляют предварительно упакованную логику для конкретного продукта или сценария.  Они могут собирать дополнительные данные в Log Analytics или обрабатывать уже собранные данные.  Как правило, эти решения содержат представление, которое помогает анализировать эти дополнительные данные.  Существуют решения для самых разных функций. Но помимо доступных решений постоянно добавляются также и новые.  Вы можете легко найти все доступные решения и [добавить их в рабочую область](log-analytics-add-solutions.md) из Azure Marketplace.  

![Marketplace](media/log-analytics-overview/solutions.png)


## <a name="query-language"></a>Язык запросов

Служба Log Analytics поддерживает [различные языки запросов](http://docs.loganalytics.io) для быстрого получения, консолидации и анализа данных.  Вы можете создавать и тестировать запросы с помощью [порталов поиска по журналам или расширенной аналитики](log-analytics-log-search-portals.md), а затем либо напрямую анализировать данные с помощью этих средств, либо сохранять запросы для использования с визуализациями и предупреждениями. Кроме того, их можно экспортировать в другие инструменты, такие как Power BI или Excel.

Язык запросов Log Analytics подходит для простых поисков по журналам, однако он также включает расширенные функции, например агрегирования, объединения и интеллектуальную аналитику. Вы можете быстро изучить язык запросов с помощью доступных [руководств](https://docs.loganalytics.io/docs/Learn/Tutorials).  Для пользователей, которые уже знакомы с [SQL](https://docs.loganalytics.io/docs/Learn/References/SQL-to-Azure-Log-Analytics) и [Splunk](https://docs.loganalytics.io/docs/Learn/References/Splunk-to-Azure-Log-Analytics), предоставляются отдельные руководства.

![Поиск по журналам](media/log-analytics-overview/analytics-query.png)


## <a name="visualize-log-analytics-data"></a>Визуализация данных Log Analytics

[Представления в Log Analytics](log-analytics-view-designer.md) визуально отображают данные, полученные при поиске по журналам.  Каждое представление содержит сочетание визуализаций, таких как линейчатые диаграммы и графики в дополнение к спискам, суммирующим важные данные.  [Решения по управлению](#add-functionality-with-management-solutions) включают представления, которые суммируют данные для конкретного приложения. Вы можете создавать собственные представления, чтобы отобразить данные, полученные при любом поиске по журналам Log Analytics.

![Представление Log Analytics](media/log-analytics-overview/view.png)

Вы также можете закрепить результаты запроса Log Analytics на [панели мониторинга Azure](../azure-portal/azure-portal-dashboards.md), которая позволяет объединять плитки различных служб Azure.  Вы даже можете закрепить на панели мониторинга представление Log Analytics.

![Панели мониторинга Azure](media/log-analytics-overview/dashboard.png)

## <a name="creating-alerts-from-log-analytics-data"></a>Создание предупреждений в Log Analytics

Используйте [Оповещения Azure](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md), чтобы получать оповещения о важных условиях в данных Log Analytics.  Запрос автоматически запускается в запланированное время, и, если результаты соответствуют определенным критериям, создается оповещение.  Это позволяет объединять оповещения из Log Analytics с другими источниками, такими как оповещения в режиме реального времени [Azure Monitor](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md) и исключения приложений [Application Insights](../application-insights/app-insights-alerts.md), предоставляя общий доступ к [группам действий](../monitoring-and-diagnostics/monitoring-action-groups.md) для ответа на условия оповещений.

![Предупреждение](media/log-analytics-overview/alerts.png)


## <a name="using-log-analytics-data-in-other-services"></a>Использование данных Log Analytics в других службах
Службы, такие как Application Insights и центр безопасности Azure, хранят свои данные в Log Analytics.  Обычно вы будете использовать различные средства анализа, предоставляемые этими службами, но можно также использовать запросы Log Analytics для доступа к их данным, объединяя их с данными других служб.  

Ниже приведен пример представления из Application Insights.  Если вы нажмете значок в правом верхнем углу, запустится консоль аналитики Log Analytics с запросами, используемыми графиком.

![Application Insights](media/log-analytics-overview/application-insights.png)


## <a name="exporting-log-analytics-data"></a>Экспорт данных Log Analytics

Log Analytics также предоставляет свои данные за пределы Azure.  Вы можете настроить [Power BI](log-analytics-powerbi.md) для импорта результатов запроса в запланированный промежуток времени. Это позволит воспользоваться такими функциями, как объединение данных из различных источников и совместное использование отчетов в веб-устройствах и мобильных устройствах.  Кроме того, используя [API поиска по журналам](log-analytics-log-search-api.md) , можно создать настраиваемые решения на основе данных Log Analytics или добиться интеграции с другими системами.

Вы можете использовать [Logic Apps](../logic-apps/logic-apps-overview.md) в Azure для создания настраиваемых рабочих процессов на основе данных Log Analytics.  Для более сложной логики на основе PowerShell вы можете использовать модули [Runbook службы автоматизации Azure](../automation/automation-runbook-types.md).

![Power BI](media/log-analytics-overview/export.png)



## <a name="next-steps"></a>Дополнительная информация
- [Сбор данных о виртуальных машинах Azure](log-analytics-quick-collect-azurevm.md).
- [Просмотр и анализ данных, собранных с помощью поиска по журналам Log Analytics](log-analytics-tutorial-viewdata.md).
* [Добавление решений для управления Azure Log Analytics в рабочую область](log-analytics-add-solutions.md).

