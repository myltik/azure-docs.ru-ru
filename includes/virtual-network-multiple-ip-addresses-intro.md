---
title: включение файла
description: включение файла
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: f1dc43b6fea4ebfb47439ec64b573cc471614d98
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31531741"
---
> [!div class="op_single_selector"]
> * [портал Azure](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [интерфейс командной строки Azure](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
>

К виртуальной машине Azure подключены один или несколько сетевых интерфейсов. Каждому такому адаптеру статически или динамически назначается один или несколько общедоступных или частных IP-адресов. Назначение нескольких IP-адресов виртуальной машине дает следующие возможности:

* Возможность размещать на одном сервере несколько веб-сайтов или служб с разными IP-адресами и SSL-сертификатами.
* возможность использовать виртуальную машину в качестве виртуального сетевого устройства, такого как брандмауэр или балансировщик нагрузки.
* Возможность добавления любых частных IP-адресов любых сетевых карт во внутренний пул Azure Load Balancer. Раньше во внутренний пул можно было добавить только основной IP-адрес для основной сетевой карты. Дополнительные сведения о балансировке нагрузки в конфигурациях с несколькими IP-адресами см. в [этой статье](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Каждому сетевому адаптеру, подключенному к виртуальной машине, присвоена одна или несколько конфигураций IP-адресов. Каждая конфигурация получает один статический или динамический частный IP-адрес. Кроме того, каждой конфигурации также может быть присвоен один ресурс общедоступного IP-адреса. Ресурсу общедоступного IP-адреса назначен один динамический или статический общедоступный IP-адрес. Дополнительные сведения об IP-адресах в Azure см. в [этой статье](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md). 

Сетевой карте может быть назначено ограниченное число частных IP-адресов. Число общедоступных IP-адресов, которые можно использовать в одной подписке Azure, также ограничено. См. дополнительные сведения об [ограничениях в Azure](../articles/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
