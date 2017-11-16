---
title: "Переход с общедоступного пиринга в Azure ExpressRoute на пиринг Майкрософт | Документация Майкрософт"
description: "В этой статье приведены этапы перехода с общедоступного пиринга на пиринг Майкрософт в ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2017
ms.author: cherylmc
ms.openlocfilehash: 311e1de3200cd684bbec1329ebd5217b4fb3a2e2
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2017
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Переход с общедоступного пиринга на пиринг Майкрософт

При использовании пиринга Майкрософт с фильтрами маршрутов ExpressRoute теперь поддерживает службы Azure PaaS, такие как хранилище Azure и база данных SQL Azure. Требуется только один домен маршрутизации для доступа к службам Microsoft PaaS и SaaS. Воспользуйтесь фильтрами маршрутов, чтобы выборочно объявить префиксы службы PaaS для регионов Azure, которые хотите использовать.

Эта статья поможет вам переместить конфигурацию общедоступного пиринга в пиринг Майкрософт без простоев. Дополнительные сведения о доменах маршрутизации и пиринге см. в статье [Каналы ExpressRoute и домены маршрутизации](expressroute-circuit-peerings.md).

> [!IMPORTANT]
> Для использования пиринга Майкрософт необходима надстройка ExpressRoute Premium. Дополнительные сведения о надстройке версии Premium см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md#expressroute-premium).

## <a name="before-you-begin"></a>Перед началом работы

* Чтобы подключиться к пирингу Майкрософт, необходимо настроить преобразование сетевых адресов (NAT) и управлять им. Поставщик услуг подключения может настроить NAT и управлять им как управляемой службой. Если вы планируете получить доступ к службам Azure PaaS и Azure SaaS в пиринге Майкрософт, очень важно правильно выбрать размер пула IP-адресов NAT. Дополнительные сведения о NAT для ExpressRoute см. в разделе [Требования NAT для пиринга Майкрософт](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Если вы используете сетевой список управления доступом (ACL) для конечных точек службы Azure PaaS с помощью общедоступного пиринга Azure, необходимо убедиться, что пул IP-адресов NAT, настроенный для пиринга Майкрософт, включен в список управления доступом, который настроен для конечной точки службы.

Чтобы переместить пиринг Майкрософт без простоев, необходимо выполнить следующие шаги, в том порядке, в котором они представлены.

## <a name="1-create-microsoft-peering"></a>1. Создание пиринга Майкрософт

Если пиринг Майкрософт не создан, выполните инструкции в любой из статей по приведенным ниже ссылкам. Если поставщик услуг подключения предоставляет управляемые службы уровня 3, он может включить пиринг Майкрософт для вашего канала.

  * [Создание пиринга Майкрософт с помощью портала Azure](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Создание пиринга Майкрософт с помощью Azure Powershell](expressroute-howto-routing-arm.md#msft)
  * [Создание пиринга Майкрософт с помощью Azure CLI](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a>2) Проверка пиринга Майкрософт включена

Убедитесь, что включен пиринг Майкрософт и объявленные общедоступные префиксы находятся в настроенном состоянии.

  * [Портал Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Интерфейс командной строки Azure](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a>3. Настройка и подключение фильтра маршрутов к каналу

По умолчанию новые пиринги Майкрософт не объявляют префиксы, пока фильтр маршрутов не будет подключен к каналу. При создании правила фильтра маршрутов можно указать список сообществ службы для регионов Azure, которые вы хотите использовать для служб Azure PaaS, как показано на следующем снимке экрана:

![Объединение общедоступного пиринга](.\media\how-to-move-peering\public.png)

Для настройки фильтров маршрутов используйте любую из следующих статей.

  * [Настройка фильтров маршрутов для пиринга Майкрософт с помощью портала Azure](how-to-routefilter-portal.md)
  * [Настройка фильтров маршрутов для пиринга Майкрософт с помощью PowerShell](how-to-routefilter-powershell.md)
  * [Настройка фильтров маршрутов для пиринга Майкрософт с помощью Azure CLI](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a>4. Удаление общедоступного пиринга

Убедившись, что пиринг Майкрософт настроен и префиксы, которые вы хотите использовать, правильно объявлены, можно удалять общедоступный пиринг. Чтобы удалить общедоступный пиринг, используйте любую из следующих ссылок:

  * [Удаление общедоступного пиринга Azure на портале Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Удаление общедоступного пиринга Azure с помощью Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Удаление общедоступного пиринга Azure с помощью CLI](howto-routing-cli.md#deletepublic)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).