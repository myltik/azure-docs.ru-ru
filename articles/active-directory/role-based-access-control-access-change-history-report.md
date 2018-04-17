---
title: Доступ к отчетам с помощью Azure RBAC | Документация Майкрософт
description: Создайте отчет, в котором указаны все изменения доступа к подпискам Azure с управлением доступом на основе ролей за последние 90 дней.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 169ed8dd6d14d8d9d0fd49ad7306b1d4fb2c4d90
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2018
---
# <a name="create-an-access-report-for-role-based-access-control"></a>Создание отчета о доступе для управления доступом на основе ролей
Когда кто-то предоставляет или отменяет доступ в рамках ваших подписок, изменения регистрируются в журнале событий Azure. Чтобы просмотреть все изменения за последние 90 дней, создайте отчет по журналу изменений доступа.

## <a name="create-a-report-with-azure-powershell"></a>Создание отчета с помощью Azure PowerShell
Чтобы создать отчет по журналу изменений доступа в PowerShell, используйте команду [Get-AzureRMAuthorizationChangeLog](/powershell/module/azurerm.resources/get-azurermauthorizationchangelog).

При вызове этой команды можно указывать, какие свойства назначения нужно включить в список, в частности:

| Свойство | ОПИСАНИЕ |
| --- | --- |
| **Действие** |Предоставление доступа или его отмена |
| **Caller** |Владелец, ответственный за изменение доступа |
| **PrincipalId** | Уникальный идентификатор пользователя, группы или приложения, которым назначена роль |
| **PrincipalName** |Имя пользователя, группы или приложения |
| **PrincipalType** |Кому адресовано назначение — пользователю, группе или приложению |
| **RoleDefinitionId** |Идентификатор GUID роли, которая была назначена или отменена |
| **RoleName** |Роль, которая была назначена или отменена |
| **Область** | Уникальный идентификатор подписки, группы ресурсов или ресурса, к которым применяется назначение | 
| **ScopeName** |Название подписки, группы ресурсов или ресурса |
| **ScopeType** |Область назначения: подписка, группа ресурсов или ресурс |
| **Timestamp** |Дата и время изменения доступа |

Следующий пример команды создает список всех изменений доступа в подписке за последние семь дней.

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog — снимок экрана](./media/role-based-access-control-configure/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Создание отчета с помощью Azure CLI
Чтобы создать отчет по журналу изменений доступа с помощью интерфейса командной строки (CLI) Azure, используйте команду `azure role assignment changelog list` .

## <a name="export-to-a-spreadsheet"></a>Экспорт в электронную таблицу
Для сохранения отчета или работы с данными экспортируйте сведения об изменениях доступа в CSV-файл. Так вы можете просмотреть отчет в виде электронной таблицы.

![Просмотр журнала изменений в виде электронной таблицы — снимок экрана](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## <a name="next-steps"></a>Дополнительная информация
* Работа с [пользовательскими ролями в Azure RBAC](role-based-access-control-custom-roles.md)
* Узнайте, как управлять [Azure RBAC с помощью PowerShell](role-based-access-control-manage-access-powershell.md).

