---
title: Аналитика поискового трафика для поиска Azure | Документация Майкрософт
description: Включите аналитику поискового трафика для поиска Azure, облачной поисковой службы Microsoft Azure, чтобы получить дополнительные сведения о пользователях и данных.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2017
ms.author: heidist
ms.openlocfilehash: 4b40e8c9f681b7489c0ab2ffe7b369cc869c73e2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777265"
---
# <a name="what-is-search-traffic-analytics"></a>Что такое аналитика поискового трафика?
Аналитика поискового трафика — это модель реализации цикла обратной связи для службы поиска. Эта модель описывает необходимые данные и метод их сбора с помощью Application Insights, лидирующего решения для мониторинга служб на нескольких платформах.

Аналитика поискового трафика позволяет обеспечить видимость службы поиска и получить сведения о пользователях и их поведении. Получив сведения о выборе пользователей, можно принимать решения о дальнейшем усовершенствовании возможностей поиска или выполнить откат при отсутствии нужных результатов.

В службе поиска Azure доступно решение телеметрии, интегрирующее Azure Application Insights и Power BI для обеспечения детализированного мониторинга и отслеживания. Так как взаимодействие со службой поиска Azure происходит только через API, разработчикам следует реализовать телеметрию с использованием поиска, следуя инструкциям на этой странице.

## <a name="identify-the-relevant-search-data"></a>Определение соответствующих данных поиска

Чтобы получать полезные метрики поиска, необходимо регистрировать некоторые пользовательские события в приложении поиска. Эти события обозначают интересующее пользователей содержимое, которое соответствует их потребностям.

Аналитика поискового трафика учитывает два типа событий:

1. События поиска, инициированные пользователями. Представляют интерес только поисковые запросы, инициируемые пользователем. Поисковые запросы, используемые для заполнения аспектов, дополнительного содержимого или каких-либо внутренних сведений, не являются важными и ведут к искажению результатов поиска.

2. Пользовательские события щелчка. Щелчки в этом документе обозначают выбор пользователем определенного результата поиска, возвращенного из поискового запроса. Щелчок обычно означает, что документ релевантный для конкретного поискового запроса.

Благодаря связи события щелчка и поиска с идентификатором корреляции вы можете анализировать поведения пользователей в приложении. Эти сведения о поиске невозможно получить с помощью только журналов поискового трафика.

## <a name="how-to-implement-search-traffic-analytics"></a>Как реализовать аналитику поискового трафика

События, указанные в предыдущем разделе, необходимо собрать из приложения поиска, так как с ним взаимодействует пользователь. Application Insights — это расширяемое решение мониторинга с гибкими параметрами инструментирования, доступное для нескольких платформ. Это решение позволяет использовать отчеты Power BI о поиске, созданные службой поиска Azure, чтобы упростить анализ данных.

