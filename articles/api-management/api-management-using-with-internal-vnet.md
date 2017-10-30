---
title: "Как использовать управление API Azure с внутренними виртуальными сетями | Документация Майкрософт"
description: "Узнайте, как установить и настроить управление API Azure для внутренней виртуальной сети"
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
ms.date: 09/29/2017
ms.author: apimpm
ms.openlocfilehash: badfee15fba5822b383b09a6cc29d9944e64e007
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Использование службы управления API Azure совместно с внутренней виртуальной сетью
Служба управления API Azure в сочетании с виртуальными сетями Azure позволяет работать с интерфейсами API, которые недоступны из Интернета. Подключение можно создать с применением разных технологий VPN. Управление API можно развернуть в виртуальной сети в одном из двух основных режимов:
* Внешний
* Внутренний


Если управление API развернуто во внутреннем режиме виртуальной сети, все конечные точки службы (шлюз, портал разработчика, портал издателя, конечная точка прямого управления и репозиторий Git) доступны только из этой виртуальной сети, и вы контролируете доступ к ней. Ни одна из конечных точек служб не регистрируется на общедоступных DNS-серверах.

Используя управление API во внутреннем режиме, вы можете реализовать следующие сценарии:
* предоставлять третьим лицам защищенный доступ к API, размещенным в частном центре обработки данных, с помощью VPN-подключений типа "сеть — сеть" или Azure ExpressRoute;
* создать гибридное облачное развертывание, предоставляя единый шлюз для облачных и локально размещенных API-интерфейсов;
* управлять API-интерфейсами, размещенными в нескольких географических расположениях, через одну общую конечную точку шлюза. 


## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

+ **Активная подписка Azure.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Экземпляр Azure API Management.** Дополнительные сведения см. в статье о [создании экземпляра управления API Azure](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Создание управления API во внутренней виртуальной сети
Служба управления API во внутренней виртуальной сети размещается за внутренней подсистемой балансировки нагрузки (ILB). IP-адрес ILB находится в диапазоне [RFC1918](http://www.faqs.org/rfcs/rfc1918.html).  

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Настройка подключения к виртуальной сети с помощью портала Azure

1. Перейдите к экземпляру управления API Azure на [портале Azure](https://portal.azure.com/).
2. Выберите **Личные домены и SSL**.
3. Укажите, что развертывание экземпляра управления API выполняется внутри виртуальной сети.

    ![Меню для настройки управления API Azure во внутренней виртуальной сети][api-management-using-internal-vnet-menu]

4. Щелкните **Сохранить**.

Завершив развертывание, вы увидите на панели мониторинга внутренний виртуальный IP-адрес вашей службы.

![Панель мониторинга службы управления API, демонстрирующая настроенную внутреннюю виртуальную сеть][api-management-internal-vnet-dashboard]

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Настройка подключения к виртуальной сети с помощью командлетов PowerShell
Вы можете настроить подключение к виртуальной сети с помощью командлетов PowerShell.

* Создание службы управления API в виртуальной сети. Чтобы создать в виртуальной сети службу управления API Azure и настроить для нее внутренний режим виртуальной сети, используйте командлет [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement).

* Развертывание существующей службы управления API в виртуальной сети. Чтобы переместить в виртуальную сеть существующую службу управления API Azure и настроить для нее внутренний режим виртуальной сети, используйте командлет [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment).

## <a name="apim-dns-configuration"></a>Настройка DNS
Если управление API настроено во внешнем режиме виртуальной сети, службой DNS управляет Azure. Чтобы использовать внутренний режим виртуальной сети, управлять DNS вы должны самостоятельно.

> [!NOTE]
> Служба управления API не прослушивает запросы, поступающие на IP-адреса. Она реагирует только на запросы к имени узла, которое настроено на конечных точках службы. К ним относятся шлюз, портал разработчика, портал издателя, конечная точка прямого управления и репозиторий Git.

### <a name="access-on-default-host-names"></a>Доступ по именам узлов по умолчанию
Если вы создадите службу управления API, например с именем contoso, по умолчанию будут настроены следующие конечные точки службы:

   * шлюз или прокси — contoso.azure api.net;

   * портал издателя и портал разработчика — contoso.portal.azure api.net;

   * конечная точка прямого управления — contoso.management.azure api.net;

   * репозиторий Git — contoso.scm.azure-api.net.

Чтобы получить доступ к этим конечным точкам службы управления API, можно создать виртуальную машину в подсети, подключенной к виртуальной сети, в которой развернута служба управления API. Если для службы используется внутренний виртуальный IP-адрес 10.0.0.5, в файле hosts (расположен по адресу %системный_диск%\drivers\etc\hosts) можно задать следующие сопоставления:

   * 10.0.0.5     contoso.azure-api.net

   * 10.0.0.5     contoso.portal.azure-api.net

   * 10.0.0.5     contoso.management.azure-api.net

   * 10.0.0.5     contoso.scm.azure-api.net

Теперь вы сможете обращаться с этой виртуальной машины к любой из конечных точек службы. Если в вашей в виртуальной сети действует пользовательский DNS-сервер, вы можете создать на нем записи DNS типа A. Тогда доступ к конечным точкам будет возможен из любого расположения в пределах виртуальной сети. 

### <a name="access-on-custom-domain-names"></a>Доступ по пользовательским доменным именам

   1. Если для обращения к службе управления API вы не хотите использовать имена узлов по умолчанию, для всех конечных точек службы можно настроить пользовательские доменные имена, как указано ниже: 

   ![Настройка пользовательского домена для управления API][api-management-custom-domain-name]

   2. После этого создайте на DNS-сервере записи типа A для обращения к конечным точкам, которые будут доступны только в пределах виртуальной сети.


## <a name="related-content"> </a>Связанная информация
Для получения дополнительных сведений ознакомьтесь со следующими статьями:
* [Распространенные проблемы конфигурации сети][Common network configuration problems] при настройке управления API Azure с виртуальными сетями
* [Виртуальная сеть Azure: часто задаваемые вопросы](../virtual-network/virtual-networks-faq.md)
* Инструкции по [созданию записей DNS типа A](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png


[Create API Management service]: api-management-get-started.md#create-service-instance
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

