<properties
	pageTitle="Отключение растяжения базы данных и возвращение удаленных данных | Microsoft Azure"
	description="Узнайте, как отключить растяжение базы данных для таблицы и при необходимости вернуть удаленные данные."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

.<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2016"
	ms.author="douglasl"/>

# Отключение растяжения базы данных и возвращение удаленных данных

Для отключения растяжения базы данных для таблицы выберите **Перенос** для таблицы в SQL Server Management Studio. Затем выберите один из следующих вариантов.

-   **Отключить| Bring data back from Azure**. Copy the remote data for the table from Azure back to SQL Server, then disable Stretch Database for the table. This operation incurs data transfer costs, and it can't be canceled.

-   **Отключить| Leave data in Azure**. Disable Stretch Database for the table.  Abandon the remote data for the table in Azure.

Для отключения растяжения базы данных для таблицы или базы данных также можно использовать Transact-SQL.

После отключения растяжения базы данных для таблицы миграция данных останавливается и результаты запроса больше не включают результаты из удаленной таблицы.

Если вы хотите просто приостановить перенос данных, см. статью [Приостановка и возобновление переноса данных (база данных Stretch)](sql-server-stretch-database-pause.md).

>   [AZURE.NOTE] Отключение базы данных Stretch для таблицы или базы данных не приводит к удалению удаленного объекта. Если вы хотите удалить удаленную таблицу или базу данных, ее необходимо удалить с помощью портала управления Azure. За хранение удаленных объектов в Azure по-прежнему будет взиматься плата, пока вы не удалите их. Дополнительные сведения см. на странице с [ценами на использование базы данных SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Отключение растяжения базы данных для таблицы

### Отключение растяжения базы данных для таблицы с помощью SQL Server Management Studio

1.  В среде SQL Server Management Studio в обозревателе объектов выберите таблицу, для которой необходимо отключить растяжение базы данных.

2.  Щелкните эту таблицу правой кнопкой мыши, выберите пункт **Перенос**, а затем — один из следующих параметров:

    -   **Отключить| Bring data back from Azure**. Copy the remote data for the table from Azure back to SQL Server, then disable Stretch Database for the table. This command can't be canceled.

        >   [AZURE.NOTE] Copying the remote data for the table from Azure back to SQL Server incurs data transfer costs. For more info, see [Data Transfers Pricing Details](https://azure.microsoft.com/pricing/details/data-transfers/).

        After all the remote data has been copied from Azure back to SQL Server, Stretch is disabled for the table.

    -   **Отключить| Leave data in Azure**. Disable Stretch Database for the table.  Abandon the remote data for the table in Azure.

    >   [AZURE.NOTE] Отключение базы данных Stretch для таблицы не приводит к удалению удаленных данных или удаленной таблицы. Если вы хотите удалить удаленную таблицу, ее необходимо удалить с помощью портала управления Azure. За хранение удаленной таблицы в Azure по-прежнему будет взиматься плата, пока вы не удалите ее. Дополнительные сведения см. на странице с [ценами на использование базы данных SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Отключение растяжения базы данных для таблицы с помощью Transact-SQL

-   Чтобы отключить Stretch для таблицы и скопировать удаленные данные для таблицы из Azure обратно в SQL Server, выполните следующую команду. После копирования всех удаленных данных из Azure обратно на SQL Server растяжение для таблицы отключается.

    Эта операция не может быть отменена.

    ```tsql
	USE <Stretch-enabled database name>;
    GO
    ALTER TABLE <Stretch-enabled table name>  
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    GO
    ```
    >   [AZURE.NOTE] При копировании удаленных данных для таблицы из Azure обратно на сервер SQL Server взимается плата за передачу данных. Дополнительные сведения см. на странице с [ценами на передачу данных](https://azure.microsoft.com/pricing/details/data-transfers/).

-   Чтобы отключить растяжение для таблицы и оставить удаленные данные в Azure, выполните следующую команду.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] Отключение базы данных Stretch для таблицы не приводит к удалению удаленных данных или удаленной таблицы. Если вы хотите удалить удаленную таблицу, ее необходимо удалить с помощью портала управления Azure. За хранение удаленной таблицы в Azure по-прежнему будет взиматься плата, пока вы не удалите ее. Дополнительные сведения см. на странице с [ценами на использование базы данных SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Отключение растяжения базы данных для базы данных
Перед отключением растяжения базы данных для базы данных необходимо отключить его для отдельных таблиц базы данных, для которых оно было включено.

### Отключение растяжения базы данных для базы данных с помощью SQL Server Management Studio

1.  В среде SQL Server Management Studio в обозревателе объектов выберите базу данных, для которой необходимо отключить растяжение базы данных.

2.  Щелкните эту базу данных правой кнопкой мыши и последовательно выберите пункты **Задачи**, **Перенос** и **Отключить**.

>   [AZURE.NOTE] Отключение базы данных Stretch для базы данных не приводит к удалению удаленной базы данных. Если вы хотите удалить удаленную базу данных, ее необходимо удалить с помощью портала управления Azure. За хранение удаленной базы данных в Azure по-прежнему будет взиматься плата, пока вы не удалите ее. Дополнительные сведения см. на странице с [ценами на использование базы данных SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Отключение растяжения базы данных для базы данных с помощью Transact-SQL
Выполните следующую команду:

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] Отключение базы данных Stretch для базы данных не приводит к удалению удаленной базы данных. Если вы хотите удалить удаленную базу данных, ее необходимо удалить с помощью портала управления Azure. За хранение удаленной базы данных в Azure по-прежнему будет взиматься плата, пока вы не удалите ее. Дополнительные сведения см. на странице с [ценами на использование базы данных SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Дополнительные материалы

[Параметры ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Приостановите и возобновите растяжение баз данных](sql-server-stretch-database-pause.md)

<!---HONumber=AcomDC_0810_2016-->