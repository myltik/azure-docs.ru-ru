<properties title="Manage an Azure website" pageTitle="Управление веб-сайтом Azure" description="Links to resources for managing a Microsoft Azure website." services="web-sites" solutions="web" documentationCenter="" authors="mwasson" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="mwasson" />

# Управление веб-сайтом Azure

В этом разделе представлены ссылки на ресурсы, посвященные управлению веб-сайтом Azure. Под управлением подразумеваются все задачи, направленные на обеспечение стабильной работы веб-сайта. 

На различных этапах существования сайта вам придется выполнять разные задачи управления: от первоначального развертывания до обычной эксплуатации, обслуживания и применения обновлений.

- [Перед развертыванием сайта в рабочей среде]
- [В ходе эксплуатации веб-сайта]
- [При обновлении веб-сайта]

Многие задачи, связанные с управлением веб-сайтом, можно выполнять на портале Azure. Дополнительные сведения см. в разделе [Управление веб-сайтами с помощью портала управления Azure](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-manage/).

## Перед развертыванием сайта в рабочей среде

### Выберите уровень.

Имеется четыре уровня веб-сайтов Azure: "Бесплатный", "Общедоступный", "Базовый" и "Стандартный". Информацию о функциях и ценах для каждого уровня см. в разделе [Сведения о ценах](http://azure.microsoft.com/ru-ru/pricing/details/websites/). 

- [Планы размещения веб-сайтов](http://azure.microsoft.com/ru-ru/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview) позволяют группировать несколько веб-сайтов на одном уровне.
- Всегда можно [сменить уровень](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-scale/) после создания веб-сайта.

### Конфигурация

На [портале управления Azure](https://manage.windowsazure.com/) можно задать различные параметры конфигурации. Дополнительные сведения см. в разделе [Настройка Веб-сайтов](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-configure/). Ниже приведен краткий контрольный список.

- При необходимости выберите **версии сред выполнения** для .NET, PHP, Java или Python.
- Если на веб-сайте используется протокол **WebSockets**, включите его поддержку. (Это относится к приложениям, в которых используется [ASP.NET SignalR](http://www.asp.net/signalr) или [socket.io](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-nodejs-chat-app-socketio/).)
- Вы используете непрерывные веб-задания? Если да, то выберите параметр **Всегда включено**.
- Задайте **документ по умолчанию**, например index.html.

Помимо этих базовых параметров конфигурации, вы также можете настроить следующее:

- **Шифрование по протоколу SSL**. Чтобы использовать SSL с пользовательским доменным именем, вам необходимо получить SSL-сертификат и настроить свой сайт для работы с ним. См. раздел [Включение HTTPS для веб-сайта Azure](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-configure-ssl-certificate/).
- **Пользовательское доменное имя.** Веб-сайт автоматически имеет поддомен в домене azurewebsites.net. Можно связать с ним пользовательское имя домена, например contoso.com. См. раздел [Настройка пользовательского имени домена](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-custom-domain-name/).

Настройка для определенных языков программирования:

- **PHP**. [Настройка PHP на веб-сайтах Azure](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-php-configure/).
- **Python**. [Настройка Python для веб-сайтов Azure](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-python-configure/)


## В ходе эксплуатации веб-сайта

В процессе эксплуатации веб-сайта важно обеспечить его постоянную доступность и масштабирование ресурсов с учетом пользовательского трафика. Кроме того, возможно, придется устранять возникающие неполадки.

### Мониторинг

- На портале управления можно [добавить метрики производительности](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-monitor), например показатель использования ЦП и количество запросов от клиентов.
- Если необходимы более подробные данные, воспользуйтесь средством New Relic для мониторинга и настройки производительности. См. раздел [Управление производительностью приложений New Relic на веб-сайтах Azure](http://azure.microsoft.com/ru-ru/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/).
- [Ресурсы веб-сайта можно масштабировать](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-scale/) с учетом трафика. В зависимости от уровня плана размещения можно изменять количество виртуальных машин и (или) размер экземпляров виртуальных машин. В плане "Стандартный" вы также можете настроить автоматическое масштабирование сайта по заданному графику или с учетом нагрузки. 
 
### Резервные копии

- Настройте [автоматическое резервное копирование](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-backup/) своего веб-сайта. Дополнительные сведения о резервном копировании см. в [этом видео](http://azure.microsoft.com/ru-ru/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Ознакомьтесь с параметрами [восстановления базы данных](http://msdn.microsoft.com/ru-ru/library/azure/hh852669.aspx) Azure SQL.

### Устранение неполадок

- Возникшие неполадки можно [устранить в среде Visual Studio](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug) с помощью журналов диагностики и отладки в облаке в режиме реального времени. 
- Кроме этого, журналы диагностики могут собираться различными способами не только в Visual Studio. См. раздел [Включение ведения журналов диагностики для веб-сайтов Azure](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-enable-diagnostic-log/).
- Для приложений Node.js см. раздел [Отладка приложения Node.js для веб-сайтов Azure](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-nodejs-debug/).

### Восстановление данных

- [Веб-сайты можно восстановить](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-restore/) из ранее созданных резервных копий.


## При обновлении веб-сайта

Если не включено автоматическое резервное копирование, то можно создать резервную копию [вручную](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-backup/).

Может пригодиться также [промежуточное развертывание](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-staged-publishing/). С его помощью вы сможете публиковать обновления в промежуточной среде, функционирующей параллельно с рабочей. 

Если вы используете Visual Studio Online, то можете настроить непрерывное развертывание из системы управления версиями:

- [С помощью системы управления версиями Team Foundation](http://azure.microsoft.com/ru-ru/documentation/articles/cloud-services-continuous-delivery-use-vso/) 
- [С помощью Git](http://azure.microsoft.com/ru-ru/documentation/articles/cloud-services-continuous-delivery-use-vso-git/)
 

 
<!-- Anchors. -->


[Перед развертыванием сайта в рабочей среде]: #before-you-deploy-your-site-to-production
[В ходе эксплуатации веб-сайта]: #while-your-website-is-running
[При обновлении веб-сайта]: #when-you-update-your-website

 
