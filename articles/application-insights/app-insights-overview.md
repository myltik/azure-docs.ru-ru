---
title: "Что такое Azure Application Insights? | Документация Майкрософт"
description: "Управление производительностью и отслеживание использования работающего веб-приложения.  Обнаружение, рассмотрение и диагностика проблем. Сведения о том, как пользователи используют ваше приложение."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/07/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 4c47041bb729a3ee1f3fb4c7baf7f988db226677
ms.lasthandoff: 04/12/2017


---
# <a name="what-is-application-insights"></a>Что такое Azure Application Insights?
Application Insights — это расширяемая служба управления производительностью приложений (APM) для веб-разработчиков на нескольких платформах. Используйте ее для мониторинга вашего работающего веб-приложения. Она автоматически обнаруживает аномалии производительности. Эта служба включает мощные аналитические средства, которые помогут вам диагностировать проблемы и понять, что пользователи фактически делают в вашем приложении.  Эта служба помогает постоянно улучшать производительность и удобство использования. Application Insights работает с приложениями на самых разнообразных платформах, включая .NET, Node.js и J2EE, с локальным или облачным размещением. Эта служба интегрируется с процессом DevOps и содержит точки подключения ко многим инструментам разработки.

![Составляйте диаграммы по действиям пользователей или детализируйте конкретные события.](./media/app-insights-overview/00-sample.png)

