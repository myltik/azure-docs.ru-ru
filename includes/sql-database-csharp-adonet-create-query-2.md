
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>Пример программы C#

В следующих разделах этой статьи представлена программа C#, в которой используется ADO.NET для отправки инструкций Transact-SQL в базу данных SQL. Программа C# выполняет следующие действия:

1. [подключается к базе данных SQL с помощью ADO.NET](#cs_1_connect);
2. [создает таблицы](#cs_2_createtables);
3. [заполняет таблицы данными, выполняя инструкции T-SQL INSERT](#cs_3_insert);
4. [обновляет данные с помощью соединения](#cs_4_updatejoin);
5. [удаляет данные с помощью соединения](#cs_5_deletejoin);
6. [выбирает данные с помощью соединения](#cs_6_selectrows);
7. разрывает подключение (с удалением всех временных таблиц из базы данных tempdb).

Программа C# содержит:

- код C# для подключения к базе данных;
- методы, которые возвращают исходный код T-SQL;
- два способа отправки кода T-SQL в базу данных.

#### <a name="to-compile-and-run"></a>Компиляция и запуск

Программа C# логически является одним CS-файлом. Но в нашем примере она физически разделена на несколько блоков кода для удобства просмотра и изучения каждого блока. Чтобы скомпилировать и запустить программу, выполните инструкции ниже.

1. Создайте проект C# в Visual Studio.
    - Проект должен быть *консольным* приложением примерно со следующей иерархией: **Шаблоны**>**Visual C#**>**Классическое приложение Windows**>**Консольное приложение (.NET Framework)**.
3. В файле **Program.cs** удалите небольшие строки кода в начале.
3. Скопируйте и вставьте в Program.cs каждый из следующих блоков в той же последовательности, в которой они приведены ниже.
4. В файле Program.cs измените указанные ниже значения в методе **Main**:

   - **cb.DataSource**
   - **cd.UserID**
   - **cb.Password**
   - **InitialCatalog**

5. Убедитесь, что на сборку **System.Data.dll** есть ссылка. Для этого разверните узел **Ссылки** на панели **обозревателя решений**.
6. Чтобы создать программу в Visual Studio, щелкните меню **Сборка**.
7. Чтобы запустить программу из Visual Studio, нажмите кнопку **Запустить**. Результаты отчета отображаются в окне cmd.exe.

> [!NOTE]
> Вы можете отредактировать T-SQL, чтобы добавить начальные **#** к именам таблиц, чтобы они создавались как временные таблицы в **tempdb**. Это удобно при демонстрации, когда тестовая база данных недоступна. Временные таблицы автоматически удаляются при закрытии подключения. Во временных таблицах ключевые слова REFERENCES для внешних ключей не применяются.
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>Блок C# 1: подключение с помощью ADO.NET

- [Далее](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>Блок C# 2: T-SQL для создания таблиц

- [Назад](#cs_1_connect) &nbsp; / &nbsp; [Далее](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>Схема отношения элементов (ERD)

Предыдущие инструкции CREATE TABLE включают ключевое слово **REFERENCES** для создания отношения *внешнего ключа* (FK) между двумя таблицами.  При использовании tempdb закомментируйте ключевое слово `--REFERENCES` с помощью пары начальных тире.

На следующей схеме ERD представлено отношение между двумя таблицами. Значения в *дочернем* столбце #tabEmployee.DepartmentCode ограничены значениями *родительского* столбца #tabDepartment.Department.

![Схема ERD с внешним ключом](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>Блок C# 3: T-SQL для вставки данных

- [Назад](#cs_2_createtables) &nbsp; / &nbsp; [Далее](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>Блок C# 4: T-SQL для обновления соединения

- [Назад](#cs_3_insert) &nbsp; / &nbsp; [Далее](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>Блок C# 5: T-SQL для удаления соединения

- [Назад](#cs_4_updatejoin) &nbsp; / &nbsp; [Далее](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>Блок C# 6: T-SQL для выбора строк

- [Назад](#cs_5_deletejoin) &nbsp; / &nbsp; [Далее](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>Блок C# 6b: ExecuteReader

- [Назад](#cs_6_selectrows) &nbsp; / &nbsp; [Далее](#cs_7_executenonquery)

Этот метод предназначен для выполнения инструкции T-SQL SELECT, созданной методом **Build_6_Tsql_SelectEmployees**.


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>Блок C# 7: ExecuteNonQuery

- [Назад](#cs_6b_datareader) &nbsp; / &nbsp; [Далее](#cs_8_output)

Этот метод вызывается для операций, которые изменяют информационное содержимое таблиц, не возвращая строки данных.


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>Блок C# 8: вывод фактических результатов тестирования на консоль

- [Назад](#cs_7_executenonquery)

Этот раздел содержит результаты, отправленные программой на консоль. Для разных тестовых запусков изменяются только значения guid.


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```
