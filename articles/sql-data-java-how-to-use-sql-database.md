<properties linkid="develop-java-sql-azure" urlDisplayName="SQL Database" pageTitle="How to use SQL Azure (Java) - Azure feature guide" metaKeywords="" description="Learn how to use the Azure SQL Database from Java code. " metaCanonical="" services="sql-database" documentationCenter="Java" title="How to Use Azure SQL Database in Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Использование базы данных SQL Azure в Java

Ниже показано, как использовать базу данных SQL Azure с Java. Для простоты показаны примеры для командной строки, но очень похожие действия будут уместны для веб-приложений, размещенных в локальной среде, Azure или в других средах. В этом руководстве освещается создание сервера и базы данных с помощью [портала управления][портала управления].

## Что такое база данных SQL Azure

База данных SQL Azure предоставляет собой систему управления реляционной базой данных для Azure и основана на технологии SQL Server. С экземпляром базы данных SQL можно легко подготовить и развернуть решения реляционных баз данных в облаке и воспользоваться преимуществами центра распределенных данных, которые представляют собой корпоративные преимущества доступности, масштабируемости и безопасности вместе со встроенной защитой данных и возможностями самостоятельной регенерации.

## Оглавление

-   [Основные понятия][Основные понятия]
-   [Предварительные требования][Предварительные требования]
-   [Создание базы данных SQL Azure][Создание базы данных SQL Azure]
-   [Определение строки подключения для базы данных SQL][Определение строки подключения для базы данных SQL]
-   [Разрешение доступа к диапазону IP-адресов][Разрешение доступа к диапазону IP-адресов]
-   [Использование базы данных SQL Azure в Java][Использование базы данных SQL Azure в Java]
-   [Взаимодействие с базой данных SQL Azure из кода][Взаимодействие с базой данных SQL Azure из кода]
-   [Создание таблицы][Создание таблицы]
-   [Создание индекса для таблицы][Создание индекса для таблицы]
-   [Вставка строк][Вставка строк]
-   [Извлечение строк][Извлечение строк]
-   [Извлечение строк с использованием предложения WHERE][Извлечение строк с использованием предложения WHERE]
-   [Получение количества строк][Получение количества строк]
-   [Обновление строк][Обновление строк]
-   [Удаление строк][Удаление строк]
-   [Проверка существования таблицы][Проверка существования таблицы]
-   [Удаление индекса][Удаление индекса]
-   [Удаление таблицы][Удаление таблицы]
-   [Использование базы данных SQL в Java в развертывании Azure][Использование базы данных SQL в Java в развертывании Azure]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="concepts"></span></a>Основные понятия

Поскольку база данных SQL Azure построена на основе технологий SQL Server, доступ к базе данных SQL из Java очень похож на доступ из Java к SQL Server. Можно разработать приложение локально (с использованием SQL Server), а затем подключиться к базе данных SQL, изменив только строку подключения. Вы можете использовать для своего приложения драйвер JDBC для SQL Server. Но существует ряд различий между базой данных SQL и SQL Server, которые могут повлиять на работу приложения. Дополнительные сведения см. в разделе [Рекомендации и ограничения (база данных SQL)][Рекомендации и ограничения (база данных SQL)].

Дополнительные ресурсы для базы данных SQL см. в разделе [Дальнейшие действия][Дальнейшие действия].

## <span id="prerequisites"></span></a>Предварительные требования

Ниже перечислены необходимые условия для использования базы данных SQL с языком Java.

-   Java Developer Kit (JDK), версия 1.6 или более поздняя.
-   Подписка на Azure, которую можно получить на веб-странице <http://www.microsoft.com/windowsazure/offers/>.
-   Если вы используете Eclipse, потребуется интегрированная среда разработки Eclipse для разработчиков Java EE, версии Indigo или более поздней. Можно загрузить с веб-страницы <http://www.eclipse.org/downloads/>. Вам также понадобится подключаемый модуль Azure для Eclipse с Java (от Microsoft Open Technologies). В процессе установки этого подключаемого модуля убедитесь, что в его состав входит Microsoft JDBC Driver 4.0 для SQL Server. Дополнительные сведения см. в разделе [Установка подключаемого модуля Azure для Eclipse с Java (от Microsoft Open Technologies)][Установка подключаемого модуля Azure для Eclipse с Java (от Microsoft Open Technologies)].
-   Если вы не используете Eclipse, вам потребуется Microsoft JDBC Driver 4.0 для SQL Server, который можно загрузить по адресу <http://www.microsoft.com/ru-ru/download/details.aspx?id=11774>.

