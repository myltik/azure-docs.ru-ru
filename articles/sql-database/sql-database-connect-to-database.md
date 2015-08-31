<properties
	pageTitle="Как подключиться к базе данных SQL Azure с помощью среды SSMS | Microsoft Azure"
	description="Узнайте, как подключиться к базе данных SQL Azure с помощью среды SSMS."
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard" editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/15/2015"
	ms.author="sidneyh" />

# Соединение с SQL Server Management Studio

Используйте следующие шаги для установки среды SQL Server Management Studio (SSMS), затем используйте среду SSMS для подключения и запроса к базе данных SQL.

## Предварительные требования
* Пример базы данных AdventureWorks Базы данных SQL, описанный в статье [Начало работы с Базой данных SQL Microsoft Azure](sql-database-get-started.md).

## Установка и запуск среды SQL Server Management Studio (SSMS)
1. Перейдите на страницу загрузки [SQL Server 2014 Express](http://www.microsoft.com/download/details.aspx?id=42299), нажмите **Скачать** и выберите либо 32-разрядную (x86), либо 64-разрядную (x64) версию MgmtStudio.

	![MgtmtStudio32BIT или MgmtStudio64BIT][1]
2.	Следуйте указаниям на экране во время установки SSMS с использованием параметров по умолчанию.
3.	После загрузки найдите SQL Server 2014 Management Studio на своем компьютере и запустите SSMS.


## Подключение к базе данных SQL
1. Откройте среду SSMS.
2. В диалоговом окне **Подключение к серверу** в поле **Имя сервера** введите имя сервера в формате *&lt;имя\_сервера>*.**database.windows.net**.
3. В списке **Проверка подлинности** выберите **Проверка подлинности SQL Server**.
4. Введите **имя пользователя** и **пароль**, указанные при создании сервера Базы данных SQL.

	![Диалоговое окно подключения к серверу][2]
5. Нажмите кнопку **Параметры**.
6. В поле **Подключение к базе данных** введите **AdventureWorks** и нажмите кнопку **Подключиться**.

	![Подключение к базе данных][3]

### Действия при сбое подключения
Убедитесь, что брандмауэр на созданном вами логическом сервере разрешает подключения с локального компьютера. Дополнительные сведения см. в разделе [Краткое руководство: настройка параметров брандмауэра (база данных SQL Azure)](https://msdn.microsoft.com/library/azure/jj553530.aspx).

## Запуск образцов запросов

1. В **обозревателе объектов** перейдите к базе данных **AdventureWorks**.
2. Щелкните правой кнопкой мыши базу данных и выберите **Создать запрос**.

	![Новый запрос][4]

3. В окне запроса скопируйте и вставьте следующий код.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Нажмите кнопку **Выполнить**. На следующем снимке экрана показан успешный запрос.

	![Успешное выполнение][5]

## Дальнейшие действия
Для создания баз данных и управления ими можно использовать инструкции Transact-SQL. Дополнительные сведения см. в статьях [СОЗДАНИЕ БАЗЫ ДАННЫХ (база данных SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) и [Управление базой данных SQL Azure с помощью SQL Server Management Studio](sql-database-manage-azure-ssms.md). Также вы можете регистрировать события в хранилище Azure. Подробные сведения см. в статье [Приступая к работе с аудитом базы данных SQL](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png

<!---HONumber=August15_HO8-->