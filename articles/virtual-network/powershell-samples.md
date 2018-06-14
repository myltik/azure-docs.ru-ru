---
title: Примеры скриптов Azure PowerShell для виртуальной сети | Документация Майкрософт
description: Примеры скриптов Azure PowerShell для виртуальной сети.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: ab3a0935f23cda70cfef49af3563f6b1e85d1d8b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841222"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Примеры скриптов Azure PowerShell для виртуальной сети

В таблице ниже приведены ссылки на скрипты Azure PowerShell.

| | |
|----|----|
| [Создание виртуальной сети для многоуровневых приложений](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Создание виртуальной сети с интерфейсной и внутренней подсетями. Трафик к интерфейсной подсети принимается по протоколу HTTP, в то время как трафик к внутренней подсети принимается только от SQL через порт 1433. |
| [Пиринг между двумя виртуальными сетями](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Создание и подключение двух виртуальных сетей в одном регионе. |
| [Маршрутизация трафика через виртуальный сетевой модуль](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Создание виртуальной сети с интерфейсной и внутренней подсетями, а также виртуальной машиной, которая может маршрутизировать трафик между этими двумя подсетями. |
| [Фильтрация входящего и исходящего сетевого трафика виртуальной машины](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Создание виртуальной сети с интерфейсной и внутренней подсетями. Входящий сетевой трафик в интерфейсной подсети ограничен протоколами HTTP и HTTPS. Исходящий интернет-трафик из внутренней подсети запрещен. |
