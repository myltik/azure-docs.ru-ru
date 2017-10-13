---
title: "Загрузка данных в хранилище данных SQL Azure | Документация Майкрософт"
description: "Распространенные сценарии загрузки данных в хранилище данных SQL. Они включают использование PolyBase, хранилища BLOB-объектов Azure, неструктурированных файлов и доставки диска. Кроме того, можно использовать сторонние средства."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: c4199a387f5cdbd477a5e348e48ba8e8b5900075
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-into-azure-sql-data-warehouse"></a>Загрузка данных в хранилище данных Azure SQL
Сводка параметров сценария и рекомендаций по загрузке данных в хранилище данных SQL.

Обычно самая сложная часть загрузки — это подготовка данных для загрузки. Azure упрощает загрузку за счет использования хранилища BLOB-объектов Azure как общего хранилища данных для многих служб и фабрики данных Azure для управления взаимодействием и перемещениям данных между службами Azure. Эти процессы интегрированы в технологию PolyBase, которая загружает данные из хранилища BLOB-объектов Azure в хранилище данных SQL с массовой параллельной обработкой (MPP). 

Инструкции по загрузке образцов баз данных в хранилище данных SQL см. в [этой статье][Load sample databases].

## <a name="load-from-azure-blob-storage"></a>Загрузка из хранилища BLOB-объектов Azure
Самый быстрый способ импорта данных в хранилище данных SQL — это использование PolyBase для загрузки данных из хранилища BLOB-объектов Azure. PolyBase использует структуру массовой параллельной обработки (MPP) в хранилище данных SQL для параллельной загрузки данных из хранилища BLOB-объектов Azure. Для работы с PolyBase можно использовать команды T-SQL или конвейер фабрики данных Azure.

### <a name="1-use-polybase-and-t-sql"></a>1. Использование PolyBase и T-SQL
Процесс загрузки:

1. Перенесите данные в хранилище BLOB-объектов Azure или Azure Data Lake Store и сохраните их в виде текстовых файлов.
2. Настройте внешние объекты в хранилище данных SQL, определив расположение и формат данных.
3. Выполните команду T-SQL для параллельной загрузки данных в новую таблицу баз данных.

<!-- 5. Schedule and run a loading job. --> 

Инструкции см. в статье [Загрузка данных из хранилища BLOB-объектов Azure в хранилище данных SQL (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="2-use-azure-data-factory"></a>2) Использование фабрики данных Azure
Чтобы упростить работу с PolyBase, можно создать конвейер фабрики данных Azure, использующий PolyBase для загрузки данных из хранилища BLOB-объектов Azure в хранилище данных SQL. Он настраивается быстро, поскольку не требует определения команд T-SQL. Если требуется только запрос внешних данных без импорта, используйте T-SQL. 

Процесс загрузки:

1. Перенесите данные в хранилище BLOB-объектов Azure и сохраните их в виде текстовых файлов. Сейчас фабрика данных Azure не поддерживает подключение к ADLS с использованием PolyBase.
2. Создайте конвейер фабрики данных Azure для приема данных. Используйте параметр PolyBase.
4. Спланируйте и запустите конвейер.

Инструкции см. в статье [Загрузка данных в хранилище данных SQL с помощью фабрики данных][Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)].

## <a name="load-from-sql-server"></a>Загрузка из SQL Server
Для загрузки данных из SQL Server в хранилище данных SQL можно использовать службы интеграции, передавать неструктурированные файлы или отправлять диски в корпорацию Майкрософт. Далее в этой статье будут рассмотрены различные процессы загрузки и даны ссылки на соответствующие учебники.

Сведения о планировании полной миграции данных из SQL Server в хранилище данных SQL см. в статье [Перенос решения в хранилище данных SQL][Migration overview]. 

### <a name="use-integration-services-ssis"></a>Использование служб интеграции (SSIS)
Если вы уже используете пакеты служб интеграции (SSIS) для загрузки в SQL Server, эти пакеты можно обновить таким образом, чтобы SQL Server использовался как источник, а хранилище данных SQL — как конечный пункт. Это делается быстро и легко и подходит, если вы не пытаетесь перенести процесс загрузки для использования данных, которые уже находится в облаке. При этом загрузка осуществляется медленнее, чем при использовании PolyBase, поскольку SSIS не выполняет параллельную загрузку.

Процесс загрузки:

1. Измените пакет служб интеграции таким образом, чтобы он указывал на экземпляр SQL Server как источник и на базу данных хранилища данных SQL как на конечный пункт.
2. Перенесите схему в хранилище данных SQL, если вы это еще не сделали.
3. Измените сопоставление в пакетах, используя только те типы данных, которые поддерживаются хранилищем данных SQL.
4. Спланируйте и запустите пакет.

