---
title: Настройка транзита VPN-шлюзов для пиринга между виртуальными сетями с использованием Azure Resource Manager | Документация Майкрософт
description: Настройка транзита VPN-шлюзов для пиринга между виртуальными сетями.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/25/2018
ms.author: yushwang
ms.openlocfilehash: cced9a313664eaeaed4dd1298488670960b76413
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195776"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Настройка транзита VPN-шлюзов для пиринга между виртуальными сетями

С помощью сведений, приведенных в этой статье, вы сможете настроить транзит шлюзов для пиринга между виртуальными сетями. [Пиринг между виртуальными сетями](../virtual-network/virtual-network-peering-overview.md) соединяет две виртуальные сети Azure, объединяя их в одну в целях подключения. [Транзит шлюзов](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) — это свойство пиринга, которое позволяет одной виртуальной сети использовать VPN-шлюз в одноранговой виртуальной сети для распределенных подключений или подключений типа "виртуальная сеть — виртуальная сеть". На следующей схеме показано, как транзит шлюзов работает с пирингом между виртуальными сетями.

![транзит шлюзов](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

На схеме транзит шлюзов позволяет одноранговым виртуальным сетям использовать VPN-шлюз Azure в концентраторе Resource Manager. Подключения к VPN-шлюзу, включая подключения S2S, P2S и "виртуальная сеть — виртуальная сеть", применяются ко всем трем виртуальным сетям. Параметр транзита доступен для пиринговой связи между одинаковыми или разными моделями развертывания. Ограничение заключается в том, что VPN-шлюз может находиться только в виртуальной сети с моделью развертывания Resource Manager, как показано на схеме.

В сетевой архитектуре типа "звезда" транзит шлюза позволяет периферийным виртуальным сетям совместно использовать VPN-шлюз в концентраторе вместо развертывания VPN-шлюзов в каждой периферийной виртуальной сети. Маршруты к подключенным к шлюзу виртуальным сетям или локальным сетям будут распространяться на таблицы маршрутизации для одноранговых виртуальных сетей, использующих транзит шлюзов. Вы можете отключить автоматическое распространение маршрута из VPN-шлюза. Создайте таблицу маршрутизации с параметром **Отключить распространение маршрутов BGP** и привяжите ее к подсетям, чтобы предотвратить распространение маршрута в этих подсетях. Дополнительные сведения см. в статье [Create, change, or delete a route table](../virtual-network/manage-route-table.md) (Создание, изменение или удаление таблицы маршрутизации).

В этом документе описаны два сценария.

1. Обе виртуальные сети используют модель развертывания Resource Manager.
2. Периферийная виртуальная сеть является классической, а виртуальная сеть концентратора с шлюзом находится в Resource Manager.

> [!IMPORTANT]
> В настоящее время глобальный пиринг между виртуальными сетями не поддерживает транзит шлюзов.

## <a name="requirements"></a>Требования

Для примера в этом документе необходимы следующие ресурсы:

1. Центральная виртуальная сеть Resource Manager (Hub-RM) с VPN-шлюзом.
2. Периферийная виртуальная сеть Resource Manager (Spoke-RM).
3. Периферийная виртуальная сеть (Spoke-Classic) с классической моделью развертывания.
4. Используемая учетная запись требует необходимых ролей и разрешений. Дополнительные сведения см. в разделе [Разрешения](#permissions) этой статьи.

Инструкции см. по следующим ссылкам:

1. [Создание VPN-шлюза в виртуальной сети](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [Создание пиринга между виртуальными сетями с одинаковой моделью развертывания](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [Создание пиринга между виртуальными сетями с разными моделями развертывания](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a>Разрешения

У учетных записей, используемых для создания пиринга между виртуальными сетями, должны быть необходимые роли или разрешения. В приведенном ниже примере при создании пиринга между двумя виртуальными сетями Hub-RM и Spoke-Classic вашей учетной записи должны быть назначены следующие роли или разрешения для каждой виртуальной сети.
    
|Виртуальная сеть|Модель развертывания|Роль|Разрешения|
|---|---|---|---|
|Hub-RM|Диспетчер ресурсов|[Участник сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Классический|[Участник классической сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Недоступно|
|Spoke-Classic|Диспетчер ресурсов|[Участник сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Классический|[Участник классической сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Подробнее о [встроенных ролях](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) и присвоении разрешений, определенных для [настраиваемых ролей](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (только для Resource Manager).

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>Пиринг между виртуальными сетями Resource Manager с транзитом шлюзов

Выполните инструкции, чтобы создать или обновить пиринг между виртуальными сетями для включения транзита шлюзов.

1. На портале Azure создайте или обновите пиринг между виртуальными сетями (от Spoke-RM к Hub-RM). Перейдите к ресурсу виртуальной сети типа Spoke-RM, щелкните "Пиринги", а затем — "Добавить".
    - Задайте параметр "Resource Manager".
    - В соответствующей подписке выберите виртуальную сеть Hub-RM.
    - Убедитесь, что параметр "Разрешить доступ к виртуальным сетям" включен.
    - Задайте параметр **Использовать удаленные шлюзы**.
    - Нажмите кнопку "ОК".

    ![spokerm-to-hubrm](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. Если пиринг уже создан, перейдите к ресурсу пиринга, затем включите параметр **Использовать удаленные шлюзы**, как показано на снимке экрана, приведенном на шаге (1).

3. На портале Azure создайте или обновите пиринг между виртуальными сетями (от Hub-RM к Spoke-RM). Перейдите к ресурсу виртуальной сети Hub-RM, щелкните "Пиринги", а затем — "Добавить".
    - Задайте параметр "Resource Manager".
    - Убедитесь, что параметр "Разрешить доступ к виртуальным сетям" включен.
    - В соответствующей подписке выберите виртуальную сеть Spoke-RM.
    - Задайте параметр **Разрешить транзит шлюзов**.
    - Нажмите кнопку "ОК".

    ![hubrm-to-spokerm](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. Если пиринг уже создан, перейдите к ресурсу пиринга, а затем включите параметр **Разрешить транзит шлюзов**, как показано на снимке экрана, приведенном на шаге (3).

5. Проверьте, чтобы состояние пиринга в обеих виртуальных сетях было следующим: **Подключено**.

### <a name="powershell-sample"></a>Пример для PowerShell

Вы также можете использовать PowerShell для создания или обновления пиринга с помощью приведенного выше примера. Замените переменные именами ваших виртуальных сетей и групп ресурсов.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzureRmVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzureRmVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzureRmVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzureRmVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>Пиринг между классической виртуальной сетью и виртуальной сетью Resource Manager с транзитом шлюзов

Шаги похожи с действиями для предыдущего сценария, за исключением того, что операции применяются только к виртуальной сети Hub-RM.

1. На портале Azure создайте или обновите пиринг между виртуальными сетями (от Hub-RM к Spoke-RM). Перейдите к ресурсу виртуальной сети Hub-RM, щелкните "Пиринги", а затем — "Добавить".
    - Для модели развертывания виртуальной сети задайте параметр "Классическая".
    - В соответствующей подписке выберите виртуальную сеть Spoke-Classic.
    - Убедитесь, что параметр "Разрешить доступ к виртуальным сетям" включен.
    - Задайте параметр **Разрешить транзит шлюзов**.
    - Нажмите кнопку "ОК".

    ![hubrm-to-spokeclassic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. Если пиринг уже создан, перейдите к ресурсу пиринга, а затем включите параметр **Разрешить транзит шлюзов**, как показано на снимке экрана, приведенном на шаге (1).

3. В виртуальной сети Spoke-Classic операции не выполняются.

4. Проверьте, чтобы состояние виртуальной сети Hub-RM было следующим: **Подключено**.

После того, как состояние изменится на "Подключено", периферийные виртуальные сети начнут использовать распределенные подключения или подключения типа "виртуальная сеть — виртуальная сеть" через VPN-шлюз в виртуальной сети концентратора.

### <a name="powershell-sample"></a>Пример для PowerShell

Вы также можете использовать PowerShell для создания или обновления пиринга с помощью приведенного выше примера. Замените переменные и идентификатор подписки на значения вашей виртуальной сети, групп ресурсов и подписки. Вам нужно всего лишь создать пиринг между виртуальными сетями в виртуальной сети концентратора.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzureRmVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzureRmVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Дополнительная информация

* Прежде чем создавать пиринг между виртуальными сетями для рабочей среды, ознакомьтесь с разделами [Requirements and constraints](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) (Требования и ограничения) и [Create a peering](../virtual-network/virtual-network-manage-peering.md#create-a-peering) (Создание пиринга).
* Сведения о создании звездообразной топологии сети с пирингом между виртуальными сетями и транзитом шлюзов см. в [этом разделе](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering).
