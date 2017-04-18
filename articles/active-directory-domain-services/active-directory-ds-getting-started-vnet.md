---
title: "Доменные службы Azure Active Directory: создание или выбор виртуальной сети | Документация Майкрософт"
description: "Приступая к работе с доменными службами Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 13ab1608-e3d8-40de-9f7b-9b5b42199af4
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: cb372232492e8f98ff1543798b92b4b60fc25021
ms.lasthandoff: 04/12/2017


---
# <a name="create-or-select-a-virtual-network-for-azure-active-directory-domain-services"></a>Создание или выбор виртуальной сети для доменных служб Azure Active Directory
## <a name="before-you-begin"></a>Перед началом работы
См. статью [Рекомендации по сетям для доменных служб Azure AD](active-directory-ds-networking.md).

## <a name="task-2-create-an-azure-virtual-network"></a>Задача 2. Создание виртуальной сети Azure
Следующая задача по настройке — создать виртуальную сеть Azure c подсетью. Вам потребуется включить доменные службы Azure Active Directory в этой подсети. Если вы хотите использовать существующую виртуальную сеть, этот шаг можно пропустить.

> [!NOTE]
> Убедитесь, что выбранная или создаваемая виртуальная сеть для использования с доменными службами Azure Active Directory принадлежит к региону Azure, поддерживаемому доменными службами Azure Active Directory. Перейдите на страницу [служб Azure по регионам](https://azure.microsoft.com/regions/#services/), чтобы просмотреть список регионов Azure, в которых доступны доменные службы Azure Active Directory.
>
>Запишите имя виртуальной сети, чтобы выбрать нужную виртуальную сеть при включении доменных служб Azure Active Directory на следующем шаге настройки.


Чтобы создать виртуальную сеть Azure, в которой вы хотите включить доменные службы Azure Active Directory, выполните следующие инструкции по настройке.

1. Войдите на [классический портал Azure](https://manage.windowsazure.com).
2. В левой области щелкните **Сети**.

    ![Узел "Сети"](./media/active-directory-domain-services-getting-started/networks-node.png)  
    Откроется окно **Виртуальные сети**.
3. В области задач в нижней части окна щелкните **Создать**.

    ![Окно виртуальных сетей](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. Щелкните **Сетевые службы**, а затем выберите **Виртуальная сеть**.
    
    ![Виртуальная сеть — быстрое создание](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
5. Чтобы создать виртуальную сеть, щелкните **Быстрое создание**.
    
6. Укажите **имя** для виртуальной сети. Далее можно сделать следующее: 
    * Вы можете настроить **адресное пространство** или указать **максимальное число виртуальных машин** для этой сети. 
    * Для параметра **DNS-сервер** пока можно оставить значение **Нет**. Этот параметр можно обновить после включения доменных служб Azure Active Directory.
7. В раскрывающемся списке **Расположение** выберите поддерживаемый регион Azure.  
    Перейдите на страницу [служб Azure по регионам](https://azure.microsoft.com/regions/#services/), чтобы просмотреть список регионов Azure, в которых доступны доменные службы Azure Active Directory.
8. Чтобы создать виртуальную сеть, нажмите кнопку **Создать виртуальную сеть**.

    ![Создание виртуальной сети для доменных служб Azure Active Directory](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. После создания виртуальной сети выберите ее имя и перейдите на вкладку **Настройка**.

    ![Создание подсети](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. В разделе **Адресное пространство виртуальной сети** щелкните **Добавить подсеть**, а затем укажите подсеть с именем **AaddsSubnet**. 

    ![Создание подсети для доменных служб Azure Active Directory](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

11. Чтобы создать подсеть, щелкните **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия
Задача 3. [Включение доменных служб Azure Active Directory](active-directory-ds-getting-started-enableaadds.md)

