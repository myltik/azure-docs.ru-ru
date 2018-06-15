---
title: Управление доступом на основе ролей (RBAC) с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как осуществлять управление доступом на основе ролей (RBAC) с помощью Azure PowerShell, включая вывод списка ролей, назначение ролей и удаление назначений ролей.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 00646187da1f93c01c3a57b50905239afd5e2bc8
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266804"
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Управление доступом на основе ролей с помощью Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](role-assignments-powershell.md)
> * [интерфейс командной строки Azure](role-assignments-cli.md)
> * [REST API](role-assignments-rest.md)

С помощью механизма управления доступом на основе ролей (RBAC) можно определить доступ для пользователей, групп и субъектов-служб, назначая роли для определенной области. В этой статье описывается, как управлять доступом с помощью Azure PowerShell.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы использовать PowerShell для управления RBAC, необходим один из следующих компонентов:

* [PowerShell в Cloud Shell в Azure](/azure/cloud-shell/overview).
* [Azure PowerShell 5.1.0 или более поздней версии](/powershell/azure/install-azurerm-ps).

## <a name="list-roles"></a>Вывод списка ролей

### <a name="list-all-available-roles"></a>Вывод списка всех доступных ролей

Чтобы вывести список доступных для назначения ролей RBAC и просмотреть операции, к которым они предоставляют доступ, используется команда [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>Указание определенной роли

Чтобы указать определенную роль, используется команда [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition <role name>
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
AssignableScopes : {/}
```

### <a name="list-a-specific-role-in-json-format"></a>Указание роли в формате JSON

Чтобы указать роль в формате JSON, используется команда [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition <role name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor" | ConvertTo-Json

{
    "Name":  "Contributor",
    "Id":  "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "IsCustom":  false,
    "Description":  "Lets you manage everything except access to resources.",
    "Actions":  [
                    "*"
                ],
    "NotActions":  [
                       "Microsoft.Authorization/*/Delete",
                       "Microsoft.Authorization/*/Write",
                       "Microsoft.Authorization/elevateAccess/Action"
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

### <a name="list-actions-of-a-role"></a>Вывод списка действий роли

Чтобы получить список действий, доступных для определенной роли, используется команда [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition <role name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action}
```

```azurepowershell
(Get-AzureRmRoleDefinition <role name>).Actions
```

```Example
PS C:\> (Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="see-who-has-access"></a>Увидеть, у кого есть доступ

Чтобы получить список назначений доступа RBAC, используется команда [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

### <a name="list-role-assignments-at-a-specific-scope"></a>Вывод списка назначений ролей в конкретной области

Будут представлены все назначенные роли для указанной подписки, группы ресурсов или ресурса. Например, чтобы просмотреть все активные назначения для группы ресурсов, используется команда [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzureRmRoleAssignment -ResourceGroupName pharma-sales-projectforecast | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

### <a name="list-roles-assigned-to-a-user"></a>Вывод списка ролей, назначенных пользователю

Чтобы получить список всех ролей, назначенных соответствующему пользователю, используется команда [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment -SignInName <user email>
```

```Example
PS C:\> Get-AzureRmRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

Чтобы получить список всех ролей, назначенных указанному пользователю и группам, к которым он принадлежит, используется команда [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment -SignInName <user email> -ExpandPrincipalGroups
```

```Example
Get-AzureRmRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Вывод списка назначений ролей классического администратора службы и соадминистратора

Чтобы получить список назначений прав доступа для администратора и соадминистраторов классической подписки, используется команда [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment):

```azurepowershell
Get-AzureRmRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>Предоставление доступа

### <a name="search-for-object-ids"></a>Поиск идентификаторов объектов

Чтобы назначить роль, необходимо определить объект (пользователя, группу или приложение) и область.

Если вы не знаете идентификатор подписки, его можно найти в колонке **Подписки** на портале Azure или воспользоваться командой [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

Чтобы получить идентификатор объекта для группы Azure AD, используется команда [Get-AzureRmADGroup](/powershell/module/azurerm.resources/get-azurermadgroup).

```azurepowershell
Get-AzureRmADGroup -SearchString <group name in quotes>
```

Чтобы получить идентификатор объекта для субъекта-службы или приложения Azure AD, используется команда [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal).

```azurepowershell
Get-AzureRmADServicePrincipal -SearchString <service name in quotes>
```

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Назначение роли для приложения в области действия подписки

Чтобы предоставить доступ к приложению в масштабе всей подписки, используется команда [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment).

```azurepowershell
New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>
```

```Example
PS C:\> New-AzureRmRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Назначение роли пользователю в области действия группы ресурсов

Чтобы предоставить доступ к приложению в масштабе группы ресурсов, используется команда [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment).

```azurepowershell
New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>
```

```Example
PS C:\> New-AzureRmRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Назначение роли для группы в области действия ресурса

Чтобы предоставить доступ к группе в масштабе ресурсов, используется команда [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment).

```azurepowershell
New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzureRmADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzureRmRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

## <a name="remove-access"></a>Запрет доступа

Чтобы лишить прав доступа пользователей, группы и приложения, используется команда [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment).

```azurepowershell
Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>
```

```Example
PS C:\> Remove-AzureRmRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast
```

## <a name="list-custom-roles"></a>Вывод списка настраиваемых ролей

Чтобы получить список ролей, доступных для назначения в области, используется команда [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

В следующем примере перечисляются все роли, доступные для назначения в выбранной подписке.

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

В следующем примере перечислены только пользовательские роли, доступные для назначения в выбранной подписке.

```azurepowershell
Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Если выбранная подписка не указана в `AssignableScopes` роли, пользовательская роль не будет отображаться.

## <a name="create-a-custom-role"></a>Создание настраиваемой роли

Чтобы создать пользовательскую роль, используется команда [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition). Существует два способа структурирования роли: с помощью объекта `PSRoleDefinition` или шаблона JSON. 

### <a name="get-operations-for-a-resource-provider"></a>Получение перечня операций, доступных поставщику ресурсов

При создании пользовательских ролей важно знать все возможные операции, которые могут осуществлять поставщики ресурсов.
Вы можете просмотреть список [операций, доступных поставщику ресурсов](resource-provider-operations.md), или воспользоваться командой [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) для получения этой информации.
Например, если нужно проверить все доступные операции для виртуальных машин, используйте следующую команду.

```azurepowershell
Get-AzureRMProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-role-with-psroledefinition-object"></a>Создание роли с помощью объекта PSRoleDefinition

При использовании PowerShell для создания пользовательской роли можно использовать одну из [встроенных ролей](built-in-roles.md) в качестве отправной точки, а можно начать и с нуля. Первый пример в этом разделе начинается со встроенной роли, и затем производится ее настройка для добавления дополнительных разрешений. Измените атрибуты для добавления нужных параметров (`Actions`, `NotActions` или `AssignableScopes`), а затем сохраните изменения в качестве новой роли.

В следующем примере сначала используется встроенная роль [Участник виртуальной машины](built-in-roles.md#virtual-machine-contributor) для создания пользовательской роли под названием *Оператор виртуальной машины*. Новая роль предоставляет доступ ко всем операциям чтения поставщиков ресурсов *Microsoft.Compute*, *Microsoft.Storage* и *Microsoft.Network*, а также доступ для запуска, перезапуска и мониторинга виртуальных машин. Настраиваемую роль можно использовать в двух подписках.

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzureRmRoleDefinition -Role $role
```

В следующем примере показан другой способ создания пользовательской роли *Оператор виртуальной машины*. Операция начинается с создания объекта PSRoleDefinition. Действия операции указаны в переменной `perms`, для которой задано свойство `Actions`. Свойство `NotActions` устанавливается путем считывания `NotActions` из встроенной роли [Оператор виртуальной машины](built-in-roles.md#virtual-machine-contributor). Так как в роли [Оператор виртуальной машины](built-in-roles.md#virtual-machine-contributor) нет `NotActions`, эта строка необязательна, но в ней показано, как можно получить сведения из другой роли.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read','Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzureRmRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzureRmRoleDefinition -Role $role
```

### <a name="create-role-with-json-template"></a>Создание роли с помощью шаблона JSON

Шаблон JSON может использоваться в качестве определения источника для пользовательской роли. В следующем примере создается пользовательская роль, которая разрешает доступ на чтение к хранилищу и вычислительным ресурсам, доступ для поддержки, и добавляет эту роль к двум подпискам. Создайте файл `C:\CustomRoles\customrole1.json` с приведенным ниже содержимым. При первичном создании роли идентификатору должно быть присвоено значение `null`, так как новый идентификатор создается автоматически. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Чтобы добавить роль к подпискам, выполните следующую команду PowerShell.

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>Изменение настраиваемой роли

Аналогично созданию пользовательской роли можно изменить имеющуюся пользовательскую роль с помощью либо объекта `PSRoleDefinition`, либо шаблона JSON.

### <a name="modify-role-with-psroledefinition-object"></a>Изменение роли с помощью объекта PSRoleDefinition

Чтобы изменить пользовательскую роль, сначала используется команда [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) для получения определения роли. Затем внесите необходимые изменения в определение роли. Наконец, с помощью команды [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) сохраняется измененное определение роли.

В следующем примере показано добавление операции `Microsoft.Insights/diagnosticSettings/*` к настраиваемой роли *Оператор виртуальной машины* .

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

В следующем примере показано добавление подписки Azure в назначаемые области настраиваемой роли *Оператор виртуальной машины* .

```azurepowershell
Get-AzureRmSubscription -SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzureRmSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

### <a name="modify-role-with-json-template"></a>Изменение роли с помощью шаблона JSON

С помощью предыдущего шаблона JSON можно легко изменить существующую пользовательскую роль, чтобы добавить или удалить действия. Обновите шаблон JSON и добавьте действие чтения для сетевых подключений, как показано в следующем примере. Определения, перечисленные в шаблоне, не применяются все вместе к существующему определению. Это означает, что роль отображается так, как указано в шаблоне. Кроме того, необходимо обновить поле идентификатора с помощью идентификатора роли. Если вы не уверены, каким является это значение, для получения этой информации можно использовать командлет [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Чтобы обновить существующую роль, выполните следующую команду PowerShell.

```azurepowershell
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Удаление настраиваемой роли

Чтобы удалить пользовательскую роль, используется команда [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition).

В следующем примере показано удаление настраиваемой роли *Оператор виртуальной машины* .

```azurepowershell
Get-AzureRmRoleDefinition "Virtual Machine Operator"
Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="see-also"></a>См. также

* [Использование Azure PowerShell с диспетчером ресурсов Azure](../azure-resource-manager/powershell-azure-resource-manager.md)

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
