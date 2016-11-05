---
title: Управление доступом на основе ролей (RBAC) с помощью интерфейса командной строки Azure | Microsoft Docs
description: Узнайте, как управлять доступом на основе ролей (RBAC) с помощью интерфейса командной строки Azure, используя вывод списков ролей и действий ролей, а также назначения ролей для областей действия подписки и приложения.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/22/2016
ms.author: kgremban

---
# Управление доступом на основе ролей с помощью интерфейса командной строки Azure
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Интерфейс командной строки Azure](role-based-access-control-manage-access-azure-cli.md)
> * [ИНТЕРФЕЙС REST API](role-based-access-control-manage-access-rest.md)
> 
> 

Функция управления доступом на основе ролей (RBAC) на портале Azure и в API Azure Resource Manager позволяет очень точно управлять доступом к подписке и ресурсам. С ее помощью вы можете предоставлять доступ пользователям, группам и субъектам-службам Active Directory, назначая им роли с определенной областью.

Чтобы использовать интерфейс командной строки (CLI) Azure для управления RBAC, вам понадобится:

* Интерфейс командной строки Azure версии 0.8.8 или более поздней. Чтобы установить последнюю версию и связать ее со своей подпиской Azure, см. статью [Установка и настройка интерфейса командной строки Azure](../xplat-cli-install.md);
* Azure Resource Manager в Azure CLI. Дополнительную информацию см. в статье [Использование интерфейса командной строки Azure с диспетчером ресурсов](../xplat-cli-azure-resource-manager.md).

## Вывод списка ролей
### Вывод списка всех доступных ролей
Для вывода списка всех доступных ролей используйте следующую команду:

        azure role list

В следующем примере показан список *всех доступных ролей*.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![Снимок экрана: командная строка RBAC Azure — список ролей Azure](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### Вывод списка действий роли
Для вывода списка действий роли используйте следующую команду:

    azure role show "<role name>"

В следующем примере показаны действия ролей *Участник* и *Участник виртуальной машины*.

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![Снимок экрана: командная строка RBAC Azure — отображение ролей Azure](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

## Вывод списка доступа
### Вывод списка назначений ролей, действующих в группе ресурсов
Чтобы вывести список назначений ролей в группе ресурсов, используйте следующую команду.

    azure role assignment list --resource-group <resource group name>

В следующем примере показаны назначения ролей в группе *pharma-sales-projecforcast*.

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

.![Снимок экрана: командная строка RBAC Azure — список назначений ролей Azure по группам](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### Список назначений ролей для пользователя
Чтобы получить список назначений ролей для конкретного пользователя и назначений для групп пользователя, используйте следующую команду:

    azure role assignment list --signInName <user email>

Также вы можете просмотреть назначения ролей, которые наследуются от групп, изменив эту команду:

    azure role assignment list --expandPrincipalGroups --signInName <user email>

В следующем примере показаны назначения ролей, предоставленные пользователю *sameert@aaddemo.com*. Сюда входят роли, непосредственно назначенные пользователю, а также роли, унаследованные от групп.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

.![Снимок экрана: командная строка RBAC Azure — список назначений ролей Azure по пользователям](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

## Предоставление доступа
После определения роли, которую вы хотите назначить, воспользуйтесь следующей командой для предоставления доступа:

    azure role assignment create

### Назначение роли для группы в области действия подписки
Для назначения роли для группы в области действия подписки воспользуйтесь следующей командой:

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

В следующем примере роль *Читатель* назначается *Команде Кристины Кох* в области действия *подписки*.

.![Снимок экрана: командная строка RBAC Azure — создание назначений ролей Azure по группам](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### Назначение роли для приложения в области действия подписки
Для назначения роли для приложения в области действия подписки воспользуйтесь следующей командой:

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

В следующем примере роль *Участник* предоставляется приложению *Azure AD* в выбранной подписке.

 .![Командная строка RBAC Azure — создание назначений ролей Azure по приложениям](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### Назначение роли пользователю в области действия группы ресурсов
Для назначения роли пользователю в области действия группы ресурсов воспользуйтесь следующей командой:

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

В следующем примере роль *Участник виртуальной машины* предоставляется пользователю *samert@aaddemo.com* в области действия группы ресурсов *Pharma-Sales-ProjectForcast*.

.![Снимок экрана: командная строка RBAC Azure — создание назначений ролей Azure по пользователям](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### Назначение роли для группы в области действия ресурса
Для назначения роли для группы в области действия ресурса воспользуйтесь следующей командой:

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

В следующем примере роль *Участник виртуальной машины* предоставляется группе *Azure AD* в *подсети*.

![Снимок экрана: командная строка RBAC Azure — создание назначений ролей Azure по группам](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

## Запрет доступа
Чтобы удалить назначение роли, воспользуйтесь командой:

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

В следующем примере удаляется назначение роли *Участник виртуальной машины* пользователя *sammert@aaddemo.com* в группе ресурсов *Pharma-Sales-ProjectForcast*. После этого выполняется удаление назначения ролей из группы в подписке.

.![Снимок экрана: командная строка RBAC Azure — удаление назначений ролей Azure](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## Создание настраиваемой роли
Чтобы создать настраиваемую роль, используйте следующую команду:

    azure role create --inputfile <file path>

В следующем примере показано создание настраиваемой роли *Оператор виртуальной машины*. Настраиваемая роль предоставляет доступ ко всем операциям чтения поставщиков ресурсов *Microsoft.Compute*, *Microsoft.Storage* и *Microsoft.Network*, а также доступ для запуска, перезапуска и мониторинга виртуальных машин. Настраиваемую роль можно использовать в двух подписках. В этом примере в качестве входных данных используется JSON-файл.

![Снимок экрана: JSON — определение пользовательской роли](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Снимок экрана: командная строка RBAC Azure — создание ролей Azure](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## Изменение настраиваемой роли
Чтобы изменить настраиваемую роль, сначала используйте команду `azure role show` для получения определения роли. Затем внесите необходимые изменения в файл определения роли. Наконец, с помощью `azure role set` сохраните измененное определение роли.

    azure role set --inputfile <file path>

В следующем примере операция *Microsoft.Insights/diagnosticSettings/* добавляется в раздел **Actions**, а подписка Azure — в раздел **AssignableScopes** настраиваемой роли "Оператор виртуальной машины".

![Снимок экрана: JSON — изменение определения пользовательской роли](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

.![Снимок экрана: командная строка RBAC Azure — настройка ролей Azure](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## Удаление настраиваемой роли
Чтобы удалить настраиваемую роль, сначала используйте команду `azure role show` для определения **идентификатора** роли. Затем с помощью команды `azure role delete` удалите роль, указав ее **идентификатор**.

В следующем примере показано удаление настраиваемой роли *Оператор виртуальной машины*.

![Снимок экрана: командная строка RBAC Azure — удаление ролей Azure](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## Вывод списка настраиваемых ролей
Чтобы получить список ролей, доступных для назначения в области, используйте команду `azure role list`.

В следующем примере перечисляются все роли, доступные для назначения в выбранной подписке.

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![Снимок экрана: командная строка RBAC Azure — список ролей Azure](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

В следующем примере настраиваемая роль *Оператор виртуальной машины* не доступна в подписке *Production4*, так как эта подписка не входит в **AssignableScopes** роли.

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

.![Снимок экрана: командная строка RBAC Azure — список пользовательских ролей Azure](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)

## Разделы о RBAC
[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0810_2016-->