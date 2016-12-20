---
title: "Настройка оповещений для запросов в Stream Analytics | Документация Майкрософт"
description: "Общая информация об оповещениях Stream Analytics"
keywords: "настройка оповещений"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/26/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c8463e68600edefecd0f3bdd19c13b13f676b82b


---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Настройка оповещений для заданий Azure Stream Analytics
## <a name="introduction-monitor-page"></a>Введение: страница мониторинга
Оповещения можно настроить на отображение в случае, если метрика достигнет указанного состояния.

Например: "Если число выходных событий за последние 15 минут < 100, отправлять уведомления на электронный адрес: abc@company.com".

Правила на основе метрик можно настраивать через портал либо [программным путем](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) , используя данные в журналах операций.

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>Настройка оповещений через классический портал Azure
Настроить оповещения на классическом портале Azure можно двумя способами:  

1. Вкладка **Монитор** задания Stream Analytics.  
2. Журнал операций в службах управления.  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>Настройка оповещений с помощью вкладки "Монитор" задания на портале
1. На вкладке "Монитор" выберите метрику, в нижней части панели мониторинга нажмите кнопку **Добавить правило** и настройте правила.  
   
   ![Панель мониторинга](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  
2. Определение имени и описания оповещения  
   
   ![Создание правила](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  
3. Введите пороговые значения, интервал оповещений и действия при получении оповещений.  
   
   ![Определение условий](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>Настройка оповещений с помощью журналов операций
1. Откройте вкладку **Оповещения** в службах управления на [классическом портале Azure](https://manage.windowsazure.com).  
2. Нажмите кнопку **Добавить правило**  
   
   ![Критерии](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  
3. Определите имя и описание оповещения. Выберите в поле "Тип службы" значение Stream Analytics, а в поле "Имя службы" — имя задания.  
   
   ![Определение оповещения](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>Настройка оповещений на портале Azure
На портале Azure перейдите к нужному заданию Stream Analytics и откройте раздел **Мониторинг** .  В появившейся колонке **Метрика** выберите команду **Добавить оповещение**.

  ![Настройка портала Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

Правилу оповещения можно дать имя и выбрать описание, которое будет отображаться в уведомлении по электронной почте.

Если вы выбрали раздел "Метрики", нужно задать условия и пороговое значение для метрики.

  ![Выбор метрики на портале Azure](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

Дополнительные сведения о настройке оповещений на портале Azure см. в статье [Получение уведомлений](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


