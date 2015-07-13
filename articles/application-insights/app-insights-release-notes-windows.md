<properties 
	pageTitle="Заметки о выпуске Application Insights" 
	description="Последние обновления." 
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
	ms.date="06/18/2015" 
	ms.author="sergkanz"/>
 
# Заметки о выпуске для пакета SDK Application Insights для Windows Phone и Магазина Windows

[Пакет SDK Application Insights](app-insights-windows-get-started.md) отправляет сведения о телеметрии, относящиеся к вашему работающему приложению, в службу [Application Insights](http://azure.microsoft.com/services/application-insights/), где вы можете проанализировать использование и производительность приложения.


#### Установка пакета SDK в приложении

См. статью [Приступая к работе со службой Application Insights для приложений Windows Phone и Магазина Windows](app-insights-windows-get-started.md).

#### Обновление до последнего пакета SDK 

* Создайте копию файла ApplicationInsights.config, чтобы сохранить выполненные вами настройки.
* В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите элемент **Управление пакетами NuGet**.
* Настройте фильтр, чтобы отображались установленные пакеты. 
* Выберите установленные пакеты Application Insights и щелкните элемент «Обновить».
* Сравните старую и новую версии файла ApplicationInsights.config. Снова объедините настройки, выполненные вами в старой версии.
* Перестройте свое решение.


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

<!---HONumber=62-->