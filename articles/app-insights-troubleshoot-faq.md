<properties title="Troubleshooting and Q & A about Application Insights" pageTitle="Troubleshooting and Q & A about Application Insights" description="Tips and troubleshooting" metaKeywords="analytics monitoring" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Устранение неполадок и ответы на вопросы: Application Insights в предварительной версии Microsoft Azure

-   [Нигде нет команды на добавление Application Insights в мой проект в Visual Studio][Нигде нет команды на добавление Application Insights в мой проект в Visual Studio]
-   [Мой новый веб-проект был создан, но добавить Application Insights не удалось.][Мой новый веб-проект был создан, но добавить Application Insights не удалось.]
-   [Мне удалось успешно добавить Application Insights и запустить приложение, но данные не появились на портале.][Мне удалось успешно добавить Application Insights и запустить приложение, но данные не появились на портале.]
-   [В разделе «Аналитические сведения об использовании» нет данных][В разделе «Аналитические сведения об использовании» нет данных]
-   [На экране открыта начальная панель предварительной версии Microsoft Azure. Как найти мои данные в Application Insights?][На экране открыта начальная панель предварительной версии Microsoft Azure. Как найти мои данные в Application Insights?]
-   [На домашнем экране предварительной версии Microsoft Azure состояние моего приложение показано на этой карте?][На домашнем экране предварительной версии Microsoft Azure состояние моего приложение показано на этой карте?]
-   [После добавления Application Insights в приложение и открытия портала Application Insights все выглядит совсем не так, как на снимках экрана.][После добавления Application Insights в приложение и открытия портала Application Insights все выглядит совсем не так, как на снимках экрана.]
-   [Можно ли использовать Application Insights для мониторинга веб-сервера в интрасети??][Можно ли использовать Application Insights для мониторинга веб-сервера в интрасети??]
-   [Как получить данные для Windows Phone или для Магазина Windows?][Как получить данные для Windows Phone или для Магазина Windows?]
-   [Как просмотреть события и просмотры страниц, записанные в журнал в коде?][Как просмотреть события и просмотры страниц, записанные в журнал в коде?]
-   [Почему существует две версии Application Insights?][Почему существует две версии Application Insights?]
-   [Что сейчас отсутствует в версия Application Insights для Azure?][Что сейчас отсутствует в версия Application Insights для Azure?]
-   [Как вернуть все удобные функции версии Application Insights для Visual Studio Online?][Как вернуть все удобные функции версии Application Insights для Visual Studio Online?]
-   [Что Application Insights изменяет в моем проекте?][Что Application Insights изменяет в моем проекте?]
-   [Как найти мои результаты в Application Insights?][Как найти мои результаты в Application Insights?]
-   [Дальнейшие действия][Дальнейшие действия]

## <a name="q01"></a>Нигде нет команды на добавление Application Insights в мой проект в Visual Studio