На странице [портала](https://portal.azure.com) службы поиска Azure в колонке "Поиск аналитики трафика" содержится памятка по следующему шаблону телеметрии. Вы также можете выбрать или создать ресурс Application Insights и просмотреть необходимые данные — все в одном расположении.

![Инструкции по аналитике поискового трафика][1]

### <a name="1-select-an-application-insights-resource"></a>1. Выбор ресурса Application Insights

Вам необходимо выбрать или создать ресурс Application Insights (если у вас его нет). Вы можете использовать ресурс, уже находящийся в применении, для регистрации требуемых пользовательских событий.

При создании ресурса Application Insights все типы приложений допустимы для этого сценария. Выберите тот, который лучше всего соответствует используемой платформе.

Чтобы создать клиент телеметрии для приложения, требуется ключ инструментирования. Вы можете получить его на панели мониторинга портала Application Insights или на странице аналитики поискового трафика, выбрав нужный экземпляр.

### <a name="2-instrument-your-application"></a>2. Инструментирование приложения

На этом шаге выполняется инструментирование собственного приложения поиска с помощью ресурса Application Insights, созданного на предыдущем шаге. Этот процесс состоит из четырех этапов:

**I. Создание клиента телеметрии**. Это объект, который отправляет события в ресурс Application Insights.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

Для других языков и платформ см. полный [список](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**II. Запрос идентификатора поиска для корреляции**. Для корреляции поисковых запросов с щелчками необходим идентификатор корреляции, связывающий эти два различных события. Служба поиска Azure предоставляет идентификатор поиска, когда он запрашивается с заголовком:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<ServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**III. Регистрация событий поиска.**

Каждый отправляемый поисковой запрос необходимо регистрировать в качестве события поиска, используя следующую схему пользовательского события Application Insights:

**ServiceName**: (строка) имя службы поиска. **SearchId**: (GUID) уникальный идентификатор поискового запроса (отображается в ответе поиска). **IndexName**: (строка) индекс службы поиска для запроса. **QueryTerms**: (строка) условия поиска, введенные пользователем. **ResultCount**: (целое число) количество возвращенных документов (отображается в ответе поиска). **ScoringProfile**: (строка) имя используемого профиля оценки (при наличии).

> [!NOTE]
> Получите число запросов, создаваемых пользователем, добавив $count=true в поисковый запрос. Дополнительные сведения см. [здесь](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).
>

> [!NOTE]
> Помните, что необходимо регистрировать только поисковые запросы, создаваемые пользователями.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**IV. Регистрация событий щелчка.**

Каждый щелчок документа — это событие, которое необходимо зарегистрировать для анализа поиска. Используйте пользовательские события Application Insights для регистрации этих событий по следующей схеме:

**ServiceName**: (строка) имя службы поиска. **SearchId**: (GUID) уникальный идентификатор связанного поискового запроса. **DocId**: (строка) идентификатор документа. **Position**: (целое число) позиция документа на странице результатов поиска.

> [!NOTE]
> Позиция ссылается на количественный порядок в приложении. Вы можете задать этот номер для сравнения, при условии, что он всегда один и тот же.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

### <a name="3-analyze-with-power-bi-desktop"></a>3. Анализ с помощью Power BI Desktop

После инструментирования приложения и проверки правильности его подключения к Application Insights можно использовать стандартный шаблон, созданный службой поиска Azure для Power BI Desktop.
Этот шаблон содержит диаграммы и таблицы, которые помогут вам принимать более взвешенные решения для улучшения производительности и релевантности поиска.

Для создания экземпляра шаблона Power BI Desktop требуется три типа сведений об Application Insights. Эти данные можно найти на странице аналитики поискового трафика при выборе подходящего ресурса.

![Данные Application Insights в колонке аналитики поискового трафика][2]

Метрики, включенные в шаблон Power BI Desktop:

*   Кликабельность (CTR). Отношение количества выбора пользователями определенного документа к количеству общих поисковых запросов.
*   Поисковые запросы без щелчков. Условия основных запросов, для которых не выполнялись щелчки.
*   Документы с наибольшим количеством щелчков. Определяются по идентификатору за последние 24 часа, неделю и месяц.
*   Распространенные пары "условие —документ". Условия, в результате которых щелкают тот же документ, c упорядочиванием по щелчкам.
*   Время щелчка. Щелчки, упорядоченные по времени с момента выполнения поискового запроса.

![Шаблон Power BI для чтения из Application Insights][3]


## <a name="next-steps"></a>Дальнейшие действия
Выполните инструментирование приложения поиска для получения детальных и полезных сведений о службе поиска.

Дополнительные сведения об Application Insights см. [здесь](https://go.microsoft.com/fwlink/?linkid=842905). Перейдите на [страницу цен](https://azure.microsoft.com/pricing/details/application-insights/) Application Insights, чтобы узнать больше о разных уровнях служб.

Узнайте больше о создании удивительных отчетов. См. статью [Начало работы с Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/).

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
