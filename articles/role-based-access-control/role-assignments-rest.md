---
title: Управление доступом на основе ролей с помощью REST в Azure AD | Документация Майкрософт
description: Управление доступом на основе ролей с помощью интерфейса REST API
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: rolyon
ms.openlocfilehash: e520b4b85ff7c840c46175cae5b853ec6979c04a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
---
# <a name="manage-role-based-access-control-with-the-rest-api"></a>Управление доступом на основе ролей с помощью REST API
> [!div class="op_single_selector"]
> * [PowerShell](role-assignments-powershell.md)
> * [интерфейс командной строки Azure](role-assignments-cli.md)
> * [REST API](role-assignments-rest.md)

С помощью механизма управления доступом на основе ролей (RBAC) можно определить доступ для пользователей, групп и субъектов-служб, назначая роли для определенной области. В этой статье описывается, как управлять доступом с помощью REST API.

## <a name="list-all-role-assignments"></a>Вывод списка всех назначений ролей
Здесь описывается вывод списка всех назначений ролей в указанной области и внутренних областях.

Чтобы вывести список назначений ролей, требуется доступ к операции `Microsoft.Authorization/roleAssignments/read` в этой области. Доступ к этой операции предоставляется всем встроенным ролям. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](role-assignments-portal.md).

### <a name="request"></a>Запрос
Используйте метод **GET** со следующим универсальным кодом ресурса (URI).

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

1. Замените *{scope}* областью, для которой требуется вывести список назначений ролей. В следующих примерах показано, как указать область для различных уровней:

   * Subscription: /subscriptions/{ИД_подписки}  
   * Группа ресурсов: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1  
   * Ресурс: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Замените *{api-version}* значением 2015-07-01.
3. Замените *{filter}* условием, по которому требуется отфильтровать список назначений ролей.

   * Вывод списка назначений ролей только для определенной области без учета внутренних областей: `atScope()`    
   * Вывод списка назначений ролей для определенного пользователя, группы или приложения: `principalId%20eq%20'{objectId of user, group, or service principal}'`  
   * Вывод списка назначений ролей для определенного пользователя, включая роли, унаследованные от групп | `assignedTo('{objectId of user}')`

### <a name="response"></a>Ответ
Код состояния: 200.

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Получение сведений о назначении роли
Здесь описывается получение сведений о назначении роли, указанной с помощью идентификатора.

Чтобы получить сведения о назначении роли, требуется доступ к операции `Microsoft.Authorization/roleAssignments/read`. Доступ к этой операции предоставляется всем встроенным ролям. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](role-assignments-portal.md).

### <a name="request"></a>Запрос
Используйте метод **GET** со следующим универсальным кодом ресурса (URI).

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

1. Замените *{scope}* областью, для которой требуется вывести список назначений ролей. В следующих примерах показано, как указать область для различных уровней:

   * Subscription: /subscriptions/{ИД_подписки}  
   * Группа ресурсов: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1  
   * Ресурс: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Замените *{role-assignment-id}* идентификатором GUID для назначения роли.
3. Замените *{api-version}* значением 2015-07-01.

### <a name="response"></a>Ответ
Код состояния: 200.

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Создание назначения роли
Здесь описывается создание назначения роли в указанной области для определенного субъекта, назначающего роль.

Чтобы создать назначение роли, требуется доступ к операции `Microsoft.Authorization/roleAssignments/write`. Из встроенных ролей эту операцию могут выполнять только *владелец* и *администратор доступа пользователей*. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](role-assignments-portal.md).

### <a name="request"></a>Запрос
Используйте метод **PUT** со следующим универсальным кодом ресурса (URI).

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

1. Замените *{scope}* областью, для которой требуется создать назначения ролей. При создании назначения роли в родительской области все дочерние области также его наследуют. В следующих примерах показано, как указать область для различных уровней:

   * Subscription: /subscriptions/{ИД_подписки}  
   * Группа ресурсов: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1   
   * Ресурс: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Замените *{role-assignment-id}* новым идентификатором GUID, который станет GUID нового назначения роли.
3. Замените *{api-version}* значением 2015-07-01.

