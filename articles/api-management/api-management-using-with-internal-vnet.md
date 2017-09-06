---
title: "Как использовать управление API Azure с внутренними виртуальными сетями | Документация Майкрософт"
description: "Узнайте, как установить и настроить управление API Azure во внутренней виртуальной сети."
services: api-management
documentationcenter: 
author: vladvino
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: a4c2bda1226ca05c775d011fba7bc59d4dab8998
ms.contentlocale: ru-ru
ms.lasthandoff: 08/31/2017

---
# <a name="using-azure-api-management-service-with-internal-virtual-network"></a>Использование службы управления API Azure совместно с внутренней виртуальной сетью
Используемая с виртуальными сетями Azure служба управления API Azure позволяет работать с интерфейсами API, которые недоступны из Интернета. Такие подключения устанавливаются с помощью разных средств, а служба управления API может быть развернута в одном из двух режимов.
* Внешний
* Внутренний

## <a name="overview"> </a>Содержание раздела
Когда вы развертываете управление API во внутреннем режиме виртуальной сети, все конечные точки службы (шлюз, портал разработчика, портал издателя, конечная точка прямого управления и Git) отображаются только в пределах виртуальной сети, доступ к которой вы контролируете. Ни одна из конечных точек службы не регистрируется на общедоступных DNS-серверах.

Используя управление API во внутреннем режиме, вы можете реализовать следующие сценарии:
* безопасно предоставлять третьим лицам доступ к API, размещенным в частном центре обработки данных, с помощью VPN-подключений типа "сеть —сеть" или ExpressRoute;
* поддерживать сценарии гибридного облака, предоставляя облачные и локально размещенные API-интерфейсы через общий шлюз;
* управлять API-интерфейсами, размещенными в разных географических расположениях, через одну общую конечную точку шлюза. 

## <a name="enable-vpn"> </a>Создание управления API во внутренней виртуальной сети
Служба управления API во внутренней виртуальной сети размещается за внутренней подсистемой балансировки нагрузки (ILB). IP-адрес ILB находится в диапазоне [RFC1918](http://www.faqs.org/rfcs/rfc1918.html).  

### <a name="enable-vnet-connection-using-azure-portal"></a>Активация подключения к виртуальной сети с помощью портала Azure
Сначала создайте службу управления API, выполнив действия из раздела [Создание экземпляра управления API][Create API Management service]. Затем укажите, что управление API будет развертываться в пределах виртуальной сети.

![Меню настройки APIM во внутренней виртуальной сети][api-management-using-internal-vnet-menu]

Завершив развертывание, вы увидите на панели мониторинга внутренний виртуальный IP-адрес вашей службы.

![Панель мониторинга управления API с настроенной внутренней сетью][api-management-internal-vnet-dashboard]

### <a name="enable-vnet-connection-using-powershell-cmdlets"></a>Активация подключения к виртуальной сети с помощью командлетов PowerShell
Подключение к виртуальной сети можно также активировать с помощью командлетов PowerShell.

* **Создание службы управления API в виртуальной сети**. Чтобы создать в виртуальной сети службу управления API Azure и настроить для нее внутренний режим виртуальной сети, используйте командлет [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement).

* **Развертывание существующей службы управления API в виртуальной сети**. Чтобы переместить в виртуальную сеть существующую службу управления API Azure и настроить для нее внутренний режим виртуальной сети, используйте командлет [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment).

## <a name="apim-dns-configuration"></a>Настройка DNS
Когда вы используете управление API во внешнем режиме виртуальной сети, службой DNS управляет Azure. Чтобы использовать внутренний режим виртуальной сети, управлять DNS вы должны самостоятельно.

> [!NOTE]
> Служба управления API не прослушивает запросы, поступающие по IP-адресам. Она отвечает только на запросы, в которых указаны имена узлов, настроенные для конечных точек этой службы (включая шлюз, портал для разработчиков, портал издателя, конечную точку прямого управления и Git).

### <a name="access-on-default-host-names"></a>По умолчанию используются такие имена узлов:
Например, при создании службы управления API в общедоступном облаке Azure с именем contoso будут по умолчанию настроены следующие конечные точки службы:

>   шлюз или прокси — contoso.azure api.net;

> портал издателя и портал для разработчиков — contoso.portal.azure api.net;

> конечная точка прямого управления — contoso.management.azure api.net;

>   Git — contoso.scm.azure-api.net.

Для получения доступа к этим конечным точкам службы управления API, можно создать виртуальную машину в подсети, подключенной к виртуальной сети, в которой развернута служба управления API. Если для службы используется внутренний виртуальный IP-адрес 10.0.0.5, в файле hosts (расположен по адресу %системный_диск%\drivers\etc\hosts) можно задать следующие сопоставления:

> 10.0.0.5    contoso.azure-api.net

> 10.0.0.5    contoso.portal.azure-api.net

> 10.0.0.5    contoso.management.azure-api.net

> 10.0.0.5    contoso.scm.azure-api.net

Теперь с созданной виртуальной машины вы сможете обращаться к любой из конечных точек службы. Если вы используете пользовательский DNS-сервер в виртуальной сети, можно создать соответствующие записи DNS типа A. Тогда доступ к конечным точкам будет возможен из любого расположения в пределах виртуальной сети. 

### <a name="access-on-custom-domain-names"></a>Доступ по пользовательским доменным именам
Если для обращения к службе управления API вы не хотите использовать имена узлов по умолчанию, для всех конечных точек службы можно настроить пользовательские доменные имена, как указано ниже:

![Настройка пользовательского домена для управления API][api-management-custom-domain-name]

После этого создайте на DNS-сервере записи тип A для обращения к этим конечным точкам, которые будут доступны только в пределах виртуальной сети.

## <a name="related-content"> </a>Связанная информация
* Обзор [распространенных проблем с конфигурацией сети при настройке APIM в виртуальной сети][Common Network Configuration Issues]
* [Часто задаваемые вопросы по виртуальной сети](../virtual-network/virtual-networks-faq.md)
* Инструкции по [созданию записей DNS типа A](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: api-management-get-started.md#create-service-instance
[Common Network Configuration Issues]: api-management-using-with-vnet.md#network-configuration-issues

