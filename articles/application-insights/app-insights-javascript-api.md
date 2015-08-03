<properties 
	pageTitle="Application Insights: API пакета SDK для JavaScript" 
	description="Справочная документация" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="awills"/>
 

# Application Insights: API пакета SDK для JavaScript

Пакет SDK для JavaScript загружается на веб-страницу на этапе настройки [отслеживания веб-страницы](app-insights-javascript.md) в [Application Insights](https://azure.microsoft.com/services/application-insights/).

[Обзор API и примеры](app-insights-api-custom-events-metrics.md)

## Класс AppInsights

«Фасадная» часть пакета SDK, которая отправляет данные телеметрии в Application Insights.

На веб-странице, на которой настроено [отслеживание](app-insights-javascript.md), можно использовать экземпляр `appInsights`. Например:
    
    appInsights.trackPageView("page1");



### trackPageView

    trackPageView(name?: string, url?: string, properties?:{[string]:string}, measurements?: {[string]:number})

Запись сведений о том, что пользователю была показана страница или аналогичный контейнер.

 | | 
---|---|---
`name` | `? string` | Имя, используемое для идентификации страницы на портале. По умолчанию это заголовок документа.
`url` | `? string` | Относительный или абсолютный URL-адрес, идентифицирующий страницу или аналогичный элемент. По умолчанию используется расположение окна.
`properties` | `? {[string]:string}` | Дополнительные данные, используемые для фильтрации страниц и показателей на портале. По умолчанию значение не указывается.
`measurements` | `? {[string]:number}` | Метрики, которые связаны с этой страницей и отображаются в обозревателе метрик на портале. По умолчанию значение не указывается.


### trackEvent

    trackEvent(name: string, properties?: {[string]:string}, measurements?: {[string]:string})

Запись действия пользователя или другого события.

На портале вы можете выбрать события по имени и [отобразить диаграммы, которые подсчитывают эти события или показывают связанные с ними показатели](app-insights-metrics-explorer.md).

Вы также можете искать и [отображать отдельные события](app-insights-diagnostic-search.md).

 | | 
---|---|---
 `name` | `string` | Определяет событие. События с одинаковым именем подсчитываются. На их основе в [обозревателе метрик](app-insights-metrics-explorer.md) можно строить диаграммы.
`properties` | `? {[string]:string}` | Дополнительные данные, используемые для фильтрации страниц и показателей на портале. По умолчанию значение не указывается.
`measurements` | `? {[string]:number}` | Метрики, которые связаны с этой страницей и отображаются в обозревателе метрик на портале. По умолчанию значение не указывается.


### trackMetric

    trackMetric(name: string, average: number, sampleCount?: number, min?: number, max?: number)


Запись числового значения, не связанного с определенным событием. Обычно используется для отправки систематических отчетов о показателях эффективности.

На портале вы можете выбрать метрику по имени и создать [диаграмму на основе значений за определенный период](app-insights-metrics-explorer.md). Отдельные вызовы trackMetric нельзя искать или просматривать.

Чтобы отправить один показатель, используйте только первые два параметра. Если показатели снимаются очень часто, вы можете объединять несколько показателей и отправлять их среднее значение через определенные интервалы. Это позволит снизить объем передаваемых телеметрических данных.

 | | 
---|---|---
`name` | `string` | Строка, определяющая метрику. Метрики для отображения можно выбирать на портале по именам.
`average` | ` number` | Один показатель или среднее значение нескольких показателей.
`sampleCount` | `? number` | Количество показателей, из которого вычисляется среднее значение. По умолчанию равно 1.
`min` | `? number` | Наименьший показатель в выборке. По умолчанию равно среднему значению.
`max` | `? number` | Наибольший показатель в выборке. По умолчанию равно среднему значению.

### trackException

    trackException(exception: Error, handledAt?: string, properties?: Object, measurements?: Object)

Запись исключения, которое вы обнаружили. (Исключения, обнаруживаемые браузером, также записываются).

На портале вы можете [искать исключения по типу и просматривать](app-insights-diagnostic-search.md) их тип, сообщение и трассировку стека.

 | | 
---|---|---
`exception` | `Error` | Ошибка в предложении catch.  
`handledAt` | `? string` | По умолчанию исключение считается необработанным.
`properties` | `? {[string]:string}` | Дополнительные данные, используемые для фильтрации страниц и показателей на портале. По умолчанию значение не указывается.
`measurements` | `? {[string]:number}` | Метрики, которые связаны с этой страницей и отображаются в обозревателе метрик на портале. По умолчанию значение не указывается.

### trackTrace

    trackTrace(message: string, properties?: Object, measurements?: Object)

Запись данных о диагностических событиях, например о входе или выходе из метода.

На портале вы можете искать содержимое сообщений и [отображать отдельные события trackTrace](app-insights-diagnostic-search.md). (В отличие от `trackEvent` на портале нельзя использовать фильтрацию по содержимому сообщений).

 | | 
---|---|---
`message` | `string` | Диагностические данные. Их длина может существенно превышать длину имени.

### flush

    flush()

Немедленная отправка всех телеметрических данных в очереди.

Используется при закрытии окна.


### config

    config: IConfig

Значения, определяющие способ отправки телеметрических данных.

    interface IConfig {
        instrumentationKey: string;
        endpointUrl: string;
        accountId: string;
        appUserId: string;
        sessionRenewalMs: number; // 30 mins. 
        sessionExpirationMs: number; // 24h. 
        maxPayloadSizeInBytes: number; // 200k
        maxBatchSizeInBytes: number; // 100k
        maxBatchInterval: number; // 15000
        enableDebug: boolean;
        autoCollectErrors: boolean; // true
        disableTelemetry: boolean; // false
        verboseLogging: boolean;
        diagnosticLogInterval: number; // 10 000
    }

Эти значения задаются во [фрагменте кода](app-insights-javascript-api.md), который затем вставляется на веб-страницы. Найдите следующую строку и добавьте дополнительные элементы.

    })({
      instrumentationKey: "000...000"
    });

### context

    context: TelemetryContext

Сведения об устройстве, расположении и пользователе, которые пакет SDK пытается извлечь из среды.


## Класс TelemetryContext




        /**
         * Details of the app you're monitoring.
         */
        public application: Context.Application;

        /**
         * The device the app is running on.
         */
        public device: Context.Device;

        /**
         * The user currently signed in.
         */
        public user: Context.User;

        /**
         * The user session. A session represents a series
         * of user actions. A session starts with a user action.
         * It ends when there is no user activity for 
         * sessionRenewalMs, 
         * or if it lasts longer than sessionExpirationMs.
         */
        public session: Context.Session;

        /**
         * The geographical location of the user's device,
         * if available.
         */
        public location: Context.Location;

        /**
         * Represents the user request. Operation id is used
         * to tie together related events in diagnostic search.
         */
        public operation: Context.Operation;

        /**
         * Default measurements to be attached by default to
         * all events.
         */
        public measurements: any;

        /**
         * Default properties to be attached by default to
         * all events. 
         */
        public properties: any;

        /**
         * Send telemetry object to the endpoint.
         * Returns telemetryObject.
         */
        public track(envelope: Telemetry.Common.Envelope) ;


## Открытый исходный код

Внесите свой вклад в [код для пакета SDK](https://github.com/Microsoft/ApplicationInsights-js) или просто следите за его развитием.

<!---HONumber=July15_HO4-->