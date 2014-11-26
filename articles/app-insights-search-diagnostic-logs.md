<properties title="Search diagnostic logs with Application Insights" pageTitle="Search diagnostic logs" description="Search logs generated with Trace, NLog, or Log4Net." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Журналы диагностики поиска в Application Insights

Можно записывать и выполнять поиск в диагностических данных System.Diagnostics.Trace, NLog и Log4Net. Application Insights предоставляет эффективное и удобное средство для сбора и изучения занесенных в журнал событий, полученных из одного или нескольких источников. Эта функциональность дополняет средства мониторинга работоспособности приложений.

Отслеживаемое веб-приложение может быть размещено в локальной среде, на виртуальной машине или на веб-сайте Microsoft Azure.

1.  [Добавление адаптера ведения журнала][Добавление адаптера ведения журнала]

-   [Настройка сбора диагностики][Настройка сбора диагностики]
-   [Вставка инструкций журнала, сборка и развертывание][Вставка инструкций журнала, сборка и развертывание]
-   [Просмотр данных журнала][Просмотр данных журнала]
-   [Поиск в данных][Поиск в данных]
-   [Дальнейшие действия][Дальнейшие действия]

## <a name="add"></a>1. Добавление адаптера ведения журнала

1.  Если это еще не сделано, [добавьте Application Insights в свой проект][добавьте Application Insights в свой проект] В Visual Studio.

    Если добавить Application Insights после добавления журналов в проект, то адаптер ведения журналов уже будет установлен и настроен. Достаточно лишь [заново развернуть проект][Вставка инструкций журнала, сборка и развертывание] и [просмотреть данные][Просмотр данных журнала].

2.  В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите **Управление пакетами NuGet**.
3.  Выберите Online \> All, выберите **Include Prerelease** и выполните поиск по тексту «Microsoft.ApplicationInsights»

    ![Получите предварительную версию соответствующего адаптера][Получите предварительную версию соответствующего адаптера]

4.  Выберите предварительную версию соответствующего пакета — одного из следующих:

-   Microsoft.ApplicationInsights.TraceListener
-   Microsoft.ApplicationInsights.NLogTarget
-   Microsoft.ApplicationInsights.Log4NetAppender

## <a name="configure"></a>2. Настройка сбора диагностики

### Для System.Diagnostics.Trace

В Web.config вставьте следующий код в раздел `<configuration>`:

    <system.diagnostics>
     <trace autoflush="true" indentsize="0">
      <listeners>
       <add name="myAppInsightsListener"  
          type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, 
         Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
     </trace>
    </system.diagnostics> 

### Для NLog

В файле Nlog.config объедините следующие фрагменты с разделами `<extensions>`, `<targets>` и `<rules>`. При необходимости создайте эти разделы

    <extensions> 
     <add  assembly="Microsoft.ApplicationInsights.NLogTarget" /> 
    </extensions> 

    <targets>
     <target xsi:type="ApplicationInsightsTarget" name="aiTarget"/>
    </targets>

    <rules>
     <logger name="*" minlevel="Trace" writeTo="aiTarget"/>
    </rules>

### Для Log4Net

В файле Web.config объедините следующие фрагменты с разделами `<configsections>` и `<log4net>`:

    <configSections>
      <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
    </configSections>

    <log4net>
     <root>
      <level value="ALL" /> <appender-ref ref="aiAppender" />
     </root>
     <appender name="aiAppender" type="Microsoft.ApplicationInsights.Log4NetAppender.ApplicationInsightsAppender, Microsoft.ApplicationInsights.Log4NetAppender">
      <layout type="log4net.Layout.PatternLayout">
       <conversionPattern value="%date [%thread] %-5level %logger - %message%newline" />
      </layout>
     </appender>
    </log4net>

## <a name="deploy"></a>3. Вставка инструкций журнала, сборка и развертывание

Вставьте вызовы журналов событий с помощью предпочитаемой платформы ведения журнала. Например, при использовании Log4Net вызовы могут выглядеть так:

    log.Warn("Slow response - database01");

Занесенные в журнал события будут отправлены в Application Insights и в среде разработки, и в рабочей среде.

## <a name="view"></a>4. Просмотр данных журнала

В Application Insights откройте поиск по журналу диагностики.

![Откройте поиск по журналу диагностики][Откройте поиск по журналу диагностики]

Выберите любой журнал для просмотра содержащихся в нем данных.

![Откройте поиск по журналу диагностики][1]

Доступные поля зависят от платформы ведения журнала и от параметров, использованных в вызове.

Для фильтрации данных внутри элементов можно использовать простые строки (без подстановочных знаков).

