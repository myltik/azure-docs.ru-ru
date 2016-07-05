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
	ms.date="06/17/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
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

- Подробные инструкции по восстановлению до точки во времени с помощью PowerShell см. в разделе [Восстановление базы данных SQL Azure до точки во времени с помощью PowerShell](sql-database-point-in-time-restore-powershell.md).
- Чтобы узнать о восстановлении до точки во времени с помощью REST API, ознакомьтесь с [данным способом восстановления](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Общие сведения о восстановлении до точки во времени см. в разделе [Обзор. Функция восстановления до точки во времени в Базе данных SQL](sql-database-point-in-time-restore.md).
- Восстановление после ошибки пользователя или приложения подробно рассматривается в разделе [Восстановление базы данных SQL Azure после ошибки пользователя](sql-database-user-error-recovery.md).

## Дополнительные ресурсы

- [Сценарии обеспечения непрерывности бизнес-процессов](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->