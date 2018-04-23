---
title: включение файла
description: включение файла
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: edfcd4d59eceada9e1c6014dad9afa7fe8ab3c8f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Создание классической виртуальной сети на портале Azure
Чтобы создать классическую виртуальную сеть по предыдущему сценарию, выполните следующие действия.

1. В браузере перейдите по адресу http://portal.azure.com и при необходимости выполните вход с помощью учетной записи Azure.
2. Последовательно выберите **Создать ресурс** > **Сети** > **Виртуальная сеть**. Обратите внимание, что в списке **Выбор модели развертывания** уже содержится вариант **Классическая**. 3. Щелкните **Создать**, как показано на следующем снимке экрана.
   
    ![Создание виртуальной сети на портале Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. В области **Виртуальная сеть** введите **имя** виртуальной сети и щелкните **Адресное пространство**. Настройте параметры адресного пространства для виртуальной сети и ее первой подсети, а затем нажмите кнопку **ОК**. На следующем снимке экрана показаны параметры блока CIDR для этого сценария.
   
    ![Область адресного пространства](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Выберите параметр **Группы ресурсов** и укажите группу ресурсов, которую нужно добавить в виртуальную сеть, или щелкните **Создать новую группу ресурсов**, чтобы добавить виртуальную сеть в новую группу ресурсов. На следующем снимке экрана показаны параметры новой группы ресурсов с именем **TestRG**. Дополнительные сведения о группах ресурсов см. в разделе "Группы ресурсов" [обзора Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Область создания группы ресурсов](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. При необходимости измените параметры **Подписка** и **Расположение** для виртуальной сети. 
7. Если вы не хотите, чтобы виртуальная сеть отображалась в виде плитки на **начальной панели**, снимите флажок **Закрепить на начальной панели**. 
8. Щелкните **Создать** и обратите внимание на плитку с именем **Создание виртуальной сети**, как показано на следующем снимке экрана.
   
    ![Создание виртуальной сети в портале](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Дождитесь создания виртуальной сети. Когда на экране появится этот элемент, щелкните его и добавьте другие подсети.
   
    ![Создание виртуальной сети в портале](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Вы увидите **конфигурацию** своей виртуальной сети, как показано ниже. 
   
    ![Создание виртуальной сети в портале](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Выберите пункты **Подсети** > **Добавить**, введите **имя** и укажите **диапазон адресов (блок CIDR)** для подсети, а затем нажмите кнопку **ОК**. На следующем снимке экрана показаны параметры для данного сценария.
    
    ![Создание виртуальной сети на портале Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

