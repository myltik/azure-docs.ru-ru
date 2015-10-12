<properties 
	pageTitle="Введение в среду службы приложения" 
	description="Сведения о функции среды службы приложений, которая предоставляет безопасные выделенные единицы масштабирования, присоединенные к виртуальной сети, для выполнения всех приложений." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/25/2015"
	ms.author="stefsch"/>

# Введение в среду службы приложения

## Обзор ##
Среда службы приложений — это план класса [Premium][PremiumTier] в службе приложений Azure. Она обеспечивает полностью изолированную и выделенную среду для безопасного выполнения приложений службы приложений Azure в большом масштабе, включая [веб-приложения][WebApps], [мобильные приложения][MobileApps] и [приложения API][APIApps].

Среда службы приложений идеально подходит для рабочих нагрузок приложений, требующих выполнения указанных ниже условий.

- Очень большой масштаб
- Изоляция и безопасный доступ к сети

Клиенты могут создавать несколько сред службы приложений как в одном, так и в нескольких регионах Azure. Благодаря этому среды службы приложений идеально подходят для горизонтально масштабируемых уровней приложений без учета состояний, поддерживающих большие рабочие нагрузки RPS.

Среды службы приложений изолированы, позволяют запускать приложения только одного клиента и всегда развернуты в виртуальной сети. Клиенты могут детально управлять входящим и исходящим сетевым трафиком приложений, а приложения могут создавать высокоскоростные безопасные подключения к локальным корпоративным ресурсам через виртуальные сети.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Выделенные вычислительные ресурсы ##
Все вычислительные ресурсы в среде службы приложений выделены исключительно для одной подписки. Для среды службы приложений можно настроить до 50 вычислительных ресурсов для монопольного использования одним приложением.

В среду службы приложений входит один интерфейсный пул вычислительных ресурсов, а также от одного до трех рабочих пулов вычислительных ресурсов.

Интерфейсный пул содержит вычислительные ресурсы, ответственные за завершение SSL-запросов, а также за автоматическую балансировку нагрузки запросов приложений в среде службы приложений.

Каждый рабочий пул содержит вычислительные ресурсы, выделенные для [планов службы приложений][AppServicePlan], которые в свою очередь содержать одно или несколько приложений службы приложения Azure. Поскольку в среде службы приложений может существовать до трех разных рабочих пулов, существует возможность выбирать различные вычислительные ресурсы для каждого рабочего пула.

Например, это позволяет создавать один рабочий пул с менее мощными вычислительными ресурсами для планов службы приложений, предназначенных для разработки или тестирования приложений. Второй (или даже третий) рабочий пул может использовать более мощные вычислительные ресурсы, предназначенные для планов службы приложений с запущенными производственными приложениями.

Дополнительные сведения о количестве вычислительных ресурсов, доступных для интерфейсных и рабочих пулов, см. в статье [Настройка среды службы приложений][HowToConfigureanAppServiceEnvironment].

Дополнительные сведения о размерах доступных вычислительных ресурсов, поддерживаемых в среде службы приложений, см. на странице [Ценовые категории службы приложений][AppServicePricing] в разделе о ценовом уровне Premium, где можно ознакомиться с доступными параметрами для сред службы приложений.

## Поддержка виртуальной сети ##
Среду службы приложений можно создать либо в уже существующей, либо в новой классической региональной виртуальной сети v1 (см. [дополнительные сведения о виртуальных сетях][MoreInfoOnVirtualNetworks]). Поскольку среда службы приложений всегда существует в региональной виртуальной сети, а точнее в подсети региональной виртуальной сети, можно использовать средства безопасности виртуальных сетей для управления как входящими, так и исходящими подключениями в сети.

Можно использовать [сетевые группы безопасности][NetworkSecurityGroups] для ограничения входящих сетевых подключений для подсети, где находится среда службы приложений. Это позволяет запускать приложения за вышестоящими устройствами и службами, например брандмауэрами веб-приложений и поставщиками SaaS сети.

Приложениям также часто требуется доступ к корпоративным ресурсам, таким как внутренние базы данных и веб-службы. Наиболее распространенный подход заключается в том, чтобы сделать эти конечные точки доступными только для внутреннего сетевого трафика в виртуальной сети Azure. После присоединения среды службы приложений к той же виртуальной сети, что и внутренние службы, запущенные в среде приложения могут получать к ним доступ, включая конечные точки, доступные с помощью соединения [сеть-сеть][SiteToSite] и [Azure ExpressRoute][ExpressRoute].

Дополнительные сведения о том, как среды службы приложений работают с виртуальными и локальными сетями, см. в статьях [Общие сведения об архитектуре сетевых сред службы приложений][NetworkArchitectureOverview], [Как управлять входящим трафиком в среде службы приложений][ControllingInboundTraffic] и [Безопасное подключение к серверным ресурсам из среды службы приложений][SecurelyConnectingToBackends].

**Примечание.** Среду службы приложений не удастся создать в виртуальной сети v2.

## Приступая к работе

Чтобы начать работу со средами службы приложений, см. статью [Создание среды службы приложений][HowToCreateAnAppServiceEnvironment].

Дополнительные сведения о платформе службы приложений Azure см. в статье [Служба приложений Azure][AzureAppService].

Обзор сетевой архитектуры среды службы приложений см. в статье [Общие сведения об архитектуре сетевых сред службы приложений][NetworkArchitectureOverview].

Дополнительные сведения об использовании среды службы приложений совместно с ExpressRoute см. в статье [Сведения о конфигурации сети для сред службы приложений с ExpressRoute][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[Azure preview portal]: http://portal.azure.com
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

<!---HONumber=Oct15_HO1-->