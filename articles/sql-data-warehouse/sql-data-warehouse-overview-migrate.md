---
title: "Перенос решения в хранилище данных SQL | Документация Майкрософт"
description: "Руководство по миграции, с помощью которого вы сможете перенести свое решение в хранилище данных Azure SQL."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e5937f8472492cd1dd77c82ed518a665718623a1
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-solution-to-sql-data-warehouse"></a>Перенос решения в хранилище данных SQL
Хранилище данных SQL — это распределенная система баз данных, которую можно гибко масштабировать согласно своим потребностям. Чтобы обеспечить высокую производительность и возможности масштабирования, не все функции SQL Server реализованы в хранилище данных SQL. Приведенные ниже статьи, посвященные переносу, содержат сведения о некоторых ключевых факторах, касающихся переноса решений в хранилище данных SQL. Проектирование масштабируемых хранилищ данных приводит к появлению иных шаблонов проектирования, поэтому традиционные подходы не всегда являются лучшими. Возможно, вы захотите адаптировать свое существующее решение и получить все преимущества распределенной платформы на основе хранилища данных SQL.

Кроме того, важно помнить, что хранилище данных SQL — это платформа, основанная на Microsoft Azure. Поэтому в ходе переноса вашего решения в облако вам, вероятно, потребуется также перенести и свои данные. Передача данных — это отдельная тема, достойная тщательного рассмотрения, особенно если приходится иметь дело с большими объемами данных. Передача данных и загрузка данных являются отдельными темами.

## <a name="migration-guidance"></a>Руководство по миграции
Прежде чем начинать перенос, обязательно прочитайте приведенные ниже статьи, чтобы понимать базовые понятия и некоторые отличия между продуктами.

* [Перенос схемы][Migrate your schema]
* [Перенос данных][Migrate your data]
* [Перенос кода][Migrate your code]

## <a name="next-steps"></a>Дальнейшие действия
У группы консультирования клиентов (CAT) также есть полезное руководство по хранилищу данных SQL, которое они публикуют в блогах.  Прочитайте их статью [Migrating data to Azure SQL Data Warehouse in practice][Migrating data to Azure SQL Data Warehouse in practice] (Перенос данных в хранилище данных SQL Azure на практике), чтобы ознакомиться с дополнительными рекомендациями по переносу.

<!--Image references-->

<!--Article references-->
[Migrate your schema]: sql-data-warehouse-migrate-schema.md
[Migrate your data]: sql-data-warehouse-migrate-data.md
[Migrate your code]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

