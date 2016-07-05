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
	ms.date="06/17/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
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

- Подробные инструкции по восстановлению базы данных SQL Azure с помощью портала Azure из геоизбыточной резервной копии см. в разделе [Геовосстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью портала Azure](sql-database-geo-restore-portal.md).
- Дополнительные подробные сведения о восстановлении базы данных SQL Azure из геоизбыточной резервной копии см. в разделе[Восстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью PowerShell](sql-database-geo-restore.md).
- Подробное обсуждение восстановления после сбоя см. в разделе [Восстановление базы данных SQL Azure или переход на базу данных-получатель при отказе](sql-database-disaster-recovery.md).

## Дополнительные ресурсы

- [Сценарии обеспечения непрерывности бизнес-процессов](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->