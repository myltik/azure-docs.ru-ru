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
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Обзор. Восстановление удаленной базы данных SQL Azure

> [AZURE.SELECTOR]
- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [Восстановление до точки во времени](sql-database-point-in-time-restore.md)
- [Восстановление удаленной базы данных](sql-database-restore-deleted-database.md)
- [Геовосстановление](sql-database-geo-restore.md)
- [Активная георепликация](sql-database-geo-replication-overview.md)
- [Сценарии обеспечения непрерывности бизнес-процессов](sql-database-business-continuity-scenarios.md)


Удаленную базу данных можно восстановить в течение срока хранения [создаваемых автоматически резервных копий базы данных SQL](sql-database-automated-backups.md) для вашего [уровня служб](sql-database-service-tiers.md). Это можно сделать с помощью [портала Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) или [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

## Восстановление недавно удаленной базы данных

Можно восстановить удаленную базу данных, используя прежнее или другое имя базы данных для логического сервера, содержавшего исходную базу данных. Точка восстановления удаленных баз данных определяется временем их удаления.

> [AZURE.IMPORTANT] При удалении экземпляра сервера базы данных SQL Azure все базы данных, находившиеся на нем, также будут удалены без возможности восстановления.

## Время восстановления

Время, необходимое для восстановления базы данных, зависит от многих факторов, включая размер базы данных, число журналов транзакций, выбранную точку во времени и объем действий, которые нужно повторить, чтобы восстановить состояние до выбранной точки. Восстановление очень большой и/или активной базы данных может занять несколько часов. При каждом восстановлении базы данных создается новая база данных на том же сервере, что и исходная. Поэтому для восстановленной базы данных нужно указать новое имя. В большинстве случаев на восстановление базы данных требуется до 12 часов.

## Сводка

Автоматическое резервное копирование защищает базы данных от случайного удаления. Это бесплатное решение, которое не требует участия администратора и доступно для всех баз данных SQL.

## Дальнейшие действия

- Подробные инструкции по восстановлению удаленной базы данных с помощью портала Azure см. в разделе [Восстановление удаленной базы данных SQL Azure на портале Azure](sql-database-restore-deleted-database-portal.md).
- Подробные инструкции по восстановлению удаленной базы данных с помощью PowerShell см. в разделе [Восстановление удаленной базы данных SQL Azure с помощью PowerShell](sql-database-restore-deleted-database-powershell.md).
- Чтобы узнать, как восстановить удаленную базу данных с помощью REST API, [ознакомьтесь с данной процедурой](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Подробные сведения об автоматически создаваемых резервных копиях базы данных SQL Azure см. в разделе [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md).

## Дополнительные ресурсы

- [Восстановление до точки во времени](sql-database-point-in-time-restore.md)
- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [Геовосстановление](sql-database-geo-restore.md)
- [Активная георепликация](sql-database-geo-replication-overview.md)
- [Проектирование приложений для аварийного восстановления в облаке](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->