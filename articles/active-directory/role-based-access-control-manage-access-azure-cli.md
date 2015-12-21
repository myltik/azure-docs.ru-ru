<properties
	pageTitle="Контроль доступа на основе ролей (RBAC) с помощью интерфейса командной строки Azure | Microsoft Azure"
	description="Узнайте, как управлять доступом на основе ролей (RBAC) с интерфейсом командной строки Azure с помощью вывода списков ролей и действий ролей и назначения ролей для областей действия подписки и приложения."
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
	ms.date="12/04/2015"
	ms.author="inhenk"/>

# Контроль доступа на основе ролей (RBAC) с помощью интерфейса командной строки Azure (Azure CLI)
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## Вывод списка ролей RBAC
###	Вывод списка всех доступных ролей
Для вывода списка всех доступных ролей используйте команду:

		azure role list

В следующем примере показан список *всех доступных ролей*.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	Вывод списка действий роли
Для вывода списка действий роли используйте команду:

    azure role show <role in quotes>

В следующем примере показаны действия ролей *Участник* и *Участник виртуальной машины*.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	Вывод списка доступа
###	Вывод списка назначений ролей, действующих в группе ресурсов
Для вывода списка назначений ролей, действующих в группе ресурсов, используйте команду:

    azure role assignment list --resource-group <resource group name>

В следующем примере показаны назначения ролей, действующие для группы *pharma-sales-projecforcast*.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	Вывод списка назначений ролей для пользователя, включая назначения для групп пользователя

В следующем примере показаны назначения ролей, действующие для группы *pharma-sales-projecforcast*.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	Предоставление доступа
После определения роли, которую вы хотите назначить, воспользуйтесь следующей командой для предоставления доступа:

    azure role assignment create

###	Назначение роли для группы в области действия подписки
Для назначения роли для группы в области действия подписки воспользуйтесь командой:

   azure role assignment create --objId <идентификатор объекта группы> --role <name of role> --scope <подписка/идентификатор подписки>

В следующем примере роль *Читатель* назначается *Команде Кристины Кох* в области действия *подписки*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	Назначение роли для приложения в области действия подписки
Для назначения роли для приложения в области действия подписки воспользуйтесь командой:

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

Следующий пример предоставляет роль *Участник* приложению *Azure AD* в выбранной подписке.

 ![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	Назначение роли пользователю в области действия группы ресурсов
Для назначения роли пользователю в области действия группы ресурсов воспользуйтесь командой:

    azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

Следующий пример предоставляет роль *Участник виртуальной машины* пользователю **samert@aaddemo.com* в области действия группы ресурсов *Pharma-Sales-ProjectForcast*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	Назначение роли для группы в области действия ресурса
Для назначения роли для группы в области действия ресурса воспользуйтесь командой:

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

Следующий пример предоставляет роль *Участник виртуальной машины* группе *Azure AD* в *подсети*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	Запрет доступа
Чтобы удалить назначение роли, воспользуйтесь командой:

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

Следующий пример удаляет назначение роли *Участник виртуальной машины* пользователя **sammert@aaddemo.com* в группе ресурсов *Pharma-Sales-ProjectForcast*. После этого выполняется удаление назначения ролей из группы в подписке.

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## Создание настраиваемой роли
Чтобы создать настраиваемую роль, используйте команду `azure role create`.

В следующем примере создается настраиваемая роль с именем *Оператор виртуальной машины*, которая предоставляет доступ ко всем операциям чтения поставщиков ресурсов *Microsoft.Compute*, *Microsoft.Storage* и *Microsoft.Network*, а также доступ для запуска, перезапуска и мониторинга виртуальных машин. Настраиваемую роль можно использовать в двух подписках. В этом примере в качестве входного файла используется JSON-файл.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role create-1.png)

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## Изменение настраиваемой роли

Чтобы изменить настраиваемую роль, используйте команду отображения роли Azure для получения определения роли. Затем внесите необходимые изменения в определение роли. Наконец, с помощью команды задания роли Azure сохраните измененное определение роли.

В следующем примере добавляется операция Microsoft.Insights/diagnosticSettings/* в действия и подписка Azure в AssignableScopes настраиваемой роли "Оператор виртуальной машины".

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## Удаление настраиваемой роли

Чтобы удалить настраиваемую роль, используйте `azure role show` команду для определения **идентификатора** роли. Затем с помощью команды `azure role delete` удалите роль, указав **идентификатор**.

В следующем примере показано удаление настраиваемой роли *Оператор виртуальной машины*.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## Вывод списка настраиваемых ролей

Чтобы получить список ролей, доступных для назначения в области, используйте команду `azure role list`.

В следующем примере перечисляются все роли, доступные для назначения в выбранной подписке.

![](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

В следующем примере настраиваемая роль *Оператор виртуальной машины* не доступна в подписке *Production4*, так как эта подписка не входит в **AssignableScopes** роли.

![](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## Разделы о RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_1210_2015-->