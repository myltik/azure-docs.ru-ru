---
title: "Мониторинг служб Node.js с помощью Azure Application Insights | Документация Майкрософт"
description: "Используйте Application Insights для мониторинга производительности и диагностики проблем в службах Node.js."
services: application-insights
documentationcenter: nodejs
author: mrbullwinkle
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/01/2017
ms.author: mbullwin
ms.openlocfilehash: 8f7a2344b6676a9067cf0adff04f49a73ce457fc
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Мониторинг служб и приложений Node.js с помощью Application Insights

[Azure Application Insights](app-insights-overview.md) отслеживает развернутые службы и компоненты серверной части для [обнаружения и быстрой диагностики проблем производительности, а также других проблем](app-insights-detect-triage-diagnose.md). Вы можете использовать Application Insights для служб Node.js, размещенных в центре обработки данных, на виртуальных машинах Azure, в веб-приложениях и даже в сторонних общедоступных облаках.

Для получения, хранения и анализа данных мониторинга включите пакет SDK в ваш код, а затем настройте соответствующий ресурс Application Insights в Azure. Пакет SDK отправляет данные в этот ресурс для дальнейшего анализа и исследования.

Пакет SDK для Node.js может автоматически отслеживать входящие и исходящие HTTP-запросы, исключения и некоторые системные метрики. Начиная с версии 0.20, пакет SDK также может отслеживать некоторые распространенные пакеты сторонних поставщиков, например MongoDB, MySQL и Redis. Все события, связанные с входящим HTTP-запросом, коррелируют для быстрого устранения неполадок.

Вы можете инструментировать вручную и отслеживать дополнительные аспекты приложения и системы с помощью API TelemetryClient. С дополнительными сведениями о API TelemetryClient можно ознакомиться далее в этой статье.

![Пример диаграмм мониторинга производительности](./media/app-insights-nodejs/10-perf.png)

## <a name="get-started"></a>Начало работы

Выполните указанные ниже задачи, чтобы настроить мониторинг для приложения или службы.

### <a name="prerequisites"></a>предварительным требованиям

Чтобы начать, у вас должна быть подписка Azure. Вы можете [создать ее бесплатно][azure-free-offer]. Если у вашей организации уже есть подписка Azure, администратору необходимо выполнить [эти инструкции][add-aad-user], чтобы добавить вас в эту подписку.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: https://docs.microsoft.com/azure/active-directory/active-directory-users-create-azure-portal


### <a name="resource"></a> Настройка ресурса Application Insights


1. Войдите на [портал Azure][portal].
2. Выберите **Создать** > **Средства разработчика** > **Application Insights**. Ресурс содержит конечную точку для приема данных телеметрии, хранилище для этих данных, сохраненные отчеты и панели мониторинга, настройки правил и оповещений и т. д.

  ![Создание ресурса Application Insights](./media/app-insights-nodejs/03-new_appinsights_resource.png)

3. На странице создания ресурса в поле **Тип приложения** выберите **Приложение Node.js**. Тип приложения определяет созданные панели мониторинга и отчеты по умолчанию. (Любой ресурс Application Insights способен собирать данные независимо от используемого языка и платформы.)

  ![Форма создания ресурса Application Insights](./media/app-insights-nodejs/04-create_appinsights_resource.png)

### <a name="sdk"></a> Настройка пакета SDK для Node.js

Включите пакет SDK в приложение, чтобы он мог собирать данные. 

1. Скопируйте ключ инструментирования ресурса (также называемый *ikey*) на портале Azure. Application Insights использует ikey для сопоставления данных с ресурсом Azure. Прежде чем пакет SDK сможет использовать ikey, необходимо указать ikey в переменной среды или в коде.  

  ![Копирование ключа инструментирования](./media/app-insights-nodejs/05-appinsights_ikey_portal.png)

2. Добавьте библиотеку пакета SDK для Node.js в зависимости от приложения с помощью файла package.json. Из корневой папки приложения выполните следующую команду:

  ```bash
  npm install applicationinsights --save
  ```

