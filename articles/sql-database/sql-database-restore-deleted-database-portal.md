<properties
	pageTitle="Восстановление удаленной базы данных SQL Azure (портал Azure) | Microsoft Azure"
	description="Восстановление удаленной базы данных SQL Azure (портал Azure)."
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
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Восстановление удаленной базы данных SQL Azure на портале Azure

> [AZURE.SELECTOR]
- [Обзор](sql-database-restore-deleted-database.md)
- [Портал Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)


## Выбор базы данных для восстановления 

Чтобы восстановить базу данных на портале Azure, выполните следующие действия.

1.	Откройте [портал Azure](https://portal.azure.com).
2.  В левой части экрана выберите **Обзор** > **Серверы SQL**.
3.  Перейдите к серверу с базой данных, которую требуется восстановить после удаления, и выберите сервер.
4.  Прокрутите вниз до раздела **Операции** колонки сервера и выберите **Удаленные базы данных**. ![Восстановление базы данных SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Выберите удаленную базу данных, которую хотите восстановить.
6.  Укажите имя базы данных и нажмите кнопку "ОК".

    ![Восстановление базы данных SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## Дальнейшие действия

- Подробные инструкции по восстановлению удаленной базы данных с помощью PowerShell см. в разделе [Восстановление удаленной базы данных SQL Azure с помощью PowerShell](sql-database-restore-deleted-database-powershell.md).
- Чтобы узнать, как восстановить удаленную базу данных с помощью REST API, [ознакомьтесь с данной процедурой](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Подробные сведения о восстановлении удаленной базы данных см. в разделе [Восстановление удаленной базы данных](sql-database-restore-deleted-database.md).
- Подробные сведения об автоматически создаваемых резервных копиях базы данных SQL Azure см. в разделе [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md).

## Дополнительные ресурсы

- [Восстановление до точки во времени](sql-database-point-in-time-restore.md)
- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [Геовосстановление](sql-database-geo-restore.md)
- [Активная георепликация](sql-database-geo-replication-overview.md)
- [Проектирование приложений для аварийного восстановления в облаке](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->