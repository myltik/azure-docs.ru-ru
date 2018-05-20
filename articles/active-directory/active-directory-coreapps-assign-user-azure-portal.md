---
title: Назначение пользователя или группы корпоративному приложению в Azure Active Directory | Документы Майкрософт
description: Узнайте, как выбрать корпоративное приложение и назначить для него пользователя или группу в Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: 1609942269a0c737239cf4198289fdc44ade2b32
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Назначение пользователя или группы корпоративному приложению в Azure Active Directory
Чтобы назначить пользователя или группу в корпоративном приложении, необходимо иметь соответствующие разрешения для управления корпоративным приложением, а также права глобального администратора для доступа к каталогу.
> [!NOTE]
> В приложениях Майкрософт (например, в приложениях Office 365) назначить пользователей в корпоративном приложении можно с помощью PowerShell.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Как назначить пользователю доступ к корпоративному приложению на портале Azure?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Все службы**, в текстовом поле введите Azure Active Directory, а затем нажмите клавишу **ВВОД**.
3. В колонке **Azure Active Directory — *имя_каталога*** (то есть в колонке Azure AD для каталога, которым вы управляете) выберите **Корпоративные приложения**.

    ![Открытие колонки "Корпоративные приложения"](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. В колонке **Корпоративные приложения** выберите **Все приложения**. Отобразится список приложений, которыми вы можете управлять.
5. В колонке **Корпоративные приложения — Все приложения** выберите приложение.
6. В колонке ***имя_приложения*** (то есть в колонке с именем выбранного приложения в заголовке) выберите **Пользователи и группы**.

    ![Выбор команды "Все приложения"](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. В колонке ***имя_приложения*****— User &amp; Group Assignment** (Назначение пользователей и групп) щелкните **Добавить**.
8. В колонке **Добавление назначения** щелкните **Пользователи и группы**.

    ![Назначение приложению пользователя или группы](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. В колонке **Пользователи и группы** выберите одного или несколько пользователей или групп из списка, а затем нажмите кнопку **Выбрать** в нижней части колонки.
10. В колонке **Добавление назначения** щелкните **Роль**. Затем в колонке **Выбор роли** выберите роль для этих пользователей или групп. Нажмите кнопку **ОК** в нижней части колонки.
11. В колонке **Добавление назначения** нажмите кнопку **Назначить** в нижней части колонки. У назначенных пользователей и групп будут разрешения, определенные выбранной ролью для этого корпоративного приложения.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>Как назначить пользователя в корпоративном приложении при помощи PowerShell?

1. Откройте окно Windows PowerShell с повышенными привилегиями.

    >[!NOTE] 
    > Вам потребуется установить модуль Azure AD (с помощью команды `Install-Module -Name AzureAD`). Если будет предложено установить модуль NuGet или новый модуль PowerShell Azure для Active Directory версии 2, введите Y и нажмите клавишу ВВОД.

2. Выполните команду `Connect-AzureAD` и войдите в систему с помощью учетных данных глобального администратора.
3. Чтобы назначить пользователя и его роль в приложении, используйте следующий скрипт:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"
    
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```     

Дополнительные сведения о том, как назначить пользователю роль в приложении, см. в документации по командлету [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0).

Чтобы назначить группу корпоративному приложению, измените `Get-AzureADUser` на `Get-AzureADGroup`.

### <a name="example"></a>Пример

В этом примере пользователь Britta Simon назначается в приложении [Microsoft Workplace Analytics](https://products.office.com/en-us/business/workplace-analytics) с помощью PowerShell.

1. В PowerShell присвойте соответствующие значения переменным $username, $app_name и $app_role_name. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. В этом примере неизвестно, как именно называется роль приложения, которую мы хотим назначить пользователю Britta Simon. Выполните следующие команды, чтобы получить пользователя ($user) и субъект-службу ($sp) с помощью отображаемых имен участника-пользователя и субъекта-службы.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Выполните команду `$sp.AppRoles`, чтобы отобразить роли, доступные для приложения Workplace Analytics. В этом примере мы хотим назначить пользователю Britta Simon роль Analyst (Limited Access) (аналитик с ограниченным доступом).
    
    ![Роль в приложении Workplace Analytics](media/active-directory-coreapps-assign-user-azure-portal/workplace-analytics-role.png)

4. Присвойте имя роли переменной `$app_role_name`.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. Выполните следующую команду, чтобы назначить пользователю роль в приложении:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Дополнительная информация
* [Просмотр всех моих групп](active-directory-groups-view-azure-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Отключение входа пользователя в корпоративное приложение](active-directory-coreapps-disable-app-azure-portal.md)
* [Изменение имени или логотипа корпоративного приложения](active-directory-coreapps-change-app-logo-user-azure-portal.md)
