<properties 
    pageTitle="Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью портала Azure | Microsoft Azure" 
    description="Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью портала Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="07/19/2016"
    ms.author="sstein"/>

# Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью портала Azure


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


В этой статье объясняется, как запустить отработку отказа в базе данных-получателе SQL Azure с помощью [портала Azure](http://portal.azure.com). Информацию о настройке георепликации для баз данных SQL Azure см. в [этой статье](sql-database-geo-replication-portal.md).


## Запуск отработки отказа

Базу данных-получатель можно сделать источником.

1. На [портале Azure](http://portal.azure.com) перейдите к базе данных-источнику в связи георепликации.
2. В колонке «База данных SQL» выберите **Все параметры** > **Георепликация**.
3. В списке **ПОЛУЧАТЕЛИ** выберите базу данных, которая должна стать новым источником, и щелкните **Отработка отказа**.

    ![отработка отказа][2]

4. Выберите **Да**, чтобы запустить отработку отказа.

Команда незамедлительно переведет базу данных-получателя в основную роль.

В течение короткого периода (от 0 до 25 секунд), пока переключаются роли, обе базы данных будут недоступны. Если база данных-источник имеет несколько баз данных-получателей, команда автоматически перенастроится на другие базы данных-получатели для подключения к новой базе данных-источнику. Обычно вся операция занимает меньше минуты.

>[AZURE.NOTE] Если при отправке команды источник находится в сети и выполняет транзакции, некоторые данные могут быть утеряны.


## Дополнительные ресурсы   


- [Отработка аварийного восстановления](sql-database-disaster-recovery-drills.md)
- [Новые возможности георепликации](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Разработка облачных приложений для непрерывности бизнес-процессов с использованием георепликации](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png

<!---HONumber=AcomDC_0720_2016-->