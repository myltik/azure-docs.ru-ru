<properties
   pageTitle="Непрерывность облачных бизнес-процессов — восстановление удаленной базы данных — база данных SQL | Microsoft Azure"
   description="В этой статье описывается функция восстановления до точки во времени, позволяющая откатить Базу данных Azure SQL до состояния на момент времени в прошлом (до 35 дней)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Обзор. Восстановление удаленной базы данных SQL Azure

> [AZURE.SELECTOR]
- [Обзор](sql-database-restore-deleted-database.md)
- [Портал Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

Удаленную базу данных можно восстановить в течение срока хранения [автоматических резервных копий базы данных SQL](sql-database-automated-backups.md). Это можно сделать с помощью [портала Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) или [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx).

Если удалить базу данных, то последняя резервная копия будет храниться в течение обычного срока хранения, что позволит вам восстановить базу данных до состояния на момент ее удаления.

## Восстановление недавно удаленной базы данных

Точка восстановления удаленных баз данных определяется временем их удаления. Удаленную базу данных можно восстановить только на сервере, на котором находилась исходная база данных. Это следует учитывать при удалении сервера, так как после этого вы не сможете восстановить базы данных, которые на нем размещались ранее.

> [AZURE.IMPORTANT] При удалении экземпляра сервера базы данных SQL Azure все базы данных, находившиеся на нем, также будут удалены без возможности восстановления.

## Сводка

Автоматическое резервное копирование защищает базы данных от случайного удаления. Это бесплатное решение, которое не требует участия администратора и доступно для всех баз данных SQL.

## Дальнейшие действия

- [Финализация восстановленной Базы данных SQL Azure](sql-database-recovered-finalize.md)
- [Восстановление удаленной базы данных SQL Azure на портале Azure](sql-database-restore-deleted-database-portal.md)
- [Восстановление удаленной базы данных SQL Azure с помощью PowerShell](sql-database-restore-deleted-database-powershell.md)
- [Create or Update Database](https://msdn.microsoft.com/library/azure/mt163685.aspx) (Создание и обновление базы данных)
- [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md)

## Дополнительные ресурсы

- [Восстановление состояния системы в определенной временной точке](sql-database-point-in-time-restore.md)
- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [Геовосстановление](sql-database-geo-restore.md)
- [Активная георепликация](sql-database-geo-replication-overview.md)
- [Проектирование приложений для аварийного восстановления в облаке](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->