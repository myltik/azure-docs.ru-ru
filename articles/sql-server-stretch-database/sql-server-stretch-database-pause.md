<properties
	pageTitle="Приостановка и возобновление переноса данных (база данных Stretch) | Microsoft Azure"
	description="Узнайте, как приостановить или возобновить перенос данных в Azure."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Приостановка и возобновление переноса данных (база данных Stretch)

Чтобы приостановить или возобновить перенос данных в Azure, выберите **Растяжение** для таблицы в SQL Server Management Studio, а затем выберите **Приостановить**, чтобы приостановить перенос данных, или **Возобновить**, чтобы возобновить перенос данных. Для приостановки или возобновления переноса данных также можно использовать Transact-SQL.

Вы можете приостановить перенос данных, если хотите устранить неполадки на локальном сервере или увеличить доступную пропускную способность сети.

## Приостановка переноса данных

### Использование SQL Server Management Studio для приостановки переноса данных

1.  В SQL Server Management Studio в обозревателе объектов выберите таблицу, совместимую со Stretch, для которой хотите приостановить перенос данных.

2.  Щелкните ее правой кнопкой мыши и выберите пункт **Растяжение**, а затем щелкните **Приостановить**.

### Использование Transact-SQL для приостановки переноса данных
Выполните следующую команду:

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## Возобновление переноса данных

### Использование SQL Server Management Studio для возобновления переноса данных

1.  В SQL Server Management Studio в обозревателе объектов выберите таблицу, совместимую со Stretch, для которой хотите возобновить перенос данных.

2.  Щелкните ее правой кнопкой мыши и выберите пункт **Растяжение**, а затем щелкните **Возобновить**.

### Использование Transact-SQL для возобновления переноса данных
Выполните следующую команду:

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## Как проверить, активна миграция или приостановлена

### Использование SQL Server Management Studio, чтобы проверить, активна миграция или приостановлена
В SQL Server Management Studio откройте **монитор базы данных Stretch** и проверьте значение столбца **Migration State** (Состояние миграции). Дополнительные сведения см. в разделе [Мониторинг и устранение неполадок переноса данных (база данных Stretch)](sql-server-stretch-database-monitor.md).

### Использование Transact-SQL, чтобы проверить, активна миграция или приостановлена
Выполните запрос к представлению каталога **sys.remote\_data\_archive\_tables** и проверьте значение столбца **is\_migration\_paused**. Дополнительные сведения см. в разделе [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## См. также

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx) [Мониторинг и устранение неполадок переноса данных (база данных Stretch)](sql-server-stretch-database-monitor.md)

<!---HONumber=AcomDC_0622_2016-->