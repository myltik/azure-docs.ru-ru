---
title: Мониторинг использования и производительности классических приложений для Windows
description: Анализ использования и производительности классического приложения для Windows с помощью Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/16/2018
ms.author: mbullwin
ms.openlocfilehash: 5b325fd4326f2594a7386c65dea17a3da19abde8
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
---
# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Мониторинг использования и производительности в классических приложениях для Windows

Несмотря на то, что данные телеметрии можно отправлять в Application Insights из классического приложения, этот подход главным образом используется для отладки и проведения экспериментов.

## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Отправка данных телеметрии в Application Insights из приложения для Windows
1. На [портале Azure](https://portal.azure.com) [создайте ресурс Application Insights](app-insights-create-new-resource.md). Для параметра типа приложения выберите приложение ASP.NET.
2. Сделайте копию ключа инструментирования. Найдите ключ в раскрывающемся списке "Основные компоненты" нового ресурса, который вы только что создали. 
3. В Visual Studio измените пакеты NuGet вашего проекта приложения и добавьте Microsoft.ApplicationInsights.WindowsServer. (Выберите Microsoft.ApplicationInsights, если нужен чистый API без модулей сбора стандартной телеметрии.)
4. Задайте ключ инструментирования в коде.
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *ваш ключ* `";` 
   
    Можно также задать его в файле ApplicationInsights.config (если установлен один из пакетов стандартной телеметрии).
   
    `<InstrumentationKey>`*ваш ключ*`</InstrumentationKey>` 
   
    Если используется файл ApplicationInsights.config, убедитесь, что его свойства в обозревателе решений имеют следующие значения: **"Действие сборки = содержимое", "Копировать в выходной каталог = копировать"**.
5. [Используйте API](app-insights-api-custom-events-metrics.md) для отправки данных телеметрии.
6. Запустите приложение и понаблюдайте за телеметрией в ресурсе, созданном на портале Azure.

## <a name="telemetry"></a>Пример кода
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Дополнительная информация
* [Создание панели мониторинга](app-insights-dashboards.md)
* [Поиск по журналу диагностики](app-insights-diagnostic-search.md)
* [Изучение метрик](app-insights-metrics-explorer.md)
* [Написание запросов аналитики](app-insights-analytics.md)

