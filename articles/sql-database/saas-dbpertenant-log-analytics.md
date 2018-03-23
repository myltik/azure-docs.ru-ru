---
title: Использование Log Analytics с помощью мультитенантного приложения базы данных SQL | Документация Майкрософт
description: Настройка и использование Log Analytics (OMS) с мультитенантным приложением SaaS для Базы данных SQL Azure
keywords: руководство по базе данных sql
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: b141ca521ae9c4d9bf6a4be620bc8e5432c52f83
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Настройка и использование Log Analytics (OMS) с мультитенантным приложением SaaS для базы данных SQL Azure

В этом руководстве описывается настройка и использование *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* для мониторинга эластичных пулов и баз данных. Это руководство основано на [руководстве по мониторингу производительности и управлению ею](saas-dbpertenant-performance-monitoring.md). Здесь показано, как использовать *Log Analytics* для расширения возможностей мониторинга и оповещений на портале Azure. В Log Analytics поддерживается мониторинг тысячи эластичных пулов и сотен тысяч баз данных. Log Analytics также обеспечивает единое решение для мониторинга, с помощью которого можно отслеживать интеграцию разных приложений и служб Azure в нескольких подписках Azure.

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Установка и настройка Log Analytics (OMS).
> * Использование Log Analytics для мониторинга пулов и баз данных.

Для работы с этим руководством выполните следующие предварительные требования:

