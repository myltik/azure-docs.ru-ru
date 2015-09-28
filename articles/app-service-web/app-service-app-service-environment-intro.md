<properties 
	pageTitle="Введение в среду службы приложения" 
	description="Сведения о функции среды службы приложений, которая предоставляет безопасные выделенные единицы масштабирования, присоединенные к виртуальной сети, для выполнения всех приложений." 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/11/2015" 
	ms.author="stefsch"/>

# Введение в среду службы приложения

## Обзор ##
Среда службы приложений — это вариант [Premium][PremiumTier] плана обслуживания службы приложений Azure, который обеспечивает полностью изолированную, выделенную среду для безопасного запуска всех ваших приложений. В нее входят [веб-приложения][WebApps], [мобильные приложения][MobileApps], [приложения API][APIApps] и [приложения логики][LogicApps] с расширенными возможностями масштабирования.

Вычислительные ресурсы для среды службы приложений выделены исключительно для запуска ваших приложений. Среда службы приложений всегда создается в классической региональной виртуальной сети v1, что предоставляет новые возможности сетевой изоляции для ваших приложений. Кроме того, среда службы приложений поддерживает дополнительные параметры масштабирования и предоставляет до 50 вычислительных ресурсов, доступных для запуска ваших приложения. За пределами среды службы приложений существует ограничение в 20 вычислительных ресурсов для размещения приложения.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Поддержка виртуальной сети ##
Среду службы приложений можно создать либо в уже существующей классической региональной виртуальной сети v1, либо в новой ([дополнительные сведения о виртуальных сетях][MoreInfoOnVirtualNetworks]). Поскольку среда службы приложений всегда существует в региональной виртуальной сети, а точнее в подсети региональной виртуальной сети, можно использовать средства безопасности виртуальных сетей для управления как входящими, так и исходящими подключениями в сети.

**Примечание.** Среду службы приложений невозможно создать в виртуальной сети v2.

Можно использовать [сетевые группы безопасности][NetworkSecurityGroups] для ограничения входящих сетевых подключений для подсети, где находится среда службы приложений. Это позволяет запускать приложения за вышестоящими устройствами и службами, например брандмауэрами веб-приложений и поставщиками SaaS сети.

Приложениям также часто требуется доступ к корпоративным ресурсам, таким как внутренние базы данных и веб-службы. Наиболее распространенный подход заключается в том, чтобы сделать эти конечные точки доступными только для внутреннего сетевого трафика в виртуальной сети Azure. После присоединения среды службы приложений к той же виртуальной сети, что и внутренние службы, запущенные в среде приложения могут получать к ним доступ, включая конечные точки, доступные с помощью соединения [сеть-сеть][SiteToSite] и [Azure ExpressRoute][ExpressRoute].

## Выделенные вычислительные ресурсы ##
Все вычислительные ресурсы в среде службы приложений предназначены исключительно для одной подписки. Среда службы приложений состоит из одного интерфейсного пула вычислительных ресурсов, а также от одного до трех рабочих пулов вычислительных ресурсов.

Интерфейсный пул содержит вычислительные ресурсы, ответственные за завершение SSL-запросов, а также за автоматическую балансировку нагрузки запросов приложений в среде службы приложений.

Каждый рабочий пул содержит вычислительные ресурсы, выделенные для [планов службы приложений][AppServicePlan], которые в свою очередь содержать одно или несколько приложений службы приложения Azure. Поскольку в среде службы приложений может существовать до трех разных рабочих пулов, существует возможность выбирать различные вычислительные ресурсы для каждого рабочего пула.

Например, это позволяет создавать один рабочий пул с менее мощными вычислительными ресурсами для планов службы приложений, предназначенных для разработки или тестирования приложений. Второй (или даже третий) рабочий пул может использовать более мощные вычислительные ресурсы, предназначенные для планов службы приложений с запущенными производственными приложениями.

Среду службы приложений можно настроить для использования до 50 вычислительных ресурсов в одном рабочем пуле. Дополнительные сведения о количестве вычислительных ресурсов, доступных для интерфейсных и рабочих пулов, см. в статье [Настройка среды службы приложений][HowToConfigureanAppServiceEnvironment].

Дополнительные сведения о размерах доступных вычислительных ресурсов, поддерживаемых в среде службы приложений, см. на странице [Ценовые категории службы приложений][AppServicePricing] в разделе о ценовом уровне Premium, где можно ознакомиться с доступными параметрами для сред службы приложений.


## Приступая к работе

Чтобы начать работу со средами службы приложений, см. статью [Создание среды службы приложений][HowToCreateAnAppServiceEnvironment].

Дополнительные сведения о платформе службы приложений Azure см. в статье [Служба приложений Azure][AzureAppService].

Обзор сетевой архитектуры среды службы приложений см. в статье [Обзор сетевой архитектуры][NetworkArchitectureOverview].

Дополнительные сведения об использовании среды службы приложений с ExpressRoute см. в статье [Среды служб приложений и ExpressRoute][NetworkConfigDetailsForExpressRoute].

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
[NetworkArchitectureOverview]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

<!---HONumber=Sept15_HO3-->