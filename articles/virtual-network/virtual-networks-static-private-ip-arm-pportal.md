---
title: Настройка частных IP-адресов для виртуальных машин с помощью портала Azure | Документация Майкрософт
description: Узнайте, как настроить частные IP-адреса для виртуальных машин с помощью портала Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d551758277373995a6f92e1a25a59d170464fe5e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Настройка частных IP-адресов для виртуальной машины с помощью портала Azure

> [!div class="op_single_selector"]
> * [портал Azure](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [интерфейс командной строки Azure](virtual-networks-static-private-ip-arm-cli.md)
> * [Портал Azure (классическая модель)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (классическая модель)](virtual-networks-static-private-ip-classic-ps.md)
> * [Интерфейс командной строки Azure (классическая модель)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

В этой статье описывается модель развертывания с использованием менеджера ресурсов. Кроме того, вы можете [управлять статическим частным IP-адресом в классической модели развертывания](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Для выполнения приведенных ниже примеров действий требуется созданная простая среда. Чтобы выполнять действия в соответствии с указаниями, представленными в этом документе, сначала создайте тестовую среду, как описано в статье [Создание виртуальной сети с помощью портала Azure](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Как создать виртуальную машину для тестирования статических частных IP-адресов
Невозможно задать статический частный IP-адрес во время создания виртуальной машины в режиме развертывания диспетчера ресурсов с помощью портала Azure. Сначала необходимо создать виртуальную машину, а затем указать, что ее частный IP-адрес является статическим.

Чтобы создать виртуальную машину *DNS01* в подсети *FrontEnd* виртуальной сети *TestVNet*, выполните следующие действия.

1. В браузере откройте адрес http://portal.azure.com и войдите с помощью учетной записи Azure.
2. Щелкните **Создать ресурс** > **Вычисление** > **Windows Server 2012 R2 Datacenter**. Обратите внимание на то, что в списке **Выберите модель развертывания** уже указан параметр **Resource Manager**, и нажмите кнопку **Создать** (см. рисунок ниже).
   
    ![Создание виртуальной машины на портале Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. В области **Основные** введите имя создаваемой виртуальной машины (в нашем случае это *DNS01*), учетную запись локального администратора и пароль, как показано на рисунке ниже.
   
    ![Область "Основные"](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. В качестве **расположения** выберите *Центральная часть США*, щелкните **Выбрать существующую** в разделе **Группа ресурсов**, а затем еще раз щелкните **Группа ресурсов**, выберите *TestRG* и нажмите кнопку **ОК**.
   
    ![Область "Основные"](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. В области **Выбор размера** выберите **A1 Standard**, а затем щелкните **Выбрать**.
   
    ![Область "Выбор размера"](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. В области **Параметры** убедитесь, что заданы приведенные ниже значения свойств, и нажмите кнопку **ОК**.
   
    -**Учетная запись**: *vnetstorage*
   
   * **Сеть**: *TestVNet*
   * **Подсеть**: *FrontEnd*
     
     ![Область "Выбор размера"](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. В области **Сводка** нажмите кнопку **ОК**. Ниже обратите внимание на элемент, отображенный в панели мониторинга.
   
    ![Создание виртуальной машины на портале Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Как получить информацию о статическом частном IP-адресе виртуальной машины
Чтобы просмотреть информацию о статическом частном IP-адресе виртуальной машины, созданной с помощью описанные выше действий, выполните следующее.

1. На портале Azure щелкните **Просмотреть все** > **Виртуальные машины** > **DNS01** > **Все параметры** > **Сетевые интерфейсы**, а затем щелкните единственный сетевой интерфейс в списке.
   
    ![Элемент «Развертывание виртуальной машины»](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. В области **Сетевой интерфейс** щелкните **Все параметры** > **IP-адреса** и просмотрите значения **Назначение** и **IP-адрес**.
   
    ![Элемент «Развертывание виртуальной машины»](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Как добавить статический частный IP-адрес для существующей виртуальной машины
Чтобы добавить статический частный IP-адрес для виртуальной машины, созданной с помощью действий, описанных выше, выполните следующее.

1. В области **IP-адреса**, показанной выше, в разделе **Назначение** щелкните **Статический**.
2. Введите *192.168.1.101* в поле **IP-адрес**, а затем нажмите кнопку **Сохранить**.
   
    ![Создание виртуальной машины на портале Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Если после нажатия кнопки **Сохранить** назначение по-прежнему остается **динамическим**, это означает, что введенный IP-адрес уже используется. Попробуйте другой IP-адрес.
> 
> 

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Удаление статического частного IP-адреса виртуальной машины
Чтобы удалить статический частный IP-адрес виртуальной машины, созданной ранее, выполните следующее.

В области **IP-адреса**, показанной выше, в разделе **Назначение** щелкните **Динамический** и нажмите кнопку **Сохранить**.

## <a name="next-steps"></a>Дополнительная информация
* Ознакомьтесь с информацией о [зарезервированных общедоступных IP-адресах](virtual-networks-reserved-public-ip.md) .
* Узнайте об [общедоступных IP-адресах уровня экземпляра (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Ознакомьтесь с информацией о [REST API зарезервированных IP-адресов](https://msdn.microsoft.com/library/azure/dn722420.aspx).

