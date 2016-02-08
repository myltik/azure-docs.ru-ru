<properties 
	pageTitle="Заметки о выпуске для адаптеров ведения журнала Application Insights" 
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
	ms.date="12/21/2015" 
	ms.author="abaranch"/>
 
# Заметки о выпуске для адаптеров ведения журнала для .NET

Если вы уже используете платформу ведения журналов, например log4net, NLog или System.Diagnostics.Trace, вы можете получать эти журналы и отправлять их в [Application Insights](https://azure.microsoft.com/services/application-insights/), а там сопоставить трассировку журналов с действиями пользователя, исключениями и другими событиями.


#### Начало работы

См. раздел [Журналы, исключения и пользовательские средства диагностики для ASP.NET в Application Insights](app-insights-search-diagnostic-logs.md).

## Версия 1.2.6

- Исправления ошибок
- log4Net: сбор свойств log4net как пользовательских свойств. UserName больше не является пользовательским свойством (оно собирается как telemetry.Context.User.Id). Timestamp больше не является пользовательским свойством.
- NLog: сбор свойств NLog как пользовательских свойств. SequenceID больше не является пользовательским свойством (оно собирается как telemetry.Sequence). Timestamp больше не является пользовательским свойством. 

## Версия 1.2.5
- Первая версия с открытым исходным кодом. Справочник по API Application Insights версии 1.2.3 или выше.

<!---HONumber=AcomDC_0128_2016-->