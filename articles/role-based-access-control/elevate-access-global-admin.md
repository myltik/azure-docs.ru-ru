---
title: Повышение прав доступа для глобального администратора в Azure Active Directory | Документы Майкрософт
description: Сведения об использовании портала Azure или REST API для повышения прав доступа для глобального администратора в Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.openlocfilehash: b671ff6b473093e59bce18c7bf98b32e9849bbb0
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077213"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Повышение прав доступа для глобального администратора в Azure Active Directory

Если вы являетесь [глобальным администратором](../active-directory/active-directory-assign-admin-roles-azure-portal.md#global-administrator) в Azure Active Directory (Azure AD), иногда могут возникать ситуации, требующие выполнения следующих задач:

- восстановление доступа к подписке Azure, если пользователь потерял доступ;
- предоставление другому пользователю или себе доступа к подписке Azure;
- просмотр всех подписок Azure в организации;
- предоставление приложению автоматизации (например, приложению для выставления счетов или аудита) доступа ко всем подпискам Azure.

По умолчанию действие роли администратора Azure AD и ролей управления доступом на основе ролей (RBAC) Azure не распространяется на Azure AD и Azure. Однако глобальный администратор в Azure AD может повысить свои права доступа для управления подписками Azure и группами управления. При повышении прав доступа администратору предоставляется роль [администратора доступа пользователей](built-in-roles.md#user-access-administrator) (роль RBAC) для всех подписок для конкретного клиента. Роль администратора доступа пользователей позволяет предоставлять другим пользователям доступ к ресурсам Azure в области root (`/`).

Такое повышение прав должно носить временный характер и происходить только при необходимости.

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>Повышение прав доступа для глобального администратора с помощью портала Azure

1. Войдите на [портал Azure](https://portal.azure.com) или [центр администрирования Azure Active Directory](https://aad.portal.azure.com).

1. В списке навигации выберите **Azure Active Directory**, а затем нажмите кнопку **Свойства**.

   ![Снимок экрана: свойства Azure AD](./media/elevate-access-global-admin/aad-properties.png)

1. В разделе **Глобальный администратор может управлять подписками Azure и группами управления** установите переключатель в положение **Да**.

   ![Снимок экрана: раздел "Глобальный администратор может управлять подписками Azure и группами управления"](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Если переключатель установлен в положение **Да**, учетная запись глобального администратора (текущего вошедшего в систему пользователя) добавляется к роли администратора доступа пользователей в Azure RBAC в области root (`/`). В результате предоставляется доступ на просмотр и составление отчетов по всем подпискам Azure, связанным с клиентом Azure AD.

   Если переключатель установлен в положение **Нет**, учетная запись глобального администратора (текущего вошедшего в систему пользователя) удаляется из роли администратора доступа пользователей в Azure RBAC. Вы не можете видеть все подписки Azure, связанные с клиентом Azure AD, а можете просматривать только те подписки Azure (и управлять ими), к которым вам был предоставлен доступ.

1. Нажмите кнопку **Сохранить**, чтобы сохранить настройки.

   Этот параметр не является глобальным свойством и применяется только к текущему выполнившему вход пользователю.

1. Выполните задачи, для которых требуются повышенные права доступа. Закончив, установите переключатель обратно в положение **Нет**.

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>Получение списка назначения ролей в области root (/) с помощью PowerShell

Чтобы получить список назначения роли администратора доступа пользователей для пользователя в области scope (`/`), выполните команду [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
```

## <a name="delete-a-role-assignment-at-the-root-scope--using-powershell"></a>Удаление назначений ролей в области root (/) с помощью PowerShell

Чтобы удалить назначение роли администратора доступа пользователей для пользователя в области scope (`/`), выполните команду [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment).

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>Повышение прав доступа для глобального администратора с помощью REST API

Чтобы повысить права доступа для глобального администратора с помощью REST API, выполните приведенные ниже основные действия.

1. С помощью REST вызовите действие `elevateAccess`, которое предоставит вам роль администратора доступа пользователей в области root (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Создание [назначение роли](/rest/api/authorization/roleassignments), чтобы назначить любую роль в любой области. В следующем примере показаны свойства для назначения роли {roleDefinitionID} в области root (`/`).

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. Администратор доступа пользователей может также удалять назначения ролей в области root (`/`).

1. Отзовите привилегии администратора доступа пользователей, пока они не понадобятся вновь.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>Как отменить действие elevateAccess с помощью REST API

При вызове `elevateAccess` для вас создается назначение роли. Поэтому, чтобы отозвать эти привилегии, необходимо удалить назначение.

1. Вызовите [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) (где `roleName` — это администратор доступа пользователей), чтобы определить GUID имени роли этого администратора.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Сохраните ИД из параметра `name`. В этом случае — `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. Также необходимо перечислить назначение ролей для администратора клиента в области клиента. Выведите список всех назначений в области клиента для `principalId` администратора клиента, совершившего вызов на повышение прав доступа. При этом будут перечислены все назначения в клиенте для objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >У администратора клиента не должно быть много назначений. Если предыдущий запрос возвращает слишком много назначений, можно запросить все назначения только на уровне области клиента, а затем отфильтровать результаты: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`.
        
    2. Предыдущие вызовы возвращают список назначения ролей. Найдите назначение роли, у которого задана область "/" и `roleDefinitionId` заканчивается значением ИД имени роли, найденным на шаге 1, а `principalId` совпадает со значением objectId администратора клиента. 
    
    Пример назначения ролей:

        ```json
        {
          "value": [
            {
              "properties": {
                "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                "principalId": "{objectID}",
                "scope": "/",
                "createdOn": "2016-08-17T19:21:16.3422480Z",
                "updatedOn": "2016-08-17T19:21:16.3422480Z",
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    Опять же, сохраните ИД из параметра `name`. В данном случае это e7dd75bc-06f6-4e71-9014-ee96a929d099.

    3. Наконец, используйте ИД назначения роли, чтобы удалить назначение, добавленное `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Дополнительная информация

- [Управление доступом на основе ролей с помощью REST](role-assignments-rest.md)
- [Управление назначениями доступа](role-assignments-users.md)
