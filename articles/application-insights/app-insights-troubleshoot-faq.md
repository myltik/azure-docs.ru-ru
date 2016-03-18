<properties 
	pageTitle="Устранение неполадок и ответы на вопросы по Application Insights" 
	description="Что-то в Visual Studio Application Insights непонятно или не работает? Попробуйте здесь." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="awills"/>
 
# Вопросы — Application Insights для ASP.NET

## Проблемы с конфигурацией

*У меня не получается настроить компоненты, о которых идет речь в таких статьях:*

* [Troubleshooting no data - Application Insights for .NET](app-insights-asp-net-troubleshoot-no-data.md) (Устранение неполадок, связанных с отсутствием данных, — Application Insights для .NET);
* [раздел "Устранение неполадок"](app-insights-monitor-performance-live-website-now.md#troubleshooting);
* [Настройка системы диагностики Azure для входа в Application Insights](app-insights-azure-diagnostics.md);
* [Устранение неполадок, а также вопросы и ответы по Application Insights для Java](app-insights-java-troubleshoot.md);
* [другие платформы.](app-insights-platforms.md)


## Можно ли использовать Application Insights с...?

[См. статью о платформах.][platforms]


## Предоставляется ли бесплатно?

* Да, если выбрать [ценовую категорию](app-insights-pricing.md) Free. Вы получаете доступ к большинству функций и значительную квоту данных. 
* Чтобы зарегистрироваться в Microsoft Azure, вы должны предоставить данные кредитной карты, но плата будет взиматься только за пользование платными службами Azure или же в случае обновления до платной категории.
* Если приложение отправляет больше данных, чем месячная квота для категории Free, запись в журнал прекращается. В этом случае вы можете начать платить за пользование службой или подождать окончания месяца, пока квота не будет сброшена.
* Квота не распространяется на основные данные об использовании и сеансах.
* Предлагается также 30-дневная пробная версия: в течение этого времени вы будете бесплатно получать доступ к функциям ценовой категории Premium.
* Каждый ресурс приложения имеет отдельную квоту, и вы устанавливаете его ценовую категорию независимо от всех других.

#### Что я получу в случае оплаты?

* Расширенную [ежемесячную квоту данных](https://azure.microsoft.com/pricing/details/application-insights/).
* Есть возможность оплатить "превышение" для продолжения сбора данных сверх месячной квоты. Если данные превышают квоту, плата будет сниматься за каждый МБ.
* [Непрерывный экспорт](app-insights-export-telemetry.md).


## <a name="q14"></a>Что Application Insights изменяет в моем проекте?

Подробности зависят от типа проекта. Для веб-приложения:


+ Добавляет в проект следующие файлы:

 + ApplicationInsights.config.
 + ai.js


+ Устанавливает следующие пакеты NuGet:

 -  *Application Insights API* — основной API

 -  *API Application Insights для веб-приложений* — используется для отправки данных телеметрии со стороны сервера

 -  *API Application Insights для приложений JavaScript* — используется для отправки данных телеметрии со стороны клиента

    Пакет включает эти сборки:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Вставляет элементы в:

 - Web.config

 - packages.config

+ (Только для новых проектов. Если нужно [добавить Application Insights в существующий проект][start], придется делать это вручную.) Вставляет фрагменты кода в код клиента и сервера для их инициализации с идентификатором ресурса Application Insights. Например, в приложении MVC код вставляется в файл главной страницы Views/Shared/\_Layout.cshtml.


## Как обновить предыдущие версии пакета SDK?

Информацию для пакета SDK, соответствующего типу вашего приложения, см. в разделе [Заметки о выпуске](app-insights-release-notes.md).



## <a name="update"></a>Как изменить ресурс Azure, в который проект отправляет данные?

В обозревателе решений щелкните правой кнопкой мыши `ApplicationInsights.config` и выберите **Обновить Application Insights**. Данные можно отправлять на существующий или новый ресурс в Azure. Мастер обновления изменяет ключ инструментирования в файле ApplicationInsights.config, который определяет, куда сервер SDK должен отправлять ваши данные. Если отменить выбор параметра "Обновить все", это также приведет к изменению ключа, отображаемого на ваших веб-страницах.


## <a name="q06"></a>Отображается ли состояние моего приложения на карте на начальном экране предварительной версии Microsoft Azure?

Нет! Здесь отображается состояние службы Azure. Чтобы увидеть результаты веб-теста, выберите Обзор > Application Insights > (ваше приложение).



#### <a name="data"></a>Как долго данные хранятся на портале? Защищены ли они?

См. раздел [Хранение данных и конфиденциальность][data].

## Ведение журналов

#### <a name="post"></a>Как просмотреть данные POST в колонке "Поиск по журналу диагностики"?

Данные POST не регистрируются автоматически, но можно использовать вызов TrackTrace. Для этого добавьте данные в параметр сообщения. В нем можно добавить больше символов, чем в свойствах строк, но по нему невозможно выполнить фильтрацию.

## Безопасность

#### Защищены ли мои данные на портале? Как долго они хранятся?

См. раздел [Хранение данных и конфиденциальность][data].


## <a name="q17"></a>Все ли активировано в Application Insights?

<table border="1">
<tr><th>Что вы должны видеть</th><th>Как это получить</th><th>Для чего это нужно</th></tr>
<tr><td>Диаграммы доступности</td><td><a href="../app-insights-monitor-web-app-availability/">Веб-тесты</a></td><td>Узнать, что ваше веб-приложение работает</td></tr>
<tr><td>Производительность приложения на сервере: время отклика и т.д.
</td><td><a href="../app-insights-asp-net/">Добавить Application Insights в свой проект</a><br/>или <br/><a href="../app-insights-monitor-performance-live-website-now/">Установить монитор состояний Application Insights на сервере</a> (или написать собственный код для <a href="../app-insights-api-custom-events-metrics/#track-dependency">отслеживания зависимостей</a>)</td><td>Выявить проблемы производительности</td></tr>
<tr><td>Телеметрия зависимостей</td><td><a href="../app-insights-monitor-performance-live-website-now/">Установить монитор состояний Application Insights на сервере</a></td><td>Выявить проблемы с базами данных или другими внешними компонентами</td></tr>
<tr><td>Получение данных трассировки стека из исключений</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Вставьте вызовы TrackException в код</a> (некоторые выводятся автоматически)</td><td>Обнаружить и диагностировать исключения</td></tr>
<tr><td>Поиск по трассировкам журнала</td><td><a href="../app-insights-search-diagnostic-logs/">Добавьте адаптер ведения журнала</a></td><td>Выявить исключения, проблемы производительности</td></tr>
<tr><td>Основная информация об использовании клиента: просмотр страниц, сеансы и т.&#160;д.</td><td><a href="../app-insights-javascript/">Инициализатор JavaScript на веб-страницах</a></td><td>Аналитика использования</td></tr>
<tr><td>Настраиваемые метрики клиента</td><td><a href="../app-insights-api-custom-events-metrics/">Трассировка вызовов на веб-страницах</a></td><td>Расширить возможности для пользователя</td></tr>
<tr><td>Настраиваемые метрики сервера</td><td><a href="../app-insights-api-custom-events-metrics/">Отслеживание вызовов в коде сервера</a></td><td>Бизнес-аналитика</td></tr>
</table>

Если ваша веб-служба работает на виртуальной машине Azure, вы можете [получить данные диагностики][azurediagnostic] здесь.

## Автоматизация

Вы можете [написать сценарий PowerShell](app-insights-powershell.md) для создания ресурса Application Insights.

## Другие ответы

* [Форум Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/ru-RU/home?forum=ApplicationInsights)


<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0302_2016-->