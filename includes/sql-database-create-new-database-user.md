

## <a name="create-new-database-user-using-ssms"></a>Создание нового пользователя базы данных с помощью SSMS
Чтобы создать нового пользователя базы данных в существующей базе данных с помощью SSMS, выполните описанные ниже действия. 

Для выполнения этих действий требуется: подключение к базе данных SQL в обозревателе объектов с помощью SSMS, а также подключение к логическому серверу базы данных SQL в качестве администратора-участника уровня сервера или с помощью учетной записи пользователя с разрешениями на создание пользователей. 

1. В обозревателе объектов разверните узел базы данных и выберите базу данных, в которой вы хотите создать учетную запись.
   
     ![SQL Server Management Studio: подключение к серверу базы данных SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)
2. Щелкните правой кнопкой мыши выбранную базу и выберите пункт **Запрос**.
   
     ![SQL Server Management Studio: подключение к серверу базы данных SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)
3. В окне запроса измените и выполните следующую инструкцию Transact-SQL, чтобы создать пользователя в базе данных. 
   
    ```CREATE USER user1 WITH PASSWORD ='p@ssw0rd1';;
    ```
   
     ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-3.png)

