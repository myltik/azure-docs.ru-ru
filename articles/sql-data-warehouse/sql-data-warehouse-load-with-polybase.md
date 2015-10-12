<properties
   pageTitle="Учебник по работе с PolyBase в хранилище данных SQL | Microsoft Azure"
   description="Узнайте, что такое средство PolyBase и как его использовать в сценариях работы с хранилищем данных."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="sahajs;barbkess"/>


# Загрузка данных с помощью PolyBase
Технология PolyBase позволяет создавать запросы и объединять данные из разных источников с помощью команд Transact-SQL.

PolyBase позволяет запрашивать данные в хранилище больших двоичных объектов Azure и загружать их в базу данных хранилища данных SQL с помощью следующих действий.

- Создание главного ключа базы данных и учетных данных.
- Создание объектов PolyBase: внешний источник данных, формат внешнего файла и внешняя таблица. 
- Отправка запроса к данным в хранилище больших двоичных объектов Azure.
- Загрузка данных из хранилища больших двоичных объектов Azure в хранилище данных SQL.


## Предварительные требования
Для выполнения этих действий необходимо иметь следующее:

- Учетная запись хранения Azure.
- Данные, хранимые в хранилище больших двоичных объектов Azure в виде текстовых файлов с разделителями.

Сначала вы создадите объекты, которые нужны PolyBase для подключения к данным и создания запросов в хранилище больших двоичных объектов Azure.

## Создание главного ключа базы данных
Подключитесь к пользовательской базе данных на сервере, чтобы создать главный ключ базы данных. Этот ключ используется для шифрования учетных данных на следующем этапе.

```
-- Creating master key
CREATE MASTER KEY;
```

Раздел справки: [СОЗДАНИЕ ГЛАВНОГО КЛЮЧА (Transact-SQL)][].

## Создание учетных данных для определенной базы данных
Для доступа к хранилищу больших двоичных объектов Azure необходимо создать учетные данные для конкретной базы данных, которые будут хранить сведения для проверки подлинности учетной записи хранения Azure. Подключитесь к базе данных хранилища данных и создайте учетные данные базы данных для каждой учетной записи хранения Azure, которая будет использоваться. Укажите имя удостоверения и ключ учетной записи хранения Azure в качестве секрета. Имя удостоверения не влияет на процесс проверки подлинности в хранилище Azure.

Чтобы уточнить наличие учетных данных для конкретной базы, используйте представление sys.database\_credentials, а не sys.credentials, возвращающее только учетные данные сервера.

```
-- Check for existing database-scoped credentials.
SELECT * FROM sys.database_credentials;

-- Create a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret 
WITH IDENTITY = 'joe'
,    Secret = '<azure_storage_account_key>'
;
```

Раздел справки: [CREATE CREDENTIAL (Transact-SQL)][].

Чтобы удалить учетные данные на уровне базы данных, используйте следующий синтаксис.

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

Раздел справки: [DROP CREDENTIAL (Transact-SQL)][].

## Создание внешнего источника данных
Внешний источник данных — это объект базы данных, который хранит расположение данных хранилища больших двоичных объектов Azure и сведения о доступе. Необходимо определить источник данных для каждого контейнера хранилища Azure.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH
(
    TYPE = HADOOP
,   LOCATION ='wasbs://mycontainer@test.blob.core.windows.net'
,   CREDENTIAL = ASBSecret
)
;
```

Раздел справки: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

Чтобы удалить внешний источник данных, используется следующий синтаксис:

```
-- Dropping external data source
DROP EXTERNAL DATA SOURCE azure_storage
;
```

Раздел справки: [DROP EXTERNAL DATA SOURCE (Transact-SQL)][].

## Создание формата внешнего файла
Формат внешнего файла является объектом базы данных, который указывает формат внешних данных. В данном случае у нас есть несжатые данные в текстовом файле, поля в котором разделены с помощью символа вертикальной черты (|).

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT 
,	FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|'
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
)
;
```

PolyBase может работать со сжатыми и несжатыми данными в виде текста с разделителями в формате Hive RCFILE и HIVE ORC.

Раздел справки: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

Чтобы удалить формат внешнего файла, используется следующий синтаксис:

```
-- Dropping external file format
DROP EXTERNAL FILE FORMAT text_file_format
;
```
Раздел справки: [DROP EXTERNAL FILE FORMAT (Transact-SQL)][].

## Создание внешней таблицы

Определение внешней таблицы схоже с определением реляционной таблицы. Ключевая разница — расположение и формат данных. Определение внешней таблицы сохраняется в базе данных хранилища данных SQL. Данные хранятся в расположении, указанном источником данных.

Параметр LOCATION указывает путь к данным из корня источника данных. В этом примере данные располагаются по адресу wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/. Все файлы для одной таблицы должны находиться в одной логической папке в большом двоичном объекте Azure.

При необходимости можно также указать параметры REJECT (REJECT\_TYPE, REJECT\_VALUE, REJECT\_SAMPLE\_VALUE), определяющие, как PolyBase будет обрабатывать измененные записи, полученные от внешнего источника данных.

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL 
,    [CustomerKey]   int    NOT NULL 
,    [GeographyKey]  int        NULL 
,    [Speed]         float  NOT NULL 
,    [YearMeasured]  int    NOT NULL
)
WITH 
(
    LOCATION    = '/Demo/'
,   DATA_SOURCE = azure_storage
,   FILE_FORMAT = text_file_format      
)
;
```

> [AZURE.NOTE]Обратите внимание, что в настоящее время нельзя создавать статистику по внешней таблице.

Раздел справки: [CREATE EXTERNAL TABLE (Transact-SQL)][].

Объекты, созданные недавно, хранятся в базе данных хранилища данных SQL. Их можно просмотреть в обозревателе объектов средств SQL Server Data Tools (SSDT).

Чтобы удалить внешнюю таблицу, необходимо использовать следующий синтаксис:

```
--Dropping external table
DROP EXTERNAL TABLE [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE]При удалении внешней таблицы необходимо использовать `DROP EXTERNAL TABLE`. Использовать `DROP TABLE` **невозможно**.

