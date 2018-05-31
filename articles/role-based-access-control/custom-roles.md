---
title: Создание настраиваемых ролей для Azure RBAC | Документация Майкрософт
description: Узнайте, как c помощью управления доступом на основе ролей Azure задавать пользовательские роли для более точного управления удостоверениями в подписке Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e2ea46ea1a6b5bd3f50d4d4c15492c16c5241c0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161062"
---
# <a name="create-custom-roles-in-azure"></a>Создание настраиваемых ролей в Azure

Если [встроенные роли](built-in-roles.md) не соответствуют определенным требованиям к доступу, можно создать собственные настраиваемые роли. Пользовательские роли, так же как и встроенные, можно назначать пользователям, группам и субъектам-службам в рамках подписки, группы ресурсов или области ресурсов. Настраиваемые роли хранятся в клиенте Azure Active Directory (Azure AD) и могут использоваться несколькими подписками. Настраиваемые роли можно создавать с помощью Azure PowerShell, Azure CLI и интерфейса REST API. В этой статье описывается пример создания настраиваемых ролей с помощью Azure CLI и PowerShell.

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>Создание настраиваемой роли с разрешением на открытие запросов в службу поддержки с помощью PowerShell

Чтобы создать пользовательскую роль, можно начать со встроенной роли. Измените ее, а затем создайте роль. В этом примере встроенная роль [Читатель](built-in-roles.md#reader) настроена для создания пользовательской роли с именем Reader support tickets access level (Уровень доступа к запросам в службу поддержки читателя). Она разрешает пользователю просматривать все в подписке, а также открывать запросы в службу поддержки.

> [!NOTE]
> Единственными двумя встроенными ролями, разрешающими пользователю открывать запросы в службу поддержки, являются [Владелец](built-in-roles.md#owner) и [Участник](built-in-roles.md#contributor). Чтобы разрешить пользователю открывать запросы в службу поддержки, ему необходимо назначить роль на уровне подписки, так как все эти запросы создаются на основе подписки Azure.

Используйте команду [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) в PowerShell, чтобы экспортировать роль [Читатель](built-in-roles.md#reader) в формате JSON.

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

Ниже приведены выходные данные JSON для роли [Читатель](built-in-roles.md#reader). Стандартная роль состоит из трех основных разделов: `Actions`, `NotActions` и `AssignableScopes`. В разделе `Actions` перечислены все разрешенные операции роли. Чтобы исключить операции из `Actions`, добавьте их в `NotActions`. Действующие разрешения вычисляется путем вычитания операций `NotActions` из операций `Actions`.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Теперь, чтобы создать пользовательскую роль, внесите правки в выходные данные JSON. В данном случае для создания запросов в службу поддержки необходимо добавить операцию `Microsoft.Support/*`. Каждая операция предоставляется поставщиком ресурсов. Чтобы получить список операций для поставщика ресурсов, можно использовать команду [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) или ознакомиться со статьей [Azure Resource Manager Resource Provider operations](resource-provider-operations.md) (Операции поставщика ресурсов Azure Resource Manager).

Роль обязательно должна содержать явные идентификаторы подписки, в которой она назначена. Идентификаторы подписки перечислены в разделе `AssignableScopes`. Если они не указаны, вы не сможете импортировать роль в подписку.

Наконец, необходимо задать свойство `IsCustom` для `true`, чтобы указать, что это настраиваемая роль.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Чтобы создать новую пользовательскую роль, используйте команду [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) и предоставьте обновленный JSON-файл определения роли.

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

После выполнения команды [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) новые пользовательские роли будут доступны на портале Azure, и их можно назначать пользователям.

![Снимок экрана пользовательской роли, импортированной на портал Azure](./media/custom-roles/18.png)

![Снимок экрана назначения импортированной пользовательской роли пользователю в том же каталоге](./media/custom-roles/19.png)

![Снимок экрана с разрешениями импортированной пользовательской роли](./media/custom-roles/20.png)

С помощью пользовательской роли пользователи могут создавать запросы в службу поддержки.

![Снимок экрана пользовательской роли, используемой для создания запросов в службу поддержки](./media/custom-roles/21.png)

Пользователи этой пользовательской роли не могут совершать других действий. Например, они не могут создавать виртуальные машины или группы ресурсов.

![Снимок экрана пользовательской роли, которая не имеет разрешений на создание виртуальных машин](./media/custom-roles/22.png)

![Снимок экрана пользовательской роли, которая не имеет разрешений на создание групп ресурсов](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>Создание настраиваемой роли с разрешением на открытие запросов в службу поддержки с помощью Azure CLI

Шаги по созданию пользовательской роли с помощью Azure CLI соответствуют шагам, используемым в PowerShell. Но выходные данные JSON отличаются.

Для этого примера можно начать со встроенной роли [Читатель](built-in-roles.md#reader). Используйте команду [az role definition list](/cli/azure/role/definition#az_role_definition_list), чтобы получить список действий роли [Читатель](built-in-roles.md#reader).

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Создайте файл JSON в следующем формате. Операция `Microsoft.Support/*` была добавлена в раздел `Actions`, чтобы этот пользователь мог открывать запросы в службу поддержки, продолжая оставаться читателем. Необходимо добавить идентификатор подписки, в которой эта роль будет назначена, в разделе `AssignableScopes`.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Чтобы создать новую пользовательскую роль, используйте команду [az role definition create](/cli/azure/role/definition#az_role_definition_create).

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

Новая пользовательская роль теперь доступна на портале Azure. Процесс использования такой же, как и в предыдущих разделах PowerShell.

![Снимок экрана портала Azure с пользовательской ролью, созданной с помощью CLI 1.0](./media/custom-roles/26.png)


## <a name="see-also"></a>См. также
- [Understand role definitions](role-definitions.md) (Определения ролей)
- [Manage role-based access control with Azure PowerShell](role-assignments-powershell.md) (Управление доступом на основе ролей с помощью Azure PowerShell)
- [Manage Role-Based Access Control with the Azure command-line interface](role-assignments-cli.md) (Управление доступом на основе ролей с помощью интерфейса командной строки Azure)
- [Manage Role-Based Access Control with the REST API](role-assignments-rest.md) (Управление доступом на основе ролей с помощью REST API)
