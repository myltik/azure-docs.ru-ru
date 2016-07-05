<properties
	pageTitle="Копирование базы данных SQL Azure с помощью портала Azure | Microsoft Azure"
	description="Создание копии Базы данных SQL Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/16/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Копирование базы данных SQL Azure с помощью портала Azure

> [AZURE.SELECTOR]
- [Обзор](sql-database-copy.md)
- [Портал Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Ниже показано, как скопировать базу данных SQL с помощью [портала Azure](https://portal.azure.com) на тот же сервер или на другой сервер.

Для копирования Базы данных SQL необходимо следующее:

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
- База данных SQL для копирования. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).


## Копирование Базы данных SQL

Откройте колонку «База данных SQL» для базы данных, которую нужно скопировать:

1.	Перейдите на [портал Azure](https://portal.azure.com).
2.	Перейдите к базе данных, которую нужно скопировать: выберите «Обзор» > «Базы данных SQL».
3.	В колонке Базы данных SQL щелкните **Копировать**, чтобы открыть колонку **Копирование**.

    ![копировать базу данных][1]

1.  Введите имя для копии базы данных. Имя по умолчанию при необходимости можно изменить.
2.  Выберите **целевой сервер**. Целевой сервер — это место, где будет создана копия базы данных. Можно выбрать сервер из списка или создать новый.
3.  Нажмите кнопку **ОК**, чтобы запустить процесс копирования.

    ![имя и сервер базы данных][2]


## Отслеживание хода выполнения операции копирования

- После запуска копирования щелкните уведомления портала, чтобы открыть подробные сведения.

    ![уведомление][3]
 
    ![monitor][4]


## Проверка активности базы данных на сервере

- Выберите **ОБЗОР** > **Базы данных SQL** и убедитесь в том, что новая база данных имеет статус **В сети**.


## Разрешение имен для входа

Сведения о разрешении имен для входа после завершения операции копирования см. в разделе [Копирование базы данных SQL Azure с помощью Transact-SQL](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes).


## Дальнейшие действия

- В разделе [Копирование Базы данных SQL Azure](sql-database-copy.md) доступны общие сведения о копировании базы данных SQL Azure.
- В разделе [Копирование базы данных SQL Azure с помощью PowerShell](sql-database-copy-powershell.md) рассматривается копирование базы данных с помощью PowerShell.
- В разделе [Копирование базы данных SQL Azure с помощью Transact-SQL](sql-database-copy-transact-sql.md) рассматривается копирование базы данных с помощью Transact-SQL.
- В разделе [Как управлять безопасностью базы данных SQL после аварийного восстановления](sql-database-geo-replication-security-config.md) описывается управление пользователями и именами для входа при копировании базы данных на другой логический сервер.



## Дополнительные ресурсы

- [Управление именами для входа](sql-database-manage-logins.md)
- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)
- [Экспорт базы данных в BACPAC](sql-database-export.md)
- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

<!---HONumber=AcomDC_0622_2016-->