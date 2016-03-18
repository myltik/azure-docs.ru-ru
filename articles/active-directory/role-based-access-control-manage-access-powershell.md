<properties
	pageTitle="Контроль доступа на основе ролей (RBAC) с помощью Azure PowerShell | Microsoft Azure"
	description="Контроль доступа на основе ролей (RBAC) с помощью Azure PowerShell включая список ролей, назначение ролей и удаление назначений ролей."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="02/29/2016"
	ms.author="kgremban"/>

# Управление доступом на основе ролей с помощью Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Интерфейс командной строки Azure](role-based-access-control-manage-access-azure-cli.md)
- [ИНТЕРФЕЙС REST API](role-based-access-control-manage-access-rest.md)


## Список ролей для управления доступом на основе ролей (RBAC)

>[AZURE.IMPORTANT] Перед использованием командлетов в этой статье необходимо [установить командлеты Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx) в PowerShell.

### Вывод списка всех доступных ролей
Для вывода списка доступных для назначения ролей RBAC и для просмотра операций, к которым они предоставляют доступ, воспользуйтесь командой:

		Get-AzureRmRoleDefinition

![RBAC PowerShell — Get-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Вывод списка действий роли
Для вывода списка действий для определенной роли воспользуйтесь командой:

    Get-AzureRmRoleDefinition <role name>

![RBAC PowerShell — Get-AzureRmRoleDefinition для конкретной роли — снимок экрана](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Вывод списка доступа
### Вывод списка всех назначений ролей в выбранной подписке
Для вывода списка назначений доступа RBAC, действующих для указанной подписки, ресурса или группы ресурсов, воспользуйтесь командой:

    Get-AzureRmRoleAssignment

###	Вывод списка назначений ролей, действующих в группе ресурсов
Для вывода списка назначений для группы ресурсов воспользуйтесь командой:

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![RBAC PowerShell — Get-AzureRmRoleAssignment для группы ресурсов — снимок экрана](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Вывод списка назначений ролей для пользователя, включая назначения для групп пользователя
Для вывода списка назначений доступа для указанного пользователя, а также для групп, членом которых он является, воспользуйтесь командой:

    Get-AzureRmRoleAssignment -ExpandPrincipalGroups

![RBAC PowerShell — Get-AzureRmRoleAssignment — для пользователя — снимок экрана](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Вывод списка назначений ролей классического администратора службы и соадминистратора
Для вывода списка назначений доступа для классического администратора подписки и соадминистраторов воспользуйтесь командой:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## Предоставление доступа
### Поиск идентификаторов объектов
Для использования следующих команд сначала необходимо найти идентификаторы объектов. Предполагается, что вы уже знаете идентификатор подписки, с которым вы работаете. Если не знаете, см. статью [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) в MSDN.

#### Поиск идентификатора объекта для группы Azure AD
Для поиска идентификатора объекта для группы Azure AD воспользуйтесь командой:

    Get-AzureRmADGroup -SearchString <group name in quotes>

#### Поиск идентификатора объекта для субъекта-службы Azure AD
Чтобы получить идентификатор объекта для субъекта-службы Azure AD, используйте эту команду:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### Назначение роли для группы в области действия подписки
Чтобы предоставить доступ к группе в области действия подписки, воспользуйтесь командой:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![RBAC PowerShell — New-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### Назначение роли для приложения в области действия подписки
Чтобы предоставить доступ к приложению в области действия подписки, воспользуйтесь командой:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![RBAC PowerShell — New-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Назначение роли пользователю в области действия группы ресурсов
Для предоставления доступа к пользователю в области действия группы ресурсов воспользуйтесь командой:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell — New-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Назначение роли для группы в области действия ресурса
Для предоставления доступа к группе в области действия ресурса воспользуйтесь командой:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell — New-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Запрет доступа
Чтобы удалить доступ для пользователей, групп и приложений, воспользуйтесь командой:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![RBAC PowerShell — Remove-AzureRmRoleAssignment — снимок экрана](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Создание настраиваемой роли
Чтобы создать настраиваемую роль, используйте команду `New-AzureRmRoleDefinition`.

В следующем примере создается настраиваемая роль с именем *Оператор виртуальной машины*, которая предоставляет доступ ко всем операциям чтения поставщиков ресурсов *Microsoft.Compute*, *Microsoft.Storage* и *Microsoft.Network*, а также доступ для запуска, перезапуска и мониторинга виртуальных машин. Настраиваемую роль можно использовать в двух подписках.

![RBAC PowerShell — Get-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## Изменение настраиваемой роли
Чтобы изменить настраиваемую роль, используйте команду `Get-AzureRmRoleDefinition` для получения определения роли. Затем внесите необходимые изменения в определение роли. Наконец, с помощью команды `Set-AzureRmRoleDefinition` сохраните измененное определение роли.

В следующем примере показано добавление операции `Microsoft.Insights/diagnosticSettings/*` к настраиваемой роли *Оператор виртуальной машины*.

![RBAC PowerShell — Set-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

В следующем примере показано добавление подписки Azure в назначаемые области настраиваемой роли "Оператор виртуальной машины".

![RBAC PowerShell — Set-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## Удаление настраиваемой роли

Чтобы удалить настраиваемую роль, используйте команду `Remove-AzureRmRoleDefinition`.

В следующем примере показано удаление настраиваемой роли *Оператор виртуальной машины*.

![RBAC PowerShell — Remove-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## Вывод списка настраиваемых ролей
Чтобы получить список ролей, доступных для назначения в области, используйте команду `Get-AzureRmRoleDefinition`.

В следующем примере перечисляются все роли, доступные для назначения в выбранной подписке.

![RBAC PowerShell — Get-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

В следующем примере настраиваемая роль *Оператор виртуальной машины* не доступна в подписке *Production4*, так как эта подписка не входит в **AssignableScopes** роли.

![RBAC PowerShell — Get-AzureRmRoleDefinition — снимок экрана](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## Разделы о RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0302_2016-->