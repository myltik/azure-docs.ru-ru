<properties
	pageTitle="Восстановление базы данных SQL Azure до момента времени в прошлом (на портале Azure) | Microsoft Azure"
	description="Восстановление базы данных SQL Azure до момента времени в прошлом"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Восстановление базы данных SQL Azure до момента времени в прошлом с помощью портала Azure


> [AZURE.SELECTOR]
- [Обзор](sql-database-point-in-time-restore.md)
- [Портал Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

В этой статье объясняется, как восстановить базу данных до момента времени в прошлом (на основании [сделанных автоматических резервных копий базы данных SQL](sql-database-automated-backups.md)) с помощью портала Azure.

## Выбор базы данных для восстановления до момента времени в прошлом

Чтобы восстановить базу данных на портале Azure, выполните следующие действия.

1.	Откройте [портал Azure](https://portal.azure.com).
2.  В левой части экрана выберите **ОБЗОР** > **Базы данных SQL**.
3.  Перейдите к базе данных, которую требуется восстановить, и выберите ее.
4.  В верхней части колонки своей базы данных выберите **Восстановить**.

    ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Укажите имя базы данных, выберите момент времени и нажмите кнопку "ОК".

    ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)


## Дальнейшие действия

- [Финализация восстановленной Базы данных SQL Azure](sql-database-recovered-finalize.md)
- [Восстановление состояния системы в определенной временной точке](sql-database-point-in-time-restore.md)
- [Create or Update Database](https://msdn.microsoft.com/library/azure/mt163685.aspx) (Создание и обновление базы данных)
- [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md)

## Дополнительные ресурсы

- [Восстановление удаленной базы данных.](sql-database-restore-deleted-database.md)
- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [Геовосстановление](sql-database-geo-restore.md)
- [Активная георепликация](sql-database-geo-replication-overview.md)
- [Проектирование приложений для аварийного восстановления в облаке](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->