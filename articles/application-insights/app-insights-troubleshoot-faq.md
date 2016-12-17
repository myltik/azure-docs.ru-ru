---
title: "Устранение неполадок и ответы на вопросы по Application Insights"
description: "Что-то в Azure Application Insights непонятно или не работает? Попробуйте здесь."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: dea21a59b189d1d3d474cbc5e67f64df485a1981
ms.openlocfilehash: 0d860856b4c871c2c8e3622597ba3753bf62cf4f


---
# <a name="questions---application-insights-for-aspnet"></a>Вопросы — Application Insights для ASP.NET
## <a name="configuration-problems"></a>Проблемы с конфигурацией
*У меня не получается настроить компоненты, о которых идет речь в таких статьях:*

* [Troubleshooting no data - Application Insights for .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [раздел "Устранение неполадок"](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Настройка системы диагностики Azure для входа в Application Insights](app-insights-azure-diagnostics.md)
* [Устранение неполадок, а также вопросы и ответы по Application Insights для Java](app-insights-java-troubleshoot.md)
* [другие платформы.](app-insights-platforms.md)

*Я не получаю данные с моего сервера*

* [Настройка исключений брандмауэра](app-insights-ip-addresses.md)
* [Настройка сервера ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Настройка сервера Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Можно ли использовать Application Insights с...?
[См. статью о платформах][платформы].

## <a name="is-it-free"></a>Предоставляется ли бесплатно?
* Да, если выбрать [ценовую категорию](app-insights-pricing.md)Free. Вы получаете доступ к большинству функций и значительную квоту данных.
* Чтобы зарегистрироваться в Microsoft Azure, вы должны предоставить данные кредитной карты, но плата будет взиматься только за пользование платными службами Azure или же в случае обновления до платной категории.
* Если приложение отправляет больше данных, чем месячная квота для категории Free, запись в журнал прекращается. В этом случае вы можете начать платить за пользование службой или подождать окончания месяца, пока квота не будет сброшена.
* Квота не распространяется на основные данные об использовании и сеансах.
* Предлагается также 30-дневная пробная версия: в течение этого времени вы будете бесплатно получать доступ к платным функциям.
* Каждый ресурс приложения имеет отдельную квоту, и вы устанавливаете его ценовую категорию независимо от всех других.

#### <a name="what-do-i-get-if-i-pay"></a>Что я получу в случае оплаты?
* Расширенную [ежемесячную квоту данных](https://azure.microsoft.com/pricing/details/application-insights/).
* Есть возможность оплатить "превышение" для продолжения сбора данных сверх месячной квоты. Если данные превышают квоту, плата будет сниматься за каждый МБ.
* [Непрерывный экспорт](app-insights-export-telemetry.md).

## <a name="a-nameq14awhat-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Что Application Insights изменяет в моем проекте?
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

## <a name="a-nameupdateahow-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Как изменить ресурс Azure, в который проект отправляет данные?
В обозревателе решений щелкните правой кнопкой мыши `ApplicationInsights.config` и выберите **Обновить Application Insights**. Данные можно отправлять на существующий или новый ресурс в Azure. Мастер обновления изменяет ключ инструментирования в файле ApplicationInsights.config, который определяет, куда сервер SDK должен отправлять ваши данные. Если отменить выбор параметра "Обновить все", это также приведет к изменению ключа, отображаемого на ваших веб-страницах.

#### <a name="a-namedataahow-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Как долго данные хранятся на портале? Защищены ли они?
См. статью [Сбор и хранение данных в Application Insights][Данные].

## <a name="logging"></a>Ведение журналов
#### <a name="a-namepostahow-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Как просмотреть данные POST в колонке «Поиск по журналу диагностики»?
Данные POST не регистрируются автоматически, но можно использовать вызов TrackTrace. Для этого добавьте данные в параметр сообщения. В нем можно добавить больше символов, чем в свойствах строк, но по нему невозможно выполнить фильтрацию.

## <a name="security"></a>Безопасность
#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>Защищены ли мои данные на портале? Как долго они хранятся?
См. статью [Сбор и хранение данных в Application Insights][Данные].

## <a name="a-nameq17a-have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Все ли активировано в Application Insights?
| Что вы должны видеть | Как это получить | Для чего это нужно |
| --- | --- | --- |
| Диаграммы доступности |[Веб-тесты](app-insights-monitor-web-app-availability.md) |Узнать, что ваше веб-приложение работает |
| Производительность приложения на сервере: время отклика и т.д. |[Добавить Application Insights в ваш проект](app-insights-asp-net.md) или [установить монитор состояний Application Insights на сервере](app-insights-monitor-performance-live-website-now.md) (или написать собственный код для [отслеживания зависимостей](app-insights-api-custom-events-metrics.md#track-dependency)) |Выявить проблемы производительности |
| Телеметрия зависимостей |[Установить монитор состояний Application Insights на сервере](app-insights-monitor-performance-live-website-now.md) |Выявить проблемы с базами данных или другими внешними компонентами |
| Получение данных трассировки стека из исключений |[Вставить вызовы TrackException в код](app-insights-search-diagnostic-logs.md#exceptions) (некоторые выводятся автоматически) |Обнаружить и диагностировать исключения |
| Поиск по трассировкам журнала |[Добавить адаптер ведения журнала](app-insights-search-diagnostic-logs.md) |Выявить исключения, проблемы производительности |
| Основная информация об использовании клиента: просмотр страниц, сеансы и т. д. |[Инициализатор JavaScript на веб-страницах](app-insights-javascript.md) |Аналитика использования |
| Настраиваемые метрики клиента |[Трассировка вызовов на веб-страницах](app-insights-api-custom-events-metrics.md) |Расширить возможности для пользователя |
| Настраиваемые метрики сервера |[Отслеживание вызовов на сервере](app-insights-api-custom-events-metrics.md) |Бизнес-аналитика |

## <a name="automation"></a>Служба автоматизации
Вы можете [написать сценарии PowerShell](app-insights-powershell.md) для создания и обновления ресурсов Application Insights.

## <a name="more-answers"></a>Другие ответы
* [Форум Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[Данные]: app-insights-data-retention-privacy.md
[платформы]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md



<!--HONumber=Nov16_HO3-->


