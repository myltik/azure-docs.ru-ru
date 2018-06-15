---
title: Настройка мониторинга и оповещений для заданий Azure Stream Analytics
description: В этой статье объясняется, как с помощью портала Azure настроить мониторинг и оповещения для заданий Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/26/2017
ms.openlocfilehash: fb69f1b52e5b17dd6794c896969c29003a929c9b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412116"
---
# <a name="tutorial-set-up-alerts-for-azure-stream-analytics-jobs"></a>Руководство. Настройка оповещений для заданий Azure Stream Analytics
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
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дополнительная информация
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

