---
title: Использование Log Analytics с мультитенантным приложением базы данных SQL | Документация Майкрософт
description: Настройка и использование Log Analytics с мультитенантным приложением SaaS для Базы данных SQL Azure
keywords: руководство по базе данных sql
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 38adf3dd2be0770dd815644ece452a82bc98baf9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645323"
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>Настройка и использование Log Analytics с мультитенантным приложением SaaS для Базы данных SQL

В этом руководстве описывается настройка и использование [Log Analytics](/azure/log-analytics/log-analytics-overview) для мониторинга эластичных пулов и баз данных. Данный документ основан на руководстве [Мониторинг производительности баз данных и пулов SQL Azure в мультитенантном приложении SaaS и управление ею](saas-dbpertenant-performance-monitoring.md). В нем показано, как использовать Log Analytics для расширения возможностей мониторинга и оповещений на портале Azure. В Log Analytics поддерживается мониторинг тысячи эластичных пулов и сотен тысяч баз данных. Log Analytics также обеспечивает единое решение для мониторинга, с помощью которого можно отслеживать интеграцию разных приложений и служб Azure в нескольких подписках Azure.

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Установка и настройка Log Analytics.
> * Использование Log Analytics для мониторинга пулов и баз данных.

Для работы с этим руководством выполните следующие предварительные требования:

