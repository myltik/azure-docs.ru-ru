<properties title="Manage an Azure website" pageTitle="Manage an Azure website" description="Links to resources for managing a Microsoft Azure website." services="web-sites" solutions="web" documentationCenter="" authors="mwasson" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="mwasson" />

# Управление веб-сайтом Azure

В этом разделе представлены ссылки на ресурсы, посвященные управлению веб-сайтом Azure. Под управлением подразумеваются все задачи, направленные на обеспечение стабильной работы веб-сайта.

На различных этапах существования сайта вам придется выполнять разные задачи управления: от первоначального развертывания до обычной эксплуатации, обслуживания и применения обновлений.

-   [Перед развертыванием сайта в рабочей среде][Перед развертыванием сайта в рабочей среде]
-   [В ходе эксплуатации веб-сайта][В ходе эксплуатации веб-сайта]
-   [При обновлении веб-сайта][При обновлении веб-сайта]

Многие задачи, связанные с управлением веб-сайтом, можно выполнять на портале Azure. Дополнительные сведения см. в разделе [Управление веб-сайтами с помощью портала управления Azure][Управление веб-сайтами с помощью портала управления Azure].

## Перед развертыванием сайта в рабочей среде

### Выберите уровень.

Доступны четыре уровня Веб-сайтов Azure: "Бесплатный", "Общедоступный", "Базовый" и "Стандартный". Информацию о функциях и ценах для каждого уровня см. в разделе [Сведения о ценах][Сведения о ценах].

-   [Планы размещения веб-сайтов][Планы размещения веб-сайтов] позволяют группировать несколько веб-сайтов на одном уровне.
-   Вы всегда можете [сменить уровни][сменить уровни] после создания веб-сайта.

### Конфигурация

На [портале управления Azure][портале управления Azure] можно задать различные параметры конфигурации. Дополнительные сведения см. в разделе [Настройка Веб-сайтов][Настройка Веб-сайтов]. Вот краткий контрольный список:

-   При необходимости выберите **версии сред выполнения** для .NET, PHP, Java или Python.
-   Если на вашем веб-сайте используется протокол **WebSocket**, включите его поддержку (это относится к приложениям, в которых применяется [ASP.NET SignalR][ASP.NET SignalR] или [socket.io][socket.io]).
-   Вы используете непрерывные веб-задания? Если да, то выберите параметр **Always On** (Всегда включено).
-   Задайте **документ по умолчанию**, например index.html.

Помимо этих базовых параметров конфигурации, вы также можете настроить следующее:

-   **Шифрование по протоколу SSL**. Чтобы использовать SSL с пользовательским доменным именем, вам необходимо получить SSL-сертификат и настроить свой сайт для работы с ним. См. раздел [Включение HTTPS для веб-сайта Azure][Включение HTTPS для веб-сайта Azure].
-   **Пользовательское доменное имя.** Вашему веб-сайту автоматически присваивается дочерний домен в azurewebsites.net. Вы можете связать сайт с пользовательским доменным именем, например contoso.com. См. раздел [Настройка пользовательского имени домена][Настройка пользовательского имени домена].

Настройка для определенных языков программирования:

-   **PHP**: [Настройка PHP на веб-сайтах Azure][Настройка PHP на веб-сайтах Azure].
-   **Python**: [Настройка Python для веб-сайтов Azure][Настройка Python для веб-сайтов Azure].

## В ходе эксплуатации веб-сайта

В ходе эксплуатации веб-сайта важно, чтобы он всегда был доступен, а его ресурсы масштабировались с учетом пользовательского трафика. Кроме того, возможно, вам придется устранять возникающие неполадки.

### Мониторинг

-   На портале управления вы можете [добавить метрики производительности][добавить метрики производительности], например показатель использования ЦП и количество запросов от клиентов.
-   Если вам нужны более подробные данные, воспользуйтесь средством New Relic для мониторинга производительности и управления ею. См. раздел [Управление производительностью приложений New Relic на веб-сайтах Azure][Управление производительностью приложений New Relic на веб-сайтах Azure].
-   [Ресурсы веб-сайта можно масштабировать][сменить уровни] с учетом трафика. В зависимости от уровня плана размещения можно изменять количество виртуальных машин и (или) размер экземпляров виртуальных машин. В плане "Стандартный" вы также можете настроить автоматическое масштабирование сайта по заданному графику или с учетом нагрузки.

