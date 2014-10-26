<properties title="Application Insights" pageTitle="Application Insights - start monitoring your app's health and usage" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Application Insights — начните отслеживать работоспособность и использование приложения

*Служба Application Insights доступна в предварительной версии.*

С помощью Application Insights можно отслеживать следующие показатели работающего приложения:

* **доступность** — мы будем тестировать ваши URL-адреса каждые несколько минут из разных точек мира;
* **производительность** — обнаружение и диагностика проблем с производительностью и исключений;
* **использование** — узнайте, что пользователи делают с вашим приложением, чтобы улучшить его.

Настроить службу очень легко, так что вы увидите результаты через несколько минут. На сегодняшний день мы поддерживаем веб-приложения ASP.NET (на ваших собственных серверах или в Azure).

Вам потребуется учетная запись в [Microsoft Azure](http://azure.com) (предлагается бесплатный пробный период).

Приступить к работе с Application Insights можно одним из двух способов:

* [добавьте Application Insights в свой проект в Visual Studio](#add), чтобы отслеживать производительность и использование приложения (рекомендуемый способ);
* [установите агент на своем сервере без повторного развертывания][redfield], чтобы отслеживать работающий веб-сайт, не развертывая его повторно и не изменяя исходный код. Это позволяет наблюдать за производительностью и исключениями. Добавить мониторинг использования можно позднее.

>[WACOM.NOTE] В Visual Studio Online есть [более ранняя версия Application Insights](http://msdn.microsoft.com/en-us/library/dn481095.aspx), которая поддерживает больше типов приложений. Мы полностью перерабатываем ее для Microsoft Azure, и это как раз та новая версия, о которой идет речь здесь.


## <a name="add"></a> Добавление Application Insights в проект

Требуется [Visual Studio 2013 с обновлением 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (или более поздней версии).

### Добавление в новый проект

При создании нового проекта в Visual Studio 2013 установите флажок для компонента Application Insights.


![Создание проекта ASP.NET](./media/appinsights/appinsights-01-vsnewp1.png)

Если вы делаете это впервые, вам будет предложено войти в предварительную версию Microsoft Azure или зарегистрироваться в ней. (При этом создается учетная запись, не связанная с учетной записью Visual Studio Online.)

Если нужно сделать так, чтобы имя ресурса Azure отличалось от имени проекта или чтобы ресурс входил в ту же группу, что и другой проект, щелкните ссылку **Configure settings** (Настроить параметры).

*Нет параметра для добавления Application Insights? Убедитесь в том, что вы используете Visual Studio 2013 с обновлением 3, что Средства Application Insights включены в разделе "Расширения и обновления" и что вы создаете веб-проект либо проект для Магазина Windows или Windows Phone.*

### ... Добавление в существующий проект

В обозревателе решений щелкните проект правой кнопкой мыши и выберите пункт Add Application Insights (Добавить Application Insights).

![Выберите пункт Add Application Insights](./media/appinsights/appinsights-03-addExisting.png)

*Чтобы настроить аналитику использования веб-сайта, нужно выполнить еще одно действие, но сначала давайте рассмотрим некоторые данные...*


### <a name="run"></a> 2. Запуск проекта

Запустите приложение, нажав клавишу F5, и попробуйте открыть разные страницы.

В Visual Studio вы увидите число полученных событий.

![](./media/appinsights/appinsights-09eventcount.png)

### <a name="monitor"></a> 3. Просмотр данных мониторинга

Откройте Application Insights из проекта.

![Щелкните проект правой кнопкой мыши и откройте портал Azure](./media/appinsights/appinsights-04-openPortal.png)


Просмотрите данные на плитках **Application health** (Работоспособность приложения). Сначала вы увидите только одну или две точки. Например:

![Щелкните плитки, чтобы увидеть больше данных](./media/appinsights/appinsights-41firstHealth.png)

Щелкните любую плитку, чтобы увидеть более подробные данные. Вы можете изменить данные, отображаемые в отчетах. [Подробнее о настройке отчетов о работоспособности приложения.][perf]


### <a name="webclient"></a> 4. Настройка аналитики использования веб-сайта

Если вы добавили Application Insights в *существующий* веб-проект, то на плитках аналитики использования пока ничего не увидите. Нужно выполнить еще одно действие.

В Application Insights щелкните Quick start (Быстрый запуск) -\> Instrument your website (Инструментировать веб-сайт).

![В проекте в службе Application Insights щелкните Quick start -\> Instrument your website и скопируйте код](./media/appinsights/appinsights-06webcode.png)

Вставьте код JavaScript на веб-страницы, которые необходимо отслеживать, прямо перед закрывающим тегом \</head\>. Для отслеживания всех страниц в проекте ASP.NET рекомендуем вставить код на главной странице, которая обычно называется `_SiteLayout` или `Views\Shared\_Layout`. Обратите внимание: код содержит ключ Application Insights для приложения.

Запустите приложение еще раз, и вы увидите данные в разделе **Usage analytics** (Аналитика использования).

![Щелкните диаграммы, чтобы увидеть больше данных](./media/appinsights/appinsights-05-usageTiles.png)

[Щелкните диаграммы, чтобы увидеть более подробные данные.][perf]

### <a name="deploy"></a> 5. Развертывание приложения

Разверните приложение и наблюдайте за тем, как накапливаются данные.



Когда приложение заработает, [настройте веб-тесты][availability], чтобы контролировать его работоспособность.

![Пример монитора приложения в Application Insights](./media/appinsights/appinsights-00-appblade.png)

### Изменение имени приложения на портале

Вы можете изменить ресурс, которому проект отправляет данные телеметрии.

(Ресурс — это именованная колонка Application Insights, в которой отображаются результаты. В группу можно добавить несколько ресурсов, например, если несколько проектов образуют одно бизнес-приложение.)

В обозревателе решений щелкните правой кнопкой мыши файл ApplicationInsights.config и выберите пункт **Update Application Insights** (Обновить Application Insights). Откроется мастер, в котором можно выбрать другой существующий ресурс или создать новый.

После этого вернитесь на портал и удалите старый ресурс.

## <a name="video"></a> Видео

### Введение

> [AZURE.VIDEO application-insights-introduction]

### Приступая к работе

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a> Дальнейшие действия

[Отслеживание использования веб-приложения][usage]

[Отслеживание производительности в веб-приложениях][perf]

[Ведение журналов диагностики и поиск по ним][diagnostic]

[Устранение неполадок][qna]


## Подробнее

* [Приступая к работе с Application Insights][start]
* [Отслеживание работающего веб-сервера][redfield]
* [Отслеживание производительности в веб-приложениях][perf]
* [Поиск по журналам диагностики][diagnostic]
* [Отслеживание доступности с помощью веб-тестов][availability]
* [Отслеживание использования][usage]
* [Часто задаваемые вопросы и устранение неполадок][qna]

<!--Link references-->



[start]: ../app-insights-start-monitoring-app-health-usage/
[redfield]: ../app-insights-monitor-performance-live-website-now/
[perf]: ../app-insights-web-monitor-performance/
[diagnostic]: ../app-insights-search-diagnostic-logs/ 
[availability]: ../app-insights-monitor-web-app-availability/
[usage]: ../app-insights-web-track-usage/
[qna]: ../app-insights-troubleshoot-faq/