Инструкции см. в статье [Загрузка данных из SQL Server в хранилище данных SQL Azure (SSIS)][Load data from SQL Server to Azure SQL Data Warehouse (SSIS)].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Использование AZCopy (рекомендуется при объеме данных меньше 10 ТБ)
Если объем данных меньше 10 ТБ, можно экспортировать данные из SQL Server в неструктурированные файлы, скопировать их в хранилище BLOB-объектов Azure, а затем загрузить данные в хранилище данных SQL с помощью PolyBase.

Процесс загрузки:

1. Экспортируйте данные из SQL Server в неструктурированные файлы с помощью служебной программы командной строки bcp.
2. Скопируйте данные из неструктурированных файлов в хранилище BLOB-объектов Azure с помощью служебной программы командной строки AZCopy.
3. Загрузите данные в хранилище данных SQL с помощью PolyBase.

Инструкции см. в статье [Загрузка данных из хранилища BLOB-объектов Azure в хранилище данных SQL (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="use-bcp"></a>Использование программы bcp
Если данных немного, их можно загрузить непосредственно в хранилище данных SQL с помощью bcp.

Процесс загрузки:

1. Экспортируйте данные из SQL Server в неструктурированные файлы с помощью служебной программы командной строки bcp.
2. Загрузите данные из неструктурированных файлов непосредственно в хранилище данных SQL с помощью bcp.

Инструкции см. в статье [Загрузка данных из SQL Server в хранилище данных SQL Azure (неструктурированные файлы)][Load data from SQL Server to Azure SQL Data Warehouse (bcp)].

### <a name="use-importexport-recommended-for--10-tb-data"></a>Использование импорта и экспорта (рекомендуется при объеме данных меньше 10 ТБ)
Если объем данных превышает 10 ТБ и вы хотите перенести их в Azure, мы рекомендуем использовать для доставки дисков нашу службу [импорта и экспорта][Import/Export]. 

Процесс загрузки:

1. Экспортируйте данные из SQL Server в неструктурированные файлы на переносных дисках с помощью служебной программы командной строки bcp.
2. Перешлите диски в корпорацию Майкрософт.
3. Майкрософт загружает данные в хранилище данных SQL

## <a name="load-from-hdinsight"></a>Загрузка из HDInsight
Хранилище данных SQL поддерживает загрузку данных из HDInsight через PolyBase. Процесс будет таким же, как при загрузке данных из хранилища BLOB-объектов Azure с использованием PolyBase для подключения к HDInsight и загрузки данных. 

### <a name="1-use-polybase-and-t-sql"></a>1. Использование PolyBase и T-SQL
Процесс загрузки:

1. Переместите данные в HDInsight и сохраните их в текстовых файлах, формате ORC или Parquet.
2. Настройте внешние объекты в хранилище данных SQL, определив расположение и формат данных.
3. Выполните команду T-SQL для параллельной загрузки данных в новую таблицу баз данных.

Инструкции см. в статье [Загрузка данных из хранилища BLOB-объектов Azure в хранилище данных SQL (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

## <a name="recommendations"></a>Рекомендации
Многие из наших партнеров предлагают решения для загрузки. Дополнительные сведения см. в статье [Партнеры по бизнес-аналитике хранилища данных SQL][solution partners]. 

Если данные поступают из нереляционного источника и их необходимо загрузить в хранилище данных SQL, перед загрузкой их нужно преобразовать в строки и столбцы. Преобразованные данные не обязательно должны храниться в базе данных, их можно хранить в текстовых файлах.

Создание статистики для вновь загруженных данных Чтобы добиться максимально высокой производительности запросов, крайне важно сформировать статистические данные для всех столбцов всех таблиц после первой загрузки или после любых значительных изменений в данных.  Чтобы добиться максимально высокой производительности запросов, крайне важно сформировать статистические данные для всех столбцов всех таблиц после первой загрузки или после любых значительных изменений в данных.  Дополнительные сведения см. в статье [Управление статистикой таблиц в хранилище данных SQL][Statistics].

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL][development overview].

<!--Image references-->

<!--Article references-->
[Load data from Azure blob storage to SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Load data from SQL Server to Azure SQL Data Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Load data from SQL Server to Azure SQL Data Warehouse (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Load sample databases]: ./sql-data-warehouse-load-sample-databases.md
[Migration overview]: ./sql-data-warehouse-overview-migrate.md
[solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Import/Export]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
