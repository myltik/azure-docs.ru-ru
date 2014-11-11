<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Наблюдение за работоспособностью и использованием приложения

*Компонент Application Insights находится в состоянии предварительной версии.*

Убедитесь, что приложение работает нормально и удовлетворяет пользователей. Application Insights будет оповещать о любых проблемах производительности и поможет находить и диагностировать корневые причины. Этот компонент позволит отслеживать действия пользователей, чтобы можно было подстраивать приложение к их требованиям.

Application Insights может наблюдать за приложениями Windows Phone, приложениями Магазина Windows и веб-приложениями, а также за веб-сайтами Microsoft Azure.

Потребуется [Visual Studio с обновлением 3][Visual Studio с обновлением 3] и учетная запись в [Microsoft Azure][Microsoft Azure] (в настоящее время идет период бесплатной пробной версии).

1.  [Добавление Application Insights][Добавление Application Insights]

-   [Запуск проекта][Запуск проекта]
-   [Просмотр данных мониторинга][Просмотр данных мониторинга]
-   [Развертывание приложения][Развертывание приложения]
-   [Дальнейшие действия][Дальнейшие действия]

*Кроме того, если требуется наблюдать за существующей веб-службой без ее повторного развертывания или использования Visual Studio, можно также [установить агент на сервере][установить агент на сервере].*

## <a name="add"></a>1. Добавление Application Insights

### Если это новый проект...

При создании нового проекта В Visual Studio 2013 необходимо установить флажок Application Insights.

![Создание проекта ASP.NET][Создание проекта ASP.NET]

Если это делается впервые, то будет предложено предоставить данные для входа в предварительную версию Microsoft Azure или зарегистрироваться. (Эти данные для входа не совпадают с учетной записью Visual Studio Online.)

> Нет варианта Application Insights? Проверьте, используется ли Visual Studio 2013 с обновлением 3, и создан ли веб-проект, проект Магазина Windows или проект Windows Phone.

### ... Если это существующий проект

Чтобы добавить Application Insights в новый проект, щелкните правой кнопкой мыши проект в обозревателе решений и выберите команду "Добавить Application Insights".

![Выбор команды "Добавить Application Insights"][Выбор команды "Добавить Application Insights"]

## <a name="run"></a>2. Запуск проекта

Запустите приложение с помощью клавиши F5 и испытайте его, открывая разные страницы.

В Visual Studio появится подсчет полученных событий.

![][0]

## <a name="monitor"></a>3. Просмотр данных мониторинга

Откройте Application Insights в проекте.

![Щелкните проект правой кнопкой мыши и откройте портал Azure][Щелкните проект правой кнопкой мыши и откройте портал Azure]

Найдите данные в плитках работоспособности приложения и аналитики использования. Например:

![Щелкните для получения дополнительных данных][Щелкните для получения дополнительных данных]

Щелкните любую плитку, чтобы получить дополнительные сведения. [Дополнительные сведения о плитках и отчетах.][Дополнительные сведения о плитках и отчетах.]

> [WACOM.NOTE] В этой предварительной версии многие плитки показывают ограниченные сведения.

## <a name="deploy"></a>4. Развертывание приложения.

Разверните приложение и понаблюдайте за сбором данных.

## <a name="next"></a>Дальнейшие действия

[Дополнительные сведения о плитках и отчетах][Дополнительные сведения о плитках и отчетах.]

[Веб-тесты][Веб-тесты]

[Сбор и поиск журналов диагностики][Сбор и поиск журналов диагностики]

[Устранение неполадок][Устранение неполадок]

## Подробнее

-   [Application Insights][Application Insights]
-   [Добавление Application Insights в проект][Добавление Application Insights в проект]
-   [Наблюдение за текущим состоянием веб-сервера][установить агент на сервере]
-   [Исследование метрик в Application Insights][Дополнительные сведения о плитках и отчетах.]
-   [Поиск журналов диагностики][Сбор и поиск журналов диагностики]
-   [Отслеживание доступности с помощью веб-тестов][Веб-тесты]
-   [Отслеживание использования с помощью событий и метрик][Отслеживание использования с помощью событий и метрик]
-   [Вопросы и ответы, устранение неполадок][Устранение неполадок]

<!--Link references-->

  [Visual Studio с обновлением 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [Microsoft Azure]: http://azure.com
  [Добавление Application Insights]: #add
  [Запуск проекта]: #run
  [Просмотр данных мониторинга]: #monitor
  [Развертывание приложения]: #deploy
  [Дальнейшие действия]: #next
  [установить агент на сервере]: ../app-insights-monitor-performance-live-website-now/
  [Создание проекта ASP.NET]: ./media/appinsights/appinsights-01-vsnewp1.png
  [Выбор команды "Добавить Application Insights"]: ./media/appinsights/appinsights-03-addExisting.png
  [0]: ./media/appinsights/appinsights-09eventcount.png
  [Щелкните проект правой кнопкой мыши и откройте портал Azure]: ./media/appinsights/appinsights-04-openPortal.png
  [Щелкните для получения дополнительных данных]: ./media/appinsights/appinsights-05-usageTiles.png
  [Дополнительные сведения о плитках и отчетах.]: ../app-insights-explore-metrics/
  [Веб-тесты]: ../app-insights-monitor-web-app-availability/
  [Сбор и поиск журналов диагностики]: ../app-insights-search-diagnostic-logs/
  [Устранение неполадок]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Добавление Application Insights в проект]: ../app-insights-monitor-application-health-usage/
  [Отслеживание использования с помощью событий и метрик]: ../app-insights-track-usage-custom-events-metrics/
