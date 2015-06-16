<properties 
	pageTitle="Просмотр журналов трассировки .NET в Application Insights" 
	description="Поиск журналов, созданных с помощью Trace, Log4Net или NLog." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/27/2015" 
	ms.author="awills"/>
 
# Просмотр журналов трассировки .NET в Application Insights  

Если вы используете NLog, log4Net или System.Diagnostics.Trace для диагностической трассировки в приложении ASP.NET, журналы этих платформ можно передавать в [Visual Studio Application Insights][start], где их можно искать и просматривать. Журналы будут объединены с другими данными телеметрии, поступающими из вашего приложения, давая возможность определять трассировки, связанные с обработкой каждого запроса пользователя, и сопоставлять их с другими событиями и отчетами об исключениях.

С помощью пакета SDK Application Insights можно также записать трассировки журналов, отчеты о событиях и исключениях.

Если вы еще не выполнили [настройку Application Insights для своего проекта][start], сделайте это сейчас.


##  Установка адаптера для вашей платформы ведения журнала

Если используется платформа ведения журналов — log4Net, NLog или System.Diagnostics.Trace — можно установить адаптер, который будет отправлять эти журналы Application Insights вместе с другими данными телеметрии.

1. Если вы планируете использовать log4Net или NLog, установите его в свой проект. 
2. В обозревателе решений щелкните правой кнопкой мыши ваш проект и выберите **Управление пакетами NuGet**.
3. Выберите Online > All, выберите **Include Prerelease** и выполните поиск по тексту «Microsoft.ApplicationInsights»

    ![Получите предварительную версию соответствующего адаптера](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)

4. Выберите соответствующий пакет из списка.
  + Microsoft.ApplicationInsights.TraceListener (для захвата вызовов System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

Пакет NuGet устанавливает необходимые сборки, а также вносит изменения в файл web.config или app.config.

#### Вставка вызовов журнала диагностики

При использовании System.Diagnostics.Trace типичный вызов будет выглядеть следующим образом:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

если вы предпочитаете log4net или NLog:

    logger.Warn("Slow response - database01");


## Непосредственное использование API трассировки

API трассировки в Application Insights можно вызывать напрямую. Адаптеры ведения журналов используют этот API.

Например:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");




## Просмотр журналов

В колонке обзора приложения на [портале Application Insights][portal] выберите элемент [Поиск][diagnostic].

![В Application Insights выберите элемент «Поиск»](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Поиск по журналу диагностики](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Можно, например:

* фильтровать трассировки журнала или элементы с определенными свойствами;
* просматривать подробные сведения конкретного элемента;
* искать другие данные телеметрии, относящиеся к тому же запросу пользователя (то есть с тем же идентификатором операции); 
* сохранять конфигурацию этой страницы в списке избранного.


## Дальнейшие действия

[Диагностика ошибок и исключений в ASP.NET][exceptions]

[Подробнее о поиске данных диагностики][diagnostic]



## Устранение неполадок

### <a name="emptykey"></a>Появляется сообщение об ошибке «ключ инструментирования не может быть пустым»

Вероятно, установка пакета Nuget адаптера ведения журнала была выполнена без установки Application Insights.

В обозревателе решений щелкните правой кнопкой мыши `ApplicationInsights.config` и выберите **Обновить Application Insights**. Появится диалоговое окно с предложением войти в Azure и создать новый ресурс Application Insights или повторно использовать уже существующий. Это должно исправить проблему.

### Я могу видеть трассировки в поиске диагностических данных, но не другие события

Иногда для получения всех событий и запросов через конвейер может потребоваться некоторое время.

### <a name="limits"></a>Какой объем данных сохраняется?

До 500 событий в секунду от каждого приложения. События сохраняются в течение семи дней.

## <a name="add"></a>Дальнейшие действия

* [Настройка тестов доступности и скорости реагирования][availability]
* [Устранение неполадок][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md

<!---HONumber=58--> 