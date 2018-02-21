---
title: "Как добавить эталонный набор данных в среду Аналитики временных рядов Azure | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как добавить эталонный набор данных в среду Аналитики временных рядов Azure. Соединив эталонные данные с исходными, можно получить расширенные значения."
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7cdcefbd0daec3b7bab59680afa1470624583c74
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Создание эталонного набора данных для среды Аналитики временных рядов с помощью портала Azure

Из этой статьи вы узнаете, как добавить эталонный набор данных в среду Аналитики временных рядов Azure. Соединив эталонные данные с исходными, можно получить расширенные значения.

Эталонный набор данных — это коллекция элементов, дополненных событиями из вашего источника события. Обработчик входящего трафика Time Series Insights соединяет событие из источника события с элементом в эталонном наборе данных. Это дополненное событие становится доступным для запроса. Это соединение основано на ключах, определенных в эталонном наборе данных.

## <a name="add-a-reference-data-set"></a>Добавление эталонного набора данных

1. Войдите на [портале Azure](https://portal.azure.com).

2. Найдите существующую среду "Аналитика временных рядов". На портале Azure щелкните **All resources** (Все ресурсы) в меню слева. Выберите среду Time Series Insights.

3. В разделе **Топология окружения** щелкните **Эталонные наборы данных**.

    ![Создание эталонного набора данных Time Series Insights](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Щелкните **+ Добавить**, чтобы добавить новый эталонный набор данных.

5. Укажите уникальное **имя эталонного набора данных**.

    ![Создание эталонного набора данных Time Series Insights — сведения](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

6. В пустом поле укажите **имя ключа** и выберите его **тип**. Эти имя и тип используются для выбора правильного свойства из событий в источнике событий с целью привязки к эталонным данным. 

   Например, если вы укажете имя ключа **DeviceId** и тип **String**, обработчик входящего трафика Аналитики временных рядов будет искать свойство с именем **DeviceId** и типом **String** в каждом входящем событии, чтобы привязаться к нему. Вы можете указать несколько ключей для соединения с событием. Сопоставление имен ключей выполняется с учетом регистра.

7. Нажмите кнопку **Создать**.

## <a name="next-steps"></a>Дополнительная информация
* [Управление эталонными данными](time-series-insights-manage-reference-data-csharp.md) программными средствами.
* Полный справочник по API см. [здесь](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).
