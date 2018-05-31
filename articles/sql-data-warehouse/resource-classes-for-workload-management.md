---
title: Классы ресурсов для управления рабочими нагрузками в хранилище данных SQL Azure | Документация Майкрософт
description: Руководство по использованию классов ресурсов для управления параллелизмом и вычислительными ресурсами для запросов в хранилище данных SQL Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/26/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 09fd39865a52767195ebf7dad13f24d883af476a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192787"
---
# <a name="workload-management-with-resource-classes-in-azure-sql-data-warehouse"></a>Управление рабочими нагрузками в хранилище данных SQL Azure с помощью классов ресурсов
Руководство по использованию классов ресурсов для управления памятью и параллелизмом для запросов в хранилище данных SQL Azure.  
 
## <a name="what-is-workload-management"></a>Что такое управление рабочей нагрузкой?
Управление рабочей нагрузкой представляет собой возможность оптимизировать общую производительность всех запросов. Хорошо настроенная рабочая нагрузка позволяет эффективно выполнять запросы и операции загрузки независимо от того, требуют ли они интенсивного использования операций ввода-вывода или вычислительных ресурсов.  Хранилище данных SQL предоставляет возможности управления рабочими нагрузками многопользовательских сред. Хранилище данных не предназначено для мультитенантных рабочих нагрузок.

Производительность хранилища данных зависит от [единиц использования хранилища данных](what-is-a-data-warehouse-unit-dwu-cdwu.md). 

- Сведения о пределах параллелизма и ограничениях на объем памяти для всех профилей производительности см. в статье [Ограничения параллелизма и памяти для хранилища данных SQL Azure](memory-and-concurrency-limits.md).
- Корректировать производительность можно с помощью [вертикального масштабирования](quickstart-scale-compute-portal.md).

Производительность запроса определяется классом ресурсов запроса. Далее в этой статье описываются классы ресурсов и способ их настройки.

## <a name="what-are-resource-classes"></a>Что такое классы ресурсов?
Производительность запроса определяется классом ресурсов пользователя.  Классы ресурсов представляют собой предопределенные ограничения ресурсов в хранилище данных SQL Azure, управляющие вычислительными ресурсами и параллелизмом для выполнения запроса. Классы ресурсов позволяют управлять рабочей нагрузкой путем установки ограничений на количество параллельно выполняющихся запросов и вычислительных ресурсов, назначенных для каждого запроса. Между памятью и уровнем параллелизма существует компромисс.

- Небольшие классы ресурсов уменьшают максимальный объем памяти для каждого запроса, но обеспечивают больший параллелизм.
- Большие классы ресурсов увеличивают максимальный объем памяти для каждого запроса, но обеспечивают меньший параллелизм. 

Существуют два типа классов ресурсов.

- Статические классы ресурсов хорошо подходят для повышения уровня параллелизма операций с наборами данных фиксированного размера.
- Динамические классы ресурсов хорошо подходят для наборов данных, размер и требуемая производительность которых увеличивается по мере повышения уровня обслуживания.   

