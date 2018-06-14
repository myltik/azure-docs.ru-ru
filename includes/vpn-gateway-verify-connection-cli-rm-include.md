---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1c3103889bd11df45710cc0d6afaeeba022c9ace
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197726"
---
Убедиться в успешном выполнении подключения можно с помощью команды [az network vpn-connection show](/cli/azure/network/vpn-connection#show). В примере параметр --name — это имя подключения, которое требуется проверить. Когда подключение устанавливается, это отображено соответствующим образом (состояние "Подключение"). После установки подключения его состояние изменяется на "Подключено".

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```