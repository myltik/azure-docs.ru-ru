## Создание нового пользователя базы данных с помощью SSMS

Для выполнения приведенных ниже инструкций требуется: установленная среда SSMS, подключение к базе данных SQL в обозревателе объектов, а также подключение к логическому серверу базы данных SQL в качестве администратора-участника уровня сервера или с помощью учетной записью пользователя с разрешениями на предоставление разрешений пользователя. Кроме того, пользователь должен существовать в базе данных, для которой вы хотите предоставить разрешения dbo.

1. В обозревателе объектов разверните узел базы данных и выберите базу данных с пользователем, которому вы хотите предоставить разрешения dbo.

     ![SQL Server Management Studio: подключение к серверу базы данных SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. Щелкните правой кнопкой мыши базу данных и выберите пункт **Запрос**.

     ![SQL Server Management Studio: подключение к серверу базы данных SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. В окне запроса измените и выполните следующую инструкцию Transact-SQL, чтобы предоставить разрешения dbo указанному пользователю.

    '''ALTER ROLE db\_owner ADD MEMBER user1;

     ![SQL Server Management Studio: подключение к серверу базы данных SQL](./media/sql-database-grant-database-user-dbo-permissions/sql-database-grant-database-user-dbo-permissions-1.png)

<!---HONumber=AcomDC_0420_2016-->