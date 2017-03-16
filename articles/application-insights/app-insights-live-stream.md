---
title: "Live Metrics Stream в Azure Application Insights | Документация Майкрософт"
description: "Выполняйте мониторинг производительности веб-приложения в режиме реального времени, чтобы отследить результаты выпуска или другие изменения."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fc6acf7b24bb017e4a93df1c15ed493a4442d6b5
ms.openlocfilehash: 917261e32845b53f2f2f3d12f60ca5fa87d441c7
ms.lasthandoff: 02/27/2017


---

# <a name="live-metrics-stream-instant-metrics-for-close-monitoring"></a>Динамический поток метрик: мгновенные метрики для подробного отслеживания
Live Metrics Stream показывает метрики [Application Insights](app-insights-overview.md) прямо в данный момент, почти в реальном времени, с задержкой в одну секунду. Такой мгновенный мониторинг очень полезен при выпуске новой сборки, когда необходимо убедиться, что все работает правильно, а также при изучении инцидентов в режиме реального времени.

В отличие от [обозревателя метрик](app-insights-metrics-explorer.md), Live Metrics Stream отображает фиксированный набор метрик. Данные сохраняются только на то время, пока они отображаются на диаграмме, а затем удаляются.

Данные Live Metrics Stream бесплатны: плата за них не взимается.

![Видео о службе Live Metrics Stream](./media/app-insights-live-stream/youtube.png) [видео о службе Live Metrics Stream](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

![В колонке обзора щелкните Live Stream (Динамический поток).](./media/app-insights-live-stream/live-stream.png)



## <a name="live-failures"></a>Динамические ошибки

Если регистрируются какие-либо ошибки или исключения, Live Stream выбирает их пример. Щелкните **Пауза**, чтобы удержать конкретный пример и выберите событие для отображения сведений о нем.

![Выборка динамических ошибок](./media/app-insights-live-stream/live-stream-failures.png)


Динамический поток метрик входит в состав последней версии [пакета SDK для Application Insights для веб-приложений](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/).

## <a name="filter-by-server-instance"></a>Фильтрация по экземпляру сервера

Если требуется отслеживать определенный экземпляр роли сервера, можно применить фильтрацию по серверу.

![Выборка динамических ошибок](./media/app-insights-live-stream/live-stream-filter.png)


## <a name="troubleshooting"></a>Устранение неполадок

Данные отсутствуют? В Live Metrics Stream используется не такой порт, как в телеметрии Application Insights. Убедитесь, что [эти порты](app-insights-ip-addresses.md) открыты в брандмауэре.



## <a name="next-steps"></a>Дальнейшие действия
* [Отслеживание использования Application Insights.](app-insights-overview-usage.md)
* [Использование диагностического поиска](app-insights-diagnostic-search.md)


