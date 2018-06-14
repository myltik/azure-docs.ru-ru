---
title: Примеры Azure PowerShell | Документация Майкрософт
description: Примеры сценариев Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: georgewallace
ms.openlocfilehash: 0bca4fb6874bd265f0ae9faeb4219abeb4ffb6d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23036599"
---
# <a name="azure-powershell-samples-for-networking"></a>Примеры Azure PowerShell для работы с сетями

В таблице ниже приведены ссылки на скрипты, созданные с помощью Azure PowerShell.

| | |
|-|-|
|**Подключение между ресурсами Azure**||
| [Создание виртуальной сети для многоуровневых приложений](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Создание виртуальной сети с интерфейсной и внутренней подсетями. Трафик к интерфейсной подсети принимается по протоколу HTTP, в то время как трафик к внутренней подсети принимается только от SQL через порт 1433. |
| [Пиринг между двумя виртуальными сетями](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Создание и подключение двух виртуальных сетей в одном регионе. |
| [Маршрутизация трафика через виртуальный сетевой модуль](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Создание виртуальной сети с интерфейсной и внутренней подсетями, а также виртуальной машиной, которая может маршрутизировать трафик между этими двумя подсетями. |
| [Фильтрация входящего и исходящего сетевого трафика виртуальной машины](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Создание виртуальной сети с интерфейсной и внутренней подсетями. Входящий сетевой трафик в интерфейсной подсети ограничен протоколами HTTP и HTTPS. Исходящий трафик в Интернет из внутренней подсети запрещен. |
|**Направление трафика и балансировка нагрузки**||
| [Балансировка трафика на виртуальных машинах для обеспечения высокого уровня доступности](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Создает несколько виртуальных машин с высокодоступной конфигурацией с балансировкой нагрузки. |
| [Балансировка нагрузки нескольких веб-сайтов на виртуальных машинах](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Создание двух виртуальных машин с несколькими IP-конфигурациями, присоединенных к группе доступности Azure, которые доступны через Azure Load Balancer. |
| [Направление трафика через несколько регионов для обеспечения высокого уровня доступности приложений](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Создание двух планов службы приложений, двух веб-приложений, а также профиля и двух конечных точек диспетчера трафика. |
| | |
