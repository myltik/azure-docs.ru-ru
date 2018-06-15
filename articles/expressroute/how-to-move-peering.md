---
title: Переход с общедоступного пиринга в Azure ExpressRoute на пиринг Майкрософт | Документация Майкрософт
description: В этой статье приведены этапы перехода с общедоступного пиринга на пиринг Майкрософт в ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: cherylmc
ms.openlocfilehash: f34fabc95d5b56edc6e37c323bebf60bd98c8b90
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30314305"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Переход с общедоступного пиринга на пиринг Майкрософт

При использовании пиринга Майкрософт с фильтрами маршрутов ExpressRoute поддерживает службы Azure PaaS, такие как хранилище Azure и база данных SQL Azure. Требуется только один домен маршрутизации для доступа к службам Microsoft PaaS и SaaS. Воспользуйтесь фильтрами маршрутов, чтобы выборочно объявить префиксы службы PaaS для регионов Azure, которые нужно использовать.

Эта статья поможет вам переместить конфигурацию общедоступного пиринга в пиринг Майкрософт без простоев. Дополнительные сведения о доменах маршрутизации и пиринге см. в статье [Каналы ExpressRoute и домены маршрутизации](expressroute-circuit-peerings.md).


## <a name="before"></a>Перед началом работы

* Чтобы подключиться к пирингу Майкрософт, необходимо настроить преобразование сетевых адресов (NAT) и управлять им. Поставщик услуг подключения может настроить NAT и управлять им как управляемой службой. Если вы планируете получить доступ к службам Azure PaaS и Azure SaaS при пиринге Майкрософт, очень важно правильно выбрать размер пула IP-адресов NAT. Дополнительные сведения о NAT для ExpressRoute см. в разделе [Требования NAT для пиринга Майкрософт](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Если вы используете общедоступный пиринг и для доступа к [службе хранилища Azure](../storage/common/storage-network-security.md) или [базе данных SQL Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md) применяются правила IP-сети для общедоступных IP-адресов, убедитесь, что пул IP-адресов NAT, настроенный с пирингом Майкрософт, указан в списке общедоступных IP-адресов для учетной записи хранения Azure или учетной записи Azure SQL.

* Чтобы переместить пиринг Майкрософт без простоев, выполните инструкции в этой статье в том порядке, в котором они представлены.

## <a name="create"></a>1. Создание пиринга Майкрософт

Если пиринг Майкрософт не создан, выполните инструкции в любой из статей по приведенным ниже ссылкам. Если поставщик услуг подключения предоставляет управляемые службы уровня 3, он может включить пиринг Майкрософт для вашего канала.

  * [Создание пиринга Майкрософт с помощью портала Azure](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Создание пиринга Майкрософт с помощью Azure Powershell](expressroute-howto-routing-arm.md#msft)
  * [Создание пиринга Майкрософт с помощью Azure CLI](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Проверка пиринга Майкрософт включена

Убедитесь, что включен пиринг Майкрософт и объявленные общедоступные префиксы находятся в настроенном состоянии.

  * [портал Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [интерфейс командной строки Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Настройка и подключение фильтра маршрутов к каналу

По умолчанию новые пиринги Майкрософт не объявляют префиксы, пока фильтр маршрутов не будет подключен к каналу. При создании правила фильтра маршрутов можно указать список сообществ службы для регионов Azure, которые вы хотите использовать для служб Azure PaaS, как показано на следующем снимке экрана:

![Объединение общедоступного пиринга](.\media\how-to-move-peering\public.png)

Настройте фильтры маршрутов, воспользовавшись инструкциями в любой из следующих статей:

  * [Настройка фильтров маршрутов для пиринга Майкрософт с помощью портала Azure](how-to-routefilter-portal.md)
  * [Настройка фильтров маршрутов для пиринга Майкрософт с помощью PowerShell](how-to-routefilter-powershell.md)
  * [Настройка фильтров маршрутов для пиринга Майкрософт с помощью Azure CLI](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Удаление общедоступного пиринга

Убедившись, что пиринг Майкрософт настроен и префиксы, которые вы хотите использовать, правильно объявлены, можно удалять общедоступный пиринг. Чтобы удалить общедоступный пиринг, воспользуйтесь инструкциями в любой из следующих статей:

  * [Удаление общедоступного пиринга Azure на портале Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Удаление общедоступного пиринга Azure с помощью Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Удаление общедоступного пиринга Azure с помощью CLI](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Просмотр пирингов
  
На портале Azure вы можете просмотреть список всех каналов и пирингов ExpressRoute. Дополнительные сведения см. в разделе [Просмотр сведений о пиринге Майкрософт](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
