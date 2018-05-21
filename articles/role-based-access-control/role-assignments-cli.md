---
title: Управление доступом на основе ролей (RBAC) с помощью интерфейса командной строки Azure | Документация Майкрософт
description: Узнайте, как управлять доступом на основе ролей (RBAC) с помощью интерфейса командной строки Azure, используя вывод списков ролей и действий ролей, а также назначения ролей для областей действия подписки и приложения.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/03/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 4a88f78f1f3fc1eaf8d6f9beae42119fe42f1807
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Управление доступом на основе ролей с помощью интерфейса командной строки Azure

> [!div class="op_single_selector"]
> * [PowerShell](role-assignments-powershell.md)
> * [интерфейс командной строки Azure](role-assignments-cli.md)
> * [REST API](role-assignments-rest.md)


С помощью механизма управления доступом на основе ролей (RBAC) можно определить доступ для пользователей, групп и субъектов-служб, назначая роли для определенной области. В этой статье описано, как управлять назначением ролей с помощью интерфейса командной строки Azure (CLI).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы использовать Azure CLI для управления назначением ролей, вам понадобится следующее:

* [Azure CLI 2.0](/cli/azure). Его можно использовать в браузере с [Azure Cloud Shell](../cloud-shell/overview.md) или [установить](/cli/azure/install-azure-cli) в macOS, Linux или Windows и запускать из командной строки.

## <a name="list-role-definitions"></a>Вывод списка определений роли

Чтобы получить список всех доступных определений роли, используйте команду [az role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

В следующем примере показан список имен доступных определений роли вместе с описанием.

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

В следующем примере перечислены все определения встроенной роли.

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role-definition"></a>Вывод списка разрешенных действий, относящихся к определению роли

Чтобы получить список разрешенных действий определения роли (свойства actions), используйте команду [az role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list --name <role_name>
```

В следующем примере показано определение роли *Участник*.

```azurecli
az role definition list --name "Contributor"
```

```Output
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

В следующем примере показаны *разрешенные* и *не разрешенные действия* (свойства notActions) роли *Участник*.

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

В следующем примере показаны разрешенные действия роли *Участник виртуальных машин*.

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-role-assignments"></a>Список назначений ролей

### <a name="list-role-assignments-for-a-user"></a>Список назначений ролей для пользователя

Чтобы вывести список назначений ролей для конкретного пользователя, используйте команду [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list).

```azurecli
az role assignment list --assignee <assignee>
```

По умолчанию отображаются только назначения в пределах подписки. Чтобы просмотреть назначения в пределах ресурсов или группы, используйте `--all`.

В следующем примере показаны назначения ролей, напрямую присвоенные пользователю *patlong@contoso.com*.

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Вывод списка назначений ролей для группы ресурсов

Чтобы вывести список назначений ролей, имеющихся в группе ресурсов, используйте команду [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list).

```azurecli
az role assignment list --resource-group <resource_group>
```

В следующем примере перечисляются назначения ролей для группы ресурсов *pharma-sales-projectforecast*.

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="create-role-assignments"></a>Создание назначений ролей

### <a name="create-a-role-assignment-for-a-user"></a>Создание назначения ролей для пользователя

Для создания назначения ролей для пользователя в пределах группы ресурсов используйте команду [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

В следующем примере роль *Участник виртуальной машины* назначается пользователю *patlong@contoso.com* в пределах группы ресурсов *pharma-sales-projectforecast*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>Создание назначения ролей для группы

Для создания назначения ролей для группы используйте команду [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

В следующем примере роль *Читатель* присваивается группе *Ann Mack Team* с идентификатором 22222222-2222-2222-2222-222222222222 в пределах подписки. Чтобы получить идентификатор группы, можно использовать команду [az ad group list](/cli/azure/ad/group#az-ad-group-list) или [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

В следующем примере роль *Участник виртуальной машины* присваивается группе *Ann Mack Team* с идентификатором 22222222-2222-2222-2222-222222222222 в пределах ресурса для виртуальной сети с именем *pharma-sales-project-network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Создание назначения ролей для приложения

Чтобы создать роль для приложения, используйте команду [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

В следующем примере роль *Участник виртуальной машины* присваивается приложению с идентификатором объекта 44444444-4444-4444-4444-444444444444 в пределах группы ресурсов *pharma-sales-projectforecast*. Чтобы получить идентификатор объекта приложения, можно использовать команду [az ad app list](/cli/azure/ad/app#az-ad-app-list) или [az ad app show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-a-role-assignment"></a>Удаление назначения ролей

Чтобы удалить назначение ролей, используйте команду [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete).

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

В следующем примере удаляется назначение роли *Участник виртуальной машины* пользователя *patlong@contoso.com* в группе ресурсов *pharma-sales-projectforecast*.

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

В следующем примере роль *Читатель* удаляется для группы *Ann Mack Team* с идентификатором 22222222-2222-2222-2222-222222222222 в пределах подписки. Чтобы получить идентификатор группы, можно использовать команду [az ad group list](/cli/azure/ad/group#az-ad-group-list) или [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>Пользовательские роли

### <a name="list-custom-roles"></a>Вывод списка настраиваемых ролей

Чтобы получить список ролей, доступных для назначения в области, используйте команду [az role definition list](/cli/azure/role/definition#az-role-definition-list).

В следующих примерах перечислены все пользовательские роли в текущей подписке.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>Создание настраиваемой роли

Чтобы создать пользовательскую роль, используйте команду [az role definition create](/cli/azure/role/definition#az-role-definition-create). Определением роли может быть описание JSON или путь к файлу, содержащему описание JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

В следующем примере показано создание пользовательской роли *Оператор виртуальной машины*. Пользовательская роль предоставляет доступ ко всем операциям чтения поставщиков ресурсов *Microsoft.Compute*, *Microsoft.Storage* и *Microsoft.Network*, а также доступ для запуска, перезапуска и мониторинга виртуальных машин. Эту настраиваемую роль можно использовать в двух подписках. В этом примере в качестве входных данных используется JSON-файл.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

### <a name="update-a-custom-role"></a>Обновление пользовательской роли

Чтобы обновить пользовательскую роль, сначала используйте команду [az role definition list](/cli/azure/role/definition#az-role-definition-list) для получения определения роли. Затем внесите необходимые изменения в определение роли. И наконец, сохраните обновленное определение роли с помощью команды [az role definition update](/cli/azure/role/definition#az-role-definition-update).

```azurecli
az role definition update --role-definition <role_definition>
```

В следующем примере операция *Microsoft.Insights/diagnosticSettings/* добавляется в раздел *Actions* пользовательской роли *Оператор виртуальной машины*.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

### <a name="delete-a-custom-role"></a>Удаление настраиваемой роли

Чтобы удалить пользовательскую роль, используйте команду [az role definition delete](/cli/azure/role/definition#az-role-definition-delete). Чтобы указать роль, которую необходимо удалить, используйте имя или идентификатор роли. Чтобы определить идентификатор роли, используйте команду [az role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

В следующем примере показано удаление пользовательской роли *Оператор виртуальной машины*.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

