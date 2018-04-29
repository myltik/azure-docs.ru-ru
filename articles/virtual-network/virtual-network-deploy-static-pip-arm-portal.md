---
title: Создание виртуальной машины со статическим общедоступным IP-адресом с помощью портала Azure | Документация Майкрософт
description: Узнайте, как создать виртуальную машину со статическим общедоступным IP-адресом с помощью портала Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50ae4d6e8c275db16f811a2a1a063eda441f150b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Создание виртуальной машины со статическим общедоступным IP-адресом с помощью портала Azure

> [!div class="op_single_selector"]
> * [портал Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [интерфейс командной строки Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (классическая модель)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания c помощью Resource Manager. Для большинства новых развертываний мы рекомендуем использовать эту модель вместо классической.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Создание виртуальной машины со статическим общедоступным IP-адресом

Чтобы создать виртуальную машину со статическим общедоступным IP-адресом на портале Azure, сделайте следующее:

1. В браузере откройте [портал Azure](https://portal.azure.com) и при необходимости войдите с помощью учетной записи Azure.
2. В верхнем левом углу портала щелкните **Создать ресурс**>>**Вычисления**>**Windows Server 2012 R2 Datacenter**.
3. В списке **Выбор модели развертывания** выберите **Resource Manager** и нажмите кнопку **Создать**.
4. В области **Основы** введите сведения о виртуальной машине, согласно примеру, и нажмите кнопку **ОК**.
   
    ![Портал Azure — основы](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. В области **Выбор размера** выберите **A1 Standard**, как показано ниже, а затем щелкните **Выбрать**.
   
    ![Портал Azure — выбор размера](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. В области **Параметры** щелкните **Общедоступный IP-адрес**, а затем в области **Создать общедоступный IP-адрес** в разделе **Назначения** выберите **Статический**, как показано ниже. Затем нажмите кнопку **ОК**.
   
    ![Портал Azure — создание общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. В области **Параметры** нажмите кнопку **ОК**.
8. Просмотрите область **Сводка** (показана ниже), а затем нажмите кнопку **ОК**.
   
    ![Портал Azure — создание общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Обратите внимание на новую плитку на панели мониторинга.
   
    ![Портал Azure — создание общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. После создания виртуальной машины область **Параметры** будет выглядеть следующим образом:
    
    ![Портал Azure — создание общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

## <a name="set-ip-addresses-within-the-operating-system"></a>Настройка IP-адресов в операционной системе

Никогда не следует вручную назначать общедоступный IP-адрес для виртуальной машины Azure в ее операционной системе. Не рекомендуем статически назначать виртуальной машине Azure частный IP-адрес в ее операционной системе за исключением ситуаций, когда это необходимо, например при [назначении нескольких IP-адресов виртуальной машине Windows](virtual-network-multiple-ip-addresses-portal.md). Если вы будете вручную устанавливать частный IP-адрес в операционной системе, убедитесь, что он соответствует частному IP-адресу, назначенному [сетевому интерфейсу](virtual-network-network-interface-addresses.md#change-ip-address-settings) Azure. Иначе соединение с виртуальной машиной может быть потеряно. Ознакомьтесь с дополнительными сведениями о параметрах [частных IP-адресов](virtual-network-network-interface-addresses.md#private).

## <a name="next-steps"></a>Дополнительная информация

Виртуальная машина, созданная в этой статье, может принимать и передавать любой сетевой трафик. Вы можете определить правила безопасности, ограничивающие входящий и исходящий трафик в группе безопасности сети для сетевого интерфейса и (или) подсети. Дополнительные сведения о группах безопасности сети см. [в этой статье](security-overview.md).