<properties
	urlDisplayName="How to connect to an Azure SQL database using SQL Server Management Studio (SSMS)"
	pageTitle="Как подключиться к базе данных SQL Azure с помощью среды SSMS | Microsoft Azure"
	metaKeywords=""
	description="Узнайте, как подключиться к базе данных SQL Azure с помощью среды SSMS."
	metaCanonical=""
	services="sql-database"
	documentationCenter=""
	title="How to connect to an Azure SQL database using SSMS"
	authors="sidneyh" solutions=""
	manager="jhubbard" editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/14/2015"
	ms.author="sidneyh" />

# Соединение с SQL Server Management Studio (SSMS)

Для подключения и выполнения запросов к базе данных SQL с помощью SQL Server Management Studio (SSMS), выполните следующие действия.

## Предварительные требования

* SQL Server Management Studio (SSMS) — скачать последнюю версию среды SSMS можно в разделе [Скачать SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
* Пример базы данных AdventureWorks, описанный в статье [Начало работы с Базой данных SQL Microsoft Azure](sql-database-get-started.md).


## Получение полного имени сервера Azure SQL

Для подключения к базе данных требуется полное имя сервера (****имя\_сервера**.database.windows.net*), содержащее базу данных, к которой необходимо подключиться.

1. Войдите на [портал предварительной версии Azure](https://portal.azure.com).
2. Перейдите к базе данных, к которой нужно подключиться.
3. Найдите полное имя сервера:

    ![полное имя сервера][6]

    Используйте полное имя сервера на шаге 3 ниже.



## Подключение к базе данных SQL

1. Откройте среду SSMS.
2. Щелкните **Подключить** > **Компонент Database Engine...**.

    ![Подключить > Компонент Database Engine...][7]

2. В диалоговом окне **Подключение к серверу** в поле **Имя сервера** введите имя сервера в формате *<имя\_сервера>*.**database.windows.net**.
3. В списке **Проверка подлинности** выберите **Проверка подлинности SQL Server**.
4. Введите **имя пользователя** и **пароль**, указанные при создании сервера Базы данных SQL, и нажмите **Подключить**.

	![Диалоговое окно подключения к серверу][2]



### Действия при сбое подключения
Убедитесь, что брандмауэр на созданном вами логическом сервере разрешает подключения с локального компьютера. Подробнее: [Как настроить параметры брандмауэра для базы данных SQL Azure](sql-database-configure-firewall-settings.md).

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
[6]: ./media/sql-database-connect-to-database/server-name.png
[7]: ./media/sql-database-connect-to-database/connect-dbengine.png

<!---HONumber=Sept15_HO3-->