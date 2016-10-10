<properties
   pageTitle="Прозрачное шифрование данных в хранилище данных SQL (T-SQL) | Microsoft Azure"
   description="Прозрачное шифрование данных в хранилище данных SQL (T-SQL)"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# Начало работы с прозрачным шифрованием данных (TDE)


> [AZURE.SELECTOR]
- [Обзор безопасности](sql-data-warehouse-overview-manage-security.md)
- [Аутентификация](sql-data-warehouse-authentication.md)
- [Шифрование (портал)](sql-data-warehouse-encryption-tde.md)
- [Шифрование (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## Необходимые права

Чтобы включить прозрачное шифрование данных, необходимо иметь права администратора или участника роли dbmanager.

## Включение шифрования

Чтобы включить прозрачное шифрование данных для хранилища данных SQL, сделайте следующее.

1. Подключитесь к *главной* базе данных на сервере, где находится база данных, указав логин администратора или члена роли **dbmanager** в главной базе данных.
2. Выполните следующий оператор для шифрования базы данных:

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## Отключение шифрования

Чтобы отключить прозрачное шифрование данных для хранилища данных SQL, сделайте следующее.

1. Подключитесь к *главной* базе данных, указав логин администратора или члена роли **dbmanager** в главной базе данных.
2. Выполните следующий оператор для шифрования базы данных:

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [AZURE.NOTE] Прежде чем вносить изменения в параметры прозрачного шифрования данных, работу приостановленного хранилища данных SQL нужно возобновить.

## Проверка шифрования

Чтобы проверить состояние шифрования для хранилища данных SQL, выполните указанные ниже действия.

1. Подключитесь к *главной* базе данных или к экземпляру базы данных, указав логин администратора или члена роли **dbmanager** в главной базе данных.
2. Выполните следующий оператор для шифрования базы данных:

```sql
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

Результат ```1``` означает зашифрованную, а ```0``` — незашифрованную базу данных.

## Динамические административные представления шифрования  

- [sys.databases][]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0928_2016-->