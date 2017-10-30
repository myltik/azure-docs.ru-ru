---
title: "Добавление эталонного набора данных в среду Azure Time Series Insights | Документация Майкрософт"
description: "Это руководство содержит сведения о добавлении эталонного набора данных в среду Time Series Insights."
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: venkatja
ms.openlocfilehash: b94ca172dba71b407ee5e9a40c283a97602efd17
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>Создание эталонного набора данных для среды Time Series Insights с помощью портала Ibiza

Эталонный набор данных — это коллекция элементов, дополненных событиями из вашего источника события. Обработчик входящего трафика Time Series Insights соединяет событие из источника события с элементом в эталонном наборе данных. Это дополненное событие становится доступным для запроса. Это соединение основано на ключах, определенных в эталонном наборе данных.

## <a name="steps-to-add-a-reference-data-set-to-your-environment"></a>Действия для добавления эталонного набора данных в среду

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Щелкните "Все ресурсы" в меню слева на портале Azure.
3. Выберите среду Time Series Insights.

    ![Создание эталонного набора данных Time Series Insights](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Выберите "Эталонные наборы данных" и щелкните "+ Добавить".

    ![Создание эталонного набора данных Time Series Insights — сведения](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

5. Укажите имя эталонного набора данных.
6. Укажите имя ключа и его тип. Эти имя и тип используются для выбора правильного свойства из события в источнике события. Например, если вы указали имя ключа DeviceId и тип String, обработчик входящего трафика Time Series Insights ищет свойство с именем DeviceId и типом String во входящих событиях. Вы можете указать несколько ключей для соединения с событием. Сопоставление имен ключей выполняется с учетом регистра.

     ![Создание эталонного набора данных Time Series Insights — сведения](media/add-reference-data-set/getstarted-create-reference-data-set-3.png)

7. Щелкните "Создать".

## <a name="next-steps"></a>Дальнейшие действия

* [Управление эталонными данными](time-series-insights-manage-reference-data-csharp.md) программными средствами.
* Полный справочник по API см. [здесь](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).
