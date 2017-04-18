---
title: "Доменные службы Azure Active Directory: обновление настроек DNS для виртуальной сети Azure | Документация Майкрософт"
description: "Приступая к работе с доменными службами Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: abb27292d4b5533fe6f3d66d6921fea8c82f18dd
ms.lasthandoff: 04/12/2017


---
# <a name="update-dns-settings-for-the-azure-virtual-network"></a>Обновление настроек DNS для виртуальной сети Azure
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Задача 4. Обновление настроек DNS для виртуальной сети Azure
В предыдущих задачах по настройке вы успешно включили доменные службы Azure Active Directory для своего каталога. Следующая задача — сделать так, чтобы компьютеры в виртуальной сети могли подключаться к этим службам и использовать их. В этой статье мы обновим параметры DNS-сервера для виртуальной сети, указав два IP-адреса, по которым доменные службы Azure Active Directory доступны в виртуальной сети.

> [!NOTE]
> После включения доменных служб Azure Active Directory для каталога запишите IP-адреса доменных служб Azure Active Directory, которые отображаются на вкладке **Настройка** вашего каталога.
>
>

Чтобы обновить параметр DNS-сервера для виртуальной сети, в которой были включены доменные службы Azure Active Directory, сделайте следующее.

1. Войдите на [классический портал Azure](https://manage.windowsazure.com).
2. В левой области щелкните **Сети**.  
    Откроется окно **Сети**.

    ![Окно виртуальных сетей](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. На вкладке **Виртуальные сети** выберите виртуальную сеть, в которой были включены доменные службы Azure Active Directory, чтобы просмотреть ее свойства.
4. Выберите вкладку **Настройка** .

    ![Окно виртуальных сетей](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. В разделе **DNS-серверы** введите оба IP-адреса, которые отображались в разделе **Доменные службы** на вкладке **Настройка** вашего каталога.
6. Чтобы сохранить параметры DNS-сервера для этой виртуальной сети, на панели задач в нижней части страницы щелкните **Сохранить**.

   ![Обновление параметров DNS-сервера для виртуальной сети.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> После обновления параметров DNS-сервера для виртуальной сети может потребоваться некоторое время на обновление конфигурации DNS виртуальных машин в сети. Если виртуальная машина не может подключиться к домену, можно освободить кэш DNS на виртуальной машине (ipconfig /flushdns). Эта команда принудительно обновит параметры DNS на виртуальной машине.
>
>

## <a name="next-steps"></a>Дальнейшие действия
Задача 5. [Включение синхронизации паролей с доменными службами Azure AD](active-directory-ds-getting-started-password-sync.md)

