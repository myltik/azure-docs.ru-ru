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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Восстановление базы данных SQL Azure до момента времени в прошлом с помощью портала Azure


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

В этой статье объясняется, как восстановить базу данных до момента времени в прошлом с помощью портала Azure.

[**Восстановление до точки во времени**](sql-database-point-in-time-restore.md) — это возможность самообслуживания, которая позволяет восстановить базу данных из автоматических резервных копий, которые создаются для всех баз данных, до любого момента времени за период хранения базы данных. Дополнительные сведения об автоматических резервных копиях и периодах хранения см. в статье [Обзор. Непрерывность облачных бизнес-процессов и аварийное восстановление баз данных с базой данных SQL](sql-database-business-continuity.md).

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
- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)



## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->