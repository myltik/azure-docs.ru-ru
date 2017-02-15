---
title: "Введение в среду службы приложения"
description: "Сведения о функции среды службы приложений, которая предоставляет безопасные выделенные единицы масштабирования, присоединенные к виртуальной сети, для выполнения всех приложений."
services: app-service
documentationcenter: 
author: stefsch
manager: wpickett
editor: 
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 465710ca02b8f905b11980e0023d7feceabf34f1


---
# <a name="introduction-to-app-service-environment"></a>Введение в среду службы приложения
## <a name="overview"></a>Обзор
Среда службы приложений — это план класса [Премиум][PremiumTier] в службе приложений Azure. Она обеспечивает полностью изолированную и выделенную среду для безопасного выполнения приложений службы приложений Azure в большом масштабе, в том числе [веб-приложений][WebApps], [мобильных приложений][MobileApps] и [приложений API][APIApps].  

Среда службы приложений идеально подходит для рабочих нагрузок приложений, требующих выполнения указанных ниже условий.

* Очень большой масштаб
* Изоляция и безопасный доступ к сети

Клиенты могут создавать несколько сред службы приложений как в одном, так и в нескольких регионах Azure.  Благодаря этому среды службы приложений идеально подходят для горизонтально масштабируемых уровней приложений без учета состояний, поддерживающих большие рабочие нагрузки RPS.

Среды службы приложений изолированы, позволяют запускать приложения только одного клиента и всегда развернуты в виртуальной сети.  Клиенты могут детально управлять входящим и исходящим сетевым трафиком приложений, а приложения могут создавать высокоскоростные безопасные подключения к локальным корпоративным ресурсам через виртуальные сети.

Все статьи и практические руководства, посвященные средам службы приложений, доступны в [файле сведений для сред службы приложений](../app-service/app-service-app-service-environments-readme.md).

Обзор масштабирования и безопасного подключения к сети, которые обеспечивают среды службы приложений, см. в статье [Подробный обзор AzureCon][AzureConDeepDive] в средах службы приложений.

Подробный обзор горизонтального масштабирования с использованием нескольких сред службы приложений см. в статье о настройке [географически распределенного приложения][GeodistributedAppFootprint].

Чтобы ознакомиться с настройкой архитектуры в подробном обзоре AzureCon, обратитесь к статье о реализации [многоуровневой архитектуры безопасности](app-service-app-service-environment-layered-security.md) со средами службы приложений.

Приложения, работающие в средах службы приложений, могут получать доступ через такие устройства, как брандмауэр веб-приложений (WAF).  Этот сценарий рассматривается в статье [Настройка WAF для сред службы приложений](app-service-app-service-environment-web-application-firewall.md) . 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Выделенные вычислительные ресурсы
Все вычислительные ресурсы в среде службы приложений выделены исключительно для одной подписки. Для среды службы приложений можно настроить до 50 вычислительных ресурсов для монопольного использования одним приложением.

В среду службы приложений входит один интерфейсный пул вычислительных ресурсов, а также от одного до трех рабочих пулов вычислительных ресурсов. 

Интерфейсный пул содержит вычислительные ресурсы, ответственные за завершение SSL-запросов, а также за автоматическую балансировку нагрузки запросов приложений в среде службы приложений. 

Каждый рабочий пул содержит вычислительные ресурсы, выделенные для [планов службы приложений][AppServicePlan], которые, в свою очередь, содержат одно или несколько приложений службы приложения Azure.  Поскольку в среде службы приложений может существовать до трех разных рабочих пулов, существует возможность выбирать различные вычислительные ресурсы для каждого рабочего пула.  

Например, это позволяет создавать один рабочий пул с менее мощными вычислительными ресурсами для планов службы приложений, предназначенных для разработки или тестирования приложений.  Второй (или даже третий) рабочий пул может использовать более мощные вычислительные ресурсы, предназначенные для планов службы приложений с запущенными производственными приложениями.

Дополнительные сведения о количестве вычислительных ресурсов, доступных для интерфейсных и рабочих пулов, см. в статье [Настройка среды службы приложений][HowToConfigureanAppServiceEnvironment].  

Дополнительные сведения о размерах доступных вычислительных ресурсов, поддерживаемых в среде службы приложений, см. на странице [Цены — служба приложений][AppServicePricing], где можно ознакомиться с доступными параметрами для сред службы приложений в разделе о ценовой категории "Премиум".

## <a name="virtual-network-support"></a>Поддержка виртуальной сети
Среду службы приложений можно создать **либо** в виртуальной сети Azure Resource Manager, **либо** в виртуальной сети классической модели развертывания. См. [дополнительные сведения о виртуальных сетях][MoreInfoOnVirtualNetworks].  Так как среда службы приложений всегда существует в виртуальной сети, а точнее в подсети виртуальной сети, можно использовать средства безопасности виртуальных сетей для управления как входящими, так и исходящими подключениями в сети.  

Среда службы приложений может быть доступной из Интернета с использованием общедоступного IP-адреса или из внутренней системы с использованием адреса внутренней подсистемы балансировки нагрузки.

Вы можете использовать [группы безопасности сети][NetworkSecurityGroups], чтобы ограничить входящие подключения к подсети, где находится среда службы приложений.  Это позволяет запускать приложения за вышестоящими устройствами и службами, например брандмауэрами веб-приложений и поставщиками SaaS сети.

Приложениям также часто требуется доступ к корпоративным ресурсам, таким как внутренние базы данных и веб-службы.  Наиболее распространенный подход заключается в том, чтобы сделать эти конечные точки доступными только для внутреннего сетевого трафика в виртуальной сети Azure.  После присоединения среды службы приложений к той же виртуальной сети, что и внутренние службы, запущенные в среде приложения могут получать к ним доступ, в том числе к конечным точкам, доступным через соединение [сеть — сеть][SiteToSite] и [Azure ExpressRoute][ExpressRoute].

Дополнительные сведения о том, как среды службы приложений работают с виртуальными и локальными сетями, см. в статьях [Общие сведения об архитектуре сетевых сред службы приложений][NetworkArchitectureOverview], [Как управлять входящим трафиком в среде службы приложений][ControllingInboundTraffic] и [Безопасное подключение к серверным ресурсам из среды службы приложений][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Приступая к работе
Сведения о том, как начать работу со средами службы приложений, см. в статье [Создание среды службы приложения][HowToCreateAnAppServiceEnvironment].

Все статьи и практические руководства, посвященные средам службы приложений, доступны в [файле сведений для сред службы приложений](../app-service/app-service-app-service-environments-readme.md).

Дополнительные сведения о платформе службы приложений Azure см. в [этой статье][AzureAppService].

Обзор сетевой архитектуры среды службы приложений см. в статье [Общие сведения об архитектуре сетевых сред службы приложений][NetworkArchitectureOverview].

Дополнительные сведения об использовании среды службы приложений совместно с ExpressRoute см. в статье [Сведения о конфигурации сети для сред службы приложений с ExpressRoute][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->





<!--HONumber=Nov16_HO3-->


