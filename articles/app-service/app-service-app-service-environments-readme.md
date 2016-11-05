---
title: Среда службы приложений | Microsoft Docs
description: Сведения о том, что из себя представляет среда службы приложений. Вводная информация о среде службы приложений.
keywords: среда службы приложений Azure, виртуальная сеть, безопасная работа в сети
services: app-service
documentationcenter: ''
author: yochay
manager: wpickett
editor: ''

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2016
ms.author: stefsch

---
# Документация по среде службы приложений
Среда службы приложений — это план класса [Premium][PremiumTier] в службе приложений Azure. Она обеспечивает полностью изолированную и выделенную среду для безопасного выполнения приложений службы приложений Azure в большом масштабе, включая [веб-приложения][WebApps], [мобильные приложения][MobileApps] и [приложения API][APIApps].

Среда службы приложений идеально подходит для рабочих нагрузок приложений, требующих выполнения указанных ниже условий.

* Очень большой масштаб
* Изоляция и безопасный доступ к сети

Клиенты могут создавать несколько сред службы приложений как в одном, так и в нескольких регионах Azure. Благодаря этому среды службы приложений идеально подходят для горизонтально масштабируемых уровней приложений без учета состояний, поддерживающих большие рабочие нагрузки RPS.

Среды службы приложений изолированы, позволяют запускать приложения только одного клиента и всегда развернуты в виртуальной сети. Клиенты могут детально управлять входящим и исходящим сетевым трафиком приложений, используя [группы сетевой безопасности][NetworkSecurityGroups], а приложения — создавать высокоскоростные безопасные подключения к локальным корпоративным ресурсам через виртуальные сети.

Приложениям также часто требуется доступ к корпоративным ресурсам, таким как внутренние базы данных и веб-службы. Приложения, работающие в средах службы приложений, могут получать доступ к ресурсам через VPN-подключение типа [сеть-сеть][SiteToSite] или через [Azure ExpressRoute][ExpressRoute].

* [Введение в среду службы приложений](../app-service-web/app-service-app-service-environment-intro.md)
* [Создание среды службы приложений](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Создание веб-приложения в среде служб приложений](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Using an Internal Load Balancer with an App Service Environment](../app-service-web/app-service-environment-with-internal-load-balancer.md) (Использование внутреннего балансировщика нагрузки в среде службы приложений)
* [Настройка среды службы приложений](../app-service-web/app-service-web-configure-an-app-service-environment.md)
* [Масштабирование приложений в среде службы приложений](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Общие сведения об архитектуре сетевых сред службы приложений](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## Инструкции
[!INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]

## Видеоролики
[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]


[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]



<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

<!---HONumber=AcomDC_0720_2016-->