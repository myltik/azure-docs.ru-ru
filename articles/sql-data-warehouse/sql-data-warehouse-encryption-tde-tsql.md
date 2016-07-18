<properties
   pageTitle="Начало работы с прозрачным шифрованием данных (TDE) хранилища данных SQL TSQL | Microsoft Azure"
   description="Начало работы с прозрачным шифрованием данных (TDE) хранилища данных SQL TSQL | Microsoft Azure"
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
   ms.date="06/07/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# Начало работы с прозрачным шифрованием данных (TDE)


> [AZURE.SELECTOR]
- [Обзор безопасности](sql-data-warehouse-overview-manage-security.md)
- [Обнаружение угроз](sql-data-warehouse-security-threat-detection.md)
- [Шифрование (портал)](sql-data-warehouse-encryption-tde.md)
- [Шифрование (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
- [Общие сведения об аудите](sql-data-warehouse-auditing-overview.md)
- [Поддержка клиентов нижнего уровня](sql-data-warehouse-auditing-downlevel-clients.md)


Прозрачное шифрование данных (TDE) хранилища данных SQL Azure помогает защититься от угрозы вредоносных атак за счет шифрования и расшифровки базы данных, связанных резервных копий и файлов журналов транзакций при хранении в реальном времени, не внося изменения в само приложение.

При использовании TDE хранилище всей базы данных шифруется с помощью симметричного ключа, который называется ключом шифрования базы данных. В Базе данных SQL ключ шифрования базы данных защищается встроенным сертификатом сервера. Каждый сервер Базы данных SQL обладает уникальным встроенным сертификатом. Корпорация Майкрософт автоматически изменяет эти сертификаты не реже, чем раз в 90 дней. Алгоритм шифрования, используемый в хранилище данных SQL, это AES-256. Общее описание TDE см. в статье [Прозрачное шифрование данных (TDE)].

##Включение шифрования

Чтобы включить прозрачное шифрование данных для хранилища данных SQL, выполните указанные ниже действия.

1. Подключитесь к *главной* базе данных на сервере, где находится база данных, указав логин администратора или члена роли **dbmanager** в главной базе данных.
2. Выполните следующий оператор для шифрования базы данных:

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

##Отключение шифрования

Чтобы отключить прозрачное шифрование данных для хранилища данных SQL, выполните указанные ниже действия.

1. Подключитесь к *главной* базе данных, указав логин администратора или члена роли **dbmanager** в главной базе данных.
2. Выполните следующий оператор для шифрования базы данных:

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

##Проверка шифрования

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

##Динамические административные представления шифрования  

- [sys.databases][]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys][]


<!--Anchors-->
[Прозрачное шифрование данных (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0706_2016-->