* Развернуть SaaS-приложение Wingtip Tickets c однотенантной базой данных. См. инструкции из руководства по быстрому [развертыванию SaaS-приложение Wingtip Tickets c однотенантной БД](saas-dbpertenant-get-started-deploy.md).
* Установите Azure PowerShell. Дополнительные сведения см. в статье [Начало работы с Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Обсуждение сценариев и шаблонов SaaS и их влияния на требования к решениям для мониторинга см. в статье [Мониторинг производительности примера приложения SaaS WTP](saas-dbpertenant-performance-monitoring.md).

## <a name="monitoring-and-managing-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite-oms"></a>Мониторинг производительности базы данных и эластичного пула и управление ими с помощью Log Analytics или Operations Management Suite (OMS)

Для базы данных SQL средства мониторинга и создания оповещений доступны в базах данных и пулах на портале Azure. Эти встроенные средства мониторинга и оповещений ресурса не предназначены для мониторинга крупных сред или обеспечения единого представления для ресурсов и подписок.

Log Analytics можно использовать в сценариях с большим количеством операций для выполнения мониторинга и создания оповещений. Log Analytics представляет собой отдельную службу Azure, которая предоставляет аналитические сведения на основе журналов диагностики и данных телеметрии, собранных в рабочей области. Log Analytics предоставляет встроенный язык запросов и средства визуализации данных, позволяющие выполнять аналитику рабочих данных. Решения службы "Аналитика SQL Azure" предоставляют несколько предопределенных представлений и запросов мониторинга и оповещений эластичных пулов и баз данных. OMS также предоставляет конструктор пользовательского представления.

Решения аналитики и рабочих областей Log Analytics можно запустить на порталах Azure и OMS. Портал Azure является новой точкой доступа, однако может уступать порталу OMS в некоторых областях.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Создание диагностических данных о производительности путем имитации рабочей нагрузки в клиентах 

1. В **интегрированной среде сценариев PowerShell** откройте *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\**Demo-PerformanceMonitoringAndManagement.ps1***. Не закрывайте этот скрипт, так как при работе с этим руководством может потребоваться запустить несколько сценариев генератора нагрузки.
1. Если вы еще не сделали этого, подготовьте пакет клиентов, чтобы предоставить более содержательный контекст мониторинга. Этот процесс займет несколько минут.
   1. Чтобы выбрать скрипт **подготовки пакета клиентов**, задайте для параметра _$DemoScenario_ значение 1.
   1. Чтобы запустить скрипт или развернуть 17 дополнительных клиентов, нажмите клавишу **F5**.  

1. Теперь запустите генератор нагрузки, чтобы выполнить имитацию нагрузки на всех клиентах.  
    1. Чтобы **создать обычную нагрузку (около 30 DTU)**, задайте для параметра _$DemoScenario_ значение 2.
    1. Нажмите клавишу **F5** для запуска сценария.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Получение скриптов для SaaS-приложения Wingtip Tickets c однотенантной БД

Сценарии для приложения SaaS Wingtip Tickets c мультитенантной базой данных и исходный код этого приложения вы найдете в репозитории GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Инструкции по скачиванию и разблокированию скриптов приложения PowerShell Wingtip Tickets см. [здесь](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Установка и настройка Log Analytics и решений Аналитики SQL Azure

Log Analytics — это отдельная служба, которую необходимо настроить. Log Analytics собирает данные журнала, телеметрию и метрики в рабочей области Log Analytics. Создайте рабочую область Log Analytics, которая ничем не отличается от других ресурсов в Azure. Рабочую область не обязательно создавать в одной и той же группе ресурсов с приложением, которое она отслеживает, однако зачастую это самый оптимальный вариант. Использование одной группы ресурсов в приложении Wingtip Tickets гарантирует, что рабочая область удаляется с приложением.

1. В **интегрированной среде сценариев PowerShell** откройте *..\\\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\**Demo-LogAnalytics.ps1***.
1. Нажмите клавишу **F5** для запуска сценария.

На этом этапе можно будет открыть Log Analytics на портале Azure (или на портале OMS). Чтобы собрать данные телеметрии и отобразить их в рабочей области Log Analytics, требуется несколько минут. Чем дольше выполняется сбор системных данных диагностики, тем содержательнее результат. Сейчас самое время сделать перерыв. Только убедитесь, что генератор нагрузки все еще работает.

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Использование Log Analytics и решений Аналитики SQL для мониторинга пулов и баз данных


В этом упражнении откройте Log Analytics и портал OMS для просмотра данных телеметрии, собираемых для баз данных и пулов.

1. Перейдите на [портал Azure](https://portal.azure.com) и откройте Log Analytics, щелкнув **Все службы** и выполнив поиск Log Analytics.

   ![Открытие службы Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Выберите рабочую область _wtploganalytics-&lt;пользователь&gt;_.

1. Щелкните **Обзор**, чтобы открыть решение Log Analytics на портале Azure.

   ![Ссылка на параметр "Обзор"](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Активация решения может занять несколько минут. Подождите.

1. Щелкните элемент Аналитики SQL Azure, чтобы открыть его.

    ![Обзор](media/saas-dbpertenant-log-analytics/overview.png)

    ![Аналитика](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Используйте внутреннюю полосу прокрутки внизу, чтобы просмотреть страницы решений (при необходимости можно обновить страницу).

1. На странице сводки можно щелкнуть нужные плитки или отдельную базу данных, чтобы открыть детальный обозреватель.

1. Измените параметр фильтра, чтобы указать диапазон времени (в рамках этого руководства используется значение _Последний час_).

    ![Фильтр времени](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Выберите одну базу данных, чтобы просмотреть данные об использовании запросов и метрики для нее.

    ![Аналитика базы данных](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Чтобы просмотреть метрики использования, прокрутите страницу аналитики вправо.
 
     ![Метрики базы данных](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Прокрутите страницу аналитики влево и щелкните плитку сервера в списке "Сведения о ресурсе". Откроется страница со списком пулов и баз данных на сервере. 

     ![Информация о ресурсах](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

 
     ![Сервер с пулами и базами данных](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. На открывшейся странице сервера, где показаны пулы и базы данных сервера, щелкните пул.  На открывшейся странице пула прокрутите вправо, чтобы просмотреть метрики пула.  

     ![Метрики пула](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)



1. В рабочей области Log Analytics выберите **Портал OMS**, чтобы открыть в нем рабочую область.

    ![OMS](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

На портале OMS можно подробнее исследовать данные журнала и метрик в рабочей области.  

В отличие от создания оповещений для каждого ресурса на портале Azure, мониторинг и оповещения в Log Analytics и OMS основаны на запросах к данным в рабочей области. Создавая оповещения на основе запросов, вместо того чтобы определять одно оповещение для каждой базы данных, можно определить одно общее оповещение для всех баз данных. Запросы ограничиваются только данными, доступными в рабочей области.

Дополнительные сведения об использовании OMS для создания запросов и настройки оповещений см. в статье [Работа с правилами генерации оповещений в Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

За использование Log Analytics для базы данных SQL будет взиматься плата, которая зависит от потребляемого объема данных в рабочей области. В этом руководстве вы создали бесплатную рабочую область с ограничением на 500 МБ в день. При достижении лимита данные больше не будут добавляться в рабочую область.


## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Установка и настройка Log Analytics (OMS).
> * Использование Log Analytics для мониторинга пулов и баз данных.

[Выполнение распределенных запросов в нескольких базах данных SQL Azure](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [SQL Database Wingtip SaaS tutorials](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) (Руководства по SQL Database Wingtip SaaS)
* [Мониторинг базы данных SQL Azure с помощью служб анализа SQL Azure (предварительная версия) в Log Analytics](../log-analytics/log-analytics-azure-sql.md).
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
