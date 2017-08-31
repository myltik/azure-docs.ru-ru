---
title: "Как масштабировать среду Azure Time Series Insights | Документация Майкрософт"
description: "Это руководство содержит сведения о масштабировании среды Azure Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8f6c66ea2173c98179ec899d6626c2ab6f7ec4b6
ms.contentlocale: ru-ru
ms.lasthandoff: 05/01/2017

---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Как масштабировать среду Azure Time Series Insights

Это руководство содержит сведения о масштабировании среды Time Series Insights.

> [!NOTE]
> Увеличение масштаба в разных номерах SKU запрещено. Среду с номером SKU S1 нельзя преобразовать в среду S2.

## <a name="s1-sku-ingress-rates-and-capacities"></a>Скорость приема и емкость номера SKU S1

| Емкость номера SKU S1 | Скорость приема | Максимальная емкость хранилища
| --- | --- | --- |
| 1 | 1 ГБ (1 млн событий) | 30 ГБ в месяц (30 млн событий) |
| 10 | 10 ГБ (10 млн событий) | 300 ГБ в месяц (300 млн событий) |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Скорость приема и емкость номера SKU S2

| Емкость номера SKU S2 | Скорость приема | Максимальная емкость хранилища
| --- | --- | --- |
| 1 | 10 ГБ (10 млн событий) | 300 ГБ в месяц (300 млн событий) |
| 10 | 100 ГБ (100 млн событий) | 3 ТБ в месяц (3 млрд событий) |

Емкость масштабируется линейно, поэтому номер SKU S1 с емкостью 2 поддерживает скорость приема 2 ГБ (2 млн событий) в неделю и 60 ГБ (60 млн событий) в месяц.

## <a name="changing-the-capacity-of-your-environment"></a>Изменение емкости среды

1. На портале Azure выберите среду, емкость которой требуется изменить.
1. В разделе "Параметры" щелкните "Настроить".
1. Используйте ползунок показателя емкости для выбора емкости, соответствующей требованиям скорости приема и емкости хранилища.

## <a name="next-steps"></a>Дальнейшие действия

* Убедитесь, что новой емкости недостаточно для предотвращения регулирования. Дополнительные сведения см. в разделе *Регулирование среды* [здесь](time-series-insights-diagnose-and-solve-problems.md).