## <span id="create_db"></span></a>Создание базы данных SQL Azure

Перед использованием базы данных SQL Azure в коде Java необходимо создать сервер базы данных SQL Azure.

1.  Войдите на [портал управления Azure][портал управления Azure].
2.  Нажмите кнопку **Создать**.

    ![Создание новой базы данных SQL][Создание новой базы данных SQL]

3.  Щелкните **База данных SQL**, затем выберите **Настраиваемое создание**.

    ![Создание настраиваемой базы данных SQL][Создание настраиваемой базы данных SQL]

4.  В диалоговом окне **Параметры базы данных** укажите имя базы данных. В данном руководстве в качестве имени базы данных следует использовать **gettingstarted**.
5.  В поле **Сервер** выберите **Создать сервер базы данных SQL**. Для других полей используйте значения по умолчанию.

    ![Параметры базы данных SQL][Параметры базы данных SQL]

6.  Щелкните по стрелке ДАЛЕЕ.
7.  В диалоговом окне **Параметры сервера** укажите имя входа SQL Server. В данном руководстве использовалось значение **MySQLAdmin**. Укажите и подтвердите пароль. Укажите регион и убедитесь, что установлен флажок **Разрешить службам Azure доступ к серверу**.

    ![Параметры SQL Server][Параметры SQL Server]

8.  Нажмите кнопку завершения.

## <span id="determine_connection_string"></span></a>Определение строки подключения для базы данных SQL

1.  Войдите на [портал управления Azure][портал управления Azure].
2.  Нажмите кнопку **Базы данных SQL**.
3.  Щелкните по базе данных, которую хотите использовать.
4.  Щелкните **Показать строки подключения**.
5.  Выделите содержимое для строки подключения **JDBC**.

    ![Определение строки подключения JDBC][Определение строки подключения JDBC]

6.  Щелкните правой кнопкой мыши по выделенному содержимому строки подключения **JDBC** и выберите пункт **Копировать**.
7.  Теперь это значение можно вставить в файл кода, чтобы создать строку подключения в следующей форме. Замените *your\_server* (в двух местах) на текст, скопированный в предыдущем шаге, и замените *your\_password* на значение пароля, указанное при создании учетной записи базы данных SQL. (Также замените значения, присвоенные **database=** и **user=**, если вы не использовали **gettingstarted** и **MySQLAdmin**, соответственно.)

    String connectionString =
     "jdbc:sqlserver://*your\_server*.database.windows.net:1433" + ";" +
     "database=gettingstarted" + ";" +
     ["user=MySQLAdmin@\*your\_server\*]["user=MySQLAdmin@\*your\_server\*]" + ";" +
     "password=*your\_password*" + ";" +
     "encrypt=true" + ";" +
     "hostNameInCertificate=\*.int.mscds.com" + ";" +
     "loginTimeout=30";

Эта строка понадобится нам позднее, а пока важно узнать о тех действиях, которые позволяют определить строку подключения. Кроме того, в зависимости от потребностей приложения вам может не понадобиться использовать параметры **encrypt** и **hostNameInCertificate** и может потребоваться изменить параметр **loginTimeout**.

## <span id="specify_allowed_ips"></span></a>Разрешение доступа к диапазону IP-адресов

1.  Войдите на [портал управления][портал управления Azure].
2.  Нажмите кнопку **Базы данных SQL**.
3.  Щелкните **Серверы**.
4.  Щелкните по серверу, который хотите использовать.
5.  Нажмите кнопку **Управление**.
6.  Нажмите **Настроить**.
7.  В разделе **Разрешенные IP-адреса** введите имя нового правила IP-адресов. Укажите начало и конец диапазона IP-адресов. Для вашего удобства отображается текущий IP-адрес клиента. В следующем примере используется отдельный IP-адрес клиента (ваш IP-адрес будет другим).

    ![Диалоговое окно "Разрешенные IP-адреса"][Диалоговое окно "Разрешенные IP-адреса"]

8.  Нажмите кнопку завершения. Теперь указанным IP-адресам будет разрешен доступ к серверу базы данных.

## <span id="use_sql_azure_in_java"></span></a>Использование базы данных SQL Azure в Java

