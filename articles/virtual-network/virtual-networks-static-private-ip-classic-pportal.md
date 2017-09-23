---
title: "Настройка частных IP-адресов для (классических) виртуальных машин с помощью портала Azure | Документация Майкрософт"
description: "Узнайте, как настроить частные IP-адреса для (классических) виртуальных машин с помощью портала Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: bde6de3495c2909b63b1f85e420a4ff5e7ac2c1a
ms.contentlocale: ru-ru
ms.lasthandoff: 02/28/2017

---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Настройка частных IP-адресов для (классической) виртуальной машины с помощью портала Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

В этой статье рассматривается классическая модель развертывания. Кроме того, вы можете [управлять статическим частным IP-адресом в модели развертывания для диспетчера ресурсов](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Для выполнения приведенных ниже примеров действий требуется созданная простая среда. Чтобы выполнять действия в соответствии с указаниями, представленными в этом документе, сначала постройте тестовую среду, как описано в статье [Создание виртуальной сети](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Указание статического частного IP-адреса при создании виртуальной машины
Чтобы создать виртуальную машину с именем *DNS01* в подсети *FrontEnd* виртуальной сети *TestVNet* со статическим частным IP-адресом *192.168.1.101*, выполните следующие действия.

1. В браузере откройте адрес http://portal.azure.com и войдите с помощью учетной записи Azure.
2. Щелкните **Создать** > **Вычисление** > **Windows Server 2012 R2 Datacenter**. Обратите внимание на то, что в списке **Выберите модель развертывания** уже указан параметр **Классический**, и нажмите кнопку **Создать**.
   
    ![Создание виртуальной машины на портале Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. В колонке **Создать VM** введите имя создаваемой виртуальной машины (в нашем случае это*DNS01* ), учетную запись локального администратора и пароль.
   
    ![Создание виртуальной машины на портале Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Щелкните **Необязательная конфигурация** > **Сеть** > **Виртуальная сеть**, а затем — **TestVNet**. Если сеть **TestVNet** недоступна, убедитесь, что вы используете расположение *Центральная часть США* и создали тестовую среду, описанную в начале этой статьи.
   
    ![Создание виртуальной машины на портале Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. В колонке **Сеть** выберите подсеть *FrontEnd* и щелкните **IP-адреса**. В разделе **Назначение IP-адресов** щелкните **Статический** и введите *192.168.1.101* в поле **IP-адрес**, как показано ниже.
   
    ![Создание виртуальной машины на портале Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Нажмите кнопку **ОК** в колонке **IP-адреса**, затем нажмите кнопку **ОК** в колонке **Сеть**. После этого нажмите кнопку **ОК** в колонке **Необязательная конфигурация**.
7. В колонке **Создать виртуальную машину** щелкните **Создать**. Ниже обратите внимание на элемент, отображенный в панели мониторинга.
   
    ![Создание виртуальной машины на портале Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Как получить информацию о статическом частном IP-адресе виртуальной машины
Чтобы просмотреть информацию о статическом частном IP-адресе виртуальной машины, созданной с помощью описанные выше действий, выполните следующее.

1. На портале Azure щелкните **Просмотреть все** > **Виртуальные машины (классические)** > **DNS01** > **Все параметры** > **IP-адреса** и обратите внимание на назначение IP-адресов и сам IP-адрес.
   
    ![Создание виртуальной машины на портале Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Как удалить статический частный IP-адрес виртуальной машины
Чтобы удалить статический частный IP-адрес виртуальной машины, созданной ранее, выполните следующее.

1. В колонке **IP-адреса**, показанной ранее, щелкните **Динамический** справа от элемента **Назначение IP-адресов**, нажмите кнопку **Сохранить**, а затем — **Да**.
   
    ![Создание виртуальной машины на портале Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Как добавить статический частный IP-адрес для существующей виртуальной машины
Чтобы добавить статический частный IP-адрес для виртуальной машины, созданной с помощью действий, описанных выше, выполните следующее:

1. В колонке **IP-адреса**, показанной выше, справа от элемента **Назначение IP-адресов** щелкните **Статический**.
2. Введите *192.168.1.101* в поле **IP-адрес**, нажмите кнопку **Сохранить**, а затем — **Да**.

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с информацией о [зарезервированных общедоступных IP-адресах](virtual-networks-reserved-public-ip.md) .
* Узнайте об [общедоступных IP-адресах уровня экземпляра (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Ознакомьтесь с информацией о [REST API зарезервированных IP-адресов](https://msdn.microsoft.com/library/azure/dn722420.aspx).


