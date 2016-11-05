---
title: Пользовательские роли в Azure RBAC | Microsoft Docs
description: Узнайте, как c помощью управления доступом на основе ролей Azure задавать пользовательские роли для более точного управления удостоверениями в подписке Azure.
services: active-directory
documentationcenter: ''
author: kgremban
manager: kgremban
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2016
ms.author: kgremban

---
# Пользовательские роли в Azure RBAC
Если ни одна из встроенных ролей не соответствует вашим требованиям к доступу, создайте пользовательскую роль с помощью механизма RBAC Azure (управление доступом на основе ролей). Пользовательские роли можно создавать с помощью [Azure PowerShell](role-based-access-control-manage-access-powershell.md), [интерфейса командной строки (CLI) Azure](role-based-access-control-manage-access-azure-cli.md) и интерфейса [REST API](role-based-access-control-manage-access-rest.md). Пользовательские роли, так же как и встроенные, могут назначаться пользователям, группам и приложениям в рамках подписки, группы ресурсов или области ресурсов. Пользовательские роли хранятся в клиенте Azure AD и могут совместно использоваться всеми подписками, которые используют этот клиент как каталог Azure AD для подписки.

Ниже приведен пример пользовательской роли, которая позволяет выполнять мониторинг и перезапуск виртуальных машин.

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
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
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## Действия
Свойство **Действия** пользовательской роли определяет операции Azure, к которым эта роль предоставляет доступ. Это коллекция строк операций, которые определяют защищенные действия поставщиков ресурсов Azure. Строки операций, содержащие подстановочные знаки (*), предоставляют доступ ко всем операциям, которые соответствуют определенной строке операции. например

* строка `*/read` предоставляет доступ к операциям чтения для всех типов ресурсов для всех поставщиков ресурсов Azure;
* строка `Microsoft.Network/*/read` предоставляет доступ к операциям чтения для всех типов ресурсов для поставщика ресурсов Microsoft.Network;
* строка `Microsoft.Compute/virtualMachines/*` предоставляет доступ ко всем операциям виртуальных машин и их вложенных типов ресурсов;
* строка `Microsoft.Web/sites/restart/Action` предоставляет доступ к перезапуску веб-сайтов.

Чтобы получить список операций поставщиков ресурсов Azure, используйте командлет `Get-AzureRmProviderOperation` (в PowerShell) или команду `azure provider operations show` (в Azure CLI). Кроме того, с помощью этих команд можно проверить, является ли строка операции допустимой, а также развернуть строки операций с подстановочными знаками.

```
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Снимок экрана PowerShell: Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Снимок экрана Azure CLI: azure provider operations show "Microsoft.Compute/virtualMachines/*/action"](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## NotActions
Используйте свойство **NotActions**, если для определения набора операций, которые вы хотите разрешить, проще указать операции, которые необходимо исключить. Доступ, которые предоставляет пользовательская роль, реализуется путем исключения операций, определяемых свойством **NotActions**, из списка операций, определяемых свойством **Actions**.

> [!NOTE]
> Пользователю одновременно могут быть назначены две роли: первая исключает определенную операцию с помощью свойства **NotActions**, а вторая предоставляет доступ к этой же операции. В таком случае пользователь имеет право на выполнение этой операции. Свойство **NotActions** не является запрещающим правилом. Это удобный способ создания набора допустимых операций путем исключения некоторых операций.
> 
> 

## AssignableScopes
Свойство пользовательской роли **AssignableScopes** определяет области (подписки, группы ресурсов или ресурсы), в которых эта пользовательская роль доступна для назначения. Вы можете разрешить использование пользовательской роли только в тех подписках или группах ресурсов, в которых она действительно нужна. В остальных подписках и группах ресурсов она просто не будет отображаться, чтобы не отвлекать пользователей.

Примеры допустимых назначаемых областей:

* /subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e и /subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624 — делают роль доступной для назначения в двух подписках;
* /subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e — делает роль доступной для назначения в одной подписке;
* /subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network — делает роль доступной для назначения только в группе ресурсов с именем Network.

> [!NOTE]
> Необходимо использовать по крайней мере одну подписку, группу ресурсов или идентификатор ресурса.
> 
> 

## Контроль доступа к пользовательским ролям
Свойство **AssignableScopes** пользовательской роли также определяет пользователей с правом просматривать, изменять и удалять эту роль.

* Кто может создавать пользовательские роли? Создавать пользовательские роли для использования в подписках, группах ресурсов и отдельных ресурсах могут владельцы (и администраторы доступа пользователей) этих областей. При создании роли пользователь должен иметь право выполнять операцию `Microsoft.Authorization/roleDefinition/write` во всех областях этой роли, определенных свойством **AssignableScopes**.
* Кто может изменять пользовательские роли? Изменять пользовательские роли для использования в подписках, группах ресурсов и отдельных ресурсах могут владельцы (и администраторы доступа пользователей) этих областей. Пользователь должен иметь право выполнять операцию `Microsoft.Authorization/roleDefinition/write` во всех областях этой роли, определенных свойством **AssignableScopes**.
* Кто может просматривать пользовательские роли? Все стандартные роли Azure RBAC позволяют просматривать список ролей, доступных для назначения. Просматривать роли RBAC, которые доступны для назначения в области, могут пользователи с правом выполнять операцию `Microsoft.Authorization/roleDefinition/read` для этой области.

## Дополнительные материалы
* [Контроль доступа на основе ролей](role-based-access-control-configure.md). Начало работы с RBAC на портале Azure.
* Сведения об управлении доступом с помощью следующих средств:
  * [PowerShell](role-based-access-control-manage-access-powershell.md)
  * [Интерфейс командной строки Azure](role-based-access-control-manage-access-azure-cli.md)
  * [ИНТЕРФЕЙС REST API](role-based-access-control-manage-access-rest.md)
* [Встроенные роли](role-based-access-built-in-roles.md). Сведения о стандартных ролях в RBAC.

<!---HONumber=AcomDC_0907_2016-->