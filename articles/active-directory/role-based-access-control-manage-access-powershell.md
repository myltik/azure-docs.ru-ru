<properties
	pageTitle="Контроль доступа на основе ролей (RBAC) с помощью Azure PowerShell | Microsoft Azure"
	description="Контроль доступа на основе ролей (RBAC) с помощью Azure PowerShell включая вывод списка ролей, назначение ролей и удаление назначений ролей."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="07/22/2016"
	ms.author="kgremban"/>

# Управление доступом на основе ролей с помощью Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Интерфейс командной строки Azure](role-based-access-control-manage-access-azure-cli.md)
- [ИНТЕРФЕЙС REST API](role-based-access-control-manage-access-rest.md)


Функция управления доступом на основе ролей (RBAC) на портале Azure и в API управления ресурсами Azure позволяет управлять доступом к подписке с высокой точностью. С ее помощью вы можете предоставлять доступ пользователям, группам и субъектам-службам Active Directory, назначая им роли с определенной областью.

Чтобы использовать PowerShell для управления RBAC, вам понадобится:

- Azure PowerShell версии 0.8.8 или выше. Чтобы установить последнюю версию и связать ее со своей подпиской Azure, см. статью [Как установить и настроить Azure PowerShell](../powershell-install-configure.md);

- Командлеты Azure Resource Manager. Установите [командлеты Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx) в PowerShell.

## Вывод списка ролей

### Вывод списка всех доступных ролей
Для вывода списка доступных для назначения ролей RBAC и для просмотра операций, к которым они предоставляют доступ, воспользуйтесь командой `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC PowerShell — Get-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Вывод списка действий роли
Для вывода списка действий для определенной роли воспользуйтесь командой `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC PowerShell — Get-AzureRmRoleDefinition для конкретной роли — снимок экрана](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Увидеть, у кого есть доступ
Чтобы вывести список назначений доступа RBAC, используйте команду `Get-AzureRmRoleAssignment`.

###	Вывод списка назначений ролей в конкретной области
Можно вывести список назначений доступа, действующих для указанной подписки, группы ресурсов или отдельного ресурса. Например, чтобы просмотреть все активные назначения для группы ресурсов, используйте следующую команду `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell — Get-AzureRmRoleAssignment для группы ресурсов — снимок экрана](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Вывод списка ролей, назначенных пользователю
Чтобы вывести список всех ролей, назначенных пользователю, включая роли, назначенные группам, в которые он входит, используйте следующую команду `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell — Get-AzureRmRoleAssignment — для пользователя — снимок экрана](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Вывод списка назначений ролей классического администратора службы и соадминистратора
Для вывода списка назначений доступа для классического администратора подписки и соадминистраторов воспользуйтесь командой:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## Предоставление доступа
### Поиск идентификаторов объектов
Чтобы назначить роль, необходимо определить объект (пользователя, группу или приложение) и область.

Если вам неизвестен идентификатор подписки, его можно найти в колонке **Подписки** на портале Azure. Или узнайте на сайте MSDN, как запросить его с помощью [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx).

Для поиска идентификатора объекта для группы Azure AD воспользуйтесь командой:

    Get-AzureRmADGroup -SearchString <group name in quotes>

Чтобы получить идентификатор объекта для субъекта-службы Azure AD или приложения, используйте следующую команду.

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### Назначение роли для приложения в области действия подписки
Чтобы предоставить доступ к приложению в области действия подписки, воспользуйтесь командой:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC PowerShell — New-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Назначение роли пользователю в области действия группы ресурсов
Для предоставления доступа к пользователю в области действия группы ресурсов воспользуйтесь командой:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell — New-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Назначение роли для группы в области действия ресурса
Для предоставления доступа к группе в области действия ресурса воспользуйтесь командой:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell — New-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Запрет доступа
Чтобы удалить доступ для пользователей, групп и приложений, воспользуйтесь командой:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell — Remove-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Создание настраиваемой роли
Чтобы создать настраиваемую роль, используйте команду `New-AzureRmRoleDefinition`.

При создании настраиваемой роли в PowerShell необходимо начать с одной из [встроенных ролей](role-based-access-built-in-roles.md). Измените атрибуты и добавьте необходимые действия Action, notAction и области, а затем сохраните изменения как новую роль.

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

## Изменение настраиваемой роли
Чтобы изменить настраиваемую роль, используйте команду `Get-AzureRmRoleDefinition` для получения определения роли. Затем внесите необходимые изменения в определение роли. Наконец, с помощью команды `Set-AzureRmRoleDefinition` сохраните измененное определение роли.

В следующем примере показано добавление операции `Microsoft.Insights/diagnosticSettings/*` к настраиваемой роли *Оператор виртуальной машины*.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell — Set-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

В следующем примере показано добавление подписки Azure в назначаемые области настраиваемой роли "Оператор виртуальной машины".

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2"
Set-AzureRmRoleDefinition -Role $role)
```

![RBAC PowerShell — Set-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## Удаление настраиваемой роли

Чтобы удалить настраиваемую роль, используйте команду `Remove-AzureRmRoleDefinition`.

В следующем примере показано удаление настраиваемой роли *Оператор виртуальной машины*.

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC PowerShell — Remove-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## Вывод списка настраиваемых ролей
Чтобы получить список ролей, доступных для назначения в области, используйте команду `Get-AzureRmRoleDefinition`.

В следующем примере перечисляются все роли, доступные для назначения в выбранной подписке.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC PowerShell — Get-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

В следующем примере настраиваемая роль *Оператор виртуальной машины* не доступна в подписке *Production4*, так как эта подписка не входит в **AssignableScopes** роли.

![RBAC PowerShell — Get-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## См. также
- [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md) 
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0727_2016-->
<!---Added breakline line 212 to 213-->