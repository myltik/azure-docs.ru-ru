## <a name="invoking-stored-procedure-for-sql-sink"></a>Вызов хранимой процедуры для приемника SQL
Для копирования данных на сервер SQL Server или в базу данных SQL Azure или SQL Server можно настроить и вызвать указанную пользователем хранимую процедуру с дополнительными параметрами. 

Хранимую процедуру можно использовать, когда встроенные механизмы копирования не подходят. Обычно хранимая процедура используется, когда дополнительную обработку (объединение столбцов, поиск дополнительных значений, вставку данных в несколько таблиц и т. д.) необходимо выполнить до окончательной вставки данных источника в целевую таблицу. 

Вы можете вызывать хранимую процедуру по своему выбору. Следующий пример показывает, как использовать хранимую процедуру для выполнения простой вставки в таблицу в базе данных. 

**Выходной набор данных**

В этом примере type имеет значение SqlServerTable. Задайте для него значение AzureSqlTable, чтобы использовать с базой данных SQL Azure. 

    {
      "name": "SqlOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlLinkedService",
        "typeProperties": {
          "tableName": "Marketing"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

Определите раздел SqlSink в действии копирования JSON следующим образом. Для вызова хранимой процедуры во время вставки данных требуются свойства SqlWriterStoredProcedureName и SqlWriterTableType.

    "sink":
    {
        "type": "SqlSink",
        "SqlWriterTableType": "MarketingType",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
        "storedProcedureParameters":
                {
                    "stringData": 
                    {
                        "value": "str1"     
                    }
                }
    }

В своей базе данных определите хранимую процедуру с тем же именем, что и SqlWriterStoredProcedureName. Она обрабатывает входные данные из указанного источника и вставляет их в выходную таблицу. Обратите внимание, что имя параметра хранимой процедуры должно совпадать с именем tableName, заданным в JSON-файле таблицы.

    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
    AS
    BEGIN
        DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
        INSERT [dbo].[Marketing](ProfileID, State)
        SELECT * FROM @Marketing
    END

В своей базе данных определите тип таблицы с тем же именем, что и SqlWriterTableType. Обратите внимание, что схема типа таблицы должны быть той же, что и схема, возвращаемая входными данными.

    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL
    )

Функциональность хранимой процедуры использует преимущества [параметров с табличным значением](https://msdn.microsoft.com/library/bb675163.aspx).



<!--HONumber=Nov16_HO3-->


