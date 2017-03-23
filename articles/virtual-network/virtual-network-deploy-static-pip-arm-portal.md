---
title: "Создание виртуальной машины со статическим общедоступным IP-адресом с помощью портала Azure | Документация Майкрософт"
description: "Узнайте, как создать виртуальную машину со статическим общедоступным IP-адресом с помощью портала Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
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
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 7740861cccfe0ba4e8ae0694f6d5c5fec3df47ef
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Создание виртуальной машины со статическим общедоступным IP-адресом с помощью портала Azure

> [!div class="op_single_selector"]
> * [Портал Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Интерфейс командной строки Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Шаблон](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (классическая модель)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания c помощью Resource Manager. Для большинства новых развертываний мы рекомендуем использовать эту модель вместо классической.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Создание виртуальной машины со статическим общедоступным IP-адресом

Чтобы создать виртуальную машину со статическим общедоступным IP-адресом на портале Azure, сделайте следующее:

1. В браузере откройте [портал Azure](https://portal.azure.com) и при необходимости войдите с помощью учетной записи Azure.
2. В верхнем левом углу портала щелкните **Создать**>>**Вычисления**>**Windows Server 2012 R2 Datacenter**.
3. В списке **Выбор модели развертывания** выберите **Resource Manager** и нажмите кнопку **Создать**.
4. В колонке **Основы** введите сведения о виртуальной машине, как показано ниже, и нажмите кнопку **ОК**.
   
    ![Портал Azure — основы](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. В колонке **Выбор размера** щелкните **A1 Standard**, как показано ниже, и нажмите кнопку **Выбрать**.
   
    ![Портал Azure — выбор размера](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. В колонке **Параметры** щелкните **Общедоступный IP-адрес**, а затем в колонке **Создать общедоступный IP-адрес** в разделе **Назначения** выберите **Статический**, как показано ниже. Затем нажмите **ОК**.
   
    ![Портал Azure — создание общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. В колонке **Параметры** нажмите кнопку **ОК**.
8. Просмотрите колонку **Сводка**, как показано ниже, а затем нажмите **ОК**.
   
    ![Портал Azure — создание общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Обратите внимание на новую плитку на панели мониторинга.
   
    ![Портал Azure — создание общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. После создания виртуальной машины колонка **Параметры** будет отображаться, как показано ниже
    
    ![Портал Azure — создание общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)


