<properties
    pageTitle="Application Insights для приложений iOS | Microsoft Azure"
	description="Анализ использования и производительности приложения iOS с помощью Application Insights."
	services="application-insights"
	documentationCenter="ios"
	authors="alancameronwills"
	manager="ronmart"/>

<tags
    ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/27/2015"
	ms.author="awills"/>

# Application Insights для приложений iOS

Visual Studio Application Insights позволяет отслеживать мобильные приложения для оценки частоты использования, характера событий и сбоев.

## Требования

Вам потребуется следующее:

* подписка на [Microsoft Azure](http://azure.com). Учетная запись Майкрософт для входа, которая уже может быть у вас для Windows, XBox Live или других облачных служб Майкрософт.
* Xcode 6 или более поздней версии.
* Пакет SDK запускается на устройствах с iOS версии 6.0 или более поздней.

## Создание ресурса Application Insights

На [портале Azure][portal] создайте новый ресурс Application Insights. Выберите вариант iOS.

![Последовательно выберите «Создать», «Службы для разработчиков», «Application Insights»](./media/app-insights-ios/11-new.png)

В отобразившейся колонке будут содержатся данные о производительности и использовании приложения. Чтобы вернуться к ней после следующего входа в Azure, найдите соответствующую плитку на начальном экране. Или щелкните «Обзор», чтобы найти ее.

## Загрузка Application Insights для Mac

(Если это еще не сделано).

1. Скачайте [Application Insights для Mac](http://go.microsoft.com/fwlink/?LinkID=533209).

2. Извлеките ZIP-файл.

3. Щелкните значок приложения для запуска Application Insights для Mac.

## <a name="signin"></a>Войдите в Azure

1. Щелкните **Войти**.

2. Войдите в систему с использованием учетной записи Azure.

## Установка пакета SDK в приложении

1. Щелкните **Интегрировать**, чтобы начать интеграцию пакета SDK.

2. Выберите проект Xcode в списке или щелкните **Открыть другие**, чтобы найти проект, а затем щелкните **Интегрировать**.

3. Выберите папку для пакета SDK для Application Insights, а затем щелкните **Установить**.

4. Добавьте следующий сценарий выполнения в этапы сборки (см. информацию о [добавлении этапа сценария выполнения](http://hockeyapp.net/help/runscriptbuildphase/)).

5. Добавьте отсутствующие платформы в свой проект Xcode.

6. Перетащите платформу Application Insights в проект Xcode, а затем щелкните **Далее**.

7. Выберите **Интегрировать пакет SDK в целевой объект** для целевого объекта.

8. Щелкните **Создать новый компонент** для создания приложения на портале Application Insights.

9. Выберите подписку, группу ресурсов и введите имя компонента. В большинстве случаев это имя должно совпадать с именем приложения. Подтвердите операцию, нажав кнопку **Создать ресурс**.

10. Убедитесь, что выбран правильный компонент, а затем щелкните **Далее**.

11. Измените исходный код, как показано в мастере, а затем щелкните **Готово**.

12. Запустите свое приложение в имитаторе iOS, нажав кнопку **Сборка и запуск**.

## Вставка вызовов телеметрии

Как только `[MSAIApplicationInsights start]` будет вызван, пакет SDK начнет отслеживать сеансы, представления страниц и любые необработанные исключения или сбои.

Дополнительные события можно добавить следующим образом:

    // Send an event with custom properties and measuremnts data
    [MSAITelemetryManager trackEventWithName:@"Hello World event!"
                                  properties:@{@"Test property 1":@"Some value",
                                             @"Test property 2":@"Some other value"}
                                 measurements:@{@"Test measurement 1":@(4.8),
                                             @"Test measurement 2":@(15.16),
                                             @"Test measurement 3":@(23.42)}];

    // Send a message
    [MSAITelemetryManager trackTraceWithMessage:@"Test message"];

    // Manually send pageviews (note: this will also be done automatically)
    [MSAITelemetryManager trackPageView:@"MyViewController"
                               duration:300
                             properties:@{@"Test measurement 1":@(4.8)}];

    // Send custom metrics
    [MSAITelemetryManager trackMetricWithName:@"Test metric"
                                        value:42.2];

## Просмотр данных в Application Insights

Вернитесь к http://portal.azure.com и перейдите к ресурсу Application Insights.

Щелкните **Поиск**, чтобы открыть [Поиск по журналу диагностики][diagnostic], — здесь события появляются в первую очередь. Если ничего не отображается, подождите одну-две минуты и щелкните **Обновить**.

![Щелкните «Поиск по журналу диагностики»](./media/app-insights-ios/21-search.png)

По мере использования приложения данные будут отображаться в колонке обзора.

![Колонка «Обзор»](./media/app-insights-ios/22-oview.png)

Щелкните любую диаграмму, чтобы получить более подробную информацию. Например, в случае сбоя:

![Щелкните диаграмму сбоев](./media/app-insights-ios/23-crashes.png)
## <a name="usage"></a>Дальнейшие действия

[Отслеживание использования приложения][track]

[Поиск по журналу диагностики][diagnostic]

[Обозреватель метрик][metrics]

[Устранение неполадок][qna]


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=August15_HO9-->