Классы ресурсов используют слоты выдачи для оценки потребления ресурсов.  [Слоты выдачи](#concurrency-slots) описаны в этой статье далее. 

- Сведения об использовании ресурсов для классов ресурсов см. в статье [Ограничения параллелизма и памяти для хранилища данных SQL Azure](memory-and-concurrency-limits.md#concurrency-maximums).
- Чтобы настроить класс ресурсов, вы можете выполнить запрос под именем другого пользователя или [изменить членство в классе ресурсов текущего пользователя](#change-a-users-resource-class). 

### <a name="static-resource-classes"></a>Статические классы ресурсов
Статические классы ресурсов выделяют один объем памяти независимо от текущего уровня производительности, измеряемого в [единицах использования хранилища данных](what-is-a-data-warehouse-unit-dwu-cdwu.md). Так как запросы получают одинаковое распределение памяти независимо от уровня производительности, [масштабирование хранилища данных](quickstart-scale-compute-portal.md) позволяет выполнять большее количество запросов в классе ресурсов.  Статические классы ресурсов идеально подходят, если объем данных известен и постоянен.

Статические классы ресурсов реализуются с помощью следующих предварительно определенных ролей базы данных:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Динамические классы ресурсов
Динамические классы ресурсов выделяют переменный объем памяти в зависимости от текущего уровня обслуживания. Хотя статические классы ресурсов подходят для обеспечения параллелизма и статических томов данных, динамические классы ресурсов лучше подходят для растущих или переменных объемов данных.  При увеличении масштаба уровня обслуживания запросы автоматически получают больший объем памяти.  

Динамические классы ресурсов реализуются с помощью следующих предварительно определенных ролей базы данных:

- smallrc
- mediumrc
- largerc
- xlargerc 

### <a name="gen2-dynamic-resource-classes-are-truly-dynamic"></a>Динамические классы ресурсов поколения 2 являются действительно динамическими
Изучая динамические классы ресурсов поколения 1 можно обнаружить некоторые сведения, которые несколько усложняют понимания их поведения.

- Класс ресурсов smallrc работает с моделью с фиксированной памятью как статический класс ресурсов.  Запросы Smallrc не получают дополнительную память динамически при повышении уровня обслуживания.
- При изменении уровня обслуживания может увеличиться или уменьшиться доступный уровень параллелизма запросов.
- Масштабирование уровней обслуживания не обеспечивает пропорциональное изменение памяти, выделяемой для одних и тех же классов ресурсов.

Только динамические классы ресурсов **поколения 2** являются действительно динамическими с учетом упомянутого выше.  Для классов ресурсов small, medium, large и xlarge действует новое правило выделения памяти в процентах, "3-10-22-70", **независимо от уровня обслуживания**.  В таблице ниже приведены сводные сведения о процентах выделения памяти и минимальном количестве выполняемых параллельных запросов вне зависимости от уровня обслуживания.

| Класс ресурсов | Процент памяти | Минимальное число одновременных запросов |
|:--------------:|:-----------------:|:----------------------:|
| smallrc        | 3 %                | 32                     |
| mediumrc       | 10 %               | 10                     |
| largerc        | 22 %               | 4.                      |
| xlargerc       | 70 %               | 1                      |


### <a name="default-resource-class"></a>Класс ресурсов по умолчанию
По умолчанию каждый пользователь является членом динамического класса ресурсов — **smallrc**. 

Класс ресурсов администратора службы является фиксированным и его нельзя изменить.  Администратором службы является пользователь, созданный во время процесса подготовки.

> [!NOTE]
> Пользователи или группы, определенные в качестве администраторов Active Directory, также являются администраторами служб.
>
>

## <a name="resource-class-operations"></a>Операции с классом ресурсов

Классы ресурсов предназначены для улучшения производительности управления данными и других действий по управлению. Сложные запросы также лучше всего выполнять с использованием большого класса ресурсов. Например, производительность запросов для больших соединений и сортировок может улучшиться, если класс ресурсов достаточно велик для выполнения запроса в памяти.

### <a name="operations-governed-by-resource-classes"></a>Операции, регулируемые классами ресурсов

Классами ресурсов регулируются следующие операции:

* INSERT-SELECT, UPDATE, DELETE;
* SELECT (при запросе таблиц пользователя)
* ALTER INDEX - REBUILD или REORGANIZE;
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* Загрузка данных
* Операции перемещения данных, осуществляемые службой Data Movement Service (DMS)

> [!NOTE]  
> На инструкции SELECT для динамических административных представлений (DMV) или других системных представлений не распространяются какие-либо ограничения параллелизма. Вы можете следить за системой независимо от количества выполняемых в ней запросов.
> 
> 

### <a name="operations-not-governed-by-resource-classes"></a>Операции, не регулируемые классами ресурсов
Некоторые запросы всегда выполняются в классе ресурса smallrc, несмотря на то, что пользователь является членом большого класса ресурсов. Такие запросы с исключениями не учитываются на уровне ограничений параллелизма. Например, если ограничение параллелизма = 16, многих пользователей можно выбрать в системных представлениях без влияния на доступные слоты выдачи.

Следующие инструкции исключены из классов ресурсов и всегда выполняются в классе ресурса smallrc:

* CREATE или DROP TABLE;
* ALTER TABLE ... SWITCH, SPLIT или MERGE PARTITION;
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE, UPDATE или DROP STATISTICS;
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE, ALTER или DROP USER;
* CREATE, ALTER или DROP PROCEDURE;
* CREATE или DROP VIEW;
* INSERT VALUES
* SELECT (из системных представлений и динамических административных представлений);
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Слоты выдачи
Слоты выдачи — это удобный способ отслеживания ресурсов, доступных для выполнения запросов. Они похожи на билеты, которые вы покупаете, чтобы зарезервировать на концерте места, количество которых ограничено. Общее количество слотов выдачи на хранилище данных определяется уровнем обслуживания. Прежде чем запрос может начать выполнение, он должен иметь возможность зарезервировать достаточное количество слотов выдачи. После завершения запрос освобождает слоты выдачи.  

- Запрос, использующий 10 слотов выдачи, может иметь доступ к в 5 раз большему количеству вычислительных ресурсов, чем запрос, использующий 2 слота выдачи.
- Если для каждого запроса требуется 10 слотов выдачи и имеется 40 слотов, то одновременно могут выполняться только 4 запроса.
 
Только запросы, управляемые ресурсами, используют слоты выдачи. Системные и некоторые простые запросы не используют ни одного слота. Точное количество потребляемых слотов выдачи определяется классом ресурсов запроса.

## <a name="view-the-resource-classes"></a>Просмотр классов ресурсов

Классы ресурсов реализуются как предварительно определенные роли базы данных. Существует два типа классов ресурсов: динамический и статический. Чтобы просмотреть список классов ресурсов, выполните следующий запрос:

```sql
SELECT name 
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Изменение класса ресурсов пользователя

Классы ресурсов реализуются путем назначения пользователям ролей базы данных. Когда пользователь выполняет запрос, он выполняется с классом ресурса пользователя. Например, если пользователь является участником роли базы данных smallrc или staticrc10, тогда запрос выполняется с использованием небольшого объема памяти. Если пользователь базы данных является участником роли базы данных xlargerc или staticrc80, запрос выполняется с использованием большого объема памяти. 

Чтобы увеличить класс ресурсов пользователя, используйте хранимую процедуру [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). 

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Чтобы уменьшить класс ресурсов, используйте хранимую процедуру [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Приоритет класса ресурсов
Пользователи могут быть участниками нескольких классов ресурсов. Если пользователь принадлежит к нескольким классам ресурсов:

- Динамические классы ресурсов имеют приоритет над статическими классами ресурсов. Например, если пользователь является членом класса mediumrc(динамического) и staticrc80 (статического), будут выполнены запросы для класса mediumrc.
- Большие классы ресурсов имеют приоритет над небольшими классами ресурсов. Например, если пользователь является членом классов mediumrc и largerc, будут выполнены запросы для класса largerc. Аналогично, если пользователь является членом классов staticrc20 и statirc80, будут выполнены запросы для выделений ресурсов класса staticrc80.

## <a name="recommendations"></a>Рекомендации
Мы рекомендуем создать пользователя, предназначенного для запуска определенного типа запроса или операций загрузки. Затем необходимо предоставить этому пользователю постоянный класс ресурсов вместо регулярного изменения класса. Учитывая, что статические классы ресурсов позволяют использовать больше возможностей управления рабочими нагрузками, мы рекомендуем сначала использовать этот класс, а не динамический.

### <a name="resource-classes-for-load-users"></a>Классы ресурсов для пользователей, выполняющих загрузку
`CREATE TABLE` использует кластеризованные индексы columnstore по умолчанию. Сжатие данных в индекс columnstore представляет собой операцию, требующую интенсивного использования памяти, а нехватка памяти может повлиять на качество индекса. Таким образом, во время загрузки данных вам потребуется, скорее всего, более высокий класс ресурсов. Чтобы убедиться, что для загрузок используется достаточно памяти, вы можете создать пользователя, предназначенного для выполнения загрузки и назначить этого пользователя более высокому классу ресурсов.

Объем памяти, необходимый для обработки загрузки, напрямую зависит от природы загружаемой таблицы и размера обрабатываемых данных. Дополнительные сведения о требованиях к памяти см. в статье [Максимальное повышение качества группы строк для индекса columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

После определения требований к объему памяти выберите класс, который следует назначить пользователю, выполняющему загрузку, — статический или динамический.

- Используйте статический класс ресурсов, когда требования к объему памяти таблицы варьируются в пределах заданного диапазона. Чтобы выполнить загрузку, требуется определенный объем памяти. При масштабировании хранилища данных загрузкам не требуется дополнительный объем памяти. При использовании статического класса ресурсов выделения памяти остаются неизменными. Такая согласованность позволяет сохранить используемый объем памяти и выполнить дополнительные параллельные запросы. Для новых решений мы рекомендуем поначалу использовать статические классы ресурсов, так как они обеспечивают широкие возможности управления.
- Используйте динамический класс ресурсов при наличии широкого ряда требований к памяти таблицы. Для загрузок может потребоваться больше памяти, чем обеспечивают текущие уровни DWU или cDWU. Таким образом, масштабирование хранилища данных добавляет больше памяти для операций загрузки, что ускоряет сам процесс загрузки.

### <a name="resource-classes-for-queries"></a>Классы ресурсов для запросов

Некоторые запросы требуют интенсивного использования вычислительных ресурсов, а другие наоборот.  

- Выберите динамический класс ресурсов, если запросы сложные, но не требуется высокий уровень параллелизма.  Например, при создании ежедневных или еженедельных отчетов потребность в ресурсах эпизодическая. Если отчеты обрабатывают большие объемы данных, масштабирование хранилища данных обеспечит больший объем памяти для имеющегося класса ресурса пользователя.
- Выберите статический класс ресурса, когда спрос на ресурсы непостоянный в течение дня. Например, статический класс ресурса эффективно работает, когда хранилище данных запрашивается многими пользователями. При масштабировании хранилища данных объем памяти, выделенной для пользователя, не изменяется. Следовательно, в системе можно выполнить больше параллельных запросов.

Выбор правильного временно предоставляемого буфера памяти зависит от многих факторов, таких как количество запрашиваемых данных, характер схем таблиц, различные предикаты соединения, выделения и группирования. С общей точки зрения, выделение большего объема памяти позволит ускорить выполнение запросов, но снизит общий уровень параллелизма. Если низкий уровень параллелизма не является проблемой, чрезмерное выделение памяти не нанесет вреда пропускной способности. 

Для настройки производительности используйте другие классы ресурсов. В следующем разделе приводится хранимая процедура, с помощью которой можно выявить оптимальный класс ресурсов.

## <a name="example-code-for-finding-the-best-resource-class"></a>Пример кода для поиска оптимального класса ресурсов
 
Вы можете использовать следующую хранимую процедуру (только в **поколении 1**), чтобы определить уровень параллелизма и выделения памяти для каждого класса ресурсов при заданном значении SLO и ближайший наилучший класс ресурсов для операций CCI с высоким объемом требуемой памяти в несекционированной таблице CCI в данном классе ресурсов.

Ниже представлено предназначение этой хранимой процедуры.  
1. Помочь пользователю определить необходимый уровень параллелизма и выделения памяти на класс ресурса при заданном значении SLO. Для этого пользователь должен предоставить значение NULL для схемы и имени таблицы, как показано в этом примере.  
2. Помочь определить ближайший оптимальный класс ресурсов для операций CCI с интенсивным использованием памяти (загрузка, копирование таблиц, перестроение индекса и т. д.) в несекционированной таблице CCI в данном классе ресурсов. Хранимая процедура использует схему таблицы, чтобы определить требуемый объем временно предоставляемого буфера памяти.

### <a name="dependencies--restrictions"></a>Зависимости и ограничения
- Эта хранимая процедура не предназначена для вычисления требований к объему памяти для секционированной таблицы CCI.    
- Эта хранимая процедура не учитывает требования к объему памяти для части SELECT оператора CTAS/INSERT-SELECT и предполагает, что это простой оператор SELECT.
- Эта хранимая процедура использует временную таблицу, доступную в сеансе, во время которого была создана данная хранимая процедура.    
- Данная хранимая процедура зависит от текущих предложений (например, конфигурации оборудования, конфигурации DMS), и если какое-либо из этих предложений изменится, тогда данная хранимая процедура будет работать неправильно.  
- Данная хранимая процедура зависит от существующего предлагаемого ограничения параллелизма, и если оно изменится, то данная хранимая процедура будет работать неправильно.  
- Данная хранимая процедура зависит от существующих предложений класса ресурсов, и если они изменятся, то данная хранимая процедура будет работать неправильно.  

>  [!NOTE]  
>  Если вы не получаете выходные данные после выполнения хранимой процедуры с предоставленными параметрами, тогда возможны два варианта. <br />1. Один из параметров хранилища данных содержит недопустимое значение SLO. <br />2. Отсутствует соответствующий класс ресурса для операции CCI в таблице. <br />Например, на уровне DW100 максимально доступный объем временно предоставляемого буфера памяти составляет 400 МБ, если схема таблицы достаточно широка, чтобы достигнуть такого показателя.
      
### <a name="usage-example"></a>Пример использования
Синтаксис:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: укажите параметр NULL для извлечения текущего значения DWU из базы данных хранилища данных или укажите любые поддерживаемые значения DWU в форме DW100.
2. @SCHEMA_NAME: введите имя схемы таблицы.
3. @TABLE_NAME: введите имя нужной таблицы.

Примеры выполнения этой хранимой процедуры.  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```
> [!NOTE]
> Значения, определенные в этой версии хранимой процедуры, применяются только к поколению 1.
>
>

С помощью следующей инструкции создается таблица Table1, используемая в предыдущих примерах.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Определение хранимой процедуры

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```




## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об управлении пользователями и безопасностью базы данных см. в статье [Защита базы данных в хранилище данных SQL][Secure a database in SQL Data Warehouse]. Дополнительные сведения о повышении качества кластеризованных индексов columnstore за счет повышения класса ресурсов см. в статье [Максимальное повышение качества группы строк для индекса columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