1.  Создайте проект Java. Для целей данного учебника назовите его **HelloSQLAzure**.
2.  Добавьте в проект файл класса Java с именем **HelloSQLAzure.java**.
3.  Добавьте **Microsoft JDBC Driver для SQL Server** в путь построения.

Если вы используете Eclipse:

    1. Within Eclipse's Project Explorer, right-click the **HelloSQLAzure** project and click **Properties**.
    2. In the left-hand pane of the **Properties** dialog, click **Java Build Path**.
    3. Click the **Libraries** tab, and then click **Add Library**.
    4. In the **Add Library** dialog, select **Microsoft JDBC Driver 4.0 for SQL Server**, click **Next**, and then click **Finish**.
    5. Click **OK** to close the **Properties** dialog.

    If you are not using Eclipse, add the Microsoft JDBC Driver 4.0 for SQL Server JAR to your class path. For related information, see [Using the JDBC Driver](http://msdn.microsoft.com/ru-ru/library/ms378526.aspx).

1.  Добавьте инструкции `import` в код **HelloSQLAzure.java**, как показано ниже.

        import java.sql.*;
        import com.microsoft.sqlserver.jdbc.*;

2.  Укажите строку подключения. Ниже приведен пример. Как и раньше, замените *your\_server* (в двух местах), *your\_user* и *your\_password* на значения для вашего сервера базы данных SQL.

        String connectionString =
            "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
                "database=master" + ";" + 
                "user=your_user@your_server" + ";" +  
                "password=your_password";

Теперь можно добавить код, который будет обеспечивать взаимодействие с сервером базы данных SQL.

## <span id="communicate_from_code"></span></a>Взаимодействие с базой данных SQL Azure из кода

В оставшейся части данного раздела приведены примеры, выполняющие следующие действия:

1.  Подключение к серверу базы данных SQL.
2.  Определение инструкции SQL, например, для создания или удаления таблицы, вставки, выбора или удаления строк и т. д.
3.  Выполнение инструкции SQL с помощью вызова **executeUpdate** или **executeQuery**.
4.  Отображение результатов запроса, если это уместно.

Следующие разделы следует читать (и осваивать) в указанном порядке. Первый фрагмент представляет собой полноценный пример; остальные будут основываться на части платформы из полного примера, например инструкции **import**, объявления **class** и **main**, обработка ошибок и закрытие ресурсов.

## <span id="to_create_table"></span></a>Создание таблицы

В следующем коде показано, как создать таблицу с именем **Person**.

    import java.sql.*;
    import com.microsoft.sqlserver.jdbc.*;

    public class HelloSQLAzure {

        public static void main(String[] args) 
        {

            // Connection string for your SQL Database server.
            // Change the values assigned to your_server, 
            // your_user@your_server,
            // and your_password.
            String connectionString = 
                "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
                    "database=gettingstarted" + ";" + 
                    "user=your_user@your_server" + ";" +  
                    "password=your_password";
            
            // The types for the following variables are
            // defined in the java.sql library.
            Connection connection = null;  // For making the connection
            Statement statement = null;    // For the SQL statement
            ResultSet resultSet = null;    // For the result set, if applicable
            
            try
            {
                // Ensure the SQL Server driver class is available.
                Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
            
                // Establish the connection.
                connection = DriverManager.getConnection(connectionString);
            
                // Define the SQL string.
                String sqlString = 
                    "CREATE TABLE Person (" + 
                        "[PersonID] [int] IDENTITY(1,1) NOT NULL," +
                        "[LastName] [nvarchar](50) NOT NULL," + 
                        "[FirstName] [nvarchar](50) NOT NULL)";
            
                // Use the connection to create the SQL statement.
                statement = connection.createStatement();
            
                // Execute the statement.
                statement.executeUpdate(sqlString);
            
                // Provide a message when processing is complete.
                System.out.println("Processing complete.");
            
            }
            // Exception handling
            catch (ClassNotFoundException cnfe)  
            {
                
                System.out.println("ClassNotFoundException " +
                                   cnfe.getMessage());
            }
            catch (Exception e)
            {
                System.out.println("Exception " + e.getMessage());
                e.printStackTrace();
            }
            finally
            {
                try
                {
                    // Close resources.
                    if (null != connection) connection.close();
                    if (null != statement) statement.close();
                    if (null != resultSet) resultSet.close();
                }
                catch (SQLException sqlException)
                {
                    // No additional action if close() statements fail.
                }
            }
            
        }

    }

## <span id="to_create_index"></span></a>Создание индекса для таблицы

Следующий код показывает, как создать индекс с именем **index1** для таблицы **Person** с помощью столбца **PersonID**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = 
            "CREATE CLUSTERED INDEX index1 " + "ON Person (PersonID)";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        statement.executeUpdate(sqlString);

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="to_insert_rows"></span></a>Вставка строк

В следующем коде показано, как добавить строки в таблицу **Person**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = 
            "SET IDENTITY_INSERT Person ON " + 
                "INSERT INTO Person " + 
                "(PersonID, LastName, FirstName) " + 
                "VALUES(1, 'Abercrombie', 'Kim')," + 
                      "(2, 'Goeschl', 'Gerhard')," + 
                      "(3, 'Grachev', 'Nikolay')," + 
                      "(4, 'Yee', 'Tai')," + 
                      "(5, 'Wilson', 'Jim')";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        statement.executeUpdate(sqlString);

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="to_retrieve_rows"></span></a>Извлечение строк

В следующем коде показано, как извлечь строки из таблицы **Person**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = "SELECT TOP 10 * FROM Person";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        resultSet = statement.executeQuery(sqlString);

        // Loop through the results
        while (resultSet.next())
        {
            // Print out the row data
            System.out.println(
                "Person with ID " + 
                resultSet.getString("PersonID") + 
                " has name " +
                resultSet.getString("FirstName") + " " +
                resultSet.getString("LastName"));
            }

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

Приведенный выше код выбирает 10 первых строк из таблицы **Person**. Если требуется возвратить все строки, измените инструкцию SQL следующим образом:

    String sqlString = "SELECT * FROM Person";

## <span id="to_retrieve_rows_using_where"></span></a>Извлечение строк с использованием предложения WHERE

Для получения строк с помощью предложения используйте приведенный выше код, включив предложение в инструкцию SQL. Следующая инструкция SQL включает в себя предложение для строк, у которых **FirstName** имеет значение **Jim**.

    // Define the SQL string.
    String sqlString = "SELECT * FROM Person WHERE FirstName='Jim'";

Предложения WHERE можно использовать при извлечении данных счетчиков, обновлении строк или удалении строк.

## <span id="to_retrieve_row_count"></span></a>Получение количества строк

В следующем коде показано, как получить число строк из таблицы **Person**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

    // Define the SQL string.
        String sqlString = "SELECT COUNT (PersonID) FROM Person";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        resultSet = statement.executeQuery(sqlString);

        // Print out the returned number of rows.
        while (resultSet.next())
        {
            System.out.println("There were " + 
                             resultSet.getInt(1) +
                             " rows returned.");
        }

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="to_update_rows"></span></a>Обновление строк

В следующем коде показано, как обновить строки. В этом примере значение **LastName** изменяется на **Kim** для всех строк, где **FirstName** имеет значение **Jim**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = 
            "UPDATE Person " + "SET LastName = 'Kim' " + "WHERE FirstName='Jim'";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();
        
        // Execute the statement.
        statement.executeUpdate(sqlString);

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }// Exception handling and resource closing not shown...

