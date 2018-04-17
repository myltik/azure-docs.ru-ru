---
title: Управление доступом на основе ролей (RBAC) с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как осуществлять управление доступом на основе ролей (RBAC) с помощью Azure PowerShell, включая вывод списка ролей, назначение ролей и удаление назначений ролей.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 5e7e01502a173cb93216c77dd43f61094fd88148
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2018
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Управление доступом на основе ролей с помощью Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Интерфейс командной строки Azure](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)

С помощью механизма управления доступом на основе ролей (RBAC) можно определить доступ для пользователей, групп и субъектов-служб, назначая роли для определенной области. В этой статье описывается, как управлять доступом с помощью Azure PowerShell.

Чтобы использовать PowerShell для управления RBAC, необходимы следующие компоненты:

* Azure PowerShell версии 0.8.8 или выше. Чтобы установить последнюю версию и связать ее со своей подпиской Azure, см. раздел об [установке и настройке Azure PowerShell](/powershell/azure/overview).
* Командлеты Azure Resource Manager. Установите [командлеты Azure Resource Manager](/powershell/azure/overview) в PowerShell.

## <a name="list-roles"></a>Вывод списка ролей
### <a name="list-all-available-roles"></a>Вывод списка всех доступных ролей
Для вывода списка доступных для назначения ролей RBAC и для просмотра операций, к которым они предоставляют доступ, воспользуйтесь командой `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC PowerShell — Get-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Вывод списка действий роли
Для вывода списка действий для определенной роли воспользуйтесь командой `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC PowerShell — Get-AzureRmRoleDefinition для конкретной роли — снимок экрана](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Увидеть, у кого есть доступ
Чтобы вывести список назначений доступа RBAC, используйте команду `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Вывод списка назначений ролей в конкретной области
Можно вывести список назначений доступа, действующих для указанной подписки, группы ресурсов или отдельного ресурса. Например, чтобы просмотреть все активные назначения для группы ресурсов, используйте команду `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell — Get-AzureRmRoleAssignment для группы ресурсов — снимок экрана](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>Вывод списка ролей, назначенных пользователю
Чтобы вывести список всех ролей, назначенных пользователю, включая роли, назначенные группам, в которые он входит, используйте команду `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell — Get-AzureRmRoleAssignment — для пользователя — снимок экрана](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Вывод списка назначений ролей классического администратора службы и соадминистратора
Для вывода списка назначений доступа для классического администратора подписки и соадминистраторов воспользуйтесь следующей командой:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Предоставление доступа
### <a name="search-for-object-ids"></a>Поиск идентификаторов объектов
Чтобы назначить роль, необходимо определить объект (пользователя, группу или приложение) и область.

Если вам неизвестен идентификатор подписки, его можно найти в колонке **Подписки** на портале Azure. Узнайте на сайте MSDN, как запросить идентификатор подписки с помощью [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) .

Для поиска идентификатора объекта для группы Azure AD воспользуйтесь следующей командой:

    Get-AzureRmADGroup -SearchString <group name in quotes>

Чтобы получить идентификатор объекта для субъекта-службы Azure AD или приложения, воспользуйтесь следующей командой:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Назначение роли для приложения в области действия подписки
Чтобы предоставить доступ к приложению в области действия подписки, воспользуйтесь следующей командой:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC PowerShell — New-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Назначение роли пользователю в области действия группы ресурсов
Для предоставления доступа пользователю в области действия группы ресурсов воспользуйтесь следующей командой:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell — New-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Назначение роли для группы в области действия ресурса
Для предоставления доступа группе в области действия ресурса воспользуйтесь следующей командой:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell — New-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Запрет доступа
Чтобы запретить доступ для пользователей, групп и приложений, воспользуйтесь следующей командой:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell — Remove-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Создание настраиваемой роли
Чтобы создать настраиваемую роль, используйте команду ```New-AzureRmRoleDefinition``` . Существует два способа структурирования роли: с помощью PSRoleDefinitionObject и с помощью шаблона JSON. 

## <a name="get-actions-for-a-resource-provider"></a>Получение действий для поставщика ресурсов
При создании пользовательских ролей с нуля важно знать все возможные операции поставщиков ресурсов.
Используйте команду ```Get-AzureRMProviderOperation``` для получения этих сведений.
Например, если вы хотите проверить все доступные операции для виртуальной машины, то используйте эту команду:

```
Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation , Description -AutoSize
```

### <a name="create-role-with-psroledefinitionobject"></a>Создание роли с помощью PSRoleDefinitionObject
При создании пользовательской роли с помощью PowerShell можно начать с нуля или использовать одну из [встроенных ролей](role-based-access-built-in-roles.md) в качестве отправной точки. В этом разделе приводится пример, в котором встроенная роль берется за основу, а затем настраивается с помощью дополнительных привилегий. Измените атрибуты и добавьте необходимые действия *Actions*, *notActions* и области *scopes*, а затем сохраните изменения как новую роль.

Следующий пример начинается с роли *Участник виртуальной машины*, с помощью которой создается настраиваемая роль *Оператор виртуальной машины*. Новая роль предоставляет доступ ко всем операциям чтения поставщиков ресурсов *Microsoft.Compute*, *Microsoft.Storage* и *Microsoft.Network*, а также доступ для запуска, перезапуска и мониторинга виртуальных машин. Настраиваемую роль можно использовать в двух подписках.

```
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
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell — Get-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

### <a name="create-role-with-json-template"></a>Создание роли с помощью шаблона JSON
Шаблон JSON может использоваться в качестве определения источника для пользовательской роли. В следующем примере создается пользовательская роль, которая разрешает доступ на чтение к хранилищу и вычислительным ресурсам, доступ для поддержки, и добавляет эту роль к двум подпискам. Создайте файл `C:\CustomRoles\customrole1.json` с приведенным ниже содержимым. При первичном создании роли идентификатору должно быть присвоено значение `null`, так как новый идентификатор создается автоматически. 

```
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
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```
Чтобы добавить роль к подпискам, выполните следующую команду PowerShell.
```
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>Изменение настраиваемой роли
Аналогично созданию пользовательской роли вы можете изменить существующую пользовательскую роль с помощью PSRoleDefinitionObject или шаблона JSON.

### <a name="modify-role-with-psroledefinitionobject"></a>Изменение роли с помощью PSRoleDefinitionObject
Чтобы изменить настраиваемую роль, сначала используйте команду `Get-AzureRmRoleDefinition` для получения определения роли. Затем внесите необходимые изменения в определение роли. Наконец, с помощью команды `Set-AzureRmRoleDefinition` сохраните измененное определение роли.

В следующем примере показано добавление операции `Microsoft.Insights/diagnosticSettings/*` к настраиваемой роли *Оператор виртуальной машины* .

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell — Set-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

В следующем примере показано добавление подписки Azure в назначаемые области настраиваемой роли *Оператор виртуальной машины* .

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell — Set-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

### <a name="modify-role-with-json-template"></a>Изменение роли с помощью шаблона JSON
С помощью предыдущего шаблона JSON можно легко изменить существующую пользовательскую роль, чтобы добавить или удалить действия. Обновите шаблон JSON и добавьте действие чтения для сетевых подключений, как показано в следующем примере. Определения, перечисленные в шаблоне, не применяются все вместе к существующему определению. Это означает, что роль отображается так, как указано в шаблоне. Кроме того, необходимо обновить поле идентификатора с помощью идентификатора роли. Если вы не уверены, каким является это значение, для получения этой информации можно использовать командлет `Get-AzureRmRoleDefinition`.

```
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
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

Чтобы обновить существующую роль, выполните следующую команду PowerShell.
```
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Удаление настраиваемой роли
Чтобы удалить настраиваемую роль, используйте команду `Remove-AzureRmRoleDefinition` .

В следующем примере показано удаление настраиваемой роли *Оператор виртуальной машины* .

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC PowerShell — Remove-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Вывод списка настраиваемых ролей
Чтобы получить список ролей, доступных для назначения в области, используйте команду `Get-AzureRmRoleDefinition` .

В следующем примере перечисляются все роли, доступные для назначения в выбранной подписке.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC PowerShell — Get-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

В следующем примере настраиваемая роль *Оператор виртуальной машины* не доступна в подписке *Production4*, так как эта подписка не входит в **AssignableScopes** роли.

![RBAC PowerShell — Get-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>См. также
* [Использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

