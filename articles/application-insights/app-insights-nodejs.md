---
title: "Мониторинг служб Node.js с помощью Azure Application Insights | Документация Майкрософт"
description: "Используйте Application Insights для мониторинга производительности и диагностики проблем в службах Node.js."
services: application-insights
documentationcenter: nodejs
author: joshgav
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/01/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 76a8025cd2a67533beb321c88e924517c1977dfc
ms.contentlocale: ru-ru
ms.lasthandoff: 05/02/2017

---

# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Мониторинг служб и приложений Node.js с помощью Application Insights

[Azure Application Insights](app-insights-overview.md) отслеживает развернутые службы и компоненты серверной части для [обнаружения и быстрой диагностики проблем производительности, а также других проблем](app-insights-detect-triage-diagnose.md). Используйте этот компонент для служб Node.js независимо от их расположения: в центре обработки данных, виртуальных машинах Azure и веб-приложениях, даже в сторонних общедоступных облачных средах.

Для получения, хранения и анализа данных мониторинга выполните приведенные ниже инструкции, чтобы включить агент в код, а также настроить соответствующий ресурс Application Insights в Azure. Агент отправляет данные в этот ресурс для дальнейшего анализа и исследования.

Агент Node.js может автоматически отслеживать входящие и исходящие HTTP-запросы, несколько системных метрик и исключения. Начиная с версии v0.20, он также может отслеживать некоторые распространенные пакеты сторонних поставщиков, например `mongodb`, `mysql` и `redis`. Все события, связанные с входящим HTTP-запросом, коррелируют для быстрого устранения неполадок.

Вы можете отслеживать несколько аспектов приложения и системы, инструментируя их вручную с помощью агента API, с которым вы ознакомитесь далее.

![Пример диаграмм мониторинга производительности](./media/app-insights-nodejs/10-perf.png)

## <a name="getting-started"></a>Приступая к работе

Давайте перейдем к настройке мониторинга приложения или службы.

### <a name="resource"></a> Настройка ресурса App Insights

**Чтобы начать**, у вас должна быть подписка Azure. Вы можете [создать ее бесплатно][azure-free-offer]. Если у вашей организации уже есть подписка Azure, администратору необходимо выполнить [эти инструкции][add-aad-user], чтобы добавить вас в эту подписку.

[azure-free-offer]: https://azure.microsoft.com/en-us/free/
[add-aad-user]: https://docs.microsoft.com/en-us/azure/active-directory/active-directory-users-create-azure-portal

Теперь войдите [на портал Azure][portal] и создайте ресурс Application Insights, как показано далее. Щелкните "Создать" > "Средства разработчика" > "Application Insights". Ресурс содержит конечную точку для приема данных телеметрии, хранилище для этих данных, сохраненные отчеты и панели мониторинга, настройки правил и оповещений и т. д.

![Создание ресурса App Insights](./media/app-insights-nodejs/03-new_appinsights_resource.png)

На странице создания ресурса выберите "Приложение Node.js" из раскрывающегося списка типов приложения. От типа приложения зависит набор панелей мониторинга и отчетов по умолчанию, которые вы получите. В любом случае беспокоиться не стоит, любой ресурс App Insights способен собирать данные независимо от используемого языка и платформы.

![Форма создания ресурса App Insights](./media/app-insights-nodejs/04-create_appinsights_resource.png)

### <a name="agent"></a> Настройка агента Node.js

Теперь пришло время включить агент в приложение, чтобы он мог собирать данные.
Для начала скопируйте ключ инструментирования ресурса (далее — `ikey`) на портале, как показано ниже. Система App Insights будет использовать этот ключ, чтобы сопоставить данные с ресурсом Azure, поэтому вам необходимо указать его в переменной среды или коде, который будет использовать агент.  

![Копирование ключа инструментирования](./media/app-insights-nodejs/05-appinsights_ikey_portal.png)

Затем добавьте библиотеку агента Node.js в зависимости приложения с помощью файла package.json. Из корневой папки приложения выполните следующую команду:

```bash
npm install applicationinsights --save
```

