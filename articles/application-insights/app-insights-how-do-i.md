<properties 
	pageTitle="Как работать с Application InsightsРесурсы в Application Insights" 
	description="Вопросы и ответы об Application Insights" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Как работать с Application Insights

## Получать уведомление по электронной почте, если...

### Уведомлять меня по электронной почте, если сайт выходит из строя

Настройте [веб-тест доступности](app-insights-monitor-web-app-availability.md).

### Уведомлять меня по электронной почте, если сайт перегружен

Настройте [оповещение](app-insights-alerts.md) для **времени ответа от сервера**. Пороговое значение может быть в пределах от 1 до 2 секунд.

![](./media/app-insights-how-do-i/030-server.png)

Приложение может также демонстрировать признаки нагрузки, выдавая коды ошибок. Настройте оповещение при **неудачных запросах**.

Если вы хотите настроить оповещение при **исключениях сервера**, для просмотра данных может потребоваться [дополнительная настройка](app-insights-asp-net-exceptions.md).


### Уведомлять меня по электронной почте о событиях в приложении

Предположим, что вы хотите получать уведомления по электронной почте при возникновении определенных событий. Application Insights не предоставляет эту функцию напрямую, но позволяет [отправлять оповещение, если метрика превысит пороговое значение](app-insights-alerts.md).

Оповещения можно настроить для [пользовательских метрик](app-insights-api-custom-events-metrics.md#track-metric), но не для пользовательских событий. Напишите код, который будет увеличивать метрику при возникновении соответствующего события:

    telemetry.TrackMetric("Alarm", 10);

или:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Так как оповещения имеют два состояния, отправьте минимальное значение, при котором оповещение должно быть отменено:

    telemetry.TrackMetric("Alarm", 0.5);

Создайте диаграмму в [обозревателе метрик](app-insights-metric-explorer.md), чтобы увидеть оповещение:

![](./media/app-insights-how-do-i/010-alarm.png)

Теперь настройте оповещение таким образом, чтобы оно отправлялось в случае, когда метрика превышает среднее значение за короткий период:


![](./media/app-insights-how-do-i/020-threshold.png)

Установите минимальный период усреднения.

Вы будете получать уведомления по электронной почте, если метрика поднимется выше или упадет ниже порогового значения.

Учитывайте следующие факторы.

* Оповещение может находиться в двух состояниях: «оповещение» и «исправен». Состояние оценивается только при получении метрики.
* Электронное письмо отправляется только при изменении состояния. Вот почему необходимо отправлять как верхнюю, так и нижнюю метрику. 
* Для оценки оповещениям берется среднее значений, полученных за предыдущий период. Это происходит при каждом получении метрики, поэтому сообщения электронной почты могут отправляться чаще установленной вами периодичности.
* Поскольку сообщения электронной почты отправляются и при состоянии «оповещение», и при состоянии «исправен», считайте это разовое событие условием с двумя состояниями. Например, вместо события «задание завершено» создайте условие «задание выполняется», при котором сообщения электронной почты будут отправляться при запуске и завершении задания.
 
## Фильтр версий приложения

При публикации новой версии приложения имеет смысл отделить телеметрию от других версий.

Свойство «Версия приложения» можно настроить так, чтобы можно было фильтровать результаты [поиска](app-insights-diagnostic-search.md) и [обозревателя метрик](app-insights-metrics-explorer.md).


![](./media/app-insights-how-do-i/050-filter.png)

Свойство «Версия приложения» можно настроить различными способами.

* Напрямую:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`

* Вставьте эту строку в [инициализатор телеметрии](app-insights-api-custom-events-metrics.md#telemetry-initializers), чтобы обеспечить согласованность всех экземпляров TelemetryClient.

* [ASP.NET] Задайте версию в `BuildInfo.config`. Веб-модуль берет номер версии из узла BuildLabel. Включите этот файл в проект и не забудьте установить свойство «Всегда копировать» в обозревателе решений.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Настройте автоматическое создание файла BuildInfo.config в MSBuild. Для этого добавьте в CSPROJ-файл несколько строк:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
    ```

    Этот код создайте файл с именем *yourProjectName*.BuildInfo.config. В процессе публикации он переименовывается в BuildInfo.config.

    При создании сборки с помощью Visual Studio в подпись включается заполнитель (AutoGen\_...). Если используется MSBuild, в подписи указывается правильный номер версии.

    Чтобы разрешить MSBuild генерировать номера версий, задайте версию вида `1.0.*` в файле AssemblyReference.cs.

<!---HONumber=Oct15_HO1-->