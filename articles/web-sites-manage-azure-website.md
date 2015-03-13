<properties 
	pageTitle="Управление веб-сайтом Azure" 
	description="Ссылки на ресурсы для управления веб-сайтом Microsoft Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2014" 
	ms.author="mwasson"/>

# Управление веб-сайтом Azure

В этом разделе представлены ссылки на ресурсы, посвященные управлению веб-сайтом Azure. Под управлением подразумеваются все задачи, направленные на обеспечение стабильной работы веб-сайта. 

На различных этапах существования сайта вам придется выполнять разные задачи управления: от первоначального развертывания до обычной эксплуатации, обслуживания и применения обновлений.

- [Перед развертыванием сайта в рабочей среде]
- [В ходе эксплуатации веб-сайта]
- [При обновлении веб-сайта]

Многие задачи, связанные с управлением веб-сайтом, можно выполнять на портале Azure. Дополнительную информацию см. в разделе [Управление веб-сайтами на портале управления Azure](http://azure.microsoft.com/documentation/articles/web-sites-manage/).

## Перед развертыванием сайта в рабочей среде

### Выберите уровень.

Доступны четыре уровня Веб-сайтов Azure: "Бесплатный", "Общедоступный", "Базовый" и "Стандартный". Информацию о функциях и ценах для каждого уровня см. на странице [информации о ценах](http://azure.microsoft.com/pricing/details/websites/). 

- [Планы размещения веб-сайтов](http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview) позволяют группировать несколько веб-сайтов на одном уровне.
- Вы всегда можете [сменить уровни](http://azure.microsoft.com/documentation/articles/web-sites-scale/) после создания веб-сайта.

### Конфигурация

На [портале управления Azure](https://manage.windowsazure.com/) можно задать различные параметры конфигурации. Дополнительную информацию см. в разделе [Настройка Веб-сайтов](http://azure.microsoft.com/documentation/articles/web-sites-configure/). Вот краткий контрольный список:

- При необходимости выберите **версии сред выполнения** для .NET, PHP, Java или Python.
- Если на вашем веб-сайте используется протокол **WebSocket**, включите его поддержку. (это относится к приложениям, в которых применяется [ASP.NET SignalR](http://www.asp.net/signalr) или [socket.io](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-chat-app-socketio/)).
- Вы используете непрерывные веб-задания? Если да, то выберите параметр **Always On** (Всегда включено).
- Задайте **документ по умолчанию**, например index.html.

Помимо этих базовых параметров конфигурации, вы также можете настроить следующее:

- **Шифрование по протоколу SSL**. Чтобы использовать SSL с пользовательским доменным именем, вам необходимо получить SSL-сертификат и настроить свой сайт для работы с ним. См. раздел [Включение HTTPS для веб-сайта Azure](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/).
- **Личное доменное имя.** Веб-сайт автоматически имеет поддомен в домене azurewebsites.net. Можно связать с ним пользовательское имя домена, например contoso.com. См. раздел [Настройка пользовательского имени домена](http://azure.microsoft.com/documentation/articles/web-sites-custom-domain-name/).

Конфигурация языков:

- **PHP**. [Настройка PHP на веб-сайтах Azure](http://azure.microsoft.com/documentation/articles/web-sites-php-configure/).
- **Python**. [Настройка Python для веб-сайтов Azure](http://azure.microsoft.com/documentation/articles/web-sites-python-configure/)


## В ходе эксплуатации веб-сайта

В процессе эксплуатации веб-сайта важно обеспечить его постоянную доступность и масштабирование ресурсов с учетом пользовательского трафика. Кроме того, возможно, вам придется устранять возникающие неполадки.

### Мониторинг

- На портале управления вы можете [добавить метрики производительности](http://azure.microsoft.com/documentation/articles/web-sites-monitor), например показатель использования ЦП и количество запросов от клиентов.
- Если вам нужны более подробные данные, воспользуйтесь средством New Relic для мониторинга производительности и управления ею. См. раздел [Инструмент управления производительностью приложений New Relic для веб-сайтов Azure](http://azure.microsoft.com/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/).
- [Ресурсы веб-сайта можно масштабировать](http://azure.microsoft.com/documentation/articles/web-sites-scale/) с учетом трафика. В зависимости от уровня плана размещения можно изменять количество виртуальных машин и (или) размер экземпляров виртуальных машин. В плане "Стандартный" вы также можете настроить автоматическое масштабирование сайта по заданному графику или с учетом нагрузки.  
 
### Резервные копии

- Настройте [автоматическое резервное копирование](http://azure.microsoft.com/documentation/articles/web-sites-backup/) своего веб-сайта. Дополнительную информацию о резервном копировании см. в [этом видео](http://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Ознакомьтесь с параметрами [восстановления базы данных](http://msdn.microsoft.com/library/azure/hh852669.aspx) SQL Azure.

### Устранение неполадок

- Возникающие неполадки можно [устранять в Visual Studio](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug) с помощью журналов диагностики и отладки в облаке в режиме реального времени. 
- Кроме того, журналы диагностики можно собирать различными способами не только в Visual Studio. См. раздел [Включение ведения журналов диагностики для веб-сайтов Azure](http://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).
- Для приложений Node.js см. раздел [Настройка приложения Node.js для веб-сайтов Microsoft Azure](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-debug/).

### Восстановление данных

- [Веб-сайты можно восстанавливать](http://azure.microsoft.com/documentation/articles/web-sites-restore/) из ранее созданных резервных копий.


## При обновлении веб-сайта

Если вы не включили автоматическое резервное копирование, то можете создавать [резервные копии вручную](http://azure.microsoft.com/documentation/articles/web-sites-backup/).

Возможно, вас также заинтересует [промежуточное развертывание](http://azure.microsoft.com/documentation/articles/web-sites-staged-publishing/). С его помощью вы сможете публиковать обновления в промежуточной среде, функционирующей параллельно с рабочей. 

Если вы используете Visual Studio Online, то можете настроить непрерывное развертывание из системы управления версиями:

- [С помощью системы управления версиями Team Foundation](http://azure.microsoft.com/documentation/articles/cloud-services-continuous-delivery-use-vso/) 
- [С помощью Git](http://azure.microsoft.com/documentation/articles/cloud-services-continuous-delivery-use-vso-git/)
 

 
<!-- Anchors. -->


[Перед развертыванием сайта в рабочей среде]: #before-you-deploy-your-site-to-production
[В ходе эксплуатации веб-сайта]: #while-your-website-is-running
[При обновлении веб-сайта]: #when-you-update-your-website

 

<!--HONumber=42-->
