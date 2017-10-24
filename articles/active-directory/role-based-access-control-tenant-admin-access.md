---
title: "Повышение прав доступа администратором клиентов в Azure AD | Документация Майкрософт"
description: "В этом разделе описаны встроенные роли для управления доступом на основе ролей (RBAC)."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andredm
ms.openlocfilehash: 22b62be1773c5042ecf6ee078e68a4ffdf791d53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Повышение прав доступа администратором клиентов с помощью управления доступом на основе ролей

Управление доступом на основе ролей позволяет администраторам клиентов временно повысить права доступа, чтобы предоставить больше разрешений, чем обычно. При необходимости администратор клиентов может повысить свои права до роли администратора доступа пользователей. Эта роль позволяет администратору клиентов предоставить себе или другим пользователям роли в области "/".

Эта возможность важна, так как она позволяет администратору клиентов видеть все подписки, которые существуют в организации. Она также позволяет приложениям службы автоматизации (например, приложениям для выставления счетов и аудита) обращаться ко всем подпискам и обеспечивать точное представление о состоянии организации с точки зрения выставления счетов или управления ресурсами.  

## <a name="how-to-use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Как использовать elevateAccess для доступа к клиенту с помощью Центра администрирования Azure AD

В [Центре администрирования Azure Active Directory](https://aad.portal.azure.com) можно вызвать эту функцию в разделе **Свойства**.
Эта функция называется **Глобальный администратор может управлять подписками Azure**. Вы можете решить, что это глобальное свойство для Azure Active Directory, но оно применимо только к области действий текущего пользователя. Как пользователь с правами глобального администратора в Azure Active Directory вы можете вызывать функцию elevateAccess для пользователя, который вошел в Центр администрирования Active Directory Azure.

Выберите **Да** и **Сохранить**. Так вы **назначите** роль **Администратор доступа пользователей** роли в корневой области (/) для пользователя, который вошел на портал.

Выберите **Нет** и **Сохранить**. Так вы **удалите** роль **Администратор доступа пользователей** роли в корневой области (/) для пользователя, который вошел на портал.

![Снимок экрана: включение управления подпиской Azure для глобального администратора в Центре администрирования Azure AD](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="how-to-use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>Как использовать elevateAccess для предоставления доступа клиентам с помощью REST API

Базовый процесс состоит из приведенных ниже действий.

1. С помощью REST вызовите действие *elevateAccess*, который предоставляет вам роль администратора доступа пользователей в области "/".

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Создание [назначение роли](/rest/api/authorization/roleassignments), чтобы назначить любую роль в любой области. В следующем примере показаны свойства для назначения роли "Читатель" для области "/".

    ```
    { "properties":{
    "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
    "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
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

1.  Вызовите [GET roleDefinitions](/rest/api/authorization/roledefinitions#RoleDefinitions_Get), где roleName — администратор доступа пользователей, чтобы определить GUID параметра name роли администратор доступа пользователей. Результат должен выглядеть следующим образом.

    ```
    {"value":[{"properties":{
    "roleName":"User Access Administrator",
    "type":"BuiltInRole",
    "description":"Lets you manage user access to Azure resources.",
    "assignableScopes":["/"],
    "permissions":[{"actions":["*/read","Microsoft.Authorization/*","Microsoft.Support/*"],"notActions":[]}],
    "createdOn":"0001-01-01T08:00:00.0000000Z",
    "updatedOn":"2016-05-31T23:14:04.6964687Z",
    "createdBy":null,
    "updatedBy":null},
    "id":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "type":"Microsoft.Authorization/roleDefinitions",
    "name":"18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"}],
    "nextLink":null}
    ```

    Сохраните GUID из параметра *name*, в данном случае это **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. Вызовите [GET roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_Get), где principalId — ваш ObjectId. Буден выведен список всех назначений в клиенте. Найдите назначение, у которого задана область "/" и RoleDefinitionId заканчивается значением GUID из параметра name роли, найденным на шаге 1. Назначение роли должно выглядеть следующим образом.

    ```
    {"value":[{"properties":{
    "roleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "principalId":"{objectID}",
    "scope":"/",
    "createdOn":"2016-08-17T19:21:16.3422480Z",
    "updatedOn":"2016-08-17T19:21:16.3422480Z",
    "createdBy":"93ce6722-3638-4222-b582-78b75c5c6d65",
    "updatedBy":"93ce6722-3638-4222-b582-78b75c5c6d65"},
    "id":"/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
    "type":"Microsoft.Authorization/roleAssignments",
    "name":"e7dd75bc-06f6-4e71-9014-ee96a929d099"}],
    "nextLink":null}
    ```

    Опять же, сохраните GUID из параметра *name*. В данном случае это **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

3. Наконец, вызовите [DELETE roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_DeleteById), где roleAssignmentId — GUID из параметра name роли, найденный на шаге 2.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше об [управлении доступом на основе ролей с помощью REST](role-based-access-control-manage-access-rest.md).

- Изучите [управление правами доступа](role-based-access-control-manage-assignments.md) на портале Azure.
