---
title: Общие сведения об определениях ролей в Azure RBAC | Документация Майкрософт
description: Сведения об определениях ролей при управлении доступом на основе ролей (RBAC) и о том, как определить пользовательские роли для точного управления доступом к ресурсам в Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161817"
---
# <a name="understand-role-definitions"></a>Определения ролей

Чтобы составить представление о роли или создать собственную [пользовательскую роль](custom-roles.md), полезно узнать, как определяются роли. В этой статье подробно описаны определения ролей и приведены примеры.

## <a name="role-definition-structure"></a>Структура определения роли

*Определение роли* представляет собой коллекцию разрешений. Иногда оно называется просто *ролью*. В определении роли перечисляются операции, которые можно выполнить, например чтение, запись и удаление. Также в определении могут перечисляться операции, которые нельзя выполнить. Определение роли имеет следующую структуру:

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

Операции указываются с помощью строк в следующем формате:

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

Часть `{action}` строки операции указывает тип операций, которые можно выполнять с ресурсом того или иного типа. Например, в `{action}` могут содержаться следующие подстроки:

| Подстрока действия    | ОПИСАНИЕ         |
| ------------------- | ------------------- |
| `*` | Подстановочный знак предоставляет доступ ко всем операциям, которые соответствуют строке. |
| `read` | Разрешает операции чтения (GET). |
| `write` | Разрешает операции записи (PUT, POST и PATCH). |
| `delete` | Разрешает операции удаления (DELETE). |

Здесь приведено определение роли [Участник](built-in-roles.md#contributor) в формате JSON. Операция подстановочного знака (`*`) в разделе `actions` указывает, что субъект, которому назначена эта роль, может выполнять все действия или, иными словами, осуществлять полное управление. Сюда входят и действия, определяемые в будущем (по мере добавления новых типов ресурсов в Azure). Операции, указанные в разделе `notActions`, вычитаются из раздела `actions`. При использовании роли [Участник](built-in-roles.md#contributor) `notActions` удаляет для этой роли возможность управлять доступом к ресурсам, а также назначать доступ к ресурсам.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>Операции управления

Управление доступом на основе ролей для операций управления указывается в разделах `actions` и `notActions` определения роли. Ниже перечислены некоторые примеры операций управления в Azure:

- управление доступом к учетной записи хранения;
- создание, обновление или удаление контейнера больших двоичных объектов;
- удаление группы ресурсов со всеми ее ресурсами.

Управление доступом не наследуется для данных. Такое разделение предотвращает неограниченный доступ ролей с подстановочными знаками (`*`) к вашим данным. Например, если у пользователя в подписке есть роль [Читатель](built-in-roles.md#reader), он может просматривать учетную запись хранения, но не имеет возможности просматривать базовые данные.

## <a name="actions"></a>actions

Разрешение `actions` указывает операции управления, к которым роль предоставляет доступ. Это коллекция строк операций, которые определяют защищенные действия поставщиков ресурсов Azure. Ниже приведены некоторые примеры операций управления, которые можно использовать в `actions`.

| Строка операции    | ОПИСАНИЕ         |
| ------------------- | ------------------- |
| `*/read` | Предоставляет доступ к операциям чтения для всех типов ресурсов всех поставщиков ресурсов Azure.|
| `Microsoft.Compute/*` | Предоставляет доступ ко всем операциям для всех типов ресурсов в поставщике ресурсов Microsoft.Compute.|
| `Microsoft.Network/*/read` | Предоставляет доступ к операциям чтения для всех типов ресурсов в поставщике ресурсов Microsoft.Network.|
| `Microsoft.Compute/virtualMachines/*` | Предоставляет доступ ко всем операциям виртуальных машин и их дочерних типов ресурсов.|
| `microsoft.web/sites/restart/Action` | Предоставляет доступ к перезапуску веб-приложения.|

## <a name="notactions"></a>notActions

Разрешение `notActions` указывает операции управления, которые исключаются из разрешенных `actions`. Разрешение `notActions` следует использовать, если для определения набора операций, которые нужно разрешить, проще указать операции, которые необходимо исключить. Доступ, предоставляемый роли (набор действующих разрешений), вычисляется путем вычитания операций `notActions` из операций `actions`.

> [!NOTE]
> Если пользователю назначена роль, которая исключает определенную операцию в `notActions`, а также другая роль, которая предоставляет доступ к той же операции, то пользователю будет разрешено выполнять эту операцию. `notActions` не является запрещающим правилом. Это просто удобный способ создания набора допустимых операций путем исключения некоторых операций.
>

## <a name="assignablescopes"></a>assignableScopes

В разделе `assignableScopes` указываются области (группы управления (сейчас в предварительной версии), подписки, группы ресурсов или ресурсы), в которых роль доступна для назначения. Вы можете разрешить использование роли только в тех подписках или группах ресурсов, в которых она действительно нужна. В остальных подписках и группах ресурсов она просто не будет отображаться, чтобы не отвлекать пользователей. Необходимо использовать по крайней мере одну группу управления, подписку, группу ресурсов или один идентификатор ресурса.

Для встроенных ролей в качестве `assignableScopes` задана корневая область (`"/"`). Корневая область указывает, что роль доступна для назначения во всех областях. В собственных пользовательских ролях корневую область использовать нельзя. При попытке это сделать происходит ошибка авторизации.

Примеры допустимых назначаемых областей:

| Сценарий | Пример |
|----------|---------|
| Роль доступна для назначения в одной подписке | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Роль доступна для назначения в двух подписках | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Роль доступна для назначения только в группе сетевых ресурсов | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Роль доступна для назначения во всех областях | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes и пользовательские роли

Раздел `assignableScopes` для пользовательской роли также определяет, кто может создавать, удалять, изменять или просматривать пользовательскую роль.

| Задача | Операция | ОПИСАНИЕ |
| --- | --- | --- |
| Создание или удаление пользовательской роли | `Microsoft.Authorization/ roleDefinition/write` | Пользователи с разрешением на эту операцию для всех `assignableScopes` пользовательской роли могут создавать (или удалять) пользовательские роли для использования в этих областях. Например, [владельцы](built-in-roles.md#owner) или [администраторы доступа пользователей](built-in-roles.md#user-access-administrator) подписок, групп ресурсов и ресурсов. |
| Изменение настраиваемой роли | `Microsoft.Authorization/ roleDefinition/write` | Пользователи с разрешением на эту операцию для всех `assignableScopes` пользовательской роли могут изменять пользовательские роли в этих областях. Например, [владельцы](built-in-roles.md#owner) или [администраторы доступа пользователей](built-in-roles.md#user-access-administrator) подписок, групп ресурсов и ресурсов. |
| Просмотр пользовательской роли | `Microsoft.Authorization/ roleDefinition/read` | Пользователи с разрешением на эту операцию в определенной области могут просматривать пользовательские роли, которые доступны для назначения в этой области. Все встроенные роли обеспечивают доступность пользовательских ролей для назначения. |

## <a name="role-definition-examples"></a>Примеры определений ролей

В следующем примере показано определение роли [Читатель](built-in-roles.md#reader), отображаемое с помощью Azure CLI:

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
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

Ниже приведен пример пользовательской роли для мониторинга и перезапуска виртуальных машин, отображаемой с помощью Azure PowerShell:

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
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
  "NotActions":  [

                 ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>См. также

* [Встроенные роли](built-in-roles.md)
* [Пользовательские роли](custom-roles.md)
* [Операции поставщиков ресурсов Azure Resource Manager](resource-provider-operations.md)
