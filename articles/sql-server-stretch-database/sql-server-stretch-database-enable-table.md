---
title: "Включение базы данных Stretch для таблицы | Документация Майкрософт"
description: "Узнайте, как настроить таблицу для растяжения базы данных."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 63724114-82c1-4b00-ac50-c3ade6a69d47
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3c9aa9a1abb4f436cb44e67d236682ff58a92531


---
# <a name="enable-stretch-database-for-a-table"></a>Включение растяжения базы данных для таблицы
Чтобы настроить базу данных Stretch для таблицы, выберите **Stretch | Включить** для таблицы в SQL Server Management Studio. После этого откроется **мастер включения Stretch для таблицы**. Кроме того, с помощью Transact\-SQL вы можете включить базу данных Stretch для имеющейся таблицы или создать таблицу с поддержкой базы данных Stretch.

* Если "холодные" данные хранятся в отдельной таблице, можно перенести всю таблицу.
* Если таблица содержит как активные, так и редко используемые данные, можно указать функцию фильтра, чтобы выбрать строки для миграции.

**Предварительные требования**. Если при выборе **Растяжение | Включить** для таблицы вы не включили базу данных Stretch для базы данных, то мастер сначала настроит базу данных для базы данных Stretch. В этом случае следуйте указаниям в разделе [Начало работы с запуска мастера "Включение базы данных Stretch"](sql-server-stretch-database-wizard.md) вместо действий, описанных в этом разделе.

**Разрешения**. Чтобы включить базу данных Stretch для базы данных или таблицы, требуются разрешения db\_owner. Кроме того, для таблицы также требуются разрешения ALTER.

> [!NOTE]
> В случае последующего отключения базы данных Stretch для таблицы или базы данных помните, что такое отключение не приводит к удалению дистанционного объекта. Если вы хотите удалить удаленную таблицу или базу данных, ее необходимо удалить с помощью портала управления Azure. За хранение дистанционных объектов в Azure по-прежнему будет взиматься плата, пока вы не удалите их вручную.
> 
> 

## <a name="a-nameenablewizardtableause-the-wizard-to-enable-stretch-database-on-a-table"></a><a name="EnableWizardTable"></a>Включение базы данных Stretch для таблицы с помощью мастера
**Запуск мастера**

1. В среде SQL Server Management Studio в обозревателе объектов выберите таблицу, для которой необходимо включить растяжение.
2. Щелкните таблицу правой кнопкой мыши и выберите **Stretch**, а затем — **Включить**. Откроется окно мастера.

**Введение**

Проверьте назначение мастера и необходимые компоненты.

**Выбор таблиц базы данных**

Убедитесь, что необходимая таблица отображается и выбрана.

Вы можете перенести всю таблицу или указать в мастере простую функцию фильтра. Если вы хотите использовать другой тип функции фильтра, чтобы выбрать строки для миграции, выполните одну из следующих процедур.

* Закройте мастер и выполните инструкцию ALTER TABLE, чтобы включить перенос для таблицы и указать функцию фильтра.
* Выполните инструкцию ALTER TABLE, чтобы указать функцию фильтра после выхода из мастера. Необходимые шаги приведены в разделе о [добавлении функции фильтра после запуска мастера](sql-server-stretch-database-predicate-function.md#addafterwiz).

Синтаксис инструкции ALTER TABLE описывается далее в этом разделе.

**Сводка**

Просмотрите введенные значения и параметры, выбранные в мастере. Затем нажмите кнопку **Готово** , чтобы включить Stretch.

**Результат**

Просмотрите результаты.

## <a name="a-nameenabletsqltableause-transact-sql-to-enable-stretch-database-on-a-table"></a><a name="EnableTSQLTable"></a>Включение базы данных Stretch для таблицы с помощью Transact\-SQL
Вы можете включить базу данных Stretch для существующей таблицы или создать новую таблицу с поддержкой базы данных Stretch с помощью Transact-SQL.

### <a name="options"></a>Параметры
При выполнении CREATE TABLE или ALTER TABLE для включения базы данных Stretch для таблицы используйте следующие параметры.

* При необходимости, если таблица содержит "горячие" и "холодные" данные, добавьте предложение `FILTER_PREDICATE = <function>`, чтобы указать функцию выбора строк для переноса. Предикат должен вызывать встроенную функцию с табличным значением. Дополнительные сведения см. в статье [Выбор строк для миграции с использованием функции фильтров (база данных Stretch)](sql-server-stretch-database-predicate-function.md). Если не указать функцию фильтра, будет выполнена миграция всей таблицы.
  
  > [!NOTE]
  > Если указать функцию фильтра, которая работает неэффективно, перенос данных также будет выполняться неэффективно. База данных Stretch применяет функцию фильтра к таблице с помощью оператора CROSS APPLY.
  > 
  > 
* Укажите `MIGRATION_STATE = OUTBOUND`, чтобы сразу начать перенос данных, или `MIGRATION_STATE = PAUSED`, чтобы отложить этот процесс.

### <a name="enable-stretch-database-for-an-existing-table"></a>Включение базы данных Stretch для существующей таблицы
Чтобы настроить существующую таблицу для использования базы данных Stretch, выполните инструкцию ALTER TABLE.

Ниже приведен пример, при котором немедленно начинается миграция всей таблицы.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```
Ниже приведен пример кода, позволяющий перенести только строки, определенные встроенной функцией с табличным значением `dbo.fn_stretchpredicate`. При этом перенос данных откладывается. Дополнительные сведения о функции фильтра см. в статье [Выбор строк для миграции с использованием функции фильтров (база данных Stretch)](sql-server-stretch-database-predicate-function.md).

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
 GO
```

Дополнительные сведения см. в статье [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

### <a name="create-a-new-table-with-stretch-database-enabled"></a>Создание новой таблицы с поддержкой базы данных Stretch
Чтобы создать новую таблицу с поддержкой базы данных Stretch, выполните команду CREATE TABLE.

Ниже приведен пример, при котором немедленно начинается миграция всей таблицы.

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```

Ниже приведен пример кода, позволяющий перенести только строки, определенные встроенной функцией с табличным значением `dbo.fn_stretchpredicate`. При этом перенос данных откладывается. Дополнительные сведения о функции фильтра см. в статье [Выбор строк для миграции с использованием функции фильтров (база данных Stretch)](sql-server-stretch-database-predicate-function.md).

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
GO  
```

Дополнительные сведения см. в статье [CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx).

## <a name="see-also"></a>Дополнительные материалы
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

[CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)




<!--HONumber=Nov16_HO3-->


