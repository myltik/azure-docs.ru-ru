<properties 
	pageTitle="Устранение неполадок Application Insights в устройствах Windows" 
	description="Руководство по устранению неполадок, а также вопросы и ответы." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/17/2015" 
	ms.author="awills"/>
 
# Устранение неполадок и вопросы и ответы по Application Insights для устройств Windows

Возникают вопросы или проблемы в связи с использованием [Visual Studio Application Insights в Windows][windows]? Ниже приведен ряд советов.



## Нет данных 

*Мне удалось успешно добавить Application Insights и запустить приложение, но данные не появились на портале.*

* Подождите минуту и нажмите «Обновить». В настоящее время обновление не происходит автоматически.
* Убедитесь, что у вас есть ключ инструментирования, определенный в файле ApplicationInsights.config, и что он совпадает с ключом на портале Application Insights. Для просмотра ключа щелкните «Основные компоненты» в колонке «Обзор».
* Убедитесь, что ваше приложение [запрашивает исходящий доступ к сети](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx).
* Существует ли брандмауэр между вашим эмулятором или тестовым устройством и порталом Application Insights? Вам, возможно, потребуется открыть TCP-порты 80 и 443 для исходящего трафика, идущего на сайты dc.services.visualstudio.com и f5.services.visualstudio.com.
* На начальном экране Microsoft Azure посмотрите на карту состояния службы. Если есть какие-либо предупреждения, дождитесь возвращения всех модулей в состояние ОК, затем закройте и заново откройте модуль приложения Application Insights.


#### Ранее видимые данные перестали отображаться

* Проверьте [блог состояний](http://blogs.msdn.com/b/applicationinsights-status/).
* Вы достигли месячной квоты точек данных? Чтобы выяснить это, см. разделы «Параметры», «Квота» и «Расценки». Если вы достигли квоты, вы можете изменить свой тарифный план или заплатить за дополнительную емкость. См. [таблицу расценок](http://azure.microsoft.com/pricing/details/application-insights/).


## Как добавить Application Insights в универсальное приложение?

Вручную добавьте пакеты NuGet для каждого проекта устройства в вашем решении. См. раздел [Приступая к работе с универсальными приложениями][universal].

## Отключение телеметрии

*Как отключить сбор данных телеметрии?*

В коде:

    TelemetryConfiguration config = TelemetryConfiguration.Active;
    config.TrackingIsDisabled = true;

## Изменение целевого ресурса

*Как изменить ресурс Application Insights, в который проект отправляет данные?*

В новой колонке «Обзор» Application Insights откройте «Основные компоненты» и скопируйте ключ инструментирования.

Вставьте ключ в файл ApplicationInsights.config в `<InstrumentationKey>` узла.

Или, если вы хотите изменить целевые объекты во время выполнения, используйте:

     var telemetry = new TelemetryClient();
     telemetry.Context.InstrumentationKey = newKey;
    
## Как выполнять мониторинг приложения клиент-сервер?

Это можно сделать двумя способами:

* Создайте два ресурса Application Insights (с разными ключами инструментирования) для клиента и сервера. Но создайте их в той же группе ресурсов Azure. Это позволяет легко переключаться между первым и вторым.
* Используйте один ресурс Application Insights и поместите его ключ инструментирования в проекты клиента и сервера. Затем вы сможете сопоставлять метрики и события из двух источников.

Чтобы согласовывать события в клиенте и сервере, создайте код операции для каждого запроса. Передавайте его между клиентом и сервером, добавляйте телеметрию на обоих концах:

    telemetry.Context.OperationId = opid;


## Начальный экран Azure

*Я просматриваю [портал Azure](http://portal.azure.com). Скажет ли карта что-нибудь о приложении?*

Нет, она показывает работоспособность серверов Azure по всему миру.

*Как найти данные о приложении на начальной доске (начальном экране) Azure?*

Если вы [уже настроили приложение Application Insights][windows], нажмите кнопку «Обзор», выберите Application Insights и выберите ресурс, созданный для вашего приложения. Чтобы попасть туда быстрее в будущем, вы можете закрепить ресурс на начальной доске.

## Хранение данных 

*Как долго данные хранятся на портале? Защищен ли он?*

См. раздел [Хранение данных и конфиденциальность][data].

## Дальнейшие действия

*Служба Application Insights настроена для серверного приложения Java. Что еще можно сделать?*

* [Отслеживать доступность веб-страниц][availability]
* [Отслеживать использование веб-страниц][usage]
* [Отслеживать использование и диагностировать проблемы в приложениях для устройств][platforms]
* [Написать код для отслеживания использования приложения][track]
* [Запись журналов диагностики][javalogs]


## Справка

* [Переполнение стека](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-custom-events-metrics-api.md
[universal]: app-insights-windows-get-started.md#universal
[usage]: app-insights-web-track-usage.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=July15_HO4-->