В тексте запроса введите значения в следующем формате:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Имя элемента | Обязательно | type | ОПИСАНИЕ |
| --- | --- | --- | --- |
| roleDefinitionId |Yes |Строка |Идентификатор роли. Он указывается в формате `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId |Yes |Строка |ObjectId субъекта Azure AD (пользователя, группы или субъекта-службы), которому назначается роль. |

### <a name="response"></a>Ответ
Код состояния: 201.

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Удаление назначения ролей
Здесь описывается удаление назначения роли в указанной области.

Чтобы удалить назначение роли, требуется доступ к операции `Microsoft.Authorization/roleAssignments/delete`. Из встроенных ролей эту операцию могут выполнять только *владелец* и *администратор доступа пользователей*. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](role-assignments-portal.md).

### <a name="request"></a>Запрос
Используйте метод **DELETE** со следующим универсальным кодом ресурса (URI).

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

1. Замените *{scope}* областью, для которой требуется создать назначения ролей. В следующих примерах показано, как указать область для различных уровней:

   * Subscription: /subscriptions/{ИД_подписки}  
   * Группа ресурсов: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1  
   * Ресурс: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Замените *{role-assignment-id}* идентификатором GUID для назначения роли.
3. Замените *{api-version}* значением 2015-07-01.

### <a name="response"></a>Ответ
Код состояния: 200.

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Вывод списка всех ролей
Здесь описывается вывод списка всех ролей, которые доступны для назначения в указанной области.

Для вывода списка ролей требуется доступ к операции `Microsoft.Authorization/roleDefinitions/read` в этой области. Доступ к этой операции предоставляется всем встроенным ролям. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](role-assignments-portal.md).

### <a name="request"></a>Запрос
Используйте метод **GET** со следующим универсальным кодом ресурса (URI).

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

1. Замените *{scope}* областью, для которой требуется вывести список ролей. В следующих примерах показано, как указать область для различных уровней:

   * Subscription: /subscriptions/{ИД_подписки}  
   * Группа ресурсов: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1  
   * Ресурс: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Замените *{api-version}* значением 2015-07-01.
3. Замените *{filter}* условием, по которому требуется отфильтровать список ролей.

   * Вывод списка ролей, доступных для назначения в указанной области и любой из ее дочерних областей: `atScopeAndBelow()`
   * Поиск с помощью точного отображаемого имени роли: `roleName%20eq%20'{role-display-name}'` Используйте точное отображаемое имя роли в формате URL-адреса. Например, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Ответ
Код состояния: 200.

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Получение сведений о роли
Здесь описывается получение сведений о роли, указанной с помощью идентификатора определения роли. Чтобы получить сведения о роли с помощью ее отображаемого имени, ознакомьтесь с разделом [Вывод списка всех ролей](role-assignments-rest.md#list-all-roles).

Чтобы получить сведения о роли, требуется доступ к операции `Microsoft.Authorization/roleDefinitions/read`. Доступ к этой операции предоставляется всем встроенным ролям. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](role-assignments-portal.md).

### <a name="request"></a>Запрос
Используйте метод **GET** со следующим универсальным кодом ресурса (URI).

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

1. Замените *{scope}* областью, для которой требуется вывести список назначений ролей. В следующих примерах показано, как указать область для различных уровней:

   * Subscription: /subscriptions/{ИД_подписки}  
   * Группа ресурсов: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1  
   * Ресурс: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Замените *{role-definition-id}* идентификатором GUID для определения роли.
3. Замените *{api-version}* значением 2015-07-01.

### <a name="response"></a>Ответ
Код состояния: 200.

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Создание настраиваемой роли
Здесь описывается создание настраиваемой роли.

Чтобы создать настраиваемую роль, требуется доступ к операции `Microsoft.Authorization/roleDefinitions/write` во всех областях `AssignableScopes`. Из встроенных ролей эту операцию могут выполнять только *владелец* и *администратор доступа пользователей*. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](role-assignments-portal.md).

### <a name="request"></a>Запрос
Используйте метод **PUT** со следующим универсальным кодом ресурса (URI).

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

1. Замените *{scope}* первой областью *AssignableScope* для настраиваемой роли. В следующих примерах показано, как указать область для различных уровней.

   * Subscription: /subscriptions/{ИД_подписки}  
   * Группа ресурсов: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1  
   * Ресурс: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Замените *{role-definition-id}* новым идентификатором GUID, который станет GUID новой настраиваемой роли.
3. Замените *{api-version}* значением 2015-07-01.

В тексте запроса введите значения в следующем формате:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Имя элемента | Обязательно | type | ОПИСАНИЕ |
| --- | --- | --- | --- |
| name |Yes |Строка |Идентификатор GUID настраиваемой роли. |
| properties.roleName |Yes |Строка |Отображаемое имя настраиваемой роли. Не может быть более 128 символов в длину. |
| properties.description |Нет  |Строка |Описание настраиваемой роли. Не может быть более 1024 символов в длину. |
| properties.type |Yes |Строка |Укажите значение CustomRole. |
| properties.permissions.actions |Yes |String[] |Массив строк действий, определяющих операции, к которым предоставляет доступ настраиваемая роль. |
| properties.permissions.notActions |Нет  |String[] |Массив строк действий, определяющих операции, исключаемые из списка операций, к которым предоставляет доступ настраиваемая роль. |
| properties.assignableScopes |Yes |String[] |Массив областей, в которых можно использовать настраиваемую роль. |

### <a name="response"></a>Ответ
Код состояния: 201.

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Обновление настраиваемой роли
Здесь описывается изменение настраиваемой роли.

Чтобы изменить настраиваемую роль, требуется доступ к операции `Microsoft.Authorization/roleDefinitions/write` во всех областях `AssignableScopes`. Из встроенных ролей эту операцию могут выполнять только *владелец* и *администратор доступа пользователей*. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](role-assignments-portal.md).

### <a name="request"></a>Запрос
Используйте метод **PUT** со следующим универсальным кодом ресурса (URI).

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

1. Замените *{scope}* первой областью *AssignableScope* для настраиваемой роли. В следующих примерах показано, как указать область для различных уровней:

   * Subscription: /subscriptions/{ИД_подписки}  
   * Группа ресурсов: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1  
   * Ресурс: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Замените *{role-definition-id}* идентификатором GUID настраиваемой роли.
3. Замените *{api-version}* значением 2015-07-01.

В тексте запроса введите значения в следующем формате:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Имя элемента | Обязательно | type | ОПИСАНИЕ |
| --- | --- | --- | --- |
| name |Yes |Строка |Идентификатор GUID настраиваемой роли. |
| properties.roleName |Yes |Строка |Отображаемое имя обновленной настраиваемой роли. |
| properties.description |Нет  |Строка |Описание обновленной настраиваемой роли. |
| properties.type |Yes |Строка |Укажите значение CustomRole. |
| properties.permissions.actions |Yes |String[] |Массив строк действий, определяющих операции, к которым предоставляет доступ обновленная настраиваемая роль. |
| properties.permissions.notActions |Нет  |String[] |Массив строк действий, определяющих операции, исключаемых из списка операций, к которым предоставляет доступ обновленная настраиваемая роль. |
| properties.assignableScopes |Yes |String[] |Массив областей, в которых можно использовать обновленную настраиваемую роль. |

### <a name="response"></a>Ответ
Код состояния: 201.

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Удаление настраиваемой роли
Здесь описывается удаление настраиваемой роли.

Чтобы удалить настраиваемую роль, требуется доступ к операции `Microsoft.Authorization/roleDefinitions/delete` во всех областях `AssignableScopes`. Из встроенных ролей эту операцию могут выполнять только *владелец* и *администратор доступа пользователей*. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](role-assignments-portal.md).

### <a name="request"></a>Запрос
Используйте метод **DELETE** со следующим универсальным кодом ресурса (URI).

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

1. Замените *{scope}* областью, в которой требуется удалить определение роли. В следующих примерах показано, как указать область для различных уровней:

   * Subscription: /subscriptions/{ИД_подписки}  
   * Группа ресурсов: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1  
   * Ресурс: /subscriptions/{ИД_подписки}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Замените *{role-definition-id}* идентификатором GUID для определения настраиваемой роли.
3. Замените *{api-version}* значением 2015-07-01.

### <a name="response"></a>Ответ
Код состояния: 200.

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
