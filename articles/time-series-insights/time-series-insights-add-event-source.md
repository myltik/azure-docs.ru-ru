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
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 9dc394c0da34a8ee2796c6114e7f2f647c5345a1
ms.lasthandoff: 04/26/2017

---

# <a name="create-an-event-source-for-your-time-series-insights-environment-using-the-azure-portal"></a>Создание источника событий для среды Time Series Insights с помощью портала Azure

Источник событий Time Series Insights является производным от брокеров событий, таких как концентраторы событий Azure. Time Series Insights подключаются напрямую к источникам событий, принимая поток данных, при этом у пользователей нет необходимости писать код. Сейчас Time Series Insights поддерживает концентраторы событий Azure и Центра Интернета вещей Azure. В будущем будут добавлены дополнительные источники событий.

## <a name="steps-to-add-an-event-source-to-your-environment"></a>Процедура добавления источника событий в среду

1.    Войдите на [портал Azure](https://portal.azure.com).
2.    Щелкните "Все ресурсы" в меню слева на портале Azure.
3.    Выберите среду Time Series Insights.

  ![Создание источника событий Time Series Insights](media/add-event-source/getstarted-create-eventsource1.png)

4.    Выберите "Источники событий" и щелкните "+ Добавить".

  ![Создание источника событий Time Series Insights — сведения](media/add-event-source/getstarted-create-eventsource2.png)

5.    Укажите имя источника событий. Это имя связано со всеми событиями из источника событий. Оно доступно во время выполнения запроса.
6.    Выберите концентратор событий из списка ресурсов концентратора событий в текущей подписке или выберите параметр импорта "Указать настройки концентратора событий вручную", чтобы указать концентратор событий, расположенный в другой подписке. События должны быть опубликованы в формате JSON.
7.    Выберите политику с разрешением на чтение в концентраторе событий.
8.    Укажите концентратор событий или группу потребителей.

  > [!IMPORTANT]
  > Убедитесь, что эта группа потребителей не используется другой службой (например, заданием Stream Analytics или другой средой Time Series Insights). Если группа потребителей используется другими службами, это негативно скажется на операции чтения в этой среде и в других службах. Использование $Default в качестве группы потребителей может потенциально привести к ее повторному использованию другими читателями.

9.    Щелкните "Создать".

После создания источника событий Time Series Insights автоматически запустит передачу данных в среду.

## <a name="next-steps"></a>Дальнейшие действия

* [Отправьте события](time-series-insights-send-events.md) в источник событий.
* Просмотрите свою среду на [портале Time Series Insights](https://insights.timeseries.azure.com).

