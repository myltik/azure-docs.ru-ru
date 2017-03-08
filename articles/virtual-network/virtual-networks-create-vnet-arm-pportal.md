---
title: "Создание виртуальной сети с помощью портала Azure | Документация Майкрософт"
description: "Узнайте, как создать виртуальную сеть с помощью портала Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/8/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 54094c18dcbb751835bfa56d53358ce19e08387d
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Создание виртуальной сети с помощью портала Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure предоставляет две модели развертывания: с помощью Azure Resource Manager и классическую. Для создания ресурсов корпорация Майкрософт рекомендует использовать модель развертывания с помощью Resource Manager. Дополнительные сведения о различиях между двумя моделями см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../azure-resource-manager/resource-manager-deployment-model.md) (Azure Resource Manager и классическое развертывание. Общие сведения о моделях развертывания и состоянии ресурсов).
 
В этой статье описывается создание виртуальной сети с помощью модели развертывания Resource Manager на портале Azure. Виртуальную сеть также можно создать с помощью Resource Manager, используя другие инструменты, либо с помощью классической модели развертывания, выбрав другой вариант из следующего списка:

> [!div class="op_single_selector"]
- [Портал](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](virtual-networks-create-vnet-arm-cli.md)
- [Шаблон](virtual-networks-create-vnet-arm-template-click.md)
- [Портал (классический)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (классическая модель)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [Интерфейс командной строки (классическая модель)](virtual-networks-create-vnet-classic-cli.md)


[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Чтобы создать виртуальную сеть на портале Azure, сделайте следующее:

1. В браузере откройте адрес http://portal.azure.com и войдите с помощью учетной записи Azure.
2. Щелкните **Создать** > **Сети** > **Виртуальная сеть**, как показано на следующем рисунке.

    ![Новая виртуальная сеть](./media/virtual-network-create-vnet-arm-pportal/1.png)

3. В появившейся колонке **Виртуальная сеть** выберите *Диспетчер ресурсов* и нажмите кнопку **Создать**, как показано на следующем рисунке.

    ![Виртуальная сеть](./media/virtual-network-create-vnet-arm-pportal/2.png)
    
4. В появившейся колонке **Создать виртуальную сеть** в поле **Имя** введите *TestVNet*, в поле **Адресное пространство** — *192.168.0.0/16*, в поле **Имя подсети** — *FrontEnd*, в поле **Диапазон адресов подсети** — *192.168.1.0/24*, в поле **Группа ресурсов** — *TestRG*, а в полях **Подписка** и **Расположение** выберите свою подписку и расположение. После этого нажмите кнопку **Создать**, как показано на следующем рисунке.

    ![Создание виртуальной сети](./media/virtual-network-create-vnet-arm-pportal/3.png)

    Кроме того, можно выбрать имеющуюся группу ресурсов. Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups). Вы также можете выбрать другое расположение. Дополнительные сведения о расположениях и регионах Azure см. в [этой статье](https://azure.microsoft.com/regions).

5. При создании виртуальной сети на портале можно создать только одну подсеть. В этом сценарии после создания виртуальной сети необходимо создать вторую подсеть. Чтобы создать вторую подсеть, выберите **Все ресурсы**, а затем в колонке **Все ресурсы** щелкните **TestVNet**, как показано на следующем рисунке.

    ![Созданная виртуальная сеть](./media/virtual-network-create-vnet-arm-pportal/4.png)

6. В появившейся колонке **TestVNet** щелкните **Подсеть**, затем — **+ Подсеть**, в поле **Имя** введите *BackEnd*, в колонке **Добавить подсеть** в поле **Диапазон адресов** введите *192.168.2.0/24* и нажмите кнопку **ОК**, как показано на следующем рисунке.

    ![Добавление подсети](./media/virtual-network-create-vnet-arm-pportal/5.png)

7. Появятся две подсети, как показано на следующем рисунке.
    
    ![Список подсетей в виртуальной сети](./media/virtual-network-create-vnet-arm-pportal/6.png)

В этой статье описано создание виртуальной сети с двумя подсетями для тестирования. Прежде чем создавать виртуальную сеть для использования в рабочей среде, мы рекомендуем прочитать статьи [Обзор виртуальной сети](virtual-networks-overview.md) и [Планирование и проектирование виртуальных сетей Azure](virtual-network-vnet-plan-design-arm.md), чтобы получить полное представление о виртуальных сетях и всех параметрах. 

## <a name="next-steps"></a>Дальнейшие действия

Инструкции по подключению:

- Сведения о подключении виртуальной машины к виртуальной сети см. в статье [Создание первой виртуальной машины Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) или [Создание виртуальной машины Linux в Azure с помощью портала](../virtual-machines/virtual-machines-linux-quick-create-portal.md). Вместо создания виртуальной сети и подсети с помощью действий, описанных в этой статье, виртуальную машину можно подключить к имеющейся виртуальной сети и подсети.
- Сведения об установке подключения между виртуальными сетями см. в статье [Настройка подключения между виртуальными сетями на портале Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- Сведения о подключении виртуальной сети к локальной сети с использованием виртуальной частной сети типа "сеть — сеть" или канала ExpressRoute см. в [этой статье](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) и в статье [Связывание виртуальной сети с каналом ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
