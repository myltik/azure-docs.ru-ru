<properties
   pageTitle="Восстановление базы данных в хранилище данных SQL Azure (портал Azure) | Microsoft Azure"
   description="Задачи REST API для восстановления работающей, удаленной или недоступной базы данных в хранилище данных SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="elfish;barbkess;sonyama;kevin"/>

# Архивация и восстановление базы данных в хранилище данных SQL Azure (REST API)

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-overview-manage-database-restore.md)
- [Портал](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

Задачи REST API для восстановления базы данных в хранилище данных SQL.

Задачи раздела.

- Восстановление активной базы данных.
- Восстановление удаленной базы данных.

## Перед началом работы

**Проверьте ресурсы DTU Базы данных SQL.** Так как восстановление хранилища данных SQL выполняется в новую базу данных на логическом сервере SQL Server, важно убедиться, что на используемом сервере имеется достаточно единиц DTU для новой базы данных. В этой записи блога вы можете узнать больше о том, [как просмотреть и увеличить квоту DTU][].

## Восстановление активной базы данных.

Процедура восстановления базы данных

1. Получите список точек восстановления базы данных с помощью операции Get Database Restore Points.
2. Начните восстановление с помощью операции [Create Database Restore Request][].
3. Отследите состояние восстановления с помощью операции [Database Operation Status][].

>[AZURE.NOTE] Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной Базы данных SQL Azure][].

## Восстановление удаленной базы данных.

Процедура восстановления удаленной базы данных

1.	Создайте список всех удаленных баз данных, доступных для восстановления, с помощью операции [List Restorable Dropped Databases][].
2.	Получите подробную информацию об удаленной базе данных, которую необходимо восстановить, с помощью операции [Get Restorable Dropped Database][].
3.	Начните восстановление с помощью операции [Create Database Restore Request][].
4.	Отследите состояние восстановления с помощью операции [Database Operation Status][].

>[AZURE.NOTE] Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной Базы данных SQL Azure][].


## Дальнейшие действия
Дополнительные сведения о функциях обеспечения непрерывности бизнес-процессов в выпусках базы данных SQL Azure см. в [обзоре обеспечения непрерывности бизнес-процессов базы данных Azure SQL][].

<!--Image references-->

<!--Article references-->
[обзоре обеспечения непрерывности бизнес-процессов базы данных Azure SQL]: sql-database-business-continuity.md
[Финализация восстановленной Базы данных SQL Azure]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[как просмотреть и увеличить квоту DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0608_2016-->