---
title: "Ограничения для базы данных Stretch | Документация Майкрософт"
description: "Дополнительные сведения об ограничениях для базы данных Stretch."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 0a865872-7d42-4873-99b9-cbae45691e54
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: anvang
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3c1599dfc6aee8adf7ec8714c392a7d8d4427d78


---
# <a name="limitations-for-stretch-database"></a>Ограничения для базы данных Stretch
Сведения об ограничениях для таблиц с поддержкой Stretch, а также ограничениях, которые в настоящий момент не позволяют включить Stretch для таблицы.

## <a name="a-namecaveatsa-limitations-for-stretch-enabled-tables"></a><a name="Caveats"></a>Ограничения для таблиц, совместимых со Stretch
В таблицах с поддержкой Stretch действуют указанные ниже ограничения.

### <a name="constraints"></a>Ограничения
* В таблице, совместимой со Stretch, не обеспечивается уникальность для ограничений UNIQUE и PRIMARY KEY в таблице Azure, содержащей перенесенные данные.

### <a name="dml-operations"></a>Операции DML
* Нельзя обновлять или удалять из таблицы с поддержкой Stretch или представления, содержащего такие таблицы, перенесенные строки или строки, подходящие для переноса.
* Нельзя вставлять строки в таблицу с поддержкой Stretch на связанном сервере.

### <a name="indexes"></a>Индексы
* Нельзя создать индекс для представления, включающего в себя таблицу, совместимую со Stretch.
* Фильтры индексов SQL Server не распространяются на удаленную таблицу.

## <a name="a-namelimitationsa-limitations-that-currently-prevent-you-from-enabling-stretch-for-a-table"></a><a name="Limitations"></a> Ограничения, не позволяющие включить растяжение для таблицы
В настоящее время включить растяжение для таблицы не позволяют следующие ограничения.

### <a name="table-properties"></a>Свойства таблицы
* Таблицы, содержащие более 1023 столбцов или более 998 индексов
* Таблицы файлов или таблицы, содержащие данные FILESTREAM
* Таблицы, которые реплицируются или активно используют отслеживание изменений или запись измененных данных
* Таблицы, оптимизированные для памяти

### <a name="data-types"></a>Типы данных
* text, ntext и image
* Timestamp
* sql\_variant
* XML
* Типы данных CLR, включая geometry, geography, hierarchyid и определяемые пользователем типы CLR

### <a name="column-types"></a>Типы столбцов
* COLUMN\_SET
* вычисляемые столбцы;

### <a name="constraints"></a>Ограничения
* Ограничения по умолчанию и проверочные ограничения
* Ограничения внешнего ключа, ссылающегося на таблицу В \-иерархическом отношении\( (например, Order и Order\_Detail\)) можно включить поддержку Stretch для дочерней таблицы \(Order\_Detail\), но не для родительской таблицы \(Order\).

### <a name="indexes"></a>Индексы
* Полнотекстовые индексы
* XML-индексы
* Пространственные индексы
* Индексированные представления, которые ссылаются на таблицу

## <a name="see-also"></a>Дополнительные материалы
[Определение баз данных и таблиц для растяжения с помощью Помощника по растяжению баз данных](sql-server-stretch-database-identify-databases.md)

[Включение растяжения базы данных для базы данных](sql-server-stretch-database-enable-database.md)

[Включение растяжения базы данных для таблицы](sql-server-stretch-database-enable-table.md)




<!--HONumber=Nov16_HO3-->


