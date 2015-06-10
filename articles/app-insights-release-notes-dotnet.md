<properties 
	pageTitle="Заметки о выпуске Application Insights" 
	description="Последние обновления." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="sergkanz"/>
 
# Заметки о выпуске пакета SDK Application Insights для .NET

[Использование пакета SDK для .NET](app-insights-start-monitoring-app-health-usage.md)

## Версия 0.16 

28.04.2015 (предварительная)

- Теперь пакет SDK включает поддержку целевой платформы DNX для мониторинга приложений на [платформе .NET Core](http://www.dotnetfoundation.org/NETCore5).
- Экземпляры ```TelemetryClient``` больше не кэшируют ключ инструментирования. Теперь, если ключ инструментирования не задан в ```TelemetryClient``` явным образом, ```InstrumentationKey``` вернет значение null. Устраняет проблему при задании ```TelemetryConfiguration.Active.InstrumentationKey``` после сбора данных телеметрии. Модули телеметрии, такие как сборщик зависимостей, коллекция данных веб-запросов и сборщик счетчиков производительности будут использовать новый ключ инструментирования.

## Версия 0.15

- Новое свойство ```Operation.SyntheticSource``` теперь доступно в ```TelemetryContext```. Теперь можно отметить элементы телеметрии, такие как «трафик нереального пользователя», и указать, как был создан этот трафик. Например, установив это свойство, можно легко отличить трафик автоматизации тестирования от трафика нагрузочного теста.
- Логика канала перемещена в отдельный NuGet, который называется Microsoft.ApplicationInsights.PersistenceChannel. Канал по умолчанию теперь называется InMemoryChannel
- Новый метод ```TelemetryClient.Flush``` позволяет синхронно очищать элементы телеметрии из буфера

## Версия 0.13

Для более старых версий заметки о выпуске не доступны.

<!---HONumber=58-->