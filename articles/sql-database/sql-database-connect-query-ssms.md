<properties
	pageTitle="Подключение к базе данных SQL с помощью SQL Server Management Studio | Microsoft Azurе"
	description="Узнайте, как подключиться к базе данных SQL в Azure с помощью SQL Server Management Studio (SSMS). Затем выполните пробный запрос с помощью Transact-SQL (T-SQL)."
	metaCanonical=""
	keywords="подключение к базе данных sql, sql server management studio"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/17/2016"
	ms.author="sstein;carlrab" />

# Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Из этой статьи вы узнаете, как подключиться к базе данных SQL Azure с помощью SQL Server Management Studio (SSMS). После успешного подключения нужно запустить простой запрос Transact-SQL (T-SQL), чтобы проверить соединение с базой данных.

[AZURE.INCLUDE [Установка SSMS](../../includes/sql-server-management-studio-install.md)]

[AZURE.INCLUDE [Подключение SSMS](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


## Запуск образцов запросов

Установив подключение к серверу, можно подключиться к базе данных и выполнить образец запроса. Если вы новичок и еще не знаете, как писать запросы, см. статью [Tutorial: Writing Transact-SQL Statements](https://msdn.microsoft.com/library/ms365303.aspx) (Учебник. Создание инструкций Transact-SQL).

1. В **обозревателе объектов** перейдите к базе данных на сервере, например к базе данных **AdventureWorks**.
2. Щелкните правой кнопкой мыши базу данных и выберите **Создать запрос**.

	![Новый запрос Подключение к базе данных SQL с помощью SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. В окне запроса скопируйте и вставьте следующий код.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Нажмите кнопку **Выполнить**.

	![Успешно. Подключение к базе данных SQL с помощью SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Дальнейшие действия

Инструкции T-SQL можно использовать для создания баз данных в Azure и управления ими таким же образом, как и в SQL Server. Если вы знаете, как использовать T-SQL в SQL Server, ознакомьтесь с отличиями, приведенными в статье [Сведения об инструкциях Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md).

Если вы не знакомы с T-SQL, см. [Руководство по написанию инструкций Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) и [Справочник по Transact-SQL (ядро СУБД)](https://msdn.microsoft.com/library/bb510741.aspx).

Сведения о том, как создавать пользователей и администраторов базы данных, см. в статье [Руководство по базам данных SQL: создание учетных записей пользователей базы данных SQL для доступа к базе данных и управления ею с помощью портала Azure](sql-database-get-started-security.md).

Дополнительные сведения о решении SSMS см. в статье [Use SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (Использование SQL Server Management Studio).

<!---HONumber=AcomDC_0824_2016-->