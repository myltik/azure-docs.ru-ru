---
title: Работа с фабрикой данных Azure и хранилищем данных SQL | Документация Майкрософт
description: Советы по использованию фабрики данных Azure (ADF) с хранилищем данных SQL для разработки решений.
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: ''
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 6adfa1264c9d196d6c6e57f1d108710b9ee73265
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Работа с фабрикой данных Azure и хранилищем данных SQL
Фабрика данных Azure обеспечивает полностью управляемый способ передачи данных и выполнения хранимых процедур в хранилище данных SQL.  Благодаря этому настройка и планирование сложных процедур ETL в хранилище данных SQL становятся проще. Более подробный обзор фабрики данных Azure см. в [документации по фабрике данных Azure][Azure Data Factory documentation].

## <a name="data-movement"></a>Перемещение данных
Фабрика Azure данных обеспечивает перемещение данных между локальными источниками и различными службами Azure.  В целом текущая интеграция с фабрикой данных Azure поддерживает перемещение данных между следующими источниками:

* Хранилище blob-объектов Azure
* Базы данных SQL Azure
* Локальный сервер SQL Server
* Сервер SQL Server в IaaS

Сведения о том, как настроить копирование данных, см. в статье [Перемещение данных с помощью действия копирования][Copy data with Azure Data Factory].

## <a name="stored-procedures"></a>Хранимые процедуры
 Фабрику данных Azure можно использовать как для планирования передачи данных, так и для управления выполнением хранимых процедур.  Это позволяет создавать более сложные конвейеры и расширять возможности фабрики данных Azure за счет использоваться вычислительных мощностей хранилища данных SQL.

## <a name="next-steps"></a>Дополнительная информация
Общие сведения об интеграции см. в статье [Интеграция других служб с хранилищем данных SQL][SQL Data Warehouse integration overview].
Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: ../data-factory/copy-activity-overview.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory documentation]:https://azure.microsoft.com/documentation/services/data-factory/

