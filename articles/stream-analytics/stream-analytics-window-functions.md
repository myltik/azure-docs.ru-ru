---
title: Общие сведения о функции управления окнами Azure Stream Analytics
description: В этой статье описаны четыре функции управления окнами ("переворачивающееся", "прыгающее", "скользящее" и "сеансовое" окно), которые используются в заданиях Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 2650058e277bc0338c779655ce381be046fb120a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33893656"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Общие сведения о функциях управления окнами Stream Analytics
При использовании потоковой передачи в реальном времени необходимо выполнять операции с теми данными, которые содержатся во временных окнах. В Stream Analytics имеется встроенная поддержка функций управления окнами. Это позволяет разработчикам выполнять сложные задания по обработке потоков с минимальными усилиями.

Есть четыре типа временных окон: [**переворачивающиеся**](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics), [**прыгающие**](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics), [**скользящие**](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics)окна и окна [**сеансов**](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics).  Функции управления окнами используются в предложении [**GROUP BY**](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) в синтаксических конструкциях запросов в заданиях Stream Analytics.

Все операции [управления окнами](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics) выводят результаты в **конце** окна. Результатом для окна будет единичное событие, полученное на основе выбранной статистической функции. Метка времени для выходного события соответствует времени завершения окна, и все функции управления окнами выполняются с фиксированной длительностью. 

![Концепции функций управления окнами в Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>"Переворачивающееся" окно
Функции "переворачивающегося" окна используются для разделения потока данных на отдельные сегменты времени, для которых и выполняются эти функции, как показано в примере ниже. "Переворачивающееся" окно характеризуется тем, что такие окна повторяются и не перекрываются, а любое событие может принадлежать только к одному "переворачивающемуся" окну.

!["Переворачивающееся" окно Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>"Прыгающее" окно
Функции "прыгающего" окна смещаются вперед на фиксированный отрезок времени. Эта концепция аналогична переворачивающимся окнам с тем исключением, что "прыгающие" окна могут пересекаться, то есть события могут принадлежать сразу к нескольким окнам из набора "прыгающих" окон. Если для "прыгающего" окна указать длину прыжка, точно совпадающую с размером окна, оно будет полностью идентично "переворачивающемуся" окну. 

!["Прыгающее" окно Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>"Скользящее" окно
Функции "скользящего" окна, в отличие от "переворачивающихся" или "прыгающих" окон, создают выходные данные **только** при наступлении определенного события. Каждое такое окно имеет по меньшей мере одно событие, и это окно непрерывно перемещается вперед на период € (эпсилон). Как и в случае с "прыгающими" окнами, каждое событие может принадлежать к нескольким "скользящим" окнам.

!["Скользящее" окно Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window-preview"></a>"Сеансовое" окно (предварительная версия)
Функции "сеансового" окна группируют события, поступающие одновременно, отфильтровывая периоды времени, в течение которых данные отсутствовали. У такого окна три основных параметра: время ожидания, максимальная длительность и ключ секционирования (необязательно).

!["Сеансовое" окно Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

"Сеансовое" окно начинается, когда происходит первое событие. Если в течение указанного времени ожидания после последнего полученного события происходит еще одно событие, то окно расширяется для включения этого нового события. В противном случае, если в течение времени ожидания события отсутствуют, то по истечении этого времени окно закрывается.

Если события непрерывно происходят в течение указанного времени ожидания, то "сеансовое" окно будет расширяться, пока не будет достигнута максимальная длительность. Интервалы проверки максимальной продолжительности задаются равными указанной максимальной длительности. Например, если максимальная длительность равна 10, то проверка превышения максимальной длительности окна будет выполняться при t = 0, 10, 20, 30 и т. д.

Если указан ключ секции, то события группируются по ключу, и "сеансовое" окно применяется отдельно к каждой группе. Такое секционирование удобно в случаях, когда требуются разные "сеансовые" окна для различных пользователей или устройств.


## <a name="next-steps"></a>Дополнительная информация
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

