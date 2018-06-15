---
title: Примеры Azure CLI для виртуальной сети | Документация Майкрософт
description: Примеры Azure CLI для виртуальной сети.
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
ms.openlocfilehash: baa4dbc8f95e068eb1a939fdee53fb2a4ee8117f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841366"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Примеры Azure CLI для виртуальной сети

Следующая таблица содержит ссылки на скрипты Bash, созданные с помощью команд Azure CLI:

| | |
|----|----|
| [Создание виртуальной сети для многоуровневых приложений](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Создание виртуальной сети с интерфейсной и внутренней подсетями. Трафик к интерфейсной подсети принимается по протоколам HTTP и SSH, в то время как трафик к внутренней подсети принимается только от MySQL по порту 3306. |
| [Пиринг между двумя виртуальными сетями](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Создание и подключение двух виртуальных сетей в одном регионе. |
| [Маршрутизация трафика через виртуальный сетевой модуль](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Создание виртуальной сети с интерфейсной и внутренней подсетями, а также виртуальной машиной, которая может маршрутизировать трафик между этими двумя подсетями. |
| [Фильтрация входящего и исходящего сетевого трафика виртуальной машины](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Создание виртуальной сети с интерфейсной и внутренней подсетями. Входящий сетевой трафик в интерфейсной подсети ограничен протоколами HTTP, HTTPS и SSH. Исходящий интернет-трафик из внутренней подсети запрещен. |