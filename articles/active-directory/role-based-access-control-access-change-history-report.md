---
title: "Создание отчета по журналу изменений доступа | Документация Майкрософт"
description: "Создайте отчет, в котором указаны все изменения доступа к подпискам Azure с управлением доступом на основе ролей за последние 90 дней."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 44295ff647cbfd2d63ffe08d101da66b83a924f6


---
# <a name="create-an-access-change-history-report"></a>Создание отчета по журналу изменений доступа
Когда кто-то предоставляет или отменяет доступ в рамках ваших подписок, изменения регистрируются в журнале событий Azure. Чтобы просмотреть все изменения за последние 90 дней, создайте отчет по журналу изменений доступа.

## <a name="create-a-report-with-azure-powershell"></a>Создание отчета с помощью Azure PowerShell
Чтобы создать отчет по журналу изменений доступа в PowerShell, используйте команду `Get-AzureRMAuthorizationChangeLog`. Дополнительные сведения об этом командлете см. в [коллекции PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/1.0.6/Content/ResourceManagerStartup.ps1).

При вызове этой команды можно указывать, какие свойства назначения нужно включить в список, в частности:

| Свойство | Описание |
| --- | --- |
| **Действие** |Предоставление доступа или его отмена |
| **Caller** |Владелец, ответственный за изменение доступа |
| **Дата** |Дата и время изменения доступа |
| **DirectoryName** |Каталог Azure Active Directory |
| **PrincipalName** |Имя пользователя, группы или приложения |
| **PrincipalType** |Кому адресовано назначение — пользователю, группе или приложению |
| **RoleId** |Идентификатор GUID роли, которая была назначена или отменена |
| **RoleName** |Роль, которая была назначена или отменена |
| **ScopeName** |Название подписки, группы ресурсов или ресурса |
| **ScopeType** |Область назначения: подписка, группа ресурсов или ресурс |
| **SubscriptionId** |Идентификатор GUID подписки Azure |
| **Параметр SubscriptionName** |Имя подписки Azure |

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

## <a name="see-also"></a>Дополнительные материалы
* Начало работы с [управлением доступом на основе ролей в Azure](role-based-access-control-configure.md)
* Работа с [пользовательскими ролями в Azure RBAC](role-based-access-control-custom-roles.md)




<!--HONumber=Nov16_HO3-->