## <a name="search"></a>5. Поиск в данных

Задайте диапазон времени и выполните поиск. Чем короче диапазон, тем быстрее поиск.

![Откройте поиск по журналу диагностики][2]

Обратите внимание, что поиск ведется по словам, а не по словосочетаниям. Слова — это последовательности букв и цифр с определенными знаками пунктуации, например, «.» и «\_». Например:

<table>
  <tr><th>Слово</th><th>НЕ совпадает</th><th>Но следующие слова совпадают:</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>New Delay</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

Вот доступные поисковые выражения:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><p>Пример запроса</p></th>
<th align="left"><p>Результат</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><span class="code">slow</span></p></td>
<td align="left"><p>Поиск всех элементов в диапазоне дат, поля которых включают слово «slow»</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">database??</span></p></td>
<td align="left"><p>Совпадения — database01, databaseAB, ...</p>
<p>? нельзя использовать в начале поискового запроса.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">database*</span></p></td>
<td align="left"><p>Совпадения — database, database01, databaseNNNN</p>
<p>* нельзя использовать в начале поискового запроса</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">яблоко AND банан</span></p></td>
<td align="left"><p>Находит события, содержащие оба этих слова. Используйте «AND» заглавными буквами, а не «and».</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">яблоко OR банан</span></p>
<p><span class="code">яблоко банан</span></p></td>
<td align="left"><p>Находит события, содержащие любое из этих слов. Используйте «OR» заглавными буквами, а не «or».</p>
<p>Краткая форма.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">яблоко NOT банан</span></p>
<p><span class="code">яблоко -банан</span></p></td>
<td align="left"><p>Находит события, содержащие только одно из этих слов, но не содержащие второе.</p>
<p>Краткая форма.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>ябл* AND банан NOT (виноград OR груша)</p>
<p><span class="code">ябл* AND банан -(виноград груша)</span></p></td>
<td align="left"><p>Логические операторы и скобки</p>
<p>Более краткая форма</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">message:slow</span></p>
<p><span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span></p>
<p><span class="code">properties.logEventInfo.level:Error</span></p></td>
<td align="left"><p>Совпадение с указанным полем. По умолчанию поиск ведется по всем полям. Чтобы посмотреть, какие поля доступны, выберите событие для просмотра его данных.</p></td>
</tr>
</tbody>
</table>

## <a name="add"></a>Дальнейшие действия

-   [Добавление Application Insights в проект][добавьте Application Insights в свой проект]
-   [Настройка тестов доступности и скорости реагирования][Настройка тестов доступности и скорости реагирования]
-   [Устранение неполадок][Устранение неполадок]

## Подробнее

-   [Application Insights][Application Insights]
-   [Добавление Application Insights в ваш проект][добавьте Application Insights в свой проект]
-   [Наблюдение за текущим состоянием веб-сервера][Наблюдение за текущим состоянием веб-сервера]
-   [Исследование метрик в Application Insights][Исследование метрик в Application Insights]
-   [Поиск журналов диагностики][Поиск журналов диагностики]
-   [Отслеживание доступности с помощью веб-тестов][Настройка тестов доступности и скорости реагирования]
-   [Отслеживание использования с помощью событий и метрик][Отслеживание использования с помощью событий и метрик]
-   [Вопросы и ответы, устранение неполадок][Устранение неполадок]

<!--Link references-->

  [Добавление адаптера ведения журнала]: #add
  [Настройка сбора диагностики]: #configure
  [Вставка инструкций журнала, сборка и развертывание]: #deploy
  [Просмотр данных журнала]: #view
  [Поиск в данных]: #search
  [Дальнейшие действия]: #next
  [добавьте Application Insights в свой проект]: ../app-insights-monitor-application-health-usage/
  [Получите предварительную версию соответствующего адаптера]: ./media/appinsights/appinsights-36nuget.png
  [Откройте поиск по журналу диагностики]: ./media/appinsights/appinsights-30openDiagnostics.png
  [1]: ./media/appinsights/appinsights-32detail.png
  [2]: ./media/appinsights/appinsights-31search.png
  [Настройка тестов доступности и скорости реагирования]: ../app-insights-monitor-web-app-availability/
  [Устранение неполадок]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Наблюдение за текущим состоянием веб-сервера]: ../app-insights-monitor-performance-live-website-now/
  [Исследование метрик в Application Insights]: ../app-insights-explore-metrics/
  [Поиск журналов диагностики]: ../app-insights-search-diagnostic-logs/
  [Отслеживание использования с помощью событий и метрик]: ../app-insights-track-usage-custom-events-metrics/