Теперь вам необходимо явным образом загрузить библиотеку в код. Так как агент внедряет инструментирование во многие другие библиотеки, вам необходимо загрузить ее как можно раньше, даже до выполнения других инструкций `require`. Чтобы приступить, добавьте в верхнюю часть первого JS-файла следующее:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.start();
```

Метод `setup` настраивает ключ инструментирования (а значит и ресурс Azure), который будет использоваться по умолчанию для всех отслеживаемых элементов. После завершения настройки вызовите `start`, чтобы начать сбор данных телеметрии и их отправку.

Вы также можете предоставить ключ ikey через переменную среды APPINSIGHTS\_INSTRUMENTATIONKEY, вместо того чтобы передавать его вручную в `setup()` или `getClient()`. Это позволит хранить ключи ikey вне выделенного исходного кода и указать различные ключи ikey для разных сред.

Дополнительные параметры настройки описаны ниже.

Вы можете использовать агент, не отправляя данные телеметрии. Для этого настройте ключ инструментирования в качестве любой заполненной строки.

### <a name="monitor"></a> Отслеживание работы приложения

Агент автоматически собирает данные телеметрии среды выполнения Node.js и некоторых распространенных модулей сторонних поставщиков. Используйте приложение для создания этих данных.

Затем на [портале Azure][portal] перейдите к созданному ранее ресурсу Application Insights и найдите минимальное количество точек данных на временной шкале обзора, как показано на рисунке ниже. Чтобы получить дополнительные сведения, щелкните диаграммы.

![Первые точки данных](./media/app-insights-nodejs/12-first-perf.png)

Нажмите кнопку схемы сопоставления приложений, чтобы просмотреть топологию, обнаруженную для приложения, как показано на рисунке ниже. Чтобы получить дополнительные сведения, щелкайте компоненты на карте.

![Простая схема сопоставления приложений](./media/app-insights-nodejs/06-appinsights_appmap.png)

Чтобы узнать больше о приложении и методах устранения неполадок, используйте другие представления, доступные в разделе изучения.

![Раздел изучения](./media/app-insights-nodejs/07-appinsights_investigate_blades.png)

#### <a name="no-data"></a>Данные отсутствуют?

Так как агент упаковывает данные для передачи, возможна некоторая задержка отображения элементов на портале. Если данные в ресурсе не отображаются, попробуйте сделать следующее:

* Выполните больше действий в приложении, чтобы получить больше данных телеметрии.
* Нажмите кнопку **обновления** в представлении ресурса на портале. Диаграммы и так периодически автоматически обновляются, но если вы нажмете эту кнопку, обновление будет выполнено сразу же.
* [Необходимые порты для исходящего трафика](app-insights-ip-addresses.md) должны быть открыты.
* Откройте плитку [поиска](app-insights-diagnostic-search.md), чтобы найти отдельные события.
* Просмотрите [часто задаваемые вопросы][].


## <a name="agent-configuration"></a>Конфигурация агента

Ниже представлены методы настройки агента, а также их значения по умолчанию.

Чтобы полностью сопоставить события в службе, задайте `.setAutoDependencyCorrelation(true)`. Благодаря этому агент будет отслеживать контекст в асинхронных обратных вызовах в службе Node.js.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(false)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .start();
```

## <a name="agent-api"></a>API агента

<!-- TODO: Fully document agent API. -->

API агента для .NET полностью описан [здесь](app-insights-api-custom-events-metrics.md).

Вы можете отслеживать любой запрос, событие, метрику или исключение с помощью клиента Node.js для Application Insights. В примере ниже приведены некоторые доступные интерфейсы API.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var
let client = appInsights.getClient();

client.trackEvent("my custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");

let http = require("http");
http.createServer( (req, res) => {
  client.trackRequest(req, res); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Отслеживание зависимостей

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.getClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency("dependency name", "command name", duration, success);
```

### <a name="add-a-custom-property-to-all-events"></a>Добавление пользовательского свойства во все события

```javascript
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Отслеживание HTTP-запросов GET

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.client.trackRequest(req, res);
    }
    // other work here....
    res.end();
});
```

### <a name="track-server-startup-time"></a>Отслеживание времени запуска сервера

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.client.trackMetric("server startup time", duration);
});
```

## <a name="more-resources"></a>Дополнительные ресурсы

* [Навигация и панели мониторинга на портале Application Insights](app-insights-dashboards.md)
* [Знакомство с аналитикой в Application Insights](app-insights-analytics-tour.md)

<!--references-->

[portal]: https://portal.azure.com/
[часто задаваемые вопросы]: app-insights-troubleshoot-faq.md

