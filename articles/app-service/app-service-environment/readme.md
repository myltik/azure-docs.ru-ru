---
title: "Файл сведений о среде службы приложений Azure"
description: "В этой статье приводится список документации по среде службы приложений Azure."
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 77452413-5193-4762-8b3d-5fa8e4edf1ca
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 5b1362854dbc3b0098718bd2ea3cffb06366000c
ms.contentlocale: ru-ru
ms.lasthandoff: 08/03/2017

---

# <a name="app-service-environment-documentation"></a>Документация по среде службы приложений
 Среда службы приложений Azure — это компонент службы приложений Azure, предоставляющий полностью изолированную и выделенную среду для безопасного выполнения приложений службы приложений в большом масштабе. В ней можно размещать свои [веб-приложения][webapps], [мобильные приложения][mobileapps], [приложения API][APIApps] и [функции][Functions].

Среды службы приложений (ASE) идеально подходят для рабочих нагрузок приложений, требующих выполнения указанных ниже условий.

* Очень большой масштаб.
* Изоляция и безопасный доступ к сети.

Клиенты могут создавать несколько сред ASE в одном или в нескольких регионах Azure. Благодаря этой гибкости среды ASE идеально подходят для горизонтально масштабируемых уровней приложений без учета состояний, поддерживающих большие рабочие нагрузки RPS.

Среды ASE изолированы, позволяют запускать приложения только одного клиента и всегда развернуты в виртуальной сети Azure. Клиенты могут детально управлять входящим и исходящим сетевым трафиком приложений, используя [группы безопасности сети][NSGs], а приложения — создавать высокоскоростные безопасные подключения к локальным корпоративным ресурсам через виртуальные сети.

Приложениям также часто требуется доступ к корпоративным ресурсам, таким как внутренние базы данных и веб-службы. Приложения, работающие в средах ASE, могут получать доступ к ресурсам через VPN-подключение типа [сеть — сеть][SiteToSite] или через канал [Azure ExpressRoute][ExpressRoute].

* [Введение в среду службы приложений][Intro]
* [Создание среды службы приложений][MakeExternalASE]
* [Создание и использование внутреннего балансировщика нагрузки со средой службы приложений][MakeILBASE]
* [Использование среды службы приложений][UsingASE]
* [Рекомендации по работе с сетями в среде службы приложений][ASENetwork]
* [Создание среды службы приложений с внутренней подсистемой балансировки нагрузки с помощью шаблонов Azure Resource Manager][MakeASEfromTemplate]


## <a name="videos"></a>Видеоролики
Создание современной корпоративной платформы PaaS с помощью службы приложений Azure
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

Развертывание высокомасштабируемых и защищенных приложений
>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

Выполнение корпоративных веб-приложений и мобильных приложений в службе приложений Azure
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]

## <a name="app-service-environment-v1"></a>Среда службы приложений версии 1 ##
Существует две версии среды службы приложений: ASEv1 и ASEv2. Сведения об ASEv1 см. в статье [Документация по среде службы приложений][ASEv1README].


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[ASEv1README]: ../app-service-app-service-environments-readme.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-site-to-site-create.md
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

