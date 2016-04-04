<properties
	pageTitle="Общие сведения о диагностике Azure"
	description="Диагностику Azure можно использовать для отладки, оценки производительности, мониторинга, а также анализа трафика в облачных службах, на виртуальных машинах и в Service Fabric."
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>


# Что такое система диагностики Microsoft Azure


Система диагностики Azure позволяет выполнять сбор диагностических данных в развернутом приложении. Можно использовать модуль диагностики из различных источников. В настоящее время поддерживаются и веб-роли, и рабочие роли облачной службы Azure, и виртуальные машины Azure под управлением Microsoft Windows и Service Fabric. Другие службы Azure располагают собственной диагностикой.

## Собираемые данные

Система диагностики Azure может собирать следующие типы данных:

Источник данных|Описание
---|---
Счетчики производительности | Системные и пользовательские счетчики производительности
Журналы приложений | Сообщения трассировки, записанные вашим приложением
Журналы событий Windows | Информация, отправляемая системой ведения журналов событий Windows
Источник событий .NET | События записи кода с использованием класса .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)
Журналы IIS | Информация о веб-сайтах IIS
Трассировка событий Windows на основе манифестов | События трассировки событий Windows, созданные любым процессом
Аварийные дампы | Информация о состоянии процесса в случае сбоя приложения
Пользовательские журналы ошибок | Журналы, созданные вашим приложением или службой
Журналы инфраструктуры системы диагностики Azure|Информация о самой системе диагностики

Модуль диагностики Azure может переносить эти данные в учетную запись хранения Azure и отправлять их через такие службы, как [Application Insights](./application-insights/app-insights-cloudservices.md). Данные можно использовать для отладки и устранения неполадок, измерения производительности, мониторинга использования ресурсов, анализа трафика и планирования производительности, а также в целях аудита.


## Управление версиями
См. [Журнал версий системы диагностики Azure](azure-diagnostics-versioning-history.md).

## Дальнейшие действия
Выберите службы, в которых вы хотите собирать данные диагностики, и выполните действия, описанные в перечисленных ниже статьях. Справку по отдельным задачам см. в общих статьях о диагностике Azure.

## Веб-приложения
Обратите внимание на то, что в веб-приложениях диагностика Azure не используется. Ищите аналогичные данные в [веб-приложениях](./app-service-web/web-sites-enable-diagnostic-log.md).

## Облачные службы с использованием диагностики Azure
- Если используется Visual Studio, см. статью [Использование Visual Studio для отслеживания приложения облачных служб](./vs-azure-tools-debug-cloud-services-virtual-machines.md). В остальных случаях см. следующие статьи:
- [Мониторинг облачных служб с помощью диагностики Azure](./cloud-services/cloud-services-how-to-monitor.md)
- [Настройка диагностики Azure в приложении облачных служб](./cloud-services/cloud-services-dotnet-diagnostics.md)

Более подробные сведения см. в статьях:
- [Использование диагностики Azure с Application Insights для облачных служб](./application-insights/app-insights-cloudservices.md)
- [Трассировка потока в приложении облачных служб с помощью системы диагностики Azure](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [Использование PowerShell для настройки диагностики в облачных службах](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)


## Виртуальные машины с использованием диагностики Azure
- Если используется Visual Studio, см. статью [Использование Visual Studio для отслеживания виртуальных машин Azure](./vs-azure-tools-debug-cloud-services-virtual-machines.md). В остальных случаях см. следующие статьи:
- [Настройка диагностики Azure на виртуальной машине Azure](./virtual-machines-dotnet-diagnostics.md)

Более подробные сведения см. в статьях:
- [Использование PowerShell для настройки диагностики на виртуальных машинах Azure](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)
- [Создание виртуальной машины Windows с мониторингом и диагностикой с использованием шаблона диспетчера ресурсов Azure](./virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## Service Fabric с использованием диагностики Azure
Начните со статьи [Мониторинг приложения Service Fabric](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). В дереве навигации слева от нее вы найдете ссылки на множество других статей по диагностике Service Fabric.

## Общие статьи о диагностике Azure
- [Схема конфигурации системы диагностики Azure](https://msdn.microsoft.com/library/azure/mt634524.aspx) — узнайте, как изменить файл схемы для сбора и маршрутизации диагностических данных. Для изменения файла схемы можно также использовать Visual Studio.
- [Как диагностические данные Azure хранятся в хранилище Azure](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) — узнайте названия таблиц и BLOB-объектов, в которые записываются диагностические данные.
- Узнайте, как [использовать счетчики производительности в Azure](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
- Узнайте, как [направлять данные диагностики Azure в Application Insights](./azure-diagnostics-configure-applicationinsights.md).
- Если возникнут проблемы с запуском диагностики или поиском данных в таблицах хранилища Azure, см. статью [Устранение неполадок системы диагностики Azure](./azure-diagnostics-troubleshooting.md).

<!---HONumber=AcomDC_0323_2016-->