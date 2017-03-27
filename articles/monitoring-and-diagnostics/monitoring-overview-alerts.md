---
title: "Обзор оповещений в Microsoft Azure и Azure Monitor | Документация Майкрософт"
description: "Оповещения позволяют отслеживать метрики ресурсов Azure, события или журналы и получать уведомления при выполнении заданных условий."
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: c9065c3346c924ea21060c9e7e5b297a3cb26941
ms.lasthandoff: 03/09/2017


---
# <a name="what-are-alerts-in-microsoft-azure"></a>Что такое оповещения в Microsoft Azure?
В этой статье объясняется, что такое оповещения, каковы их преимущества и как начать их использовать. Эта статья касается Azure Monitor, но содержит ссылки и на другие службы.  

Оповещения — это способ мониторинга метрик, событий или журналов ресурсов Azure с уведомлением при выполнении заданного условия.  

## <a name="alerts-in-different-azure-services"></a>Оповещения в различных службах Azure
Оповещения доступны в разных службах, включая следующие.

* **Application Insights**: позволяет использовать оповещения для веб-тестов и метрик. Ознакомьтесь с разделами [Настройка оповещений в Application Insights](../application-insights/app-insights-alerts.md) и [Наблюдение за доступностью и скоростью реагирования веб-сайта](../application-insights/app-insights-monitor-web-app-availability.md).
* **Log Analytics (Operations Management Suite)**: включает маршрутизацию журналов диагностики и действий в Log Analytics. Operations Management Suite поддерживает оповещения на основе метрик, журналов и другие типы оповещений. Дополнительные сведения см. в разделе [Оповещения в Log Analytics](../log-analytics/log-analytics-alerts.md).  
* **Azure Monitor**: позволяет использовать оповещения на основе как значений метрик, так и событий журнала действий. Вы можете управлять оповещениями с помощью [REST API Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).  Ознакомьтесь со статьей [Создание оповещений для служб Azure с помощью портала Azure](insights-alerts-portal.md).

## <a name="visual-summary"></a>Визуальное представление
На следующей схеме показаны оповещения и действия, которые с ними можно выполнять в Azure Monitor. Для служб, перечисленных ранее, могут быть доступны другие действия. Например, сейчас оповещения для журналов диагностики доступны только в Log Analytics.

![Работа с оповещениями](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="what-can-trigger-alerts-in-azure-monitor"></a>Что активирует оповещения в Azure Monitor

Можно получать оповещения на основе:

* **Значений метрик**. Оповещение активируется, когда значение указанной метрики выходит за рамки заданного порогового значения. То есть сначала оно активируется, когда условие выполняется, а затем — когда условие перестает выполняться. Обновляемый список доступных метрик, поддерживаемых Azure Monitor, см. в статье [Метрики, поддерживаемые Azure Monitor](monitoring-supported-metrics.md).
* **События журнала действий**. Это оповещение может активироваться при каждом событии или когда происходит определенное число событий.

## <a name="what-can-alerts-do"></a>Возможности оповещений
Для оповещения можно настроить следующие действия:

* отправка уведомлений по электронной почте администратору службы, соадминистраторам или на дополнительные электронные адреса, указанные вами;
* вызов webhook, который позволяет запускать дополнительные автоматизированные действия. Примеры включают вызов:
    - Runbook службы автоматизации Azure
    - функции Azure;
    - Приложение логики Azure
    - сторонней службы.


## <a name="next-steps"></a>Дальнейшие действия
Изучите правила создания оповещений и ознакомьтесь с их настройкой с помощью следующих средств:

* [Портал Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [интерфейса командной строки (CLI)](insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

