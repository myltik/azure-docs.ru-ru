<properties
   pageTitle="Прозрачное шифрование данных в хранилище данных SQL на портале | Microsoft Azure"
   description="Прозрачное шифрование данных в хранилище данных SQL на портале"
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

# Начало работы с прозрачным шифрованием данных (TDE) в хранилище данных SQL

> [AZURE.SELECTOR]
- [Обзор безопасности](sql-data-warehouse-overview-manage-security.md)
- [Аутентификация](sql-data-warehouse-authentication.md)
- [Шифрование (портал)](sql-data-warehouse-encryption-tde.md)
- [Шифрование (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## Необходимые права

Чтобы включить прозрачное шифрование данных, необходимо иметь права администратора или участника роли dbmanager.

## Включение шифрования

Чтобы включить прозрачное шифрование данных для хранилища данных SQL, выполните указанные ниже действия.

1. Откройте базу данных на [портале Azure](https://portal.azure.com).
2. В колонке базы данных нажмите кнопку **Параметры**.
3. Выберите параметр **Прозрачное шифрование данных**. ![][1]
4. Выберите параметр **Вкл**. ![][2]
5. Щелкните **Сохранить**. ![][3]

## Отключение шифрования

Чтобы отключить прозрачное шифрование данных для хранилища данных SQL, выполните указанные ниже действия.

1. Откройте базу данных на [портале Azure](https://portal.azure.com).
2. В колонке базы данных нажмите кнопку **Параметры**.
3. Выберите параметр **Прозрачное шифрование данных**. ![][1]
4. Выберите параметр **Выкл.**. ![][4]
5. Щелкните **Сохранить**. ![][5]

## Динамические административные представления шифрования

Шифрование может быть подтверждено с помощью следующих динамических административных представлений.

- [sys.databases]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

.<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_0928_2016-->