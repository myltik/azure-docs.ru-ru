<properties
    pageTitle="Аналитика для приложений iOS | Microsoft Azure"
    description="Анализ использования и производительности приложения iOS. "
    services="application-insights"
    documentationCenter="ios"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/15/2015"
    ms.author="awills"/>

# Аналитика для приложений iOS

Visual Studio Application Insights позволяет отслеживать мобильные приложения для оценки частоты использования, характера событий и сбоев.


> [AZURE.NOTE]Для создания отчетов об аварийном завершении, аналитики, распространения и обратной связи рекомендуем использовать [HockeyApp](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x/hockeyapp-for-ios).

## Требования

Вам потребуется следующее:

* подписка на [Microsoft Azure](http://azure.com). Учетная запись Майкрософт для входа, которая уже может быть у вас для Windows, XBox Live или других облачных служб Майкрософт.
* Xcode 6 или более поздней версии.
* Пакет SDK запускается на устройствах с iOS версии 6.0 или более поздней.

## Создание ресурса Application Insights

На [портале Azure][portal] создайте новый ресурс Application Insights. Выберите вариант iOS.

![Последовательно выберите «Создать», «Службы для разработчиков», «Application Insights»](./media/app-insights-ios/11-new.png)

В отобразившейся колонке будут содержатся данные о производительности и использовании приложения. Чтобы возвратиться к нему после очередного входа в Azure, найдите соответствующую плитку на начальном экране. Или щелкните «Обзор», чтобы найти ее.

## Настройка

Чтобы установить пакет SDK в приложении, следуйте инструкциям в [руководстве по установке](https://github.com/Microsoft/ApplicationInsights-iOS#setup).

## Просмотр данных в Application Insights

Запустите приложение, чтобы создать данные телеметрии.

Вернитесь к http://portal.azure.com и перейдите к ресурсу Application Insights.

Нажмите кнопку **Поиск**, чтобы открыть раздел [Поиск по журналу диагностики][diagnostic], — здесь события появляются в первую очередь. Если ничего не отображается, подождите одну-две минуты и щелкните **Обновить**.

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

<!---HONumber=Nov15_HO4-->