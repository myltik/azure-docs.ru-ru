<properties
    pageTitle="Аналитика для приложений Android | Microsoft Azure"
    description="Анализ использования и производительности приложения Android. "
    services="application-insights"
    documentationCenter="android"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="11/14/2015"
    ms.author="awills"/>

# Аналитика для приложений Android

Visual Studio Application Insights позволяет отслеживать мобильные приложения для оценки частоты использования, характера событий и сбоев.

> [AZURE.NOTE]Для создания отчетов об аварийном завершении, аналитики, распространения и обратной связи рекомендуем использовать [HockeyApp](http://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk).

## Требования

Вам потребуется следующее:

* подписка на [Microsoft Azure](http://azure.com). Вход выполняется с помощью учетной записи Майкрософт, которая уже может быть у вас для Windows, XBox Live или других облачных служб Майкрософт;
* Android Studio
* пакет Android SDK версии 9 и выше.

## Создание ресурса Application Insights

На [портале Azure][portal] создайте новый ресурс Application Insights. Выберите вариант Android.

![Последовательно выберите «Создать», «Службы для разработчиков», «Application Insights»](./media/app-insights-android/11-new.png)

В отобразившейся колонке будут содержатся данные о производительности и использовании приложения. Чтобы возвратиться к нему после очередного входа в Azure, найдите соответствующую плитку на начальном экране. Или щелкните «Обзор», чтобы найти ее.

## Настройка

Следуйте указаниям в этом [руководстве по установке](https://github.com/Microsoft/ApplicationInsights-Android#-3-setup).


## Использование пакета SDK

Инициализируйте пакет SDK и начните отслеживать телеметрию.

Добавьте следующее утверждение import в корневое действие вашего приложения:

```java

     import com.microsoft.applicationinsights.library.ApplicationInsights;
```

Кроме того, добавьте следующий код в вызов `onCreate` действия:

```java

    ApplicationInsights.setup(this.getApplicationContext(), this.getApplication());
    ApplicationInsights.start();
```

Как только функция `ApplicationInsights.start()` будет вызвана, пакет SDK начнет отслеживать действия, связанные с жизненным циклом приложения Android, и все необработанные исключения.

> [AZURE.NOTE]События жизненного цикла приложения собираются только в пакете SDK для Android версии 15 и более поздней (Ice Cream Sandwich+).

Кроме того, могут собираться пользовательские события, трассировки, метрики и обработанные исключения. Используйте любой из [API Application Insights][api] для отправки телеметрии.

* TrackEvent(eventName) для других действий пользователя;
* TrackTrace(logEvent) для [ведения журнала диагностики][diagnostic];
* TrackException(exception) в предложениях catch;
* TrackMetric(имя, значение) в фоновой задаче для отправки периодических отчетов с метриками, не присоединенными к определенным событиям.

Следующий код приведен в качестве примера инициализации и сбора данных телеметрии вручную:

```java

    public class MyActivity extends Activity {

      @Override
      protected void onCreate(Bundle savedInstanceState) {

        ApplicationInsights.setup(this);
        //... other initialization code ...//
        ApplicationInsights.start();

        // track telemetry data
        TelemetryClient client = TelemetryClient.getInstance();
        HashMap<String, String> properties = new HashMap<String, String>();
        properties.put("property1", "my custom property");
        client.trackEvent("sample event", properties);
        client.trackTrace("sample trace");
        client.trackMetric("sample metric", 3);
        client.trackHandledException(new Exception("sample exception"));
      }
    }
```

## Запуск проекта

Запустите приложение (SHIFT+F10 в Windows, CTRL+R в OS X) для формирования телеметрии.

## Просмотр данных в Application Insights

Вернитесь к http://portal.azure.com и перейдите к ресурсу Application Insights.

Нажмите кнопку **Поиск**, чтобы открыть раздел [Поиск по журналу диагностики][diagnostic], — здесь события появляются в первую очередь. Если ничего не отображается, подождите одну-две минуты и щелкните **Обновить**.

![Щелкните «Поиск по журналу диагностики»](./media/app-insights-android/21-search.png)

По мере использования приложения данные отображаются в колонке обзора.

![Колонка «Обзор»](./media/app-insights-android/22-oview.png)

Щелкните любую диаграмму, чтобы получить более подробную информацию. Например, в случае сбоя:

![Щелкните диаграмму сбоев](./media/app-insights-android/23-crashes.png)


## <a name="usage"></a>Дальнейшие действия

[Отслеживание использования приложения][track]

[Поиск по журналу диагностики][diagnostic]

[Обозреватель метрик][metrics]

[Устранение неполадок][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Nov15_HO4-->