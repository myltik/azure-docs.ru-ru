---
title: "Копирование базы данных SQL Azure с помощью Transact-SQL | Документация Майкрософт"
description: "Создание копии Базы данных SQL Azure с помощью Transact-SQL"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 40ea3718-33f8-41af-90cb-3aa15059365e
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 09/19/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: da730116fcc83d53b3665f953332a30c7fc6239d


---
# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Копирование базы данных SQL Azure с помощью Transact-SQL
> [!div class="op_single_selector"]
> * [Обзор](sql-database-copy.md)
> * [Портал Azure](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

Ниже показано, как скопировать базу данных SQL с помощью Transact-SQL на тот же сервер или на другой сервер. Для операции копирования базы данных используется инструкция [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) .

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Подписка Azure. Если вам нужна подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
* База данных Azure SQL. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).
* [SQL Server Management Studio (SSMS).](https://msdn.microsoft.com/library/ms174173.aspx) Если у вас нет SSMS или функции, описанные в этой статье, недоступны, [скачайте последнюю версию](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="copy-your-sql-database"></a>Копирование Базы данных SQL
Войдите в главную базу данных под основным именем входа на уровне сервера или под именем входа, которое использовалось для создания копируемой базы данных. Для копирования баз данных имена входа, не являющиеся основными на уровне сервера, должны относиться к роли dbmanager. Дополнительные сведения об именах для входа и подключении к серверу см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).

Начните копирование исходной базы данных с помощью инструкции [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . Данный оператор запускает процесс копирования базы данных. Поскольку копирование базы данных — процесс асинхронный, оператор CREATE DATABASE возвращается до завершения копирования базы данных.

### <a name="copy-a-sql-database-to-the-same-server"></a>Копирование Базы данных SQL на тот же сервер
Войдите в главную базу данных под основным именем входа на уровне сервера или под именем входа, которое использовалось для создания копируемой базы данных. Для копирования баз данных имена входа, не являющиеся основными на уровне сервера, должны относиться к роли dbmanager.

Эта команда копирует Database1 в новую базу данных с именем Database2 на том же сервере. Операция копирования может занять некоторое время в зависимости от размера базы данных.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Копирование Базы данных SQL на другой сервер
Войдите в главную базу данных на целевом сервере, т. е. сервере Базы данных SQL Azure, на котором должна быть создана новая база данных. Для входа укажите имя пользователя и пароль владельца исходной базы данных на исходном сервере Базы данных SQL Azure. Имя входа на целевом сервере также должно относиться к роли dbmanager или являться основным именем входа на уровне сервера.

Эта команда копирует Database1 на сервере server1 в новую базу данных с именем Database2 на сервере server2. Операция копирования может занять некоторое время в зависимости от размера базы данных.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


## <a name="monitor-the-progress-of-the-copy-operation"></a>Отслеживание хода выполнения операции копирования
Следить за ходом процесса копирования можно в представлениях sys.databases и sys.dm_database_copies. Во время копирования в столбце state_desc представления sys.databases для новой базы данных отображается значение COPYING.

* Если копирование завершается неудачей, в столбце state_desc представления sys.databases для новой базы данных отображается значение COPYING. В этом случае выполните оператор DROP для новой базы данных и повторите попытку позднее.
* Если копирование завершается успешно, в столбце state_desc представления sys.databases для новой базы данных отображается значение ONLINE. Это означает, что копирование завершено и новая база данных является обычной базой данных, которую можно изменять независимо от исходной.

> [!NOTE]
> * Чтобы отменить копирование до его завершения, выполните в новой базе данных инструкцию [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx). Кроме того, процесс копирования отменяет также выполнение оператора DROP DATABASE в исходной базе данных.
> 
> 

## <a name="resolve-logins-after-the-copy-operation-completes"></a>Разрешение имен для входа после завершения операции копирования
После того как новая база данных на целевом сервере будет в сети, сопоставьте пользователей из новой базы данных с именами входа на целевом сервере с помощью оператора [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx). Сведения о разрешении потерянных пользователей см. в разделе [Диагностика пользователей, утративших связь с учетной записью](https://msdn.microsoft.com/library/ms175475.aspx). Ознакомьтесь также со статьей [Как управлять безопасностью базы данных SQL после аварийного восстановления](sql-database-geo-replication-security-config.md).

Все пользователи в новой базе данных получают такие же разрешения, как и в исходной базе данных. Пользователь, инициировавший копирование базы данных, становится владельцем новой базы данных и получает новый идентификатор безопасности (SID). С момента успешного завершения копирования и вплоть до сопоставления остальных пользователей войти в новую базу данных может только ее владелец, т. е. пользователь, инициировавший копирование.

## <a name="next-steps"></a>Дальнейшие действия
* В разделе [Копирование Базы данных SQL Azure](sql-database-copy.md) доступны общие сведения о копировании базы данных SQL Azure.
* В статье [Копирование базы данных SQL Azure с помощью портала Azure](sql-database-copy-portal.md) рассматривается копирование базы данных с помощью портала Azure.
* В разделе [Копирование базы данных SQL Azure с помощью PowerShell](sql-database-copy-powershell.md) рассматривается копирование базы данных с помощью PowerShell.
* В разделе [Как управлять безопасностью базы данных SQL после аварийного восстановления](sql-database-geo-replication-security-config.md) описывается управление пользователями и именами для входа при копировании базы данных на другой логический сервер.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Управление именами для входа](sql-database-manage-logins.md)
* [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)
* [Экспорт базы данных в BACPAC](sql-database-export.md)
* [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
* [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)




<!--HONumber=Nov16_HO3-->


