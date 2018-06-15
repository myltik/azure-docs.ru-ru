---
title: Live Metrics Stream с пользовательскими метриками и диагностика в Azure Application Insights | Документы Майкрософт
description: Мониторинг веб-приложения в реальном времени с помощью пользовательских метрик и диагностика проблем с помощью динамического веб-канала сбоев, трассировок и событий.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: mbullwin; Soubhagya.Dash
ms.openlocfilehash: 352fff53d9e35ddd8d8e0c107e969357d9c766b3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599241"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: мониторинг и диагностика с задержкой в 1 секунду

Держите руку на пульсе работы веб-приложения с помощью Live Metrics Stream в [Application Insights](app-insights-overview.md). Выбирайте и фильтруйте метрики и счетчики производительности для отслеживания в режиме реального времени, не нарушая работу служб. Проверяйте трассировки стека на основе образцов неудавшихся запросов и исключений. Вместе с [Profiler](app-insights-profiler.md), [отладчиком моментальных снимков](app-insights-snapshot-debugger.md) и [тестированием производительности](app-insights-monitor-web-app-availability.md#performance-tests) Live Metrics Stream предоставляет эффективное средство диагностики веб-сайта, не вмешивающееся в его работу.

С помощью Live Metrics Stream можно выполнять следующие действия:

* Проверять выпущенное исправление, наблюдая за производительностью и числом сбоев.
* Наблюдать за результатом применения тестовых нагрузок и диагностировать проблемы в режиме реального времени. 
* Сосредотачиваться на определенных тестовых сеансах или отфильтровывать известные проблемы, выбирая и фильтруя метрики, подлежащие отслеживанию.
* Получать трассировки исключений по мере того, как они возникают.
* Экспериментировать с фильтрами, чтобы найти наиболее важные ключевые показатели эффективности.
* Отслеживать любые счетчики производительности Windows в режиме реального времени.
* Легко определить сервер, на котором возникают проблемы, и с помощью фильтра получить все КПЭ и динамические веб-каналы, относящиеся только к этому серверу.

[![Видео, посвященное Live Metrics Stream](./media/app-insights-live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

## <a name="get-started"></a>Начало работы

1. Если вы еще не [установили Application Insights](app-insights-asp-net.md) в своем веб-приложении ASP.NET или [приложении Windows Server](app-insights-windows-services.md), сделайте это сейчас. 
2. **Выполните обновление до последней версии** пакета Application Insights. В Visual Studio щелкните проект правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**. Откройте вкладку **Обновления**, установите флажок **Включить предварительные выпуски** и выберите все пакеты Microsoft.ApplicationInsights.*.

    Разверните приложение заново.

3. На [портале Azure](https://portal.azure.com) откройте ресурс Application Insights для своего приложения, а затем откройте Live Stream.

4. [Защитите канал управления](#secure-the-control-channel), если в фильтрах могут использоваться конфиденциальные данные, например имена клиентов.


![В колонке обзора щелкните Live Stream (Динамический поток).](./media/app-insights-live-stream/live-stream-2.png)

### <a name="no-data-check-your-server-firewall"></a>Данные отсутствуют? Проверьте брандмауэр сервера

Убедитесь в том, что [исходящие порты для Live Metrics Stream](app-insights-ip-addresses.md#outgoing-ports) открыты в брандмауэре ваших серверов. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Чем Live Metrics Stream отличается от обозревателя метрик и службы аналитики?

| |Live Stream | Обозреватель метрик и служба аналитики |
|---|---|---|
|Задержка|Данные отображаются в течение одной секунды|Агрегирование выполняется в течение нескольких минут|
|Нет сохранения|Данные сохраняются, только пока они отображаются на диаграмме, а затем удаляются.|[Данные сохраняются 90 дней](app-insights-data-retention-privacy.md#how-long-is-the-data-kept)|
|По запросу|Данные передаются, пока открыта служба Live Metrics|Данные отправляются, когда пакет SDK установлен и включен|
|Free|Плата за данные Live Stream не взимается|Действуют [расценки](app-insights-pricing.md)
|Выборка|Передаются все выбранные метрики и счетчики. Производится выборка сбоев и трассировок стека. TelemetryProcessors не применяются.|Может производиться [выборка](app-insights-api-filtering-sampling.md) событий.|
|Канал управления|В пакет SDK отправляются управляющие сигналы фильтрации. Рекомендуется [защитить этот канал](#secure-channel).|Взаимодействие является односторонним (в сторону портала)|


## <a name="select-and-filter-your-metrics"></a>Выбор и фильтрация метрик

(Доступно для классических приложений ASP.NET с последней версией пакета SDK.)

Можно отслеживать пользовательский КПЭ в реальном времени, применяя произвольные фильтры к любым данным телеметрии Application Insights на портале. Щелкните элемент управления фильтром, появляющийся при наведении указателя мыши на любую диаграмму. На диаграмме ниже показан пользовательский КПЭ числа запросов с фильтрами по для атрибутам URL-адреса и длительности. Проверьте результат фильтрации в разделе "Просмотр потока", в котором отображается динамический веб-канал телеметрии, соответствующей критериям, которые можно указать в любой момент времени. 

![Пользовательский КПЭ запросов](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Вы можете отслеживать не только количество. Параметры зависят от типа потока. Он может передавать любые данные телеметрии Application Insights: запросы, зависимости, исключения, трассировки, события или метрики. Поток может передавать и ваше [пользовательское измерение](app-insights-api-custom-events-metrics.md#properties).

![Параметры значения](./media/app-insights-live-stream/live-stream-valueoptions.png)

Помимо телеметрии Application Insights, можно также отслеживать любой счетчик производительности Windows, выбрав его в параметрах потока и указав имя счетчика производительности.

Динамические метрики объединяются дважды: локально на каждом сервере, а затем на всех серверах. Можно изменить режим по умолчанию для обоих случаев, выбрав другие параметры в соответствующих раскрывающихся списках.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Пример данных телеметрии: пользовательские события динамической диагностики
По умолчанию динамический веб-канал событий отображает примеры завершившихся сбоем запросов и вызовов зависимостей, исключений, событий и трассировок. Щелкните значок фильтра, чтобы просмотреть действующие критерии в любой момент времени. 

![Динамический веб-канал по умолчанию](./media/app-insights-live-stream/live-stream-eventsdefault.png)

Как и для метрик, можно указать любые произвольные критерии для любого типа данных телеметрии Application Insights. В этом примере мы выбираем конкретные сбои запросов, трассировки и события. Мы также выбираем все исключения и сбои зависимостей.

![Пользовательский динамический веб-канал](./media/app-insights-live-stream/live-stream-events.png)

Примечание. В настоящее время для критериев на основе сообщений об исключениях используйте сообщение о внешнем исключении. В предыдущем примере, чтобы отфильтровать неопасные исключения с сообщением о внутреннем исключении (после разделителя "<--") "Клиент отключен", использовался критерий "Message not-contains "Error reading request content"" (Сообщение не содержит "Ошибка при чтении содержимого запроса").

Просмотрите сведения об элементе динамического веб-канала, щелкнув его. Можно приостановить веб-канал, щелкнув **Приостановить**, прокрутив вниз или щелкнув элемент. Работа динамического веб-канала возобновится, когда вы прокрутите его обратно до начала или щелкните счетчик элементов, собранных во время приостановки.

![Выборка динамических ошибок](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Фильтрация по экземпляру сервера

Если требуется отслеживать определенный экземпляр роли сервера, можно применить фильтрацию по серверу.

![Выборка динамических ошибок](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Требования к пакетам SDK
Пользовательские метрики и события Live Metrics Stream доступны при использовании версии 2.4.0-beta2 или более новой версии [пакета SDK для Application Insights для веб-приложений](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Не забудьте выбрать параметр "Включить предварительные выпуски" в диспетчере пакетов NuGet.

## <a name="secure-the-control-channel"></a>Защита канала управления
Указываемые вами пользовательские критерии фильтра передаются в компонент Live Metrics в пакете SDK для Application Insights. Фильтры могут содержать конфиденциальные сведения, например идентификаторы клиентов. Помимо ключа инструментирования, канал для их передачи можно защитить секретным ключом API.
### <a name="create-an-api-key"></a>Создание ключа API

![Создание ключа API](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Добавление ключа API в конфигурацию

### <a name="classic-aspnet"></a>Классический ASP.NET

В файле applicationinsights.config добавьте AuthenticationApiKey в QuickPulseTelemetryModule.
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Или задайте его в коде для QuickPulseTelemetryModule.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (требует пакет SDK для ASP.NET Core для Application Insights версии 2.3.0-beta или выше)

Измените файл startup.cs следующим образом.

Сначала добавьте приведенный ниже код.

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Затем в методе ConfigureServices добавьте:

``` C#
services.ConfigureTelemetryModule<QuickPulseTelemetryModule>( module => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```


Однако, если все подключенные серверы опознаны и надежны, можно использовать настраиваемые фильтры без аутентифицированного канала. Эта возможность доступна в течение шести месяцев. Это переопределение требуется после каждого создания сеанса или подключения нового сервера.

![Параметры аутентификации Live Metrics](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>Настоятельно рекомендуется установить аутентифицированный канал перед вводом в критерии фильтра потенциально конфиденциальной информации, например идентификатора клиента.
>

## <a name="generating-a-performance-test-load"></a>Создание нагрузки для тестирования производительности

Если необходимо отследить результат повышения нагрузки, используйте колонку "Тест производительности". В ней имитируются одновременные запросы от нескольких пользователей. Можно выполнить "ручные тесты" (проверку связи) для отдельного URL-адреса или [многоэтапный веб-тест производительности](app-insights-monitor-web-app-availability.md#multi-step-web-tests), который добавляется так же, как тест доступности.

> [!TIP]
> После создания теста производительности откройте его и колонку Live Stream в отдельных окнах. Вы сможете увидеть, когда начнется тест производительности, поставленный в очередь, одновременно наблюдая за динамическим потоком.
>


## <a name="troubleshooting"></a>Устранение неполадок

Данные отсутствуют? Если приложение находится в защищенной сети: Live Metrics Stream использует IP-адреса, отличающиеся IP-адресов другой телеметрии Application Insights. Убедитесь, что [эти IP-адреса](app-insights-ip-addresses.md) открыты в брандмауэре.



## <a name="next-steps"></a>Дополнительная информация
* [Отслеживание использования Application Insights.](app-insights-web-track-usage.md)
* [Использование диагностического поиска](app-insights-diagnostic-search.md)
* [Профилировщик](app-insights-profiler.md)
* [Отладчик моментальных снимков](app-insights-snapshot-debugger.md)
