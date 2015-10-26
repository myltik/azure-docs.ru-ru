<properties
	pageTitle="Контроль доступа на основе ролей (RBAC) с помощью Azure PowerShell | Microsoft Azure"
	description="Контроль доступа на основе ролей (RBAC) с помощью Azure PowerShell включая список ролей, назначение ролей и удаление назначений ролей."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="10/12/2015"
	ms.author="inhenk"/>

# Контроль доступа на основе ролей (RBAC) с помощью Azure PowerShell
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## Вывод списка ролей RBAC
### Вывод списка всех доступных ролей
Для вывода списка доступных для назначения ролей RBAC и для просмотра операций, к которым они предоставляют доступ, воспользуйтесь командой:

		Get-AzureRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Вывод списка действий роли
Для вывода списка действий для определенной роли воспользуйтесь командой:

    Get-AzureRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Вывод списка доступа
### Вывод списка всех назначений ролей в выбранной подписке
Для вывода списка назначений доступа RBAC, действующих для указанной подписки, ресурса или группы ресурсов, воспользуйтесь командой:

    Get-AzureRoleAssignment

###	Вывод списка назначений ролей, действующих в группе ресурсов
Для вывода списка назначений для группы ресурсов воспользуйтесь командой:

    Get-AzureRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Вывод списка назначений ролей для пользователя, включая назначения для групп пользователя
Для вывода списка назначений доступа для указанного пользователя, а также для групп, членом которых он является, воспользуйтесь командой:

    Get-AzureRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Вывод списка назначений ролей классического администратора службы и соадминистратора
Для вывода списка назначений доступа для классического администратора подписки и соадминистраторов воспользуйтесь командой:

    Get-AzureRoleAssignment -IncludeClassicAdministrators

## Предоставление доступа
### Поиск идентификаторов объектов
Для использования следующих команд сначала необходимо найти идентификаторы объектов. Предполагается, что вы уже знаете идентификатор подписки, с которым вы работаете, в противном случае обратитесь к статье [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) в MSDN.

#### Поиск идентификатора объекта для группы Azure AD
Для поиска идентификатора объекта для группы Azure AD воспользуйтесь командой:

    Get-AzureADGroup -SearchString <group name in quotes>

#### Поиск идентификатора объекта для субъекта-службы Azure AD
Для получения идентификатора объекта для субъекта-службы Azure AD воспользуйтесь командой: Get-AzureADServicePrincipal — SearchString <service name in quotes>

### Назначение роли для группы в области действия подписки
Чтобы предоставить доступ к группе в области действия подписки, воспользуйтесь командой:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### Назначение роли для приложения в области действия подписки
Чтобы предоставить доступ к приложению в области действия подписки, воспользуйтесь командой:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Назначение роли пользователю в области действия группы ресурсов
Для предоставления доступа к пользователю в области действия группы ресурсов воспользуйтесь командой:

    New-AzureRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Назначение роли для группы в области действия ресурса
Для предоставления доступа к группе в области действия ресурса воспользуйтесь командой:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Запрет доступа
Чтобы удалить доступ для пользователей, групп и приложений, воспользуйтесь командой:

    Remove-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Разделы о RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->