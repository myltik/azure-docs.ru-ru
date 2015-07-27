<properties
   pageTitle="Динамический SQL в хранилище данных SQL | Microsoft Azure"
   description="Рекомендации по использованию динамического SQL в хранилище данных SQL для разработки решений."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Динамический SQL в хранилище данных SQL
При разработке кода приложения для хранилища данных SQL может возникнуть потребность в динамическом SQL, позволяющем создавать гибкие, модульные решения универсального характера. Однако на данный момент хранилище данных SQL не поддерживает двоичные типы данных. Это может ограничивать размер строк, поскольку к двоичным относятся типы данных nvarchar(max) и varchar(max). Возможно, вы использовали эти типы данных в коде приложения, записывая очень длинные строки динамического кода SQL, который нужно было выполнить.

В этом случае код необходимо разбить на фрагменты и использовать оператор EXEC.

Упрощенный пример приведен ниже:

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Если строка не слишком длинная, [sp_executesql][] можно использовать как обычно.


## Дальнейшие действия
Дополнительные советы по разработке см. в статье [Общие сведения о разработке][].

<!--Image references-->

<!--Article references-->
[Общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/ru-ru/library/ms188001.aspx

<!--Other Web references-->

<!---HONumber=July15_HO3-->