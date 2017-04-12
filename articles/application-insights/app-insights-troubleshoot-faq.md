---
title: "Устранение неполадок и ответы на вопросы по Azure Application Insights | Документация Майкрософт"
description: "Что-то в Azure Application Insights непонятно или не работает? Попробуйте здесь."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 35f57e1046597d30adb65b81b1e73226cb08c05f
ms.lasthandoff: 04/12/2017


---
# <a name="questions---application-insights-for-aspnet"></a>Вопросы — Application Insights для ASP.NET
## <a name="configuration-problems"></a>Проблемы с конфигурацией
*У меня не получается настроить компоненты, о которых идет речь в таких статьях:*

* [Troubleshooting no data - Application Insights for .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [раздел "Устранение неполадок"](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Настройка системы диагностики Azure для входа в Application Insights](app-insights-azure-diagnostics.md)
* [Устранение неполадок, а также вопросы и ответы по Application Insights для Java](app-insights-java-troubleshoot.md)
* [другие платформы.](app-insights-platforms.md)

*Я не получаю данные с моего сервера*

* [Настройка исключений брандмауэра](app-insights-ip-addresses.md)
* [Настройка сервера ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Настройка сервера Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Можно ли использовать Application Insights с...?
[Ознакомьтесь со статьей о платформах.][platforms]

## <a name="is-it-free"></a>Предоставляется ли бесплатно?

Да, для экспериментальных целей. По условиям тарифного плана "Базовый" приложение может отправлять определенный лимит данных каждый месяц бесплатно. Бесплатный лимит достаточен для разработки и публикации приложения для небольшого числа пользователей. Можно задать ограничение, запрещающее обработку данных сверх заданного лимита.

План "Корпоративный" нужен, чтобы получить определенные функции, такие как непрерывный экспорт. В этом случае оплата взимается ежедневно.

[Изучите тарифный план](https://azure.microsoft.com/pricing/details/application-insights/).


## <a name="q14"></a>Что Application Insights изменяет в моем проекте?
Подробности зависят от типа проекта. Для веб-приложения:

* Добавляет в проект следующие файлы:

  * ApplicationInsights.config.
  * ai.js
* Устанавливает следующие пакеты NuGet:

  * *Application Insights API* — основной API
  * *API Application Insights для веб-приложений* — используется для отправки данных телеметрии со стороны сервера
  * *API Application Insights для приложений JavaScript* — используется для отправки данных телеметрии со стороны клиента

    Пакет включает эти сборки:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Вставляет элементы в:

  * Web.config
  * packages.config
* (Только для новых проектов. Если нужно [добавить Application Insights в существующий проект][start], придется делать это вручную.) Вставляет фрагменты кода в код клиента и сервера для их инициализации с идентификатором ресурса Application Insights. Например, в приложении MVC код вставляется в файл главной страницы Views/Shared/_Layout.cshtml.

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Как обновить предыдущие версии пакета SDK?
Информацию для пакета SDK, соответствующего типу вашего приложения, см. в статье с [заметками о выпуске](app-insights-release-notes.md).

## <a name="update"></a>Как изменить ресурс Azure, в который проект отправляет данные?
В обозревателе решений щелкните правой кнопкой мыши `ApplicationInsights.config` и выберите **Обновить Application Insights**. Данные можно отправлять на существующий или новый ресурс в Azure. Мастер обновления изменяет ключ инструментирования в файле ApplicationInsights.config, который определяет, куда сервер SDK должен отправлять ваши данные. Если отменить выбор параметра "Обновить все", это также приведет к изменению ключа, отображаемого на ваших веб-страницах.

#### <a name="data"></a>Как долго данные хранятся на портале? Защищены ли они?
Ознакомьтесь с разделом [Хранение данных и конфиденциальность][data].

## <a name="logging"></a>Ведение журналов
#### <a name="post"></a>Как просмотреть данные POST в колонке «Поиск по журналу диагностики»?
Данные POST не регистрируются автоматически, но можно использовать вызов TrackTrace. Для этого добавьте данные в параметр сообщения. В нем можно добавить больше символов, чем в свойствах строк, но по нему невозможно выполнить фильтрацию.

## <a name="security"></a>Безопасность
#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>Защищены ли мои данные на портале? Как долго они хранятся?
Ознакомьтесь с разделом [Хранение данных и конфиденциальность][data].

## <a name="q17"></a> Все ли активировано в Application Insights?
| Что вы должны видеть | Как это получить | Для чего это нужно |
| --- | --- | --- |
| Диаграммы доступности |[Веб-тесты](app-insights-monitor-web-app-availability.md) |Узнать, что ваше веб-приложение работает |
| Производительность приложения на сервере: время отклика и т.д. |[Добавить Application Insights в ваш проект](app-insights-asp-net.md) или [установить монитор состояний Application Insights на сервере](app-insights-monitor-performance-live-website-now.md) (или написать собственный код для [отслеживания зависимостей](app-insights-api-custom-events-metrics.md#trackdependency)) |Выявить проблемы производительности |
| Телеметрия зависимостей |[Установить монитор состояний Application Insights на сервере](app-insights-monitor-performance-live-website-now.md) |Выявить проблемы с базами данных или другими внешними компонентами |
| Получение данных трассировки стека из исключений |[Вставить вызовы TrackException в код](app-insights-asp-net-exceptions.md) (некоторые выводятся автоматически) |Обнаружить и диагностировать исключения |
| Поиск по трассировкам журнала |[Добавить адаптер ведения журнала](app-insights-asp-net-trace-logs.md) |Выявить исключения, проблемы производительности |
| Основная информация об использовании клиента: просмотр страниц, сеансы и т. д. |[Инициализатор JavaScript на веб-страницах](app-insights-javascript.md) |Аналитика использования |
| Настраиваемые метрики клиента |[Трассировка вызовов на веб-страницах](app-insights-api-custom-events-metrics.md) |Расширить возможности для пользователя |
| Настраиваемые метрики сервера |[Отслеживание вызовов на сервере](app-insights-api-custom-events-metrics.md) |Бизнес-аналитика |

## <a name="automation"></a>Служба автоматизации
Вы можете [написать сценарии PowerShell](app-insights-powershell.md) для создания и обновления ресурсов Application Insights.

## <a name="more-answers"></a>Другие ответы
* [Форум Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

