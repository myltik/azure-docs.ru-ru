---
title: "Отслеживание работоспособности службы Azure с использованием журналов действий Azure Monitor | Документация Майкрософт"
description: "Информация о снижении производительности Azure или нарушении работы службы. "
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 30db9258-98e6-4855-abcd-f06c06277aa8
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d31ee12c783b845e0a8ff1791d21b2ef9013eee3


---
# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Отслеживание работоспособности службы Azure с помощью журналов активности Azure Monitor
Azure сообщает каждый раз, когда происходит прерывание работы службы или снижение производительности. Эти события можно просмотреть на портале Azure. Кроме того, можно использовать [REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) или [пакет SDK для .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) для программного доступа ко всем событиям.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>Просмотр журнала работоспособности службы для подписки
1. Войдите на [портал Azure](https://portal.azure.com/).
2. На **Домашней странице** вы увидите плитку которая называется **Работоспособность службы**. Щелкните ее.
   
    ![HOME](./media/insights-service-health/Insights_Home.png)
3. Просмотр списка всех регионов в Azure. Щелкните любой регион, чтобы открыть запрос журнала действий, отображающий инциденты службы, которые повлияли на любую из ваших подписок за последние 24 часа.
   
    ![Работоспособность службы подписки журнала действий](./media/insights-service-health/AzureActivityLogServiceHealth3.png)
4. Вы также можете просмотреть информацию об отдельных инцидентах, щелкнув событие в таблице.
5. Изменение **интервал времени**, чтобы увидеть информацию за больший отрезок времени.

## <a name="next-steps"></a>Дальнейшие действия
* [Отслеживайте доступность и скорость реагирования любой веб-страницы](../application-insights/app-insights-monitor-web-app-availability.md) с помощью Application Insights, так вы сможете узнать, что страница не работает.




<!--HONumber=Nov16_HO3-->


