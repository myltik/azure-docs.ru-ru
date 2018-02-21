---
title: "Динамический SQL в хранилище данных SQL | Документация Майкрософт"
description: "Рекомендации по использованию динамического SQL в хранилище данных SQL для разработки решений."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: a948c2c3-3cd1-4373-90a9-79e59414b778
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 29228676373aee8dbc7b1b2a7d92ffc978333804
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Динамический SQL в хранилище данных SQL
При разработке кода приложения для хранилища данных SQL может возникнуть потребность в динамическом SQL, позволяющем создавать гибкие, модульные решения универсального характера. На данный момент хранилище данных SQL не поддерживает двоичные типы данных. Это может ограничивать размер строк, поскольку к двоичным относятся типы данных nvarchar(max) и varchar(max). Если вы использовали эти типы в коде приложения при построении очень больших строк, необходимо будет разбить код на фрагменты и вместо этого использовать инструкцию EXEC.

Вот простой пример:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Если строка короткая, то можно использовать [sp_executesql][sp_executesql] как обычно.

> [!NOTE]
> К инструкциям, выполняемым как динамический код SQL, по-прежнему будут применяться все правила проверки TSQL.
> 
> 

## <a name="next-steps"></a>Дополнительная информация
Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
