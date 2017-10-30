---
title: "Создание среды Azure Time Series Insights | Документация Майкрософт"
description: "Это руководство содержит сведения о создании среды Time Series, ее подключении к источнику событий и способы анализа данных событий за несколько минут."
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
ms.date: 10/18/2017
ms.author: omravi
ms.openlocfilehash: d8a3f79630868c83cd9fde6ea0c414e334a58e22
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2017
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Создание среды Time Series Insights на портале Azure

Time Series Insights — это ресурс Azure с входящим трафиком и объемом хранилища. Клиенты подготавливают среды через портал Azure, используя требуемую емкость.

## <a name="steps-to-create-the-environment"></a>Процедура создания среды

Чтобы создать среду, сделайте следующее:

1.  Выполните вход на [портал Azure](https://portal.azure.com).
2.  Щелкните знак плюс ("+") в верхнем левом углу.
3.  В поле поиска выполните поиск по запросу Time Series Insights.

  ![Создание среды Time Series Insights](media/get-started/getstarted-create-environment1.png)

4.  Выберите Time Series Insights и щелкните "Создать".

  ![Создание группы ресурсов Time Series Insights](media/get-started/getstarted-create-environment2.png)

5.  Укажите имя среды, которое будет представлять ее в [обозревателе Time Series](https://insights.timeseries.azure.com).
6.  Выберите подписку. Выберите подписку, содержащую источник событий. Time Series Insights может автоматически определить ресурсы Центра Интернета вещей и концентратора событий Azure в одной подписке.
7.  Выберите или создайте группу ресурсов. Группы ресурсов — это набор совместно используемых ресурсов Azure.
8.  Выберите расположение для размещения. Чтобы не перемещать данные между центрами обработки данных, выберите расположение, содержащее источник событий.
9.  Выберите ценовую категорию.
10. Выберите емкость. Емкость среды можно изменить после ее создания.
11. Создайте среду. Вы также можете закрепить среду на панели мониторинга, чтобы к ней можно было быстро обратиться после каждого входа в систему.

  ![Закрепление Time Series Insights на панели мониторинга](media/get-started/getstarted-create-environment3.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Определите политики доступа к данным](time-series-insights-data-access.md), чтобы получить доступ к среде на [портале Time Series Insights](https://insights.timeseries.azure.com).
* [Создайте источник событий](time-series-insights-add-event-source.md).
* [Отправьте события](time-series-insights-send-events.md) в источник событий.
