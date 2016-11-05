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
# <a name="overview-of-alerts-in-microsoft-azure"></a>Обзор оповещений в Microsoft Azure
В этой статье объясняется, что такое оповещения, каковы их преимущества и как начать их использовать.  

## <a name="what-are-alerts?"></a>Что такое оповещения?
Оповещения — это способ мониторинга метрик ресурсов Azure, событий или журналов с уведомлением при выполнении заданного условия.

Можно получать оповещения на основе:

* **Значений метрик**. Оповещение активируется, когда значение указанной метрики выходит за рамки заданного порогового значения. То есть сначала оно активируется, когда условие выполняется, а затем — когда условие перестает выполняться.
* **Событий журнала действий**. Оповещение может активироваться при каждом событии или только тогда, когда выполняется определенное число событий.

## <a name="alerts-in-different-azure-services"></a>Оповещения в различных службах Azure
Оповещения доступны в разных службах, включая следующие.

* **Application Insights**: позволяет использовать оповещения для веб-тестов и метрик. Ознакомьтесь с разделами [Настройка оповещений в Application Insights](../application-insights/app-insights-alerts.md) и [Наблюдение за доступностью и скоростью реагирования веб-сайта](../application-insights/app-insights-monitor-web-app-availability.md).
* **Log Analytics (Operations Management Suite)**: включает маршрутизацию журналов диагностики в Log Analytics. Operations Management Suite поддерживает оповещения на основе метрик, журналов и другие типы оповещений. Дополнительные сведения см. в разделе [Оповещения в Log Analytics](../log-analytics/log-analytics-alerts.md).   
* **Azure Monitor**: позволяет использовать оповещения на основе как значений метрик, так и событий журнала действий. Azure Monitor включает в себя [REST API Azure Insights](https://msdn.microsoft.com/library/dn931943.aspx).  Ознакомьтесь со статьей [Создание оповещений для служб Azure с помощью портала Azure](insights-alerts-portal.md).

## <a name="alert-actions"></a>Действия оповещений
Для оповещения можно настроить следующие действия:

* отправка уведомлений по электронной почте администратору службы, соадминистраторам или на дополнительные электронные адреса, указанные вами;
* вызов webhook, который позволяет запускать дополнительные автоматизированные действия.
  
  ![Работа с оповещениями](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)

## <a name="next-steps"></a>Дальнейшие действия
Изучите правила создания оповещений и ознакомьтесь с их настройкой с помощью следующих средств:

* [Портал Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [интерфейса командной строки (CLI)](../azure-portal/insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

<!--HONumber=Oct16_HO2-->


