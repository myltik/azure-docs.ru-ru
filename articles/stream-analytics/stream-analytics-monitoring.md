---
title: "Основные сведения о мониторинге заданий в Stream Analytics | Документация Майкрософт"
description: "Основные сведения о мониторинге заданий в Stream Analytics"
keywords: "монитор запросов"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 5f5cc00f-4a7b-491e-89e1-dbafea46d399
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: e9ea21be1fabb5846c4f3b7b05f3a93298830e57
ms.openlocfilehash: 5119d1d6c02b8e080a9e0905ae21a2bddefab03a


---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Основные сведения о мониторинге заданий Stream Analytics и порядок мониторинга запросов

## <a name="introduction-the-monitor-page"></a>Введение: страница мониторинга
На портале Azure отображаются ключевые метрики производительности, которые можно использовать для мониторинга и устранения неполадок производительности запросов и заданий. Для просмотра метрик перейдите к нужному заданию Stream Analytics и просмотрите раздел **Мониторинг** на странице "Обзор".  

![Мониторинг связи](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Откроется окно, как показано ниже:

![Панель мониторинга заданий](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Метрики, доступные в Stream Analytics
| Метрика                 | Определение                               |
| ---------------------- | ---------------------------------------- |
| Использование единиц потоковой передачи (%)       | Использование единиц потоковой передачи, назначенных заданию на вкладке «Масштаб» соответствующего задания. Как только этот индикатор достигнет 80 % и более существует высокая вероятность приостановки или отсрочки обработки события. |
| Входные события           | Объем данных, получаемых заданием Stream Analytics, измеряемый количеством событий. Эти данные можно использовать, чтобы проверить, отправляются ли события в источник входных данных. |
| Выходные события          | Объем данных, отправляемых заданием Stream Analytics в место назначения выходных данных, измеряемый количеством событий. |
| Неупорядоченные события    | Количество событий, полученных в неактуальное время, которые были удалены или получили откорректированную метку времени в соответствии с политикой упорядочения событий. Эта метрика может зависеть от настройки параметра «Интервал для событий, полученных в неправильном порядке». |
| Ошибки преобразования данных | Количество ошибок преобразования данных, возникших в задании Stream Analytics. |
| Ошибки среды выполнения         | Общее число ошибок, возникающих при выполнении задания Stream Analytics. |
| События позднего ввода      | Число событий, поздно поступающих из источника, которые либо были удалены, либо имеют измененные отметки времени (рассчитывается на основе конфигурации политики упорядочения событий для параметра «Интервал для событий, наступивших с задержкой»). |
| Запросы функций      | Количество вызовов функции машинного обучения Azure (при наличии). |
| Неудачные запросы функций | Количество неудачных вызовов функции машинного обучения Azure (при наличии). |
| События функций        | Количество событий, отправленных в функцию машинного обучения Azure (при наличии). |
| Байты входного события      | Объем данных, получаемых заданием Stream Analytics, измеряемый в байтах. Эти данные можно использовать, чтобы проверить, отправляются ли события в источник входных данных. |


## <a name="customizing-monitoring-in-the-azure-portal"></a>Настройка мониторинга на портале Azure
Вы можете изменить тип диаграммы, отображаемые метрики и временной диапазон на экране "Редактирование диаграммы". Дополнительные сведения см. в статье [Настройка мониторинга](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Диаграмма значений времени в мониторе запросов](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Jan17_HO1-->


