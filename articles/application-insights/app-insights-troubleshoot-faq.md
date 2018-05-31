---
title: Вопросы и ответы об Azure Application Insights | Документы Майкрософт
description: Вопросы и ответы об Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: mbullwin
ms.openlocfilehash: d7abfd1ac6f914c75297ff49462590e5b6169dbd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32310020"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: вопросы и ответы

## <a name="configuration-problems"></a>Проблемы с конфигурацией
*У меня не получается настроить компоненты, о которых идет речь в таких статьях:*

* [Troubleshooting no data - Application Insights for .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [раздел "Устранение неполадок"](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Настройка системы диагностики Azure для входа в Application Insights](app-insights-azure-diagnostics.md)
* [Веб-приложение Java](app-insights-java-troubleshoot.md)

*Я не получаю данные с моего сервера*

* [Настройка исключений брандмауэра](app-insights-ip-addresses.md)
* [Настройка сервера ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Настройка сервера Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Можно ли использовать Application Insights с...?

* [Веб-приложения на сервере IIS — локальном или в виртуальной машине](app-insights-asp-net.md)
* [Веб-приложения Java](app-insights-java-get-started.md)
* [Приложения Node.js](app-insights-nodejs.md)
* [Веб-приложения в Azure](app-insights-azure-web-apps.md)
* [Облачные службы в Azure](app-insights-cloudservices.md)
* [Серверы приложений, работающие в Docker](app-insights-docker.md)
* [Одностраничные веб-приложения](app-insights-javascript.md)
* [SharePoint](app-insights-sharepoint.md)
* [Классические приложения Windows](app-insights-windows-desktop.md)
* [другие платформы.](app-insights-platforms.md)

## <a name="is-it-free"></a>Предоставляется ли бесплатно?

Да, для экспериментальных целей. По условиям тарифного плана "Базовый" приложение может отправлять определенный лимит данных каждый месяц бесплатно. Бесплатный лимит достаточен для разработки и публикации приложения для небольшого числа пользователей. Можно задать ограничение, запрещающее обработку данных сверх заданного лимита.

Оплата за большие объемы данных телеметрии взимается по ГБ. Мы даем ряд советов по тому, как [ограничить расходы](app-insights-pricing.md).

В рамках плана "Корпоративный" оплата взимается за каждый день, в который каждый узел веб-сервера отправляет телеметрию. Он подходит для случаев, когда требуется использовать непрерывный экспорт в большом масштабе.

[Изучите тарифный план](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Сколько это стоит?

* Откройте страницу **Usage and estimated costs** (Данные об использовании и предполагаемые расходы) для ресурса Application Insights. На ней приводится диаграмма недавнего использования. При желании можно задать ограничение на объем данных.
* Откройте [колонку подписок и выставления счетов Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview), чтобы просмотреть счета за все ресурсы.

## <a name="q14"></a>Что Application Insights изменяет в моем проекте?
Подробности зависят от типа проекта. Для веб-приложения:

* Добавляет в проект следующие файлы:

  * ApplicationInsights.config.
  * ai.js
* Устанавливает следующие пакеты NuGet:

  * *Application Insights API* — основной API
  * *API Application Insights для веб-приложений* — используется для отправки данных телеметрии со стороны сервера
  * *API Application Insights для приложений JavaScript* — используется для отправки данных телеметрии со стороны клиента

    Пакет включает эти сборки:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Вставляет элементы в:

  * Web.config
  * packages.config
* (Только для новых проектов. Если нужно [добавить Application Insights в существующий проект][start], придется делать это вручную.) Вставляет фрагменты кода в код клиента и сервера для их инициализации с идентификатором ресурса Application Insights. Например, в приложении MVC код вставляется в файл главной страницы Views/Shared/_Layout.cshtml.

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Как обновить предыдущие версии пакета SDK?
Информацию для пакета SDK, соответствующего типу вашего приложения, см. в статье с [заметками о выпуске](app-insights-release-notes.md).

## <a name="update"></a>Как изменить ресурс Azure, в который проект отправляет данные?
В обозревателе решений щелкните правой кнопкой мыши `ApplicationInsights.config` и выберите **Обновить Application Insights**. Данные можно отправлять на существующий или новый ресурс в Azure. Мастер обновления изменяет ключ инструментирования в файле ApplicationInsights.config, который определяет, куда сервер SDK должен отправлять ваши данные. Если отменить выбор параметра "Обновить все", это также приведет к изменению ключа, отображаемого на ваших веб-страницах.

## <a name="what-is-status-monitor"></a>Что такое монитор состояния?

Это классическое приложение, которое можно использовать на веб-сервере IIS для настройки Application Insights в веб-приложениях. Оно не собирает телеметрию: его можно остановить, когда вы не настраиваете приложение. 

[Узнайте больше](app-insights-monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Какую телеметрию собирает Application Insights?

Из серверных веб-приложений:

* HTTP-запросы;
* [зависимости](app-insights-asp-net-dependencies.md); вызовы к базам данных SQL, Azure Cosmos DB, таблице, хранилищу BLOB-объектов и очереди; HTTP-вызовы к внешним службам; 
* [исключения](app-insights-asp-net-exceptions.md) и трассировки стека;
* [счетчики производительности](app-insights-performance-counters.md) — если вы используете [монитор состояния](app-insights-monitor-performance-live-website-now.md), [мониторинг Azure](app-insights-azure-web-apps.md) или [средство записи collectd Application Insights](app-insights-java-collectd.md);
* [пользовательские события и метрики](app-insights-api-custom-events-metrics.md), которые вы создаете в коде;
* [журналы трассировки](app-insights-asp-net-trace-logs.md), если вы настраиваете соответствующий сборщик.

С [клиентских веб-страниц](app-insights-javascript.md):

* [число просмотров страниц](app-insights-web-track-usage.md);
* [вызовы AJAX](app-insights-asp-net-dependencies.md) — запросы, выполняемые из запущенного скрипта;
* загрузка данных при просмотре страниц;
* количество пользователей и сеансов;
* [идентификаторы пользователей, прошедших проверку подлинности](app-insights-api-custom-events-metrics.md#authenticated-users).

Из других источников, если они настроены:

* [Настройка системы диагностики Azure для входа в Application Insights](app-insights-azure-diagnostics.md)
* [контейнеры Docker](app-insights-docker.md);
* [импорт таблиц в Analytics](app-insights-analytics-import.md);
* [Служба Log Analytics](https://azure.microsoft.com/blog/omssolutionforappinsightspublicpreview/)
* [Logstash](app-insights-analytics-import.md).

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Можно ли отфильтровать или изменить некоторые данные телеметрии?

Да, на сервере можно создать следующие компоненты:

* обработчик данных телеметрии для фильтрации или добавления свойств в выбранные элементы телеметрии перед их отправкой из приложения;
* инициализатор телеметрии для добавления свойств во все элементы телеметрии.

Дополнительные сведения об [ASP.NET](app-insights-api-filtering-sampling.md) или [Java](app-insights-java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Как определяются данные по городу, стране и другие данные по географическому расположению?

Мы определяем IP-адрес (IPv4 или IPv6) веб-клиента с помощью [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Телеметрия браузера: мы собираем сведения об IP-адресе отправителя.
* Телеметрия сервера: модуль Application Insights собирает сведения об IP-адресе клиента. Эти сведения не собираются, если задан заголовок `X-Forwarded-For`.

Вы можете настроить `ClientIpHeaderTelemetryInitializer` для получения IP-адреса из другого заголовка. Например, в некоторых системах он переносится прокси-сервером, балансировщиком нагрузки или сетью CDN в `X-Originating-IP`. [Узнайте больше](http://apmtips.com/blog/2016/07/05/client-ip-address/).

Вы можете [использовать Power BI](app-insights-export-power-bi.md) для отображения данных телеметрии по запросам на карте.


## <a name="data"></a>Как долго данные хранятся на портале? Защищены ли они?
Ознакомьтесь с разделом [Хранение данных и конфиденциальность][data].

## <a name="might-personally-identifiable-information-pii-be-sent-in-the-telemetry"></a>Могут ли при передаче телеметрии отправляться персональные данные?

Это возможно, если ваш код отправляет такие данные. Это также может происходить, если переменные в трассировках стека содержат персональные данные. Ваша команда разработчиков должна провести оценку рисков, чтобы обеспечить надлежащую обработку персональных данных. [Дополнительные сведения о хранении и конфиденциальности данных](app-insights-data-retention-privacy.md).

Для **всех** октетов веб-адреса клиента всегда задается значение 0 после подстановки атрибутов географического расположения.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>Мой ключ iKey доступен в исходном коде веб-страницы. 

* Это обычная ситуация в решениях для мониторинга.
* С его помощью нельзя похитить ваши данные.
* Он может использоваться для искажения данных или активации оповещений.
* Мы не получали сообщений от клиентов о возникновении таких проблем.

Вы можете:

* использовать два отдельных ключа iKey (отдельных ресурса Application Insights) для данных клиента и сервера; или
* создать прокси-сервер, работающий на вашем сервере, и передавать данные веб-клиента через него.

## <a name="post"></a>Как просмотреть данные POST в колонке «Поиск по журналу диагностики»?
Данные POST не регистрируются автоматически, но можно использовать вызов TrackTrace. Для этого добавьте данные в параметр сообщения. В нем можно добавить больше символов, чем в свойствах строк, но по нему невозможно выполнить фильтрацию.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Следует ли использовать один или несколько ресурсов Application Insights?

Используйте один ресурс для всех компонентов или ролей в единой бизнес-системе. Используйте отдельные ресурсы для стадий разработки, тестирования и выпуска, а также для независимых приложений.

* [Ознакомьтесь с этим обсуждением](app-insights-separate-resources.md)
* [Пример: облачная служба с рабочей ролью и веб-ролью](app-insights-cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Как динамически изменить ключ инструментирования?

* [Ознакомьтесь с этим обсуждением](app-insights-separate-resources.md)
* [Пример: облачная служба с рабочей ролью и веб-ролью](app-insights-cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Что такое количество пользователей и сеансов?

* Пакет SDK для JavaScript создает файл cookie пользователя в веб-клиенте для определения возвращающихся пользователей и файл cookie сеанса для группировки действий.
* Если скрипт на стороне клиента отсутствует, вы можете [задать файлы cookie на сервере](http://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Если один реальный пользователь работает с вашим сайтом в разных браузерах, на разных компьютерах либо использует конфиденциальный режим просмотра или режим инкогнито, то он будет учитываться несколько раз.
* Для определения вошедшего в систему пользователя на разных компьютерах и в разных браузерах добавьте вызов [setAuthenticatedUserContext()](app-insights-api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> Все ли активировано в Application Insights?
| Что вы должны видеть | Как это получить | Для чего это нужно |
| --- | --- | --- |
| Диаграммы доступности |[Веб-тесты](app-insights-monitor-web-app-availability.md) |Узнать, что ваше веб-приложение работает |
| Производительность приложения на сервере: время отклика и т.д. |[Добавить Application Insights в ваш проект](app-insights-asp-net.md) или [установить монитор состояний Application Insights на сервере](app-insights-monitor-performance-live-website-now.md) (или написать собственный код для [отслеживания зависимостей](app-insights-api-custom-events-metrics.md#trackdependency)) |Выявить проблемы производительности |
| Телеметрия зависимостей |[Установить монитор состояний Application Insights на сервере](app-insights-monitor-performance-live-website-now.md) |Выявить проблемы с базами данных или другими внешними компонентами |
| Получение данных трассировки стека из исключений |[Вставить вызовы TrackException в код](app-insights-asp-net-exceptions.md) (некоторые выводятся автоматически) |Обнаружить и диагностировать исключения |
| Поиск по трассировкам журнала |[Добавить адаптер ведения журнала](app-insights-asp-net-trace-logs.md) |Выявить исключения, проблемы производительности |
| Основная информация об использовании клиента: просмотр страниц, сеансы и т. д. |[Инициализатор JavaScript на веб-страницах](app-insights-javascript.md) |Аналитика использования |
| Настраиваемые метрики клиента |[Трассировка вызовов на веб-страницах](app-insights-api-custom-events-metrics.md) |Расширить возможности для пользователя |
| Настраиваемые метрики сервера |[Отслеживание вызовов на сервере](app-insights-api-custom-events-metrics.md) |Бизнес-аналитика |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Почему счетчики в результатах поиска и на диаграммах метрик не совпадают?

[Выборка](app-insights-sampling.md) сокращает число элементов телеметрии (запросов, пользовательских событий и т. д.), которые передаются из приложения на портал. В результатах поиска вы видите число фактически полученных элементов. На диаграммах метрик, на которых приводится число событий, вы видите количество исходных событий, которые произошли. 

Каждый передаваемый элемент имеет свойство `itemCount`, которое показывает, сколько исходных событий представляет этот элемент. Чтобы увидеть, как работает выборка, можно выполнить в Analytics следующий запрос:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Служба автоматизации

### <a name="configuring-application-insights"></a>Настройка Application Insights

С помощью монитора ресурсов Azure можно [создавать скрипты PowerShell](app-insights-powershell.md) для выполнения следующих задач:

* создание и обновление ресурсов Application Insights;
* задание ценового плана;
* получение ключа инструментирования;
* добавление оповещения метрики;
* добавление теста доступности.

Вы не можете настроить отчет обозревателя метрик или непрерывный экспорт.

### <a name="querying-the-telemetry"></a>Запрос телеметрии

Используйте [REST API](https://dev.applicationinsights.io/) для выполнения запросов [аналитики](app-insights-analytics.md).

## <a name="how-can-i-set-an-alert-on-an-event"></a>Как можно настроить оповещение о событии?

Оповещения Azure настраиваются только для метрик. Создайте пользовательскую метрику, пороговое значение которой будет нарушаться при наступлении события. Затем настройте оповещение для метрики. Обратите внимание на то, что вы будете получать уведомление при нарушении порогового значения метрики в любом направлении. Вы не получите уведомление, пока не произойдет первое нарушение, независимо от того, является ли начальное значение высоким или низким. Всегда имеется задержка в несколько минут.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Взимается ли плата за передачу данных между веб-приложением Azure и Application Insights?

* Если ваше веб-приложение Azure размещается в центре данных с конечной точкой сбора Application Insights, плата не взимается. 
* Если в центре данных размещения нет конечной точки сбора, то за передачу данных телеметрии приложения будет взиматься [плата за передачу исходящих данных Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Это не зависит от того, где размещается ресурс Application Insights. Значение имеет только распределение наших конечных точек.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Можно ли отправлять данные телеметрии на портал Application Insights?

Мы рекомендуем использовать наши пакеты SDK, а также [API пакета SDK](app-insights-api-custom-events-metrics.md). Существуют разновидности пакетов SDK для разных [платформ](app-insights-platforms.md). Эти пакеты SDK управляют буферизацией, сжатием, регулированием, повторными попытками и другими операциями. Однако [схема приема](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) и [протокол конечной точки](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) являются открытыми.

## <a name="can-i-monitor-an-intranet-web-server"></a>Можно ли отслеживать веб-сервер в интрасети?

Есть два способа.

### <a name="firewall-door"></a>Разрешение в брандмауэре

Разрешите веб-серверу отправлять данные телеметрии в наши конечные точки https://dc.services.visualstudio.com:443 и https://rt.services.visualstudio.com:443. 

### <a name="proxy"></a>Прокси-сервер

Маршрутизируйте трафик со своего сервера на шлюз в интрасети, перезаписав эти параметры в примере ApplicationInsights.config. Если эти свойства "Конечная точка" отсутствуют в вашей конфигурации, эти классы будут использовать значения по умолчанию, показанные в приведенном ниже примере.

#### <a name="example-applicationinsightsconfig"></a>Пример ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <TelemetryChannel>
         <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
    </TelemetryChannel>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

_Примечание. ApplicationIdProvider доступен, начиная с версии 2.6.0_

Шлюз должен направлять трафик на адрес https://dc.services.visualstudio.com:443.

Замените значения выше на `http://<your.gateway.address>/<relative path>`
 
Пример: 
```
http://<your.gateway.endpoint>/v2/track 
http://<your.gateway.endpoint>/api/profiles/{0}/apiId
```




## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Можно ли выполнять веб-тесты доступности на сервере в интрасети?

Наши [веб-тесты](app-insights-monitor-web-app-availability.md) выполняются в точках подключения, которые распределены по всему миру. Есть два решения.

* Разрешение в брандмауэре: разрешите передачу запросов к серверу от [агентов веб-тестирования из длинного меняющегося списка](app-insights-ip-addresses.md).
* Напишите собственный код для периодической отправки запросов на сервер из интрасети. Для этой цели можно выполнять веб-тесты Visual Studio. Тест-инженер может отправлять результаты в Application Insights с помощью API TrackAvailability().

## <a name="more-answers"></a>Другие ответы
* [Форум Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md