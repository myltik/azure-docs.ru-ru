---
title: Создание общедоступной подсистемы балансировки нагрузки уровня "Стандартный" с избыточным в пределах зоны интерфейсным общедоступным IP-адресом с помощью портала Azure | Документация Майкрософт
description: Сведения о создании общедоступной подсистемы балансировки нагрузки уровня "Стандартный" с избыточным в пределах зоны интерфейсным общедоступным IP-адресом с помощью портала Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 10a264609469245d4743886b58730304da3df7bb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Создание общедоступной подсистемы балансировки нагрузки уровня "Стандартный" с избыточным в пределах зоны интерфейсным общедоступным IP-адресом с помощью портала Azure

В этой статье приведены действия по созданию общедоступной [подсистемы балансировки нагрузки уровня "Стандартный"](https://aka.ms/azureloadbalancerstandard) с избыточным в пределах зоны интерфейсным сервером с помощью общедоступного стандартного IP-адреса.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="register-for-availability-zones-preview"></a>Регистрация для использования предварительной версии зон доступности
 
Зоны доступности используются в режиме предварительной версии в сценариях разработки и тестирования. Поддержка реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются для зон доступности, см. в разделе [Обзор зон доступности](https://docs.microsoft.com/azure/availability-zones/az-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Вход в Azure 

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Создание избыточной в пределах зоны подсистемы балансировки нагрузки

1. В браузере откройте портал Azure [http://portal.azure.com](http://portal.azure.com) и выполните вход с помощью учетной записи Azure.
2. Вверху слева последовательно выберите **Создать ресурс** > **Сети** > **Load Balancer**.
3. В окне **Create load balancer (Создание подсистемы балансировки нагрузки) в поле **Имя** введите **myPublicLB**.
4. В разделе **Тип** выберите **Общедоступный**.
5. В разделе "Номер SKU" выберите **Стандартный (предварительная версия)**.
6. Щелкните **Общедоступный IP-адрес**. На странице **Create a public IP address** (Создание общедоступного IP-адреса) выберите **Создать**, в поле "Имя" введите **myPublicIPStandard**, а затем в раскрывающемся списке **Availability zone (Preview)** (Зона доступности (предварительная версия)) выберите **Избыточное в пределах зоны**.
7. В разделе **Расположение** выберите **Восточная часть США 2** и нажмите кнопку **ОК**. После этого подсистема балансировки нагрузки начнет развертывание. Этот процесс может занять несколько минут.

    ![Создание избыточной в пределах зоны подсистемы балансировки нагрузки уровня "Стандартный" с помощью портала Azure](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Дополнительная информация
- Узнайте о [создании общедоступного IP-адреса в зоне доступности](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address).



