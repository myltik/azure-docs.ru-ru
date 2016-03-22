<properties
   pageTitle="Работа с фабрикой данных Azure и хранилищем данных SQL | Microsoft Azure"
   description="Советы по использованию фабрики данных Azure (ADF) с хранилищем данных SQL для разработки решений."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Работа с фабрикой данных Azure и хранилищем данных SQL

Фабрика данных Azure обеспечивает полностью управляемый способ передачи данных и выполнения хранимых процедур в хранилище данных SQL. Благодаря этому настройка и планирование сложных процедур ETL в хранилище данных SQL становятся проще. Более подробный обзор фабрики данных Azure см. в [документации к фабрике данных Azure][].

## Перемещение данных

Фабрика Azure данных обеспечивает перемещение данных между локальными источниками и различными службами Azure. В целом текущая интеграция с фабрикой данных Azure поддерживает перемещение данных между следующими источниками:

+ Хранилище blob-объектов Azure
+ База данных SQL Azure
+ Локальный сервер SQL Server
+ Сервер SQL Server в IaaS

Сведения о настройке копирования данных см. в разделе [Копирование данных с помощью фабрики данных Azure](../data-factory/data-factory-data-movement-activities.md).

## Хранимые процедуры
 Фабрику данных Azure можно использовать как для планирования передачи данных, так и для управления выполнением хранимых процедур. Это позволяет создавать более сложные конвейеры и расширять возможности фабрики данных Azure за счет использоваться вычислительных мощностей хранилища данных SQL.

## Дальнейшие действия
Общие сведения об интеграции см. в разделе [Обзор интеграции хранилища данных SQL](sql-data-warehouse-overview-integrate.md). Дополнительные советы по разработке см. в разделе [Общие сведения о разработке для хранилища данных SQL](sql-data-warehouse-overview-develop.md).

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: https://azure.microsoft.com/ru-RU/documentation/articles/data-factory-azure-sql-connector/
[SQL Data Warehouse development overview]: https://azure.microsoft.com/ru-RU/documentation/articles/sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]: https://azure.microsoft.com/ru-RU/documentation/articles/sql-data-warehouse-overview-integrate/

<!--MSDN references-->

<!--Other Web references-->
[документации к фабрике данных Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[Copy data with Azure Data Factory]: https://azure.microsoft.com/ru-RU/documentation/articles/data-factory-data-movement-activities/

<!---HONumber=AcomDC_0309_2016-->