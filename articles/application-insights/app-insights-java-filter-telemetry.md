---
title: "Фильтрация данных телеметрии Azure Application Insights в веб-приложении Java | Документация Майкрософт"
description: "Уменьшите трафик телеметрии с помощью фильтрации событий, которые не нужно отслеживать."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: f9e061c010667bc18ac54e6546cc25339e9c0e3e
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2017
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Фильтрация данных телеметрии в веб-приложении Java

Фильтры позволяют выбрать данные телеметрии, которые [веб-приложение Java отправляет в Application Insights](app-insights-java-get-started.md). Имеется несколько готовых фильтров, которые можно использовать. Можно также создать собственные пользовательские фильтры.

В готовых фильтры используется следующее:

* уровень серьезности трассировки;
* определенные URL-адреса, ключевые слова или коды ответов;
* быстрые ответы — запросы, на которые приложение отвечает быстро;
* имена определенных событий.

> [!NOTE]
> Фильтры искажают значения метрик приложения. Например, вы можете задать фильтр для отклонения небольших значений времени ответа, чтобы диагностировать медленные ответы. Однако необходимо иметь в виду, что в этом случае среднее время ответа, отображаемое Application Insights, будет медленнее, а количество запросов — меньше, чем на самом деле.
> Если это представляет собой проблему, то используйте [выборки](app-insights-sampling.md).

## <a name="setting-filters"></a>Задание фильтров

Добавьте в файл ApplicationInsights.xml раздел `TelemetryProcessors`, как в примере ниже.


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Просмотрите полный набор встроенных обработчиков](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Встроенные фильтры

### <a name="metric-telemetry-filter"></a>Фильтр телеметрии метрик

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` — разделенный запятыми список имен пользовательских метрик.


### <a name="page-view-telemetry-filter"></a>Фильтр телеметрии просмотров страницы

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` — длительность означает время, необходимое для загрузки страницы. Если он установлен, то страницы, которые загружаются быстрее, чем это значение, не учитываются.
* `NotNeededNames` — разделенный запятыми список имен страниц.
* `NotNeededUrls` — разделенный запятыми список фрагментов URL-адресов. Например, `"home"` отфильтровывает все страницы, которые содержат в URL-адресе слово "home".


### <a name="request-telemetry-filter"></a>Фильтр телеметрии запросов


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Фильтр искусственных источников

Отфильтровывает все данные телеметрии, имеющие значение в свойстве SyntheticSource. К ним относятся запросы от программ-роботов, программ-обходчиков и тестов доступности.

Фильтрация данных телеметрии для всех запросов от искусственных источников:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Фильтрация данных телеметрии для определенных запросов от искусственных источников:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` — разделенный запятыми список имен искусственных источников.

### <a name="telemetry-event-filter"></a>Фильтр телеметрии событий

Фильтрует пользовательские события (добавленные в журнал с помощью [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` — разделенный запятыми список имен событий.


### <a name="trace-telemetry-filter"></a>Фильтр телеметрии трассировок

Фильтрует трассировки журнала (добавленные в журнал с помощью [TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) или [сборщика платформа ведения журналов](app-insights-java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* Допустимые значения `FromSeverityLevel`:
 *  OFF — отфильтровывание ВСЕХ трассировок;
 *  TRACE — фильтрация отключена; соответствует уровню трассировки (TRACE);
 *  INFO — отфильтровывание уровня TRACE;
 *  WARN — отфильтровывание уровней TRACE и INFO;
 *  ERROR — отфильтровывание уровней WARN, INFO и TRACE;
 *  CRITICAL — отфильтровывание всех уровней, кроме уровня CRITICAL.


## <a name="custom-filters"></a>Настраиваемые фильтры

### <a name="1-code-your-filter"></a>1. Программирование фильтра

В коде создайте класс, реализующий `TelemetryProcessor`.

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2) Вызов фильтра в файле конфигурации

В файле ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

## <a name="troubleshooting"></a>Устранение неполадок

*Мой фильтр не работает.*

* Убедитесь, что для параметров указаны допустимые значения. Например, значения длительности должны быть целыми числами. Недопустимые значения приведут к тому, что фильтр будет проигнорирован. Если пользовательский фильтр породит исключение из конструктора или метода set, он будет проигнорирован.

## <a name="next-steps"></a>Дальнейшие действия

* Использование [выборки](app-insights-sampling.md) — альтернативный метод, который не искажает метрики.
