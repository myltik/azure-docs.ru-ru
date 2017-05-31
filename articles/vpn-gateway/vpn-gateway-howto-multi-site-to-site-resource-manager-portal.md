---
title: "Добавление нескольких подключений типа &quot;сеть-сеть&quot; через VPN-шлюз к виртуальной сети. Портал Azure: Resource Manager | Документы Майкрософт"
description: "Добавление многосайтовых подключений типа &quot;сеть — сеть&quot; к VPN-шлюзу с имеющимся подключением"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 9550b0076105b018bd8ede50519ac96bfb773001
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Добавление подключения типа "сеть — сеть" к виртуальной сети с помощью существующего подключения VPN-шлюза
> [!div class="op_single_selector"]
> * [Resource Manager: портал](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [Классическая модель: PowerShell](vpn-gateway-multi-site.md)
> 
> 

В этой статье рассматривается использование портала Azure для добавления подключений типа "сеть — сеть" (S2S) к VPN-шлюзу с имеющимся подключением. Этот тип подключения часто называется "многосайтовым". Можно добавить подключение типа "сеть-сеть" к виртуальной сети, которая уже имеет подключение типа "сеть-сеть", "точка-сеть" или "виртуальная сеть — виртуальная сеть". При добавлении подключения есть некоторые ограничения. Прежде чем начать настройку, просмотрите раздел [Перед началом работы](#before) в этой статье. 

Сведения в этой статье применимы к виртуальным сетям, созданным с помощью модели развертывания Resource Manager, с VPN-шлюзом типа RouteBased. Эти действия не применяются к параллельно существующим конфигурациям подключений ExpressRoute и "сеть — сеть". Сведения о параллельно существующих подключениях см. в разделе [Параллельно существующие подключения ExpressRoute и "сеть — сеть"](../expressroute/expressroute-howto-coexist-resource-manager.md).

### <a name="deployment-models-and-methods"></a>Модели и методы развертывания
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Мы обновляем эту таблицу по мере выпуска новых статей и дополнительных инструментов для этой конфигурации. При появлении статьи мы указываем прямую ссылку на нее в этой таблице.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Перед началом работы
Проверьте следующее.

* Убедитесь, что не создаете параллельно действующие подключения ExpressRoute и "сеть — сеть".
* У вас должна быть виртуальная сеть, созданная с помощью модели развертывания Resource Manager, с имеющимся подключением.
* Тип шлюза виртуальной сети — RouteBased. При наличии VPN-шлюза типа PolicyBased необходимо удалить шлюз виртуальной сети и создать VPN-шлюз типа RouteBased.
* Ни один из диапазонов адресов не должен перекрываться ни в одной из виртуальных сетей, к которой подключается эта виртуальная сеть.
* У вас есть совместимое VPN-устройство и пользователь, который может настроить его. См. статью о [VPN-устройствах](vpn-gateway-about-vpn-devices.md). Если вы не умеете настраивать VPN-устройство или не знаете диапазоны IP-адресов в своей локальной сети, вам следует найти того, кто сможет предоставить вам нужную информацию.
* Имеется внешний общедоступный IP-адрес для VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).

## <a name="part1"></a>Часть 1. Настройка подключения
1. В браузере откройте [портал Azure](http://portal.azure.com) и при необходимости войдите с помощью учетной записи Azure.
2. Щелкните **Все ресурсы**, а затем найдите в списке ресурсов **Шлюз виртуальной сети** и щелкните его.
3. В колонке **шлюза виртуальной сети** щелкните **Подключения**.
   
    ![Колонка "Подключения"](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Connections blade")<br>
4. В колонке **Подключения** нажмите кнопку **Добавить**.
   
    ![Кнопка "Добавить подключение"](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Add connection button")<br>
5. В колонке **Добавление подключения** заполните следующие поля.
   
   * **Имя.** Имя, назначаемое сайту, к которому создается подключение.
   * **Тип подключения.** Выберите **Сеть — сеть (IPSec)**.
     
     ![Колонка "Добавление подключения"](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Add connection blade")<br>

## <a name="part2"></a>Часть 2. Добавление шлюза локальной сети
1. Щелкните **Шлюз локальной сети**, а затем ***Выберите шлюз локальной сети***. Откроется колонка **Выберите шлюз локальной сети**.
   
    ![Выбор шлюза локальной сети](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Choose local network gateway")<br>
2. Щелкните **Создать**, чтобы открыть колонку **Создание шлюза локальной сети**.
   
    ![Колонка "Создание шлюза локальной сети"](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Create local network gateway")<br>
3. В колонке **Создание шлюза локальной сети** заполните следующие поля.
   
   * **Имя.** Имя, которое будет использоваться для шлюза локальной сети.
   * **IP-адрес.** Общедоступный IP-адрес VPN-устройства сайта, к которому необходимо подключиться.
   * **Пространство адресов.** Пространство адресов, к которому будет направляться новый сайт локальной сети.
4. Чтобы сохранить изменения, в колонке **Создание шлюза локальной сети** нажмите кнопку **ОК**.

## <a name="part3"></a>Часть 3. Добавление общего ключа и создание подключения
1. В колонке **Добавление подключения** добавьте общий ключ, который будет использоваться для создания подключения. Можно получить общий ключ из VPN-устройства или создать его в этой колонке, а затем настроить использование этого ключа для VPN-устройства. Важно, чтобы ключи полностью совпадали.
   
    ![Общий ключ](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Shared key")<br>
2. В нижней части колонки нажмите кнопку **ОК**, чтобы создать подключение.

## <a name="part4"></a>Часть 4. Проверка VPN-подключения


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

Установив подключение, можно добавить виртуальные машины в виртуальные сети. Дополнительную информацию о виртуальных машинах см. в [схеме обучения](https://azure.microsoft.com/documentation/learning-paths/virtual-machines).

