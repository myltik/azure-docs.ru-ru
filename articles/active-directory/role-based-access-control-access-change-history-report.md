<properties
	pageTitle="Создание отчета по журналу изменений доступа | Microsoft Azure"
	description="Создайте отчет, в котором указаны все изменения доступа к подпискам Azure с управлением доступом на основе ролей за последние 90 дней."
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
	ms.date="04/28/2016"
	ms.author="kgremban"/>

# Создание отчета по журналу изменений доступа

Если вы не являетесь единственным владельцем подписок Azure или ресурсов и групп ресурсов в этих подписках, у вас должна быть возможность отслеживать все изменения доступа. Когда кто-то предоставляет или отменяет доступ в рамках ваших подписок, изменения регистрируются в журнале событий Azure. Чтобы просмотреть все изменения за последние 90 дней, создайте отчет по журналу изменений доступа.

## Создание отчета с помощью Azure PowerShell
Чтобы создать отчет по журналу изменений доступа в PowerShell, используйте следующую команду:

```
Get-AzureRMAuthorizationChangeLog
```

Вы можете указывать, какие свойства назначения нужно включить в список, в частности:

| Свойство | Описание |
| -------- | ----------- |
| **Действие** | Предоставление доступа или его отмена |
| **Caller** | Владелец, ответственный за изменение доступа |
| **Дата** | Дата и время изменения доступа |
| **DirectoryName** | Каталог Azure Active Directory |
| **PrincipalName** | Имя пользователя, группы или приложения |
| **PrincipalType** | Кому адресовано назначение — пользователю, группе или приложению |
| **RoleId** | Идентификатор GUID роли, которая была назначена или отменена |
| **RoleName** | Роль, которая была назначена или отменена |
| **ScopeName** | Название подписки, группы ресурсов или ресурса |
| **ScopeType** | Область назначения: подписка, группа ресурсов или ресурс |
| **SubscriptionId** | Идентификатор GUID подписки Azure |
| **Параметр SubscriptionName** | Имя подписки Azure |

Следующий пример команды создает список всех изменений доступа в подписке за последние 7 дней.

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog — снимок экрана](./media/role-based-access-control-configure/access-change-history.png)

## Создание отчета с помощью Azure CLI
Чтобы создать отчет по журналу изменений доступа с помощью интерфейса командной строки (CLI) Azure, используйте следующую команду:
```
azure role assignment changelog list
```

## Экспорт в электронную таблицу
Для сохранения отчета или работы с данными экспортируйте сведения об изменениях доступа в CSV-файл. Так вы можете просмотреть отчет в виде электронной таблицы.

![Просмотр журнала изменений в виде электронной таблицы — снимок экрана](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## См. также
- Начало работы с [управлением доступом на основе ролей в Azure](role-based-access-control-configure.md)
- Работа с [пользовательскими ролями в Azure RBAC](role-based-access-control-custom-roles.md)

<!---HONumber=AcomDC_0727_2016-->