## <span id="to_delete_rows"></span></a>Удаление строк

В следующем коде показано, как удалить строки. В этом примере удаляются все строки, где **FirstName** имеет значение **Jim**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = 
            "DELETE from Person " + 
                "WHERE FirstName='Jim'";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        statement.executeUpdate(sqlString);

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="to_check_table_existence"></span></a>Проверка существования таблицы

В следующем коде показано, как определить, существует ли таблица.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = 
            "IF EXISTS (SELECT 1 " +
                "FROM sysobjects " + 
                "WHERE xtype='u' AND name='Person') " +
                "SELECT 'Person table exists.'" +
                "ELSE  " +
                "SELECT 'Person table does not exist.'";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        resultSet = statement.executeQuery(sqlString);

        // Display the result.
        while (resultSet.next())
        {
            System.out.println(resultSet.getString(1));
        }

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="to_drop_index"></span></a>Удаление индекса

Следующий код показывает, как удалить индекс с именем **index1** для таблицы **Person**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +
            "database=gettingstarted" + ";" +
            "user=your_user@your_server" + ";" +
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = 
            "DROP INDEX index1 " + 
                "ON Person";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        statement.executeUpdate(sqlString);

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="to_drop_table"></span></a>Удаление таблицы

В следующем коде показано, как удалить таблицу с именем **Person**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = "DROP TABLE Person";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        statement.executeUpdate(sqlString);

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="using_in_azure"></span></a>Использование базы данных SQL в Java в развертывании Azure

