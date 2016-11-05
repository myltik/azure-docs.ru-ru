---
title: Обзор оповещений в Microsoft Azure | Microsoft Docs
description: Оповещения позволяют отслеживать метрики ресурсов Azure, события или журналы и получать уведомления при выполнении заданных условий.
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2016
ms.author: robb

---
# Обзор оповещений в Microsoft Azure
В этой статье объясняется, что такое оповещения, каковы их преимущества и как начать их использовать.

## Что такое оповещения?
Оповещения — это способ мониторинга метрик ресурсов Azure, событий или журналов с уведомлением при выполнении заданного условия.

Можно получать оповещения на основе:

* Значений метрик. Оповещение активируется, когда значение указанной метрики выходит за рамки заданного порогового значения. То есть сначала оно активируется, когда условие выполняется, а затем — когда условие перестает выполняться.
* Событий журнала действий. Оповещение может активироваться при каждом событии или только тогда, когда выполняется определенное число событий.

## Оповещения в различных службах Azure
Оповещения доступны в разных службах.

* **Application Insights** позволяет использовать оповещения для веб-тестов и метрик. Ознакомьтесь с разделами [Настройка оповещений в Application Insights](../application-insights/app-insights-alerts.md) и [Наблюдение за доступностью и скоростью реагирования веб-сайта](../application-insights/app-insights-monitor-web-app-availability.md).
* **OMS Log Analytics** позволяет направлять журналы диагностики в Log Analytics. OMS позволяет использовать оповещения на основе метрик, журнала и другие типы оповещений. Дополнительные сведения см. в разделе [Оповещения в Log Analytics](../log-analytics/log-analytics-alerts.md).
* **Azure Monitor** позволяет использовать оповещения на основе значений метрик и событий журнала действий. Azure Monitor включает в себя [REST API Azure Insights](https://msdn.microsoft.com/library/dn931943.aspx). Ознакомьтесь с разделом [Создание оповещений для служб Azure с помощью портала Azure](../monitoring-and-diagnostics/insights-alerts-portal.md).

## Действия оповещений
Для оповещения можно настроить действие, выполняемое при активации оповещения:

* отправка уведомлений по электронной почте администратору службы, соадминистраторам и (или) на дополнительные электронные адреса, указанные вами;
* вызов webhook, который позволяет запускать дополнительные автоматизированные действия.
  
  ![Описана работа с оповещениями.](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)

## Дальнейшие действия
Изучите правила генерации оповещений и ознакомьтесь с их настройкой с помощью:

* [Портал Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [интерфейса командной строки (CLI)](insights-alerts-command-line-interface.md);
* [REST API Azure Insights](https://msdn.microsoft.com/library/azure/dn931945.aspx).

<!---HONumber=AcomDC_0928_2016-->