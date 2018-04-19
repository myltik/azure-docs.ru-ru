---
title: Общие сведения о функции управления окнами Azure Stream Analytics
description: В этой статье описаны три функции управления окнами ("переворачивающееся", "прыгающее" и "скользящее" окно), которые используются в заданиях Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: c6f5dbe49cb60e3c7b2bc6562acf2d7fd79096ec
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="introduction-to-stream-analytics-window-functions"></a>Введение в функции окна Stream Analytics
При использовании потоковой передачи во многих случаях необходимо выполнять операции только с теми данными, которые содержатся во временных окнах. Ключевой возможностью Azure Stream Analytics является встроенная поддержка функций управления окнами, которая существенно повышает производительность разработчиков при создании сложных задач обработки потоков. Stream Analytics позволяет разработчикам использовать [**"переворачивающиеся"**](https://msdn.microsoft.com/library/dn835055.aspx), [**"прыгающие"**](https://msdn.microsoft.com/library/dn835041.aspx) и [**"скользящие"**](https://msdn.microsoft.com/library/dn835051.aspx) окна для временных операций при потоковой передаче данных. Следует отметить, что все операции с [окном](https://msdn.microsoft.com/library/dn835019.aspx) выводят результаты в **конце** этого окна. Результатом для окна будет единичное событие, полученное на основе выбранной статистической функции. Метка времени для этого события соответствует времени завершения окна, и все функции окна выполняются с фиксированной длительностью. И последнее важное замечание: все функции окна следует использовать в предложении [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx).

![Концепции функций окна в Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>"Переворачивающееся" окно
Функции "переворачивающегося" окна используются для разделения потока данных на отдельные сегменты времени, для которых и выполняются эти функции, как показано в примере ниже. "Переворачивающееся" окно характеризуется тем, что такие окна повторяются и не перекрываются, а любое событие может принадлежать только к одному "переворачивающемуся" окну.

![Введение в функции "переворачивающегося" окна в Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>"Прыгающее" окно
Функции "прыгающего" окна смещаются вперед на фиксированный отрезок времени. Эта концепция аналогична переворачивающимся окнам с тем исключением, что "прыгающие" окна могут пересекаться, то есть события могут принадлежать сразу к нескольким окнам из набора "прыгающих" окон. Если для "прыгающего" окна указать длину прыжка, точно совпадающую с размером окна, оно будет полностью идентично "переворачивающемуся" окну. 

![Введение в функции "прыгающего" окна в Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>"Скользящее" окно
Функции "скользящего" окна, в отличие от "переворачивающихся" или "прыгающих" окон, создают выходные данные **только** при наступлении определенного события. Каждое такое окно имеет по меньшей мере одно событие, и это окно непрерывно перемещается вперед на период € (эпсилон). Как и случае с "прыгающими" окнами, каждое событие может принадлежать к нескольким "скользящим" окнам.

![Введение в функции "скользящего" окна в Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Справочные сведения по функциям окна
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дополнительная информация
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

