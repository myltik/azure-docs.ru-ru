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
ms.openlocfilehash: a8dec00373d991a7aeaf11f1a4d95463ab71d9a2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197080"
---
1. На [портале](http://portal.azure.com) перейдите к виртуальной сети Resource Manager, для которой необходимо создать шлюз.
2. На странице виртуальной сети в разделе **Параметры** щелкните **Подсети**, чтобы открыть страницу **Подсети**.
3. На странице **Подсети** щелкните **+Подсеть шлюза**. Откроется страница **Добавление подсети**. 

  ![Добавить подсеть шлюза](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Add the gateway subnet")
4. В поле **Имя** автоматически добавляется значение GatewaySubnet. По этому имени Azure идентифицирует подсеть как подсеть шлюза. Настройте автоматическое заполнение значений **диапазона адресов** для соответствия требованиям конфигурации, а затем щелкните **ОК** в нижней части страницы, чтобы создать подсеть.

  ![Добавление подсети](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Adding the subnet")