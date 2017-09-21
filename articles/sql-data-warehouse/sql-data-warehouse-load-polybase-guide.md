---
title: "Руководство по использованию PolyBase в хранилище данных SQL | Документация Майкрософт"
description: "Правила и рекомендации по использованию PolyBase в сценариях с хранилищем данных SQL."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4757fce1-96b3-48ea-8a51-be1385705f9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 9/13/2017
ms.custom: loading
ms.author: cakarst;barbkess
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 7594a0730477fe3f3bd34b0b6207478de70c7595
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---
# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Руководство по использованию PolyBase в хранилище данных SQL
Это руководство содержит практические сведения об использовании PolyBase в хранилище данных SQL.

Инструкции по началу работы см. в руководстве [Загрузка данных в хранилище данных SQL с помощью PolyBase][Load data with PolyBase].

## <a name="rotating-storage-keys"></a>Ротация ключей хранилищ данных
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



## <a name="load-data-with-external-tables"></a>Загрузка данных с помощью внешних таблиц
В этом примере данные загружаются из хранилища больших двоичных объектов Azure в базу данных хранилища данных SQL.

Прямое хранение данных сокращает время переноса данных для запроса. Хранение данных с индексом columnstore повышает производительность аналитических запросов до 10 раз.

В этом примере для загрузки данных используется инструкция CREATE TABLE AS SELECT. Новая таблица наследует столбцы с именами, указанные в запросе. Таблица наследует типы данных столбцов из определения внешней таблицы.

CREATE TABLE AS SELECT — это высокопроизводительная инструкция Transact-SQL, которая загружает данные одновременно во все вычислительные узлы вашего хранилища данных SQL.  Изначально указания были разработаны для подсистемы вычислений с массовым параллелизмом (MPP) в Analytics Platform System и теперь используются в хранилище данных SQL.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Ознакомьтесь с разделом [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)].

> [!NOTE]
> Загрузка с помощью внешней таблицы может завершиться ошибкой *Запрос прерван — достигнуто максимальное пороговое значение отклонения при чтении из внешнего источника*. Это означает, что внешние данные содержат *«грязные»* записи. Запись данных считается "грязной", если фактические типы данных и количество столбцов не соответствуют определениям столбцов из внешней таблицы или если данные не соответствуют указанному формату внешнего файла. Чтобы устранить эту проблему, убедитесь в правильности определений внешней таблицы и формата внешнего файла, а также в том, что внешние данные соответствуют этим определениям. Если подмножество записей внешних данных "грязные", можно отклонить эти записи для запросов с помощью параметров отклонения в CREATE EXTERNAL TABLE DDL.
> 
> 


## <a name="create-statistics-on-newly-loaded-data"></a>Создание статистики для вновь загруженных данных
Хранилище данных SQL Azure пока не поддерживает автоматическое создание или автоматическое обновление статистики.  Чтобы добиться максимально высокой производительности запросов, крайне важно сформировать статистические данные для всех столбцов всех таблиц после первой загрузки или после любых значительных изменений в данных.  Подробные сведения о работе со статистикой см. в статье [Управление статистикой таблиц в хранилище данных SQL][Statistics].  Ниже приведен краткий пример создания статистики по табличным данным, загруженным в этом примере.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-with-external-tables"></a>Экспорт данных с помощью внешних таблиц
В этом разделе показано, как экспортировать данные из хранилища данных SQL в хранилище BLOB-объектов Azure с помощью внешних таблиц. В этом примере для экспорта данных в параллельном режиме из всех вычислительных узлов используется высокопроизводительная инструкция Transact-SQL CREATE EXTERNAL TABLE AS SELECT.

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
## <a name="isolate-loading-users"></a>Ограничение возможности загрузки для пользователей
Зачастую необходимо иметь несколько пользователей, которые могут загрузить данные в хранилище данных SQL. Так как для использования инструкции [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] требуются разрешения на управление базой данных, в результате у вас будет несколько пользователей с контролем доступа во всех схемах. Чтобы ограничить это, можно использовать инструкцию DENY CONTROL.

Пример. Рассмотрим схемы базы данных schema_A для отдела A и schema_B для отдела B. Задайте пользователей базы данных user_A и user_B в качестве пользователей для загрузки PolyBase в отделе A и B соответственно. Им обоим предоставлены разрешения CONTROL для базы данных.
Теперь создатели схем A и B блокируют свои схемы, используя инструкцию DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   
 Таким образом для user_A и user_B теперь должен быть заблокирован доступ к схеме другого отдела.
 
## <a name="polybase-performance-optimizations"></a>Оптимизация производительности PolyBase
Для достижения оптимальной производительности загрузки с помощью PolyBase рекомендуем сделать следующее:
- Разбейте большие сжатые файлы на сжатые файлы меньшего размера. Типы сжатия, поддерживаемые в настоящее время, не предусматривают возможность разделения. При этом загрузка одного большого файла снижает производительность.
- Чтобы максимально ускорить загрузку, выполняйте ее в round_robin — промежуточную таблицу кучи. Это наиболее эффективный способ перемещения данных с уровня хранения в хранилище данных.
- Все форматы файлов имеют разные характеристики производительности. Чтобы максимально ускорить загрузку, используйте сжатые текстовые файлы с разделителями. Разница между производительностью UTF-8 и UTF-16 будет минимальной.
- Совместите уровень хранения и хранилище данных, чтобы свести задержки к минимуму.
- Увеличьте масштаб хранилища данных, если планируется выполнение большого задания загрузки.

## <a name="polybase-limitations"></a>Ограничения PolyBase
Для PolyBase в хранилище данных SQL действуют следующие ограничения, которые нужно учитывать при разработке задания загрузки:
- Ширина одной строки не должна превышать 1 000 000 байт. Это требование не зависит от определенной схемы таблицы, включая столбцы (n)varchar(max). Это означает, что для внешних таблиц максимальная ширина столбцов (n)varchar(max) составляет 1 000 000 байт, а не 2 ГБ (ограничение, определенное для типа данных).
- При экспорте данных в формат файлов ORC из SQL Server или хранилища данных SQL Azure число текстовых столбцов большого объема может быть ограничено до 50 из-за ошибок нехватки памяти в Java. Чтобы решить эту проблему, экспортируйте только подмножество столбцов.





## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о перемещении данных в хранилище данных SQL см. в статье [Перенос решения в хранилище данных SQL][data migration overview].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[data migration overview]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
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

<!-- External Links -->

