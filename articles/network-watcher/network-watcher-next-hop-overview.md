---
title: "Общие сведения о функции определения следующего прыжка в Наблюдателе за сетями Azure | Документация Майкрософт"
description: "Эта страница содержит общие сведения о функции определения следующего прыжка в Наблюдателе за сетями."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 864185e62fb6c3cef4116824b36ee7e5d3447662
ms.lasthandoff: 03/04/2017

---

# <a name="introduction-to-next-hop-in-azure-network-watcher"></a>Общие сведения о функции определения следующего прыжка в Наблюдателе за сетями Azure

Трафик из виртуальной машины отправляется по назначению в зависимости от действующих маршрутов, связанных с сетевой картой. Функция определения следующего прыжка возвращает тип следующего прыжка и IP-адрес пакета из определенной виртуальной машины и сетевой карты. Это позволяет определить, направляется ли пакет по назначению или трафик передается в "черную дыру". Неправильная настройка маршрутов пользователем, при которой трафик направляется в локальное расположение или на виртуальное устройство, может привести к проблемам подключения. Функция определения следующего прыжка также возвращает таблицу маршрутов, связанную со следующим прыжком. Если при запросе следующего прыжка маршрут задан как определяемый пользователем, то возвращается этот маршрут. В противном случае возвращается системный маршрут.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

![Обзор функции определения следующего прыжка][1]

Ниже приведен список типов следующего прыжка, которые могут быть возвращены при запросе следующего прыжка.

* Интернет
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

### <a name="next-steps"></a>Дальнейшие действия

Узнайте, как использовать функцию определения следующего прыжка для поиска проблем с сетевыми подключениями, ознакомившись с разделом [Find out what the next hop type is using the Next Hop capability in Azure Network Watcher using the portal](network-watcher-check-next-hop-portal.md) (Определение типа следующего прыжка с помощью функции определения следующего прыжка в Наблюдателе за сетями на портале).

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png