Чтобы использовать базу данных SQL в Java в развертывании Azure, кроме указания Microsoft JDBC Driver 4.0 для SQL Server в виде библиотеки в пути класса, как показано выше, необходимо упаковать его в развертывании.

**Упаковка Microsoft JDBC Driver 4.0 для SQL Server при использовании Eclipse**

1.  В обозревателе проектов Eclipse щелкните правой кнопкой мыши по своему проекту и выберите пункт **Свойства**.
2.  В левой части диалогового окна **Свойства** щелкните по элементу **Сборка развертывания** и нажмите кнопку **Добавить**.
3.  В диалоговом окне **Новая директива сборки** щелкните **Записи пути построения Java** и нажмите кнопку **Далее**.
4.  Выберите **Microsoft JDBC Driver 4.0 для SQL Server** и нажмите кнопку **Готово**.
5.  Нажмите **OK**, чтобы закрыть диалоговое окно **Свойства**.
6.  Экспортируйте WAR-файл проекта в папку approot и заново постройте проект Azure в соответствии с процедурой, описанной в разделе [Создание приложения Hello World с использованием подключаемого модуля Azure для Eclipse с помощью Java (Microsoft Open Technologies)][Создание приложения Hello World с использованием подключаемого модуля Azure для Eclipse с помощью Java (Microsoft Open Technologies)]. В этом разделе также описывается выполнение приложения в эмуляторе вычислений и в Azure.

**Упаковка Microsoft JDBC Driver 4.0 для SQL Server, когда Eclipse не используется**

-   Убедитесь, что библиотека Microsoft JDBC Driver 4.0 для SQL Server включена в ту же роль Azure, что и ваше приложение Java, и добавлена в путь класса приложения.

## <span id="nextsteps"></span></a>Дальнейшие действия

Для получения дополнительных сведений о Microsoft JDBC Driver для SQL Server см. раздел [Обзор драйвера JDBC][Обзор драйвера JDBC]. Для получения дополнительных сведений о базе данных SQL см. раздел [Обзор базы данных SQL][Обзор базы данных SQL].

  [портала управления]: https://windows.azure.com
  [Основные понятия]: #concepts
  [Предварительные требования]: #prerequisites
  [Создание базы данных SQL Azure]: #create_db
  [Определение строки подключения для базы данных SQL]: #determine_connection_string
  [Разрешение доступа к диапазону IP-адресов]: #specify_allowed_ips
  [Использование базы данных SQL Azure в Java]: #use_sql_azure_in_java
  [Взаимодействие с базой данных SQL Azure из кода]: #communicate_from_code
  [Создание таблицы]: #to_create_table
  [Создание индекса для таблицы]: #to_create_index
  [Вставка строк]: #to_insert_rows
  [Извлечение строк]: #to_retrieve_rows
  [Извлечение строк с использованием предложения WHERE]: #to_retrieve_rows_using_where
  [Получение количества строк]: #to_retrieve_row_count
  [Обновление строк]: #to_update_rows
  [Удаление строк]: #to_delete_rows
  [Проверка существования таблицы]: #to_check_table_existence
  [Удаление индекса]: #to_drop_index
  [Удаление таблицы]: #to_drop_table
  [Использование базы данных SQL в Java в развертывании Azure]: #using_in_azure
  [Дальнейшие действия]: #nextsteps
  [портал управления Azure]: https://manage.windowsazure.com
  [Создание новой базы данных SQL]: ./media/sql-data-java-how-to-use-sql-database/WA_New.png
  [Создание настраиваемой базы данных SQL]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_DB_Create.png
  [Параметры базы данных SQL]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_1.png
  [Параметры SQL Server]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_2.png
  [Определение строки подключения JDBC]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_JDBC_ConnectionString.png
  [Диалоговое окно "Разрешенные IP-адреса"]: ./media/sql-data-java-how-to-use-sql-database/WA_Allowed_IPs.png
  [Обзор драйвера JDBC]: http://msdn.microsoft.com/ru-ru/library/ms378749.aspx
  [Обзор базы данных SQL]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee336241.aspx
