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
ms.openlocfilehash: 3e02242822283ec8597a7be217228cb95e748761
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
1. На портале перейдите к виртуальной сети, для которой необходимо создать шлюз.
2. На странице виртуальной сети в разделе **Параметры** щелкните **Подсети**, чтобы открыть страницу "Подсети".
3. На странице **Подсети** щелкните **+Подсеть шлюза** в верхней части страницы. Откроется страница **Добавление подсети**.

  ![Добавить подсеть шлюза](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addgwsub.png "Add the gateway subnet")
4. В поле **Имя** автоматически добавляется значение GatewaySubnet. Значение GatewaySubnet необходимо для Azure при идентификации подсети в качестве подсети шлюза. Замените автоматически заполненное значение **диапазона адресов** в соответствии с требованиями к конфигурации.

  ![Добавление подсети шлюза](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addsub2.png "Adding the gateway subnet")
5. Чтобы создать подсеть, в нижней части страницы щелкните **ОК**.