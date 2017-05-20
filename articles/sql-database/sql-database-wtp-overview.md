---
title: "Готовое приложение SaaS (пример приложения SaaS, использующего базу данных SQL Azure) | Документация Майкрософт"
description: "Создание приложений SaaS с помощью базы данных SQL"
keywords: "руководство по базе данных sql"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: bf5745a788cd9ab6bf2ea8d5d97b8c04f083fc5d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-the-wingtip-tickets-platform-wtp-sample-saas-application"></a>Знакомство с примером приложения SaaS Wingtip Tickets Platform

Приложение SaaS Wingtip Tickets Platform — это пример многопользовательского приложения, которое демонстрирует уникальные преимущества базы данных SQL. Для обслуживания нескольких клиентов приложение использует шаблон приложения SaaS, в котором на один клиент используется одна база данных. Приложение WTP разработано для демонстрации возможностей базы данных SQL Azure, которые позволяют работать со сценариями SaaS, включая модели проектирования и управления SaaS. [Приложение WTP развертывается менее чем за пять минут](sql-database-saas-tutorial.md), поэтому вы сможете быстро приступить к работе.

Развернув приложение WTP, изучите [коллекцию руководств](#sql-database-saas-tutorials), которые созданы на основе первоначального развертывания. Каждое руководство посвящено типичной задаче, которая реализована в приложениях SaaS. Задачи реализуются в соответствии с шаблонами SaaS, использующими встроенные функции SQL Database. Описанные шаблоны включают в себя подготовку новых клиентов, восстановление баз данных клиентов, выполнение распределенных запросов по всем клиентам и развертывание изменений схемы во все базы данных клиентов. Каждое руководство включает повторно используемые скрипты с подробными объяснениями, которые значительно упрощают понимание и реализацию шаблонов управления SaaS в ваших приложениях.

Хотя приложение WTP частично завершено и считается примером приложения, важно сосредоточиться на основных шаблонах SaaS, так как они относятся к уровню данных. Другими словами сосредоточиться на уровне данных, а не анализировать само приложение. Для реализации этих основных шаблонов SaaS в ваших приложениях важно понять суть этого процесса, принимая во внимание любые необходимые изменения для ваших конкретных бизнес-требований.



## <a name="application-architecture"></a>Архитектура приложения

Приложение WTP использует модель, предполагающую применение одной базы данных на каждого клиента, максимизируя эффективность с помощью эластичных пулов SQL,
использует каталог клиента для подготовки управления и подключения,
мониторинг и оповещение интегрированных приложений, пулов и баз данных (OMS),
управление схемами и справочными данными между клиентами (задания обработки эластичных баз данных),
запросы между клиентами, операционную аналитику (запросы к эластичным БД),
использует географически распределенные данные для расширения области взаимодействия,
обеспечивает непрерывность бизнес-процессов, аварийное восстановление с одним клиентом в масштабе (геовосстановление, георепликация, автоматическое аварийное восстановление), самостоятельное управление клиента через API управления для восстановления от ошибок, вызванных пользователем.

Основное приложение Wingtip использует пул с тремя примерами клиентов и базу данных каталога.

![Архитектура WTP](media/sql-database-wtp-overview/wtp-architecture.png)


## <a name="sql-database-wtp-saas-tutorials"></a>Руководства по SaaS WTP базы данных SQL

В следующих руководствах рассматривается первоначальное развертывание [примера приложения SaaS Wingtip Tickets Platform](sql-database-saas-tutorial.md):

| Область | Описание | Расположение скрипта |
|:--|:--|:--|
|[Подготовка новых клиентов и их регистрация в каталоге](sql-database-saas-tutorial-provision-and-catalog.md)| Подготовка новых клиентов и их регистрация в каталоге | [Скрипты на GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Provision%20and%20Catalog) |
|[Мониторинг производительности примера приложения SaaS WTP](sql-database-saas-tutorial-performance-monitoring.md)| Мониторинг производительности базы данных и пулов и управление ею | [Скрипты на GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management) |
|[Restore a single tenant database](sql-database-saas-tutorial-restore-single-tenant.md) (Восстановление базы данных отдельного клиента)| Восстановление базы данных клиента | [Скрипты на GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Business%20Continuity%20and%20Disaster%20Recovery/RestoreTenant) |
|[Управление схемой для нескольких клиентов в приложении SaaS WTP](sql-database-saas-tutorial-schema-management.md)| Выполнение запросов по всем клиентам  | [Скрипты на GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Schema%20Management) |
|[Run ad-hoc analytics queries across all WTP tenants](sql-database-saas-tutorial-adhoc-analytics.md) (Выполнение запросов аналитики ad-hoc по всем клиентам WTP) | Создание базы данных аналитики ad-hoc и выполнение запросов по всем клиентам  | [Скрипты на GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Adhoc%20Analytics) |
|[Установка и использование Log Analytics (OMS) с примером приложения SaaS WTP](sql-database-saas-tutorial-log-analytics.md) | Настройка и просмотр Log Analytics | [Скрипты на GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management/LogAnalytics) |
|[Run distributed queries across multiple Azure SQL databases](sql-database-saas-tutorial-tenant-analytics.md) (Выполнение распределенных запросов по нескольким клиентам баз данных SQL Azure) | Настройка и выполнение запросов аналитики клиентов | [Скрипты на GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Tenant%20Analytics) |

## <a name="get-the-wingtip-application-scripts"></a>Получение скриптов приложения Wingtip

Скрипты Wingtip Tickets и исходный код приложения доступны в репозитории GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Файлы скриптов находятся в папке [Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Скачайте папку **Learning Modules** на локальный компьютер, сохраняя ее структуру.

## <a name="working-with-the-wtp-powershell-scripts"></a>Работа со скриптами PowerShell WTP

Преимущество работы с приложением WTP заключается в том, что вы можете начать работу с предоставленными скриптами и изучить реализацию разных шаблонов SaaS.

Чтобы просмотреть предоставленные скрипты и модули, а также упростить работу с ними, используйте [интегрированную среду сценариев Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Так как большинство скриптов с префиксом *Demo-* содержат переменные, которые можно изменить перед выполнением, использование интегрированной среды сценариев PowerShell упрощает работу с этими скриптами.

Для каждого развертывания приложения WTP существует файл **UserConfig.psm1**, содержащий два параметра для определения значений группы ресурсов и имен пользователей, заданных во время развертывания. Завершив развертывание, измените параметры _ResourceGroupName_ и _Name_ модуля **UserConfig.psm1**. Эти значения используются для успешного выполнения других скриптов, поэтому мы рекомендуем задать их после завершения развертывания.



### <a name="execute-scripts-by-pressing-f5"></a>Выполнение скрипта нажатием клавиши F5

Некоторые скрипты используют *$PSScriptRoot*, чтобы разрешить перемещение по папкам. Эта переменная обрабатывается только при выполнении скрипта по нажатию клавиши **F5**.  Если выделить несколько скриптов и запустить их (**F8**), может произойти ошибка, поэтому нажмите клавишу **F5** при выполнении скриптов WTP.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Пошаговое выполнение скриптов и изучение их реализации

Важнее всего при изучении скриптов поэтапно выполнять их, чтобы узнать, что они делают. Ознакомьтесь с скриптами первого уровня _Demo-_ с понятным детализированным рабочим процессом, в котором продемонстрированы необходимые для выполнения задачи шаги. Подробно изучите отдельные вызовы, чтобы ознакомиться с подробностями реализации различных шаблонов SaaS.

Советы по [работе со скриптами PowerShell и их отладкой](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise):

* Откройте и настройте демонстрационные версии скриптов в интегрированной среде сценариев PowerShell.
* Выполните скрипт или продолжайте работу, нажав клавишу **F5**. Мы не рекомендуем использовать клавишу **F8**, так как при этом переменная *$PSScriptRoot* не обрабатывается во время выполнения нескольких скриптов.
* Поместите точки останова, щелкнув или выбрав линию и нажав клавишу **F9**.
* Чтобы обойти функцию или вызов скрипта, нажмите клавишу **F10**.
* Чтобы перейти к функции или вызову скрипта, нажмите клавишу **F11**.
* Чтобы выйти из текущей функции или скрипта, нажмите клавиши **SHIFT+F11**.




## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Просмотр схемы базы данных и выполнение SQL-запросов с помощью SSMS

Используйте [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) для подключения и просмотра серверов и баз данных WTP.

Для примера приложения WTP изначально доступно два сервера баз данных SQL, к которым оно может подключиться: *tenants1* и *catalog*:


1. Откройте среду *SSMS* и подключитесь к серверу *tenants1-&lt;пользователь&gt;.database.windows.net*.
2. Щелкните **Подключить** > **Компонент ядра СУБД…**:

   ![сервер каталога](media/sql-database-wtp-overview/connect.png)

1. Используйте такие демонстрационные учетные данные: имя пользователя — *developer*, пароль — *P@ssword1*.

   ![connection;](media\sql-database-wtp-overview\tenants1-connect.png)

1. Повторите шаги 2-3 и подключитесь к серверу *catalog-&lt;пользователь&gt;.database.windows.net*.

После успешного подключения вы увидите оба сервера. Число баз данных может отличаться в зависимости от числа подготовленных клиентов:

![обозреватель объектов](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Дальнейшие действия

[Развертывание и изучение приложения SaaS, использующего базу данных SQL Azure](sql-database-saas-tutorial.md)
