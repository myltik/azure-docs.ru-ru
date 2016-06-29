<properties
	pageTitle="Восстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью портала Azure | Microsoft Azure"
	description="Геовосстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью портала Azure."
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


# Геовосстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью портала Azure


> [AZURE.SELECTOR]
- [Обзор](sql-database-geo-restore.md)
- [Портал Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

В этой статье показано, как восстановить базу данных на новый сервер с помощью геовосстановления и портала Azure.

## Выбор базы данных для восстановления

Чтобы восстановить базу данных на портале Azure, выполните следующие действия.

1.	Откройте [портал Azure](https://portal.azure.com).
2.  В левой части экрана последовательно выберите **Создать** > **Данные и хранилище** > **База данных SQL**.
3.  Выберите **Резервная копия** в качестве источника, затем выберите геоизбыточную резервную копию, из которой вы хотите восстановить базу данных.

    ![Восстановление базы данных SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  Укажите имя базы данных, сервер, на котором вы хотите восстановить базу данных, и нажмите кнопку "Создать".

## Дальнейшие действия

- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)
- [Отработка аварийного восстановления](sql-database-disaster-recovery-drills.md)


## Дополнительные ресурсы

- [Геовосстановление](sql-database-geo-restore.md)
- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0615_2016-->