* Должно быть развернуто приложение SaaS Wingtip Tickets, использующее одну базу данных на клиент. Изучите инструкции из руководства по быстрому [развертыванию приложения SaaS Wingtip Tickets, использующего одну базу данных на клиент](saas-dbpertenant-get-started-deploy.md).
* Установите Azure PowerShell. Дополнительные сведения см. в статье [Начало работы с Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Обсуждение сценариев и шаблонов SaaS и их влияния на требования к решениям для мониторинга можно найти в статье [Мониторинг производительности баз данных и пулов SQL Azure в мультитенантном приложении SaaS и управление ею](saas-dbpertenant-performance-monitoring.md).

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>Мониторинг производительности базы данных и эластичного пула и управление ими с помощью Log Analytics

Для базы данных SQL средства мониторинга и создания оповещений для баз данных и пулов доступны на портале Azure. Эти встроенные функции мониторинга и оповещения удобны, но зависят от ресурсов. Это означает, что они не так хорошо подходят для отслеживания крупных систем или предоставления унифицированного представления ресурсов и подписок.

В сценариях с большим количеством операций для мониторинга и оповещения можно использовать Log Analytics. Log Analytics представляет собой отдельную службу Azure, которая предоставляет аналитические сведения на основе журналов диагностики и данных телеметрии, собранных в рабочей области. Log Analytics предоставляет встроенный язык запросов и инструменты визуализации данных, позволяющие выполнять анализ рабочих данных. Решение "Аналитика SQL Azure" предоставляет несколько предопределенных представлений и запросов мониторинга и оповещений эластичных пулов и баз данных. Log Analytics также предоставляет конструктор пользовательского представления.

Решения на основе аналитики и рабочих областей Log Analytics можно запустить на порталах Azure и Operations Management Suite. Портал Azure является более новой точкой доступа, однако может уступать порталу Operations Management Suite в некоторых областях.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Создание диагностических данных о производительности путем имитации рабочей нагрузки в клиентах 

1. В интегрированной среде сценариев PowerShell откройте файл *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Demo-PerformanceMonitoringAndManagement.ps1*. Не закрывайте этот сценарий, так как при работе с данным руководством может потребоваться запустить несколько сценариев генератора нагрузки.
2. Если вы еще не сделали этого, подготовьте пакет клиентов, чтобы предоставить более содержательный контекст мониторинга. Этот процесс займет несколько минут.

   a. Чтобы выбрать сценарий **подготовки пакета клиентов**, задайте для параметра _$DemoScenario_ значение 1.

   Б. Чтобы запустить сценарий или развернуть 17 дополнительных клиентов, нажмите клавишу F5.

3. Теперь запустите генератор нагрузки, чтобы выполнить имитацию нагрузки на всех клиентах.

    a. Чтобы **создать обычную нагрузку (около 30 DTU)**, задайте для параметра _$DemoScenario_ значение 2.

    Б. Нажмите клавишу F5 для запуска сценария.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Получение сценариев для приложения SaaS Wingtip Tickets, использующего одну базу данных на клиент

Сценарии и исходный код приложения SaaS Wingtip Tickets мультитенантной базы данных размещены в репозитории GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Инструкции по скачиванию и разблокированию сценариев PowerShell приложения Wingtip Tickets приведены [здесь](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>Установка и настройка Log Analytics и решения "Аналитика SQL Azure"

Log Analytics — это отдельная служба, которую необходимо настроить. Log Analytics собирает данные журнала, телеметрии и метрик в рабочей области Log Analytics. Рабочую область Log Analytics нужно создать, как и другие ресурсы в Azure. Рабочую область не обязательно создавать в одной группе ресурсов с приложением, которое она отслеживает. Однако зачастую это самый оптимальный вариант. Использование одной группы ресурсов с приложением Wingtip Tickets гарантирует, что рабочая область удаляется вместе с приложением.

1. В интегрированной среде сценариев PowerShell откройте файл *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\Demo-LogAnalytics.ps1*.
2. Нажмите клавишу F5 для запуска сценария.

Теперь можно открыть Log Analytics на портале Azure или на портале Operations Management Suite. Для сбора данных телеметрии и их отображения в рабочей области Log Analytics требуется несколько минут. Чем дольше выполняется сбор системных данных диагностики, тем содержательнее результат. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Использование Log Analytics и решений Аналитики SQL для мониторинга пулов и баз данных


В этом упражнении откройте Log Analytics и портал Operations Management Suite для просмотра данных телеметрии, собираемых для баз данных и пулов.

1. Перейдите на [портал Azure](https://portal.azure.com). Выберите **Все службы**, чтобы открыть Log Analytics. Найдите Log Analytics.

   ![Открытие Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

2. Выберите рабочую область _wtploganalytics-&lt;пользователь&gt;_.

3. Щелкните **Обзор**, чтобы открыть решение Log Analytics на портале Azure.

   ![Обзор](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Активация решения может занять несколько минут. 

4. Щелкните элемент **Аналитика SQL Azure**, чтобы открыть его.

    ![Элемент обзора](media/saas-dbpertenant-log-analytics/overview.png)

5. Используйте внутреннюю полосу прокрутки внизу, чтобы просмотреть страницы решений в представлении. При необходимости обновите страницу.

6. На странице сводки можно щелкнуть нужные элементы или отдельную базу данных, чтобы открыть обозреватель с детализированным данными.

    ![Панель мониторинга Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

7. Измените параметр фильтра, чтобы указать диапазон времени. В рамках этого руководства выберите **Последний час**.

    ![Фильтр времени](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

8. Выберите одну базу данных, чтобы просмотреть данные об использовании запросов и метрики для нее.

    ![Аналитика базы данных](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

9. Чтобы просмотреть метрики использования, прокрутите страницу аналитики вправо.
 
     ![Метрики базы данных](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

10. Прокрутите страницу аналитики влево и щелкните элемент сервера в списке **Сведения о ресурсе**.  

    ![Список "Сведения о ресурсе"](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Откроется страница со списком пулов и баз данных на сервере.

    ![Сервер с пулами и базами данных](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

11. Выберите пул. На открывшейся странице пула прокрутите вправо, чтобы просмотреть метрики пула. 

    ![Метрики пула](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


12. В рабочей области Log Analytics выберите **Портал OMS**, чтобы открыть на нем рабочую область.

    ![Элемент портала Operations Management Suite](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

На портале Operations Management Suite можно подробнее исследовать данные журнала и метрик в рабочей области. 

В отличие от создания оповещений для каждого ресурса на портале Azure, мониторинг и оповещения в Log Analytics основаны на запросах к данным в рабочей области. Создавая оповещения на основе запросов, вместо того чтобы определять одно оповещение для каждой базы данных, можно определить одно общее оповещение для всех баз данных. Запросы ограничиваются только данными, доступными в рабочей области.

Дополнительные сведения об использовании Log Analytics для создания запросов и настройки оповещений см. в статье [Работа с правилами генерации оповещений в Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

За использование Log Analytics для базы данных SQL будет взиматься плата, которая зависит от потребляемого объема данных в рабочей области. В этом руководстве вы создали бесплатную рабочую область с ограничением в 500 МБ в день. По достижении этого ограничения данные больше не будут добавляться в рабочую область.


## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Установка и настройка Log Analytics.
> * Использование Log Analytics для мониторинга пулов и баз данных.

Ознакомьтесь со статьей [Настройка и использование Log Analytics (OMS) с мультитенантным приложением SaaS для базы данных SQL Azure](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Дополнительные руководства на основе приложения SaaS Wingtip Tickets, использующего одну базу данных на клиент](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Мониторинг базы данных SQL Azure с помощью служб анализа SQL Azure (предварительная версия) в Log Analytics](../log-analytics/log-analytics-azure-sql.md).