### Резервные копии

-   Настройте [автоматическое резервное копирование][автоматическое резервное копирование] своего веб-сайта. Дополнительные сведения о резервном копировании см. в [этом видео][этом видео].
-   Ознакомьтесь с параметрами [восстановления базы данных][восстановления базы данных] SQL Azure.

### Устранение неполадок

-   Возникающие неполадки можно [устранять в Visual Studio][устранять в Visual Studio] с помощью журналов диагностики и отладки в облаке в режиме реального времени.
-   Кроме того, журналы диагностики можно собирать различными способами не только в Visual Studio. См. раздел [Включение ведения журналов диагностики для веб-сайтов Azure][Включение ведения журналов диагностики для веб-сайтов Azure].
-   Для приложений Node.js см. раздел [Отладка приложения Node.js для веб-сайтов Azure][Отладка приложения Node.js для веб-сайтов Azure].

### Восстановление данных

-   [Веб-сайты можно восстанавливать][Веб-сайты можно восстанавливать] из ранее созданных резервных копий.

## При обновлении веб-сайта

Если вы не включили автоматическое резервное копирование, то можете создавать резервные копии [вручную][автоматическое резервное копирование].

Возможно, вас также заинтересует [промежуточное развертывание][промежуточное развертывание]. С его помощью вы сможете публиковать обновления в промежуточной среде, функционирующей параллельно с рабочей.

Если вы используете Visual Studio Online, то можете настроить непрерывное развертывание из системы управления версиями:

-   [С помощью системы управления версиями Team Foundation][С помощью системы управления версиями Team Foundation]
-   [С помощью Git][С помощью Git]



  [Перед развертыванием сайта в рабочей среде]: #before-you-deploy-your-site-to-production
  [В ходе эксплуатации веб-сайта]: #while-your-website-is-running
  [При обновлении веб-сайта]: #when-you-update-your-website
  [Управление веб-сайтами с помощью портала управления Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-manage/
  [Сведения о ценах]: http://azure.microsoft.com/ru-ru/pricing/details/websites/
  [Планы размещения веб-сайтов]: http://azure.microsoft.com/ru-ru/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview
  [сменить уровни]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-scale/
  [портале управления Azure]: https://manage.windowsazure.com/
  [Настройка Веб-сайтов]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-configure/
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [socket.io]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-nodejs-chat-app-socketio/
  [Включение HTTPS для веб-сайта Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-configure-ssl-certificate/
  [Настройка пользовательского имени домена]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-custom-domain-name/
  [Настройка PHP на веб-сайтах Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-php-configure/
  [Настройка Python для веб-сайтов Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-python-configure/
  [добавить метрики производительности]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-monitor
  [Управление производительностью приложений New Relic на веб-сайтах Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/
  [автоматическое резервное копирование]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-backup/
  [этом видео]: http://azure.microsoft.com/ru-ru/documentation/videos/azure-websites-automatic-and-easy-backup/
  [восстановления базы данных]: http://msdn.microsoft.com/ru-ru/library/azure/hh852669.aspx
  [устранять в Visual Studio]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [Включение ведения журналов диагностики для веб-сайтов Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-enable-diagnostic-log/
  [Отладка приложения Node.js для веб-сайтов Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-nodejs-debug/
  [Веб-сайты можно восстанавливать]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-restore/
  [промежуточное развертывание]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-staged-publishing/
  [С помощью системы управления версиями Team Foundation]: http://azure.microsoft.com/ru-ru/documentation/articles/cloud-services-continuous-delivery-use-vso/
  [С помощью Git]: http://azure.microsoft.com/ru-ru/documentation/articles/cloud-services-continuous-delivery-use-vso-git/
