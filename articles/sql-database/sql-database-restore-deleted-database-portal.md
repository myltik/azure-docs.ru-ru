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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Восстановление удаленной базы данных SQL Azure на портале Azure


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

В этой статье показано, как восстановить удаленную базу данных SQL Azure.

Если база данных удалена, База данных SQL Azure позволяет восстановить ее на момент удаления. База данных SQL Azure хранит резервную копию удаленной базы данных в течение срока хранения.

Срок хранения удаленной базы данных определяется в зависимости от уровня службы базы данных во время ее существования или от количества дней, в течение которых существовала база данных. Выбирается меньшее значение. Подробнее о хранении баз данных можно прочесть в статье [Обзор. Непрерывность облачных бизнес-процессов и аварийное восстановление баз данных с базой данных SQL](sql-database-business-continuity.md).

## Выбор базы данных для восстановления 

Чтобы восстановить базу данных на портале Azure, выполните следующие действия.

1.	Откройте [портал Azure](https://portal.azure.com).
2.  В левой части экрана выберите **Обзор** > **Серверы SQL**.
3.  Перейдите к серверу с базой данных, которую требуется восстановить после удаления, и выберите сервер.
4.  Прокрутите вниз до раздела **Операции** колонки сервера и выберите **Удаленные базы данных**: ![Восстановление базы данных SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Выберите удаленную базу данных, которую хотите восстановить.
6.  Укажите имя базы данных и нажмите кнопку "ОК".

    ![Восстановление базы данных SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## Дальнейшие действия

- [Финализация восстановленной Базы данных SQL Azure](sql-database-recovered-finalize.md)
- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)



## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->