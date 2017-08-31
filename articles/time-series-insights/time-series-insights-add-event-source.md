---
title: "Добавление источника событий в среду Azure Time Series Insights | Документация Майкрософт"
description: "Это руководство содержит сведения о подключении источника событий к среде Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: ffa2eaf3680e68ac14aabf49b6308caeb173fd43
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017

---

# <a name="create-an-event-source-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>Создание источника событий для среды Time Series Insights с помощью портала Ibiza

Источник событий Time Series Insights является производным от брокеров событий, таких как концентраторы событий Azure. Time Series Insights подключаются напрямую к источникам событий, принимая поток данных, при этом у пользователей нет необходимости писать код. Сейчас Time Series Insights поддерживает концентраторы событий Azure и Центр Интернета вещей. В дальнейшем будут добавлены дополнительные источники событий.

## <a name="steps-to-add-an-event-source-to-your-environment"></a>Процедура добавления источника событий в среду

1.  Войдите на [портал Ibiza](https://portal.azure.com).
2.  В меню слева на портале Ibiza щелкните "Все ресурсы".
3.  Выберите среду Time Series Insights.

  ![Создание источника событий Time Series Insights](media/add-event-source/getstarted-create-event-source-1.png)

4.  Выберите "Источники событий" и щелкните "+ Добавить".

  ![Создание источника событий Time Series Insights — сведения](media/add-event-source/getstarted-create-event-source-2.png)

5.  Укажите имя источника событий. Это имя связано со всеми событиями из источника событий. Оно доступно во время выполнения запроса.
6.  Выберите концентратор событий в списке ресурсов концентратора событий в текущей подписке. В противном случае выберите параметр импорта "Указать параметры концентратора событий вручную", чтобы выбрать концентратор событий в другой подписке. События должны быть опубликованы в формате JSON.
7.  Выберите политику с разрешением на чтение в концентраторе событий.
8.  Укажите концентратор событий или группу потребителей.

  > [!IMPORTANT]
  > Убедитесь, что эта группа потребителей не используется другой службой (например, заданием Stream Analytics или другой средой Time Series Insights). Если группа потребителей используется другими службами, это негативно скажется на операции чтения в этой среде и в других службах. Использование $Default в качестве группы потребителей может потенциально привести к ее повторному использованию другими читателями.

9.  Щелкните "Создать".

После создания источника событий Time Series Insights автоматически запустит передачу данных в среду.

## <a name="next-steps"></a>Дальнейшие действия

* [Отправьте события](time-series-insights-send-events.md) в источник событий.
* Просмотрите свою среду на [портале Time Series Insights](https://insights.timeseries.azure.com).

