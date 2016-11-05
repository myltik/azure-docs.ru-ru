---
title: Интеграция пакета SDK универсальных приложений для Windows
description: Интеграция универсальных приложений для Windows с пакетом SDK для Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal

---
# Интеграция пакета SDK универсальных приложений для Windows для Azure Mobile Engagement
В этом документе описываются все параметры интеграции и конфигурации, доступные для пакета SDK универсальных приложений для Windows для Azure Mobile Engagement.

## Предварительные требования
Прежде чем приступить к этому учебнику, необходимо изучить [наш 15-минутный учебник](mobile-engagement-windows-store-dotnet-get-started.md).

## Дополнительные функции
### Функции отчетов
Можно добавить такие функции:

1. [дополнительные параметры отчетов;](mobile-engagement-windows-store-advanced-reporting.md)
2. [Расширенные параметры конфигурации](mobile-engagement-windows-store-advanced-configuration.md)

### Уведомления
[Как интегрировать Reach (Notifications) в универсальное приложение для Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

### Реализация плана добавления тегов:
[Как использовать API для расширенного добавления тегов Mobile Engagement в универсальном приложении для Windows](mobile-engagement-windows-store-use-engagement-api.md)

## Заметки о выпуске
### 3\.4.0 (19.04.2016)
* Улучшения наложения Reach.
* Добавлен API "TestLogLevel" для включения, отключения или фильтрации журналов консоли, созданных с помощью пакета SDK.
* Исправлены уведомления в действиях, предназначенные для первого действия, которое не отображается при запуске приложения.

Информацию о предыдущих версиях см. в [полных заметках о выпуске](mobile-engagement-windows-store-release-notes.md).

## Процедуры обновления
Если вы уже интегрировали в приложение старую версию службы Engagement, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, то вам понадобится выполнить несколько процедур. Посмотрите полные [процедуры обновления](mobile-engagement-windows-store-upgrade-procedure.md). Например, при миграции с версии 0.10.1 в версию 0.11.0 необходимо сначала выполнить процедуру миграции «с 0.9.0 в 0.10.1», а затем процедуру миграции «с 0.10.1 в 0.11.0».

### С 3.3.0 в 3.4.0
#### Журналы тестирования
Теперь журналы консоли, созданные с помощью пакета SDK, можно включать, отключать или фильтровать. Для настройки обновите свойство `EngagementAgent.Instance.TestLogEnabled`, присвоив ему одно из значений, доступных в перечислении `EngagementTestLogLevel`, например:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### Ресурсы
Было улучшено наложение Reach. Оно входит в состав ресурсов пакета SDK NuGet.

Во время обновления до новой версии пакета SDK можно выбрать возможность сохранения существующих файлов из папки наложения для ресурсов.

* Если вы используете предыдущее наложение или интегрируете элементы `WebView` вручную, можно оставить существующие файлы — наложение будет работать.
* Чтобы выполнить обновление до нового наложения, замените всю папку `overlay` из ресурсов на новую из пакета SDK (приложения UWP: после обновления можно получить новую папку наложения из %USERPROFILE%\\.nuget\\packages\\MicrosoftAzure.MobileEngagement\\3.4.0\\content\\win81\\Resources).

> [!WARNING]
> Новое наложение перезапишет изменения, внесенные в предыдущую версию.
> 
> 

### Обновление предыдущих версий
См. статью [Процедуры обновления](mobile-engagement-windows-store-upgrade-procedure.md).

<!---HONumber=AcomDC_0817_2016-->