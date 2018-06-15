---
title: Использование определяемых пользователем схем в хранилище данных SQL | Документация Майкрософт
description: Советы по использованию определяемых пользователем схем T-SQL в хранилище данных SQL Azure для разработки решений.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: c18e6d34416390ae7e93b69b28d508a540f7b1ab
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31522713"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Использование определяемых пользователем схем в хранилище данных SQL
Советы по использованию определяемых пользователем схем T-SQL в хранилище данных SQL Azure для разработки решений.

## <a name="schemas-for-application-boundaries"></a>Схемы для границ приложений

В традиционных хранилищах данных часто используются отдельные базы данных, чтобы создать границы приложений на основе рабочей нагрузки, домену или безопасности. Например, традиционное хранилище данных SQL Server может включать в себя промежуточную базу данных, базу данных хранилища данных и базы данных киоска данных. В этой топологии каждая база данных работает как рабочая нагрузка и граница безопасности в архитектуре.

Напротив, хранилище данных SQL выполняет всю рабочую нагрузку хранилища данных в одной базе данных. Кроссплатформенные соединения баз данных не допускаются. Поэтому хранилище данных SQL ожидает, что все таблицы, используемые в хранилище, должны храниться в одной базе данных.

> [!NOTE]
> Хранилище данных SQL не поддерживает какие-либо перекрестные запросы к базам данных. Следовательно, реализации хранилища данных, использующих этот шаблон, будет необходимо пересмотреть.
> 
> 

## <a name="recommendations"></a>Рекомендации
Ниже приведены рекомендации по консолидации границ рабочих нагрузок, безопасности, домена и функциональных границы с помощью определяемых пользователем схем.

1. Используйте одну базу данных хранилища данных SQL для выполнения рабочей нагрузки хранилища данных.
2. Объедините существующую среду хранилища данных, чтобы использовать одну базу данных хранилища данных SQL.
3. Используйте **определяемые пользователем схемы** , чтобы создать границы, ранее реализуемые с помощью баз данных.

Если ранее определяемые пользователем схемы не использовалось, следует начать с нуля. Просто используйте старое имя базы данных в определяемых пользователем схемах в базе данных хранилища данных SQL.

Если же схемы уже использовались, у вас несколько вариантов:

1. Удалить прежние имена схем и создать новые.
2. Сохранить прежние имена схем, добавляя прежнее имя схемы в начало имени таблицы.
3. Сохранить прежние имена схем, реализовав представления таблицы в дополнительной схеме, чтобы воссоздать старую структуру схемы.

> [!NOTE]
> На первый взгляд вариант 3 может показаться наиболее привлекательным. Однако черт прячется в деталях. Представления в хранилище данных SQL доступны только для чтения. Любое изменение данных или таблицы потребуется выполнять в базовой таблице. Кроме того, вариант 3 добавляет в систему уровень представлений. Вы можете обдумать это, если в вашей архитектуре уже используются представления.
> 
> 

### <a name="examples"></a>Примеры:
Реализация определяемых пользователем схем на основе имен баз данных

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Сохраните прежние имена схем, добавляя их в начало имени таблицы. Используйте схемы для создания границы рабочих нагрузок.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Сохранение прежних имен схем с помощью представлений

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Любое изменение в стратегии схем влечет за собой пересмотр модели безопасности базы данных. Во многих случаях можно упростить модель безопасности, назначая разрешения на уровне схем. Если требуются более детализированные разрешения, можно использовать роли базы данных.
> 
> 

## <a name="next-steps"></a>Дополнительная информация
Дополнительные советы см. в [общих сведениях о разработке](sql-data-warehouse-overview-develop.md).