[Просмотрите этот ознакомительный анимированный ролик](https://www.youtube.com/watch?v=fX2NtGrh-Y0).

## <a name="how-does-application-insights-work"></a>Как работает Application Insights?
Вы устанавливаете небольшой пакет инструментирования в приложение и настраиваете ресурс Application Insights на портале Microsoft Azure. Пакет инструментирования отслеживает приложение и отправляет данные телеметрии на портал. (Приложение может работать где угодно. Его не обязательно размещать в Azure.)

Вы можете инструментировать не только приложение веб-службы, но и любые фоновые компоненты, а также JavaScript на веб-страницах. 

![Инструментирование Application Insights в приложении отправляет данные телеметрии в ресурс Application Insights.](./media/app-insights-overview/01-scheme.png)


Кроме того, можно извлечь данные телеметрии из сред размещения, например данные счетчиков производительности, диагностики Azure или журналов Docker. Вы также можете настроить веб-тесты, которые периодически отправляют искусственные запросы в веб-службу.

Эти потоки данных телеметрии интегрированы на портале Azure, где к необработанным данным можно применять мощные инструменты анализа и поиска.


### <a name="whats-the-overhead"></a>Увеличение нагрузки
Влияние на производительность вашего приложения очень мало. Вызовы отслеживания не приводят к блокировке, выполняются в пакетном режиме и отправляется в отдельном потоке.

## <a name="what-does-application-insights-monitor"></a>Как работает монитор Application Insights?

Служба Application Insights предназначена для команды разработчиков. Она позволяет получить сведения о производительности и использовании приложения. Она отслеживает следующее:

* **Частота запросов, время отклика и частота сбоев.** Узнайте, какие страницы наиболее популярны, в какое время дня их посещают чаще всего, а также узнайте о расположении пользователей. Узнайте, какие страницы работают лучше всего. Если при увеличении количества запросов повышается время отклика и частота сбоев, возможно, возникла проблема с ресурсами. 
* **Частота зависимостей, время отклика и частота сбоев.** Узнайте, замедляют ли внешние службы вашу работу.
* **Исключения.** Проанализируйте сводные статистические данные или выберите конкретные экземпляры и выполните детализацию трассировки стека и связанных запросов. Исключения сервера и браузера регистрируются.
* **Просмотры страниц и производительность загрузки.** Эти сведения сообщаются через браузеры пользователей.
* **Вызовы AJAX** с веб-страницы. Скорость, время отклика и частота сбоев.
* **Количество пользователей и сеансов.**
* **Счетчики производительности** с компьютеров с сервером Windows или Linux, такие как ЦП, память и использование сети. 
* **Размещение диагностики** из Docker или Azure. 
* **Журналы диагностики трассировки** из вашего приложения. Предназначены для сопоставления событий трассировки с запросами.
* **Пользовательские события и метрики**, которые вы напишете самостоятельно в коде клиента или сервера, для отслеживания бизнес-событий, таких как количество проданных единиц или выигранных игр.

## <a name="where-do-i-see-my-telemetry"></a>Где отображаются мои данные телеметрии?

Существует множество способов изучения данных. Ознакомьтесь со следующими статьями:

|  |  |
| --- | --- |
| [**Схема сопоставления приложений в Application Insights**](app-insights-app-map.md)<br/>Компоненты приложения с основными метриками и оповещениями. |![Схема сопоставления приложений](./media/app-insights-overview/appmap-tn.png)  |
| [**Работа с Application Insights в Visual Studio**](app-insights-diagnostic-search.md)<br/>Поиск и фильтрация событий, таких как запросы, исключения, вызовы зависимостей, журналы трассировки и просмотры страниц.  |![Поиск данных телеметрии](./media/app-insights-overview/search-tn.png) |
| [**Исследование метрик в Application Insights**](app-insights-metrics-explorer.md)<br/>Просмотр, фильтрация и сегментирование объединенных данных, таких как частоты запросов, ошибок и исключений, время отклика и время загрузки страницы. |![Метрики](./media/app-insights-overview/metrics-tn.png) |
| [**Панели мониторинга**](app-insights-dashboards.md#dashboards)<br/>Объединение разнородных данных из нескольких ресурсов и их совместное использование с другими пользователями. Идеальное решение для многокомпонентных приложений, а также для непрерывного отображения в комнате команды. |![Пример панели мониторинга](./media/app-insights-overview/dashboard-tn.png) |
| [**Динамический поток метрик: мгновенные метрики для подробного отслеживания**](app-insights-live-stream.md)<br/>При развертывании новой сборки просматривайте эти индикаторы производительности в режиме, близком к реальному времени, чтобы убедиться, что все работает правильно. |![Пример Live Metrics](./media/app-insights-overview/live-metrics-tn.png) |
| [**Аналитика в Application Insights**](app-insights-analytics.md)<br/>Получите ответы на сложные вопросы о производительности и использовании приложения с помощью этого мощного языка запросов. |![Пример аналитики](./media/app-insights-overview/analytics-tn.png) |
| [**Настройка оповещений в Application Insights**](app-insights-alerts.md)<br/>Автоматические оповещения адаптируются к стандартным шаблонам телеметрии приложения и активируются, когда что-то не соответствует стандартному шаблону. Также можно настроить оповещения для определенных уровней пользовательских или стандартных метрик. |![Пример оповещения](./media/app-insights-overview/alerts-tn.png) |
| [**Работа с Application Insights в Visual Studio**](app-insights-visual-studio.md)<br/>Просматривайте данные производительности в коде. Перейдите к коду из трассировки стека.|![Visual studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Использование данных Application Insights в Power BI**](app-insights-export-power-bi.md)<br/>Интегрируйте метрики использования с другими метриками бизнес-аналитики.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**Use the Application Insights REST API to build custom solutions**](https://dev.applicationinsights.io/) (Использование интерфейса REST API Application Insights для создания пользовательских решений)<br/>Напишите код для выполнения запросов с помощью метрик и необработанных данных.| ![Интерфейс REST API](./media/app-insights-overview/rest-tn.png) |
| [**Экспорт данных телеметрии из Application Insights**](app-insights-export-telemetry.md)<br/>Выполняйте массовый экспорт необработанных данных в хранилище по мере их поступления. |![экспорт.](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Как использовать Application Insights?

### <a name="monitor"></a>Монитор
Установите Application Insights в веб-приложении, настройте [доступность веб-тестов](app-insights-monitor-web-app-availability.md) и:

* настройте [панель мониторинга](app-insights-dashboards.md) для комнаты своей команды, чтобы следить за нагрузкой, скоростью реагирования и производительностью зависимостей, загрузки страниц и вызовов AJAX;
* узнавайте, какие запросы выполняются медленнее всех и какие чаще всего не выполняются;
* просматривайте [Live Stream](app-insights-live-stream.md) при развертывании новых выпусков, чтобы сразу же узнавать о любом снижении производительности.

### <a name="detect-diagnose"></a>Обнаружение и диагностика
При получении предупреждения или обнаружении проблемы:

* Оцените, сколько пользователей столкнулось с проблемами.
* сопоставляйте сбои с исключениями, вызовами зависимостей и трассировками;
* проверяйте журналы трассировки и копии стека.

### <a name="build-measure-learn"></a>Создание, измерение и обучение
Оцените эффективность каждой новой развертываемой функции:

* планируйте измерение того, как пользователи используют новые UX или бизнес-функции;
* записывайте пользовательскую телеметрию в свой код;
* основывайте следующий цикл разработки на объективных данных телеметрии.

## <a name="get-started"></a>Приступая к работе
Application Insights — одна из многих служб, размещенных в Microsoft Azure, и данные телеметрии отправляются в нее для анализа и представления. Поэтому, чтобы приступить к каким-либо действиям, вам потребуется подписка на [Microsoft Azure](http://azure.com). Плата за регистрацию не взимается, и если выбрать [тарифный план](https://azure.microsoft.com/pricing/details/application-insights/) "Базовый" Application Insights, то плата не будет взиматься, пока ваше приложение не начнет значительно использовать ресурсы. Если у вашей организации уже есть подписка, в нее можно добавить вашу учетную запись Майкрософт.

Начать работу можно несколькими способами. Начните с того, который вам лучше подходит. Остальные можно использовать позже.

* **Во время выполнения — инструментирование веб-приложения на сервере.** Не допускает обновление кода. Требуется административный доступ на сервер.
  * [**Локальные или размещенные на виртуальной машине службы IIS**](app-insights-monitor-performance-live-website-now.md);
  * [**веб-приложения или виртуальные машины Azure**](app-insights-monitor-performance-live-website-now.md);
  * [**J2EE**](app-insights-java-live.md).
* **Во время разработки — добавление Application Insights в код.** Таким образом вы получаете возможность записывать пользовательскую телеметрию, а также инструментировать серверные и классические приложения.
  * [Visual Studio](app-insights-asp-net.md) 2013 с обновлением 2 или более поздняя версия.
  * Java в [Eclipse](app-insights-java-eclipse.md) или [другие средства](app-insights-java-get-started.md);
  * [Node.js](app-insights-nodejs.md)
  * [другие платформы.](app-insights-platforms.md)
* **[Инструментирование веб-страниц](app-insights-javascript.md)** для получения сведений о просмотрах страниц, вызовах AJAX и других данных телеметрии на стороне клиента.
* **[Тесты доступности](app-insights-monitor-web-app-availability.md)** с наших серверов для регулярной проверки связи с вашим веб-сайтом.


## <a name="next-steps"></a>Дальнейшие действия
Приступите к работе во время выполнения с помощью:

* [сервера IIS;](app-insights-monitor-performance-live-website-now.md)
* [сервера J2EE.](app-insights-java-live.md)

Приступите к работе во время разработки с помощью:

* [ASP.NET](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Node.js](app-insights-nodejs.md)

## <a name="support-and-feedback"></a>Поддержка и обратная связь
* Вопросы и проблемы
  * [Устранение неполадок][qna]
  * [Форум MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [Stackoverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* Ваши предложения:
  * [UserVoice](https://visualstudio.uservoice.com/forums/357324)
* Блог:
  * [Блог Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

## <a name="videos"></a>Видеоролики

[![Анимационное ознакомительное видео](./media/app-insights-overview/video-front-1.png)](https://www.youtube.com/watch?v=fX2NtGrh-Y0)

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md

