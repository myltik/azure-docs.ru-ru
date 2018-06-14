---
title: Добавление новой учетной записи клиента Azure Stack в Azure Active Directory | Документация Майкрософт
description: Развернув Пакет средств разработки Azure Stack, создайте хотя бы одну учетную запись клиента, чтобы вы могли просматривать портал клиента.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 590426563936c66b1353f769be138759bb53f58c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2018
ms.locfileid: "29553211"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Добавление новой учетной записи клиента Azure Stack в Azure Active Directory
[Развернув Пакет средств разработки Azure Stack](azure-stack-run-powershell-script.md), создайте учетную запись клиента, чтобы вы могли просматривать портал клиента и тестировать предложения и планы. Учетную запись можно создать с помощью [портала Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) или [PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Создание учетной записи клиента Azure Stack с помощью портала Azure
Для использования портала Azure необходима подписка Azure.

1. Войдите в [Azure](https://portal.azure.com).
2. На левой навигационной панели Microsoft Azure щелкните **Active Directory**.
3. В списке каталогов щелкните каталог, который хотите использовать для Azure Stack, или создайте новый каталог.
4. На странице каталога щелкните **Пользователи**.
5. Нажмите кнопку **Добавить пользователя**.
6. В мастере **Добавление пользователя** в списке **Тип пользователя** выберите **Новый пользователь в вашей организации**.
7. В поле **Имя пользователя** введите имя пользователя.
8. В мастере **@** выберите соответствующую запись.
9. Щелкните стрелку «Далее».
10. В мастере на странице **Профиль пользователя** введите **имя**, **фамилию** и **отображаемое имя**.
11. В списке **Роль** выберите **Пользователь**.
12. Щелкните стрелку «Далее».
13. На странице **Получить временный пароль** нажмите кнопку **Создать**.
14. Скопируйте **новый пароль**.
15. Войдите в Microsoft Azure с помощью новой учетной записи. В ответ на запрос измените пароль.
16. Войдите в `https://portal.local.azurestack.external` с помощью новой учетной записи для просмотра портала клиента.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Создание учетной записи клиента Azure Stack с помощью PowerShell
Если у вас нет подписки Azure, вы не сможете использовать портал Azure для добавления учетной записи клиента. В таком случае вы можете использовать модуль Azure Active Directory для Windows PowerShell.

> [!NOTE]
> Если вы работаете с учетной записью Майкрософт (Live ID) для развертывания Пакета средств разработки Azure Stack, вы не сможете использовать AAD PowerShell для создания учетной записи клиента. 
> 
> 

1. Установите [помощник по входу в Microsoft Online Services для профессиональной версии RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Установите [модуль Azure Active Directory для Windows PowerShell (64-разрядную версию)](http://go.microsoft.com/fwlink/p/?linkid=236297) и откройте его.
3. Выполните следующие командлеты.

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Войдите в Microsoft Azure с помощью новой учетной записи. В ответ на запрос измените пароль.
2. Войдите в `https://portal.local.azurestack.external` с помощью новой учетной записи для просмотра портала клиента.

