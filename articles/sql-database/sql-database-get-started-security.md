<properties
	pageTitle="Руководство по базам данных SQL: обеспечение безопасности"
	description="Узнайте, как создавать учетные записи пользователей для доступа к базе данных и управления ею."
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/03/2016"
	ms.author="carlrab"/>

# Руководство по базам данных SQL: создание учетных записей пользователей базы данных SQL для доступа к базе данных и управления ею с помощью портала Azure


> [AZURE.SELECTOR]
- [Учебник по началу работы](sql-database-get-started-security.md)
- [Предоставление доступа](sql-database-manage-logins.md)

Из этого руководства вы узнаете, как с помощью портала Azure выполнять следующие операции.

- Вход в базу данных SQL Azure с использованием учетных данных субъекта на уровне сервера
- Создание учетной записи пользователя базы данных SQL
- Предоставление разрешений dbo учетной записи пользователя базы данных SQL в рамках базы данных пользователя
- Подключение к базе данных SQL с помощью учетной записи, которая не является субъектом уровня сервера 

[AZURE.INCLUDE [Вход](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Создание логического сервера базы данных SQL](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Создание базы данных SQL](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Создание базы данных SQL](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Создание базы данных SQL](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## Дальнейшие действия
Теперь, когда вы завершили работу с этим руководством по базам данных SQL, создав учетную запись пользователя и предоставив разрешения dbo пользователя учетной записи, узнайте, как [защитить базу данных SQL](sql-database-manage-logins.md).

<!---HONumber=AcomDC_0615_2016-->