-   Убедитесь в наличии [Visual Studio Update 3][Visual Studio Update 3]. В состав этой версии входят инструменты Application Insights, которые должны отображаться в диспетчере расширений.
-   Application Insights в предварительной версии Microsoft Azure в настоящее время доступны только для веб-проектов ASP.NET на языке C# или Visual Basic.
-   Если у вас есть существующий проект, перейдите в обозреватель решений и щелкните именно веб-проект (а не другой проект или решение). Должен появиться элемент меню «Add Application Insights Telemetry to Project».
-   Если вы создаете новый проект, в Visual Studio выберите «Файл» \> «Создать проект», выберите {Visual C#|Visual Basic} \> «Веб» \> «Веб-приложение ASP.NET». Должна быть возможность добавить Application Insights в проект.

## <a name="q02"></a>Мой новый веб-проект был создан, но добавить Application Insights не удалось.

Это может произойти при сбое связи с порталом Application Insights или при наличии каких-либо неполадок с вашей учетной записью.

-   Убедитесь, что вы предоставили правильные учетные данные для учетной записи Azure. Учетные данные Microsoft Azure credentials, которые вы видите в диалоговом окне «Создание проекта», могут отличаться от учетных данных Visual Studio Online, которые отображаются в правом верхнем углу окна Visual Studio.
-   Подождите немного, затем [добавьте Application Insights в ваш существующий проект][добавьте Application Insights в ваш существующий проект].
-   Перейдите к настройкам учетной записи Microsoft Azure и проверьте ограничения. Проверьте, можно ли вручную добавить приложение Application Insights.

## <a name="q03"></a>Мне удалось успешно добавить Application Insights и запустить приложение, но данные не появились на портале.

-   Нужно закрыть и снова открыть любой модуль, где вы ожидаете данных. В текущей версии содержимое модулей не обновляется автоматически.
-   На начальном экране Microsoft Azure посмотрите на карту состояния службы. Если есть какие-либо предупреждения, дождитесь возвращения всех модулей в состояние ОК, затем закройте и заново откройте модуль приложения Application Insights.
-   В брандмауэре веб-сервера может потребоваться открыть порт 443 для исходящего трафика.

## <a name="q04"></a>В разделе «Аналитические сведения об использовании» нет данных

-   Данные поступают из сценариев на веб-страницах. Если вы добавили Application Insights в существующий веб-проект, [нужно добавить сценарии вручную][добавьте Application Insights в ваш существующий проект].

## <a name="q05"></a>На экране открыта начальная панель предварительной версии Microsoft Azure. Как найти мои данные в Application Insights?

Одно из двух:

-   Выберите Browse и Application Insights, затем выберите свой проект. Если там пока нет проектов, нужно добавить [Application Insights в ваш веб-проект в Visual Studio][добавьте Application Insights в ваш существующий проект].

-   В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект веб-приложения и выберите Открыть портал Application Insights.

## <a name="q06"></a>На домашнем экране предварительной версии Microsoft Azure состояние моего приложение показано на этой карте?

Нет! Здесь отображается состояние службы Azure. Чтобы увидеть результаты веб-теста, выберите «Обзор» \> Application Insights \> (ваше приложение), затем ознакомьтесь с результатами.

## <a name="q07"></a>После добавления Application Insights в приложение и открытия портала Application Insights все выглядит совсем не так, как на снимках экрана.

Возможно, вы используете устаревшую версию Application Insights Tools, которая подключается к версии Visual Studio Online.

Страницы справки относятся к Application Insights для предварительной версии Microsoft Azure, которая поставляется с Visual Studio Update 3.

## <a name="q08"></a>Можно ли использовать Application Insights для мониторинга веб-сервера в интрасети?

Да, можно отслеживать состояние и использование сервера, если сервер может отправлять данные в общедоступный Интернет.

Если же вы хотите запускать веб-тесты для вашей службы, она должна быть доступна из Интернета.

## <a name="q10"></a>Как просмотреть события и просмотры страниц, записанные в журнал в коде?

В нынешней версии Microsoft Azure такая возможность пока не поддерживается. Выпуск ожидается в ближайшее время. Пока можно попробовать [прежнюю версию][прежнюю версию].

## <a name="q11"></a>Почему существует две версии Application Insights?

Более старый портал — часть Visual Studio Online. Мы не будем вносить существенных изменений в эту версию. Если вы используете устаревшую версию Application Insights, она подключается к порталу Visual Studio Online.

Visual Studio Update 3 включает предустановленную версию новых инструментов Application Insights. Они подключаются к новому порталу Application Insights, который является компонентом предварительной версии Microsoft Azure. В настоящее время мы переносим Application Insights в эту новую среду. Эта работа пока не завершена, существует ряд ограничений.

## <a name="q12"></a>Что сейчас отсутствует в версии Application Insights для Azure?

На будущее планируется много изменений.

В настоящий момент отсутствуют следующие основные функции: поддержка приложений для устройств, таких как Windows Phone и Магазин Windows, отчеты по настраиваемым показателям, таким как `trackEvent()` и `trackPageView()`.

## <a name="q13"></a>Как вернуть все удобные функции версии Application Insights для Visual Studio Online?

1.  Перейдите в диспетчер расширений Visual Studio.
2.  Удалите Application Insights Tools.
3.  Запустите [установщик для устаревшей версии][установщик для устаревшей версии] и прочтите [руководство «Приступая к работе»][прежнюю версию].

## <a name="q14"></a>Что Application Insights изменяет в моем проекте?

-   Добавляет в проект следующие файлы:

	-   ApplicationInsights.config.
	-   ai.js

-   Устанавливает следующие пакеты NuGet:

	-   *Application Insights API* — основной API

	-   *API Application Insights для веб-приложений* — используется для отправки данных телеметрии со стороны сервера

	-   *API Application Insights для приложений JavaScript* — используется для отправки данных телеметрии со стороны клиента

    	Пакет включает эти сборки:

	-   Microsoft.ApplicationInsights

	-   Microsoft.ApplicationInsights.Platform

-   (Только для новых проектов. Если [добавить Application Insights в существующий проект][добавьте Application Insights в ваш существующий проект], то придется это делать вручную.) Вставляет фрагменты кода в код клиента и сервера для их инициализации с идентификатором ресурса Application Insights. Например, в приложении MVC код вставляется в:

-   главная страница Views/Shared/\_Layout.cshtml

-   Web.config

-   packages.config

## <a name="q15"></a>Как найти мои результаты в Application Insights?

1.  Откройте Microsoft Azure:

-   В Visual Studio щелкните правой кнопкой мыши проект веб-приложения и выберите **Открыть портал Azure Preview**.
-   Или в веб-браузере откройте свою учетную запись в предварительной версии Microsoft Azure.

2.  Выберите Browse и Application Insights, затем выберите свой проект.

## <a name="next"></a>Дополнительные сведения

-   [Application Insights][Application Insights]
-   [Добавление Application Insights в проект][добавьте Application Insights в ваш существующий проект]
-   [Наблюдение за текущим состоянием веб-сервера][Наблюдение за текущим состоянием веб-сервера]
-   [Исследование метрик в Application Insights][Исследование метрик в Application Insights]
-   [Поиск журналов диагностики][Поиск журналов диагностики]
-   [Отслеживание доступности с помощью веб-тестов][Отслеживание доступности с помощью веб-тестов]
-   [Отслеживание использования с помощью событий и метрик][Отслеживание использования с помощью событий и метрик]
-   [Вопросы и ответы, устранение неполадок][Вопросы и ответы, устранение неполадок]

<!--Link references-->

  [Нигде нет команды на добавление Application Insights в мой проект в Visual Studio]: #q01
  [Мой новый веб-проект был создан, но добавить Application Insights не удалось.]: #q02
  [Мне удалось успешно добавить Application Insights и запустить приложение, но данные не появились на портале.]: #q03
  [В разделе «Аналитические сведения об использовании» нет данных]: #q04
  [На экране открыта начальная панель предварительной версии Microsoft Azure. Как найти мои данные в Application Insights?]: #q05
  [На домашнем экране предварительной версии Microsoft Azure состояние моего приложение показано на этой карте?]: #q06
  [После добавления Application Insights в приложение и открытия портала Application Insights все выглядит совсем не так, как на снимках экрана.]: #q07
  [Можно ли использовать Application Insights для мониторинга веб-сервера в интрасети??]: #q08
  [Как получить данные для Windows Phone или для Магазина Windows?]: #q09
  [Как просмотреть события и просмотры страниц, записанные в журнал в коде?]: #q10
  [Почему существует две версии Application Insights?]: #q11
  [Что сейчас отсутствует в версия Application Insights для Azure?]: #q12
  [Как вернуть все удобные функции версии Application Insights для Visual Studio Online?]: #q13
  [Что Application Insights изменяет в моем проекте?]: #q14
  [Как найти мои результаты в Application Insights?]: #q15
  [Дальнейшие действия]: #next
  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [добавьте Application Insights в ваш существующий проект]: ../app-insights-monitor-application-health-usage/
  [прежнюю версию]: http://www.visualstudio.com/get-started/get-usage-data-vs
  [установщик для устаревшей версии]: http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a
  [Application Insights]: ../app-insights-get-started/
  [Наблюдение за текущим состоянием веб-сервера]: ../app-insights-monitor-performance-live-website-now/
  [Исследование метрик в Application Insights]: ../app-insights-explore-metrics/
  [Поиск журналов диагностики]: ../app-insights-search-diagnostic-logs/
  [Отслеживание доступности с помощью веб-тестов]: ../app-insights-monitor-web-app-availability/
  [Отслеживание использования с помощью событий и метрик]: ../app-insights-track-usage-custom-events-metrics/
  [Вопросы и ответы, устранение неполадок]: ../app-insights-troubleshoot-faq/