3. Загрузите явным образом библиотеку в код. Так как пакет SDK внедряет инструментирование во многие другие библиотеки, вам необходимо загрузить библиотеку как можно раньше, даже до выполнения других инструкций `require`. 

  Добавьте в верхнюю часть первого JS-файла указанный ниже код. Метод `setup` настраивает ikey (а значит и ресурс Azure), который будет использоваться по умолчанию для всех отслеживаемых элементов.

  ```javascript
  const appInsights = require("applicationinsights");
  appInsights.setup("<instrumentation_key>");
  appInsights.start();
  ```
   
  Вы также можете предоставить ключ ikey через переменную среды APPINSIGHTS\_INSTRUMENTATIONKEY, вместо того чтобы передавать его вручную в `setup()` или `new appInsights.TelemetryClient()`. Это позволит хранить ключи ikey вне выделенного исходного кода, и вы можете указать различные ключи ikey для разных сред.

  Дополнительные параметры конфигурации приведены в указанных ниже разделах.

  Вы можете использовать пакет SDK, не отправляя данные телеметрии. Для этого задайте `appInsights.defaultClient.config.disableAppInsights = true`.

### <a name="monitor"></a> Отслеживание работы приложения

Пакет SDK автоматически собирает данные телеметрии среды выполнения Node.js и некоторых распространенных модулей сторонних поставщиков. Используйте приложение для создания этих данных.

Затем на [портале Azure][portal] перейдите к ресурсу Application Insights, созданному ранее. На **временной шкале обзора** просмотрите первые несколько точек данных. Для просмотра более подробных данных выбирайте различные компоненты на диаграммах.

![Первые точки данных](./media/app-insights-nodejs/12-first-perf.png)

Чтобы просмотреть топологию, обнаруженную для приложения, нажмите кнопку **схемы сопоставления приложений**. Выберите компоненты на карте для просмотра дополнительных сведений.

![Простая схема сопоставления приложений](./media/app-insights-nodejs/06-appinsights_appmap.png)

Чтобы узнать больше о приложении и методах устранения неполадок, в разделе **изучения** выберите другие доступные представления.

![Раздел изучения](./media/app-insights-nodejs/07-appinsights_investigate_blades.png)

#### <a name="no-data"></a>Данные отсутствуют?

Так как пакет SDK упаковывает данные для передачи, возможна некоторая задержка отображения элементов на портале. Если данные в ресурсе не отображаются, попробуйте сделать следующее:

* Продолжайте использовать приложение. Выполните дополнительные действия, чтобы создать больше данных телеметрии.
* Нажмите кнопку **обновления** в представлении ресурса на портале. Диаграммы периодически обновляются, но если нажать эту кнопку, обновление будет выполнено сразу же.
* [Необходимые порты для исходящего трафика](app-insights-ip-addresses.md) должны быть открыты.
* Найти отдельные события можно с помощью функции [поиска](app-insights-diagnostic-search.md).
* Просмотрите [часто задаваемые вопросы][FAQ].


## <a name="sdk-configuration"></a>Конфигурация пакета SDK

В примере кода ниже перечислены методы конфигурации пакета SDK, а также их значения по умолчанию.

Чтобы полностью сопоставить события в службе, задайте `.setAutoDependencyCorrelation(true)`. Благодаря этому пакет SDK может отслеживать контекст в асинхронных обратных вызовах в службе Node.js.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

## <a name="telemetryclient-api"></a>API TelemetryClient

Полное описание API TelemetryClient см. в статье [API Application Insights для пользовательских событий и метрик](app-insights-api-custom-events-metrics.md).

Вы можете отслеживать любой запрос, событие, метрику или исключение с помощью пакета SDK для Node.js для Application Insights. В примере кода ниже приведены некоторые API, которые можно использовать.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey is in env var
let client = appInsights.defaultClient;

client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Отслеживание зависимостей

С помощью следующего кода можно отслеживать зависимости:

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.defaultClient;

var success = false;
let startTime = Date.now();
// Execute dependency call here...
let duration = Date.now() - startTime;
success = true;

client.trackDependency({dependencyTypeName: "dependency name", name: "command name", duration: duration, success: success});
```

### <a name="add-a-custom-property-to-all-events"></a>Добавление пользовательского свойства во все события

С помощью следующего кода можно добавить пользовательское свойство во все события:

```javascript
appInsights.defaultClient.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Отслеживание HTTP-запросов GET

С помощью следующего кода можно отслеживать HTTP-запросы GET:

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.defaultClient.trackNodeHttpRequest({request: req, response: res});
    }
    // Other work here...
    res.end();
});
```

### <a name="track-server-startup-time"></a>Отслеживание времени запуска сервера

С помощью следующего кода можно отслеживать время запуска сервера:

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

## <a name="next-steps"></a>Дополнительная информация

* [Навигация и панели мониторинга на портале Application Insights](app-insights-dashboards.md)
* [Знакомство с аналитикой в Application Insights](app-insights-analytics-tour.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: app-insights-troubleshoot-faq.md

