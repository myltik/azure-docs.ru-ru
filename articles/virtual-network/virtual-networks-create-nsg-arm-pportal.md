---
title: Создание группы безопасности сети с помощью портала Azure | Документация Майкрософт
description: Узнайте, как создавать и развертывать группы безопасности сети с помощью портала Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>Создание группы безопасности сети с помощью портала Azure

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

В этой статье описывается модель развертывания с использованием менеджера ресурсов. Вы также можете [создавать группы безопасности сети с помощью классической модели развертывания](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>Создание группы безопасности сети NSG-FrontEnd
Чтобы создать группу безопасности сети **NSG-FrontEnd**, как показано в этом сценарии, выполните указанные ниже действия.

1. В браузере перейдите по адресу https://portal.azure.com и при необходимости выполните вход с помощью учетной записи Azure.
2. Выберите **+ Создать ресурс >** > **Группы безопасности сети**.
   
    ![Портал Azure — группы безопасности сети](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. В разделе **Группы безопасности сети** выберите **Добавить**.
   
    ![Портал Azure — группы безопасности сети](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. В разделе **Создание группы безопасности сети** создайте группу безопасности *NSG-FrontEnd* в группе ресурсов *RG-NSG*, а затем щелкните **Создать**.
   
    ![Портал Azure — группы безопасности сети](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Создание правил в существующей сетевой группе безопасности
Чтобы создать правила в существующей группе безопасности сети с помощью портала Azure, выполните следующие шаги.

1. Выберите **Все службы** и выполните поиск по фразе **группы безопасности сети**. Когда появится элемент **Группы безопасности сети**, выберите его.
2. В списке групп безопасности сети выберите **NSG-FrontEnd** > **Правила безопасности для входящего трафика**.
   
    ![Портал Azure — NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. В списке **правил безопасности для входящего трафика** выберите **Добавить**.
   
    ![Портал Azure — добавление правила](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. В разделе **Добавление правила безопасности для входящего трафика** создайте правило с именем *web-rule* и приоритетом *200*, которое разрешает доступ по протоколу *TCP* от любого источника к порту *80* на любой виртуальной машине, и щелкните **ОК**. Обратите внимание, что для большинства этих параметров уже заданы значения по умолчанию.
   
    ![Портал Azure — параметры правила](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Через несколько секунд новое правило появится в группе безопасности сети.
   
    ![Портал Azure — новое правило](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Повторите действия до шага 6, чтобы создать правило входящих подключений *rdp-rule* с приоритетом *250*, которое разрешает доступ через *TCP*-порт *3389* к любой виртуальной машине из любого источника.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Связывание группы безопасности сети с подсетью FrontEnd

1. Выберите **Все службы**, введите фразу **группы ресурсов**, выберите появившийся элемент **Группы ресурсов** и щелкните **RG-NSG**.
2. В разделе **RG-NSG** выберите **...** > **TestVNet**.
   
    ![Портал Azure — TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. В разделе **Параметры** выберите элементы **Подсети** > **FrontEnd** > **Группа безопасности сети** > **NSG-FrontEnd**.
   
    ![Портал Azure — параметры подсети](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. В колонке **FrontEnd** щелкните **Сохранить**.
   
    ![Портал Azure — параметры подсети](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Создание группы безопасности сети NSG-BackEnd
Чтобы создать группу безопасности сети **NSG-BackEnd** и связать ее с подсетью **BackEnd**, выполните следующие шаги.

1. Повторите шаги из раздела [Создание группы безопасности сети NSG-FrontEnd](#Create-the-NSG-FrontEnd-NSG), чтобы создать группу безопасности сети *NSG-BackEnd*.
2. Повторите шаги из раздела [Создание правил в существующей сетевой группе безопасности](#Create-rules-in-an-existing-NSG), чтобы создать правила для **входящих подключений**, перечисленные в таблице ниже.
   
   | Правило входящих подключений | Правило исходящих подключений |
   | --- | --- |
   | ![Портал Azure — правило входящих подключений](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Портал Azure — правило исходящих подключений](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Повторите шаги из раздела [Связывание группы безопасности сети с подсетью FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet), чтобы связать группу безопасности сети **NSG-BackEnd** с подсетью **BackEnd**.

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [управлять существующими группами безопасности сети](manage-network-security-group.md)
* [Включите ведение журнала](virtual-network-nsg-manage-log.md) для групп безопасности сети.