Раздел справки: [DROP EXTERNAL TABLE (Transact-SQL)][].

Также стоит отметить, что внешние таблицы видимы в представлениях каталогов как в `sys.tables`, так и (более подробно) `sys.external_tables`.

## Ротация ключей хранилищ данных

Время от времени вам потребуется изменять ключи доступа к хранилищу больших двоичных объектов по соображениям безопасности.

Самым элегантным способом выполнить эту задачу является процесс, который называется "ротация ключей". Возможно, вы уже заметили, что для учетной записи хранения больших двоичных объектов имеется два ключа хранения. Это необходимо для перехода

Ротация ключей учетной записи хранения Azure представляет собой простой процесс из трех шагов

1. Создайте второй набор учетных данных на уровне базы данных на основе вторичного ключа доступа к хранилищу
2. Создайте второй внешний источник данных, основанных на этих учетных данных
3. Удалите и создайте внешнюю таблицу или таблицы, указывающие на только что созданный внешний источник данных

После переноса всех внешних таблиц во внешний источник данных вы можете выполнить задачи очистки.
 
1. Удалите первый внешний источник данных
2. Удалите первые учетные данные, собранные в базе данных, основанные на основном ключе доступа к хранилищу
3. Выполните вход в Azure и снова создайте основной ключ доступа хранилища, который будет готов к следующему переходу

## Отправка запроса для данных хранилища больших двоичных объектов Azure
Запросы к внешним таблицам используют имя таблицы так, как будто это реляционная таблица.


```

-- Query Azure storage resident data via external table. 
SELECT * FROM [ext].[CarSensor_Data]
;

```

> [AZURE.NOTE]Запрос на внешнюю таблицу может завершиться с ошибкой *"Запрос прерван — достигнуто максимальное число отклонений при чтении из внешнего источника"*. Это означает, что внешние данные содержат *"грязные"* записи. Запись данных считается "грязной", если фактические типы данных и количество столбцов не соответствуют определениям столбцов из внешней таблицы или если данные не соответствуют указанному формату внешнего файла. Чтобы устранить эту проблему, убедитесь в правильности определений внешней таблицы и формата внешнего файла, а также в том, что внешние данные соответствуют этим определениям. Если подмножество записей внешних данных "грязные", можно отклонить эти записи для запросов с помощью параметров отклонения в CREATE EXTERNAL TABLE DDL.


## Загрузка данных из хранилища больших двоичных объектов Azure
В этом примере данные загружаются из хранилища больших двоичных объектов Azure в базу данных хранилища данных SQL.

Прямое хранение данных сокращает время переноса данных для запроса. Хранение данных с индексом columnstore повышает производительность аналитических запросов до 10 раз.

В этом примере для загрузки данных используется инструкция CREATE TABLE AS SELECT. Новая таблица наследует столбцы с именами, указанные в запросе. Таблица наследует типы данных столбцов из определения внешней таблицы.

CREATE TABLE AS SELECT — это высокопроизводительная инструкция Transact-SQL, которая заменяет инструкцию INSERT...SELECT. Инструкция была изначально разработана для подсистемы MPP в Analytics Platform System и теперь используется в хранилище данных SQL.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX
,	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS 
SELECT * 
FROM   [ext].[CarSensor_Data]
;
```

См. раздел [CREATE TABLE AS SELECT (Transact-SQL)][].


## Обход требования PolyBase UTF-8
В настоящее время PolyBase поддерживает загрузку файлов данных с кодировкой UTF-8. Поскольку UTF-8 использует ту же кодировку символов, что и ASCII, PolyBase будет также поддерживать загрузку данных с кодировкой ASCII. Однако PolyBase не поддерживает кодировку символов, подобную UTF-16 или Юникод или символы расширенной кодировки ASCII. Символы в расширенной кодировке ASCII включают символы с диакритическими знаками, например с умляутом, который часто используется в немецком языке.

Чтобы обойти это требование, лучше перевести набор символов в кодировку UTF-8.

Для этого существует несколько способов. Ниже приведены два подхода, реализуемые при помощи Powershell.

### Простой пример для небольших файлов

Ниже приведен простой одностроковый скрипт Powershell, создающий файл.
 
```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

Однако, хотя этот способ является самым простым способом изменения кодировки данных, он ни в коем случае не является самым эффективным. Следующий пример с потоковым вводом-выводом позволяет получить тот же результат намного быстрее.

### Пример с потоковым вводом-выводом для более крупных файлов

Следующий пример кода более сложен, однако поскольку он передает из источника в целевой объект строки данных в потоке, то обладает гораздо большей эффективностью. Используйте этот подход для более крупных файлов.

```
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## Дальнейшие действия
Дополнительные советы по разработке см. в статье [Общие сведения о разработке][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[Общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/ru-RU/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/ru-RU/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/ru-RU/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[СОЗДАНИЕ ГЛАВНОГО КЛЮЧА (Transact-SQL)]: https://msdn.microsoft.com/ru-RU/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/ru-RU/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/ru-RU/library/ms189450.aspx

<!---HONumber=Oct15_HO1-->