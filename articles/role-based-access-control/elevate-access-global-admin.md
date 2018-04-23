---
title: Повышение прав доступа администратором клиентов в Azure AD | Документация Майкрософт
description: В этом разделе описаны встроенные роли для управления доступом на основе ролей (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: rolyon
ms.openlocfilehash: 7b4625bff277851fb9002e54b26485b948981252
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Повышение прав доступа администратором клиентов с помощью управления доступом на основе ролей

Управление доступом на основе ролей позволяет администраторам клиентов временно повысить права доступа, чтобы предоставить больше разрешений, чем обычно. При необходимости администратор клиентов может повысить свои права до роли администратора доступа пользователей. Эта роль позволяет администратору клиентов предоставить себе или другим пользователям роли в области "/".

Эта возможность важна, так как она позволяет администратору клиентов видеть все подписки, которые существуют в организации. Она также позволяет приложениям службы автоматизации (например, приложениям для выставления счетов и аудита) обращаться ко всем подпискам и обеспечивать точное представление о состоянии организации с точки зрения выставления счетов или управления ресурсами.  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Использование elevateAccess для доступа к клиенту с помощью Центра администрирования Azure AD

1. Перейдите в [центр администрирования Azure Active Directory](https://aad.portal.azure.com) и войдите в систему, используя свои учетные данные.

2. В левом меню Azure AD выберите **Свойства**.

3. Найдите пункт **Глобальный администратор может управлять подписками Azure**, выберите **Да**, а затем нажмите кнопку **Сохранить**.
    > [!IMPORTANT] 
    > Когда вы выбираете вариант **Да**, вы назначаете роль **Администратор доступа пользователей** в корневой области (/) для пользователя, который вошел на портал. **Это позволяет пользователю видеть все остальные подписки Azure.**
    
    > [!NOTE] 
    > Когда вы выбираете вариант **Нет**, вы удаляете роль **Администратор доступа пользователей** в корневой области (/) для пользователя, который вошел на портал.

> [!TIP] 
> Вы можете решить, что это глобальное свойство для Azure Active Directory, но оно применимо только к области действий текущего пользователя. Как пользователь с правами глобального администратора в Azure Active Directory вы можете вызывать функцию elevateAccess для пользователя, который вошел в Центр администрирования Active Directory Azure.

![Снимок экрана: включение управления подпиской Azure для глобального администратора в Центре администрирования Azure AD](./media/elevate-access-global-admin/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="view-role-assignments-at-the--scope-using-powershell"></a>Просмотр назначений ролей в области / с помощью PowerShell
Чтобы просмотреть назначение **администратора доступа пользователей** в области **/**, используйте командлет PowerShell `Get-AzureRmRoleAssignment`.
    
```powershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" -and $_.SignInName -eq "<username@somedomain.com>" -and $_.Scope -eq "/"}
```

**Пример выходных данных**:
```
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0    
Scope              : /    
DisplayName        : username    
SignInName         : username@somedomain.com    
RoleDefinitionName : User Access Administrator    
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9    
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc    
ObjectType         : User    
```

## <a name="delete-the-role-assignment-at--scope-using-powershell"></a>Удаление назначений ролей в области / с помощью PowerShell
Чтобы удалить назначение, используйте следующий командлет PowerShell:

```powershell
Remove-AzureRmRoleAssignment -SignInName <username@somedomain.com> -RoleDefinitionName "User Access Administrator" -Scope "/" 
```

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>Использование elevateAccess для предоставления доступа клиентам с помощью REST API

Базовый процесс состоит из приведенных ниже действий.

1. С помощью REST вызовите действие *elevateAccess*, который предоставляет вам роль администратора доступа пользователей в области "/".

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Создание [назначение роли](/rest/api/authorization/roleassignments), чтобы назначить любую роль в любой области. В следующем примере показаны свойства для назначения роли "Читатель" для области "/".

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

3. Являясь администратором доступа пользователей, можно также удалять назначения роли для области "/".

4. Отзовите привилегии администратора доступа пользователей, пока они не понадобятся вновь.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>Как отменить действие elevateAccess с помощью REST API

При вызове *elevateAccess* для вас создается назначение роли. Поэтому, чтобы отозвать эти привилегии, необходимо удалить назначение.

1.  Вызовите GET roleDefinitions, где roleName — администратор доступа пользователей, чтобы определить GUID имени роли этого администратора.
    ```
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

    Сохраните GUID из параметра *name*, в данном случае это **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. Также необходимо перечислить назначение ролей для администратора клиента в области клиента. Перечислите все назначения в области клиента для PrincipalId администратора клиента (TenantAdmin), совершившего вызов на повышение прав доступа. При этом будут перечислены все назначения в клиенте для ObjectID.

    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >У администратора клиента не должно быть много назначений. Если предыдущий запрос возвращает слишком много назначений, можно запросить все назначения только на уровне области клиента, а затем отфильтровать результаты: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
    
        
    2. Предыдущие вызовы возвращают список назначения ролей. Найдите назначение роли, у которого задана область "/" и RoleDefinitionId заканчивается значением GUID из параметра name роли, найденным на шаге 1, а PrincipalId совпадает со значением ObjectId администратора клиента. 
    
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
        
    Опять же, сохраните GUID из параметра *name*. В данном случае это **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

    3. Наконец, используйте выделенное значение **RoleAssignment ID**, чтобы удалить назначение, добавленное запросом на повышение прав доступа:

    ```
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше об [управлении доступом на основе ролей с помощью REST](role-assignments-rest.md).

- Изучите [управление правами доступа](role-assignments-users.md) на портале Azure.
