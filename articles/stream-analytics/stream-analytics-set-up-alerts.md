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
ms.date: 06/26/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: a0c9c771c273728cf35d9cd727e1570e2a9bff5a
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017


---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Настройка оповещений для заданий Azure Stream Analytics
## <a name="introduction-monitor-page"></a>Введение: страница мониторинга
Оповещения можно настроить на отображение в случае, если метрика достигнет указанного состояния. Например, оповещение для условия можно настроить следующим образом:

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

Правила на основе метрик можно настраивать через портал либо [программным путем](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) , используя данные в журналах операций.

## <a name="set-up-alerts-in-the-azure-portal"></a>Настройка оповещений на портале Azure
1. На портале Azure откройте задание Stream Analytics, для которого требуется создать оповещение. 

2. В колонке **Задание** щелкните раздел **Мониторинг**.  

3. В колонке **Метрика** выберите команду **Добавить оповещение**.

      ![Настройка портала Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Введите имя и описание.

5. Используйте селекторы для определения условий, при которых будут отправляться оповещения.

6. Предоставьте сведения о том, куда следует отправлять оповещения.

      ![Настройка оповещений для заданий Azure Streaming Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Дополнительные сведения о настройке оповещений на портале Azure см. в статье [Получение уведомлений](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


