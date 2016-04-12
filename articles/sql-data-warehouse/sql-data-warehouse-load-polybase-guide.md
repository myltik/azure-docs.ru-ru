<properties
   pageTitle="Руководство по использованию PolyBase в хранилище данных SQL | Microsoft Azure"
   description="Правила и рекомендации по использованию PolyBase в сценариях с хранилищем данных SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="sahajs;barbkess;sonyama"/>


# Руководство по использованию PolyBase в хранилище данных SQL

Это руководство содержит практические сведения об использовании PolyBase в хранилище данных SQL.

Инструкции по началу работы см. в руководстве [Загрузка данных с помощью PolyBase][].


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

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE] Запрос к внешней таблице может завершиться с ошибкой *Запрос прерван — достигнуто максимальное число отклонений при чтении из внешнего источника*. Это означает, что внешние данные содержат *"грязные"* записи. Запись данных считается "грязной", если фактические типы данных и количество столбцов не соответствуют определениям столбцов из внешней таблицы или если данные не соответствуют указанному формату внешнего файла. Чтобы устранить эту проблему, убедитесь в правильности определений внешней таблицы и формата внешнего файла, а также в том, что внешние данные соответствуют этим определениям. Если подмножество записей внешних данных "грязные", можно отклонить эти записи для запросов с помощью параметров отклонения в CREATE EXTERNAL TABLE DDL.


## Загрузка данных из хранилища больших двоичных объектов Azure
В этом примере данные загружаются из хранилища больших двоичных объектов Azure в базу данных хранилища данных SQL.

Прямое хранение данных сокращает время переноса данных для запроса. Хранение данных с индексом columnstore повышает производительность аналитических запросов до 10 раз.

В этом примере для загрузки данных используется инструкция CREATE TABLE AS SELECT. Новая таблица наследует столбцы с именами, указанные в запросе. Таблица наследует типы данных столбцов из определения внешней таблицы.

CREATE TABLE AS SELECT — это высокопроизводительная инструкция Transact-SQL, которая загружает данные одновременно во все вычислительные узлы вашего хранилища данных SQL. Инструкция была изначально разработана для подсистемы MPP в Analytics Platform System и теперь используется в хранилище данных SQL.

```sql
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

## Создание статистики для вновь загруженных данных

Хранилище данных SQL Azure пока не поддерживает автоматическое создание или автоматическое обновление статистики. Чтобы добиться максимально высокой производительности запросов, крайне важно сформировать статистические данные для всех столбцов всех таблиц после первой загрузки или после любых значительных изменений в данных. Подробные сведения о работе со статистикой см. в разделе [Статистика][] из группы разделов по разработке. Ниже приведен краткий пример создания статистики по табличным данным, загруженным в этом примере.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## Экспорт данных в хранилище BLOB-объектов Azure
В этом разделе рассказывается, как экспортировать данные из хранилища данных SQL в хранилище BLOB-объектов Azure. В этом примере для экспорта данных в параллельном режиме из всех вычислительных узлов используется высокопроизводительная инструкция Transact-SQL CREATE EXTERNAL TABLE AS SELECT.

В следующем примере создается внешняя таблица Weblogs2014 с помощью определений столбцов и данных из таблицы dbo.Weblogs. Определение внешней таблицы хранится в хранилище данных SQL, а результаты инструкции SELECT экспортируются в каталог /archive/log2014/ контейнера BLOB-объектов, указанного в источнике данных. Данные экспортируются в указанном текстовом формате.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


## Обход требования PolyBase UTF-8
В настоящее время PolyBase поддерживает загрузку файлов данных с кодировкой UTF-8. Поскольку UTF-8 использует ту же кодировку символов, что и ASCII, PolyBase будет также поддерживать загрузку данных с кодировкой ASCII. Однако PolyBase не поддерживает кодировку символов, подобную UTF-16 или Юникод или символы расширенной кодировки ASCII. Символы в расширенной кодировке ASCII включают символы с диакритическими знаками, например с умляутом, который часто используется в немецком языке.

Чтобы обойти это требование, лучше перевести набор символов в кодировку UTF-8.

Для этого существует несколько способов. Ниже приведены два подхода, реализуемые при помощи Powershell.

### Простой пример для небольших файлов

Ниже приведен простой одностроковый скрипт Powershell, создающий файл.

```PowerShell
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

Однако, хотя этот способ является самым простым способом изменения кодировки данных, он ни в коем случае не является самым эффективным. Следующий пример с потоковым вводом-выводом позволяет получить тот же результат намного быстрее.

### Пример с потоковым вводом-выводом для более крупных файлов

Следующий пример кода более сложен, однако поскольку он передает из источника в целевой объект строки данных в потоке, то обладает гораздо большей эффективностью. Используйте этот подход для более крупных файлов.

```PowerShell
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
Дополнительные сведения о перемещении данных в хранилище данных SQL см. в статье [Общие сведения о переносе данных][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Загрузка данных с помощью PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[development overview]: sql-data-warehouse-overview-develop.md
[Статистика]: sql-data-warehouse-develop-statistics.md
[Общие сведения о переносе данных]: sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!---HONumber=AcomDC_0330_2016-->