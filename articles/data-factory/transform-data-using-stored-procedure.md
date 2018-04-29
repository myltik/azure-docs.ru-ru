---
title: Преобразование данных с помощью действия хранимой процедуры в фабрике данных Azure | Документация Майкрософт
description: В этой статье содержатся сведения о том, как использовать действия хранимой процедуры SQL Server для вызова хранимой процедуры в базе данных SQL Azure или хранилище данных из конвейера фабрики данных.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 729974d37351c12f551e165567bb11467a0dd963
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Преобразование данных с помощью действия хранимой процедуры SQL Server в фабрике данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-stored-proc-activity.md)
> * [Версия 2 — предварительная](transform-data-using-stored-procedure.md)


Действия преобразования данных в [конвейере](concepts-pipelines-activities.md) фабрики данных позволяют преобразовать необработанные данные и переработать их в прогнозы и аналитику. Действие хранимой процедуры — это одно из действий преобразования данных, которые поддерживает фабрика данных. Данная статья основана на материалах статьи о [преобразовании данных](transform-data.md), в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования в фабрике данных.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая сейчас доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Действие "Хранимая процедура SQL Server"](v1/data-factory-stored-proc-activity.md).
> 
> Если вы не знакомы с фабрикой данных Azure, сначала ознакомьтесь со статьей [Введение в фабрику данных Azure](introduction.md) и руководством [Преобразование данных в облаке с помощью действия Spark в фабрике данных Azure](tutorial-transform-data-spark-powershell.md). 

C его помощью можно вызвать хранимую процедуру в одном из следующих хранилищ данных вашего предприятия или на виртуальной машине Azure. 

- Базы данных SQL Azure
- Хранилище данных SQL Azure
- База данных SQL Server.  Если вы используете SQL Server, установите локальную среду выполнения интеграции на том же компьютере, на котором размещена база данных, или на отдельном компьютере, имеющем доступ к базе данных. Локальная среда выполнения интеграции — это компонент, который обеспечивает безопасное и управляемое подключение локальных источников данных или данных виртуальной машины Azure к облачным службам. Дополнительные сведения см. в статье [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md).

> [!IMPORTANT]
> При копировании данных в базу данных SQL Azure или SQL Server можно настроить класс **SqlSink** в действии копирования для вызова хранимой процедуры с помощью свойства **sqlWriterStoredProcedureName**. Дополнительные сведения о свойствах см. в следующих статьях о соединителях: [Перемещение данных в базу данных SQL Azure и из нее с помощью фабрики данных Azure](connector-azure-sql-database.md) и [Перемещение данных в базу данных SQL Server и обратно на локальных компьютерах и виртуальных машинах Azure IaaS с помощью фабрики данных Azure](connector-sql-server.md). Вызов хранимой процедуры во время копирования данных в хранилище данных SQL Azure с помощью операции копирования не поддерживается. Однако действие хранимой процедуры можно использовать для вызова хранимой процедуры в хранилище данных SQL. 
>
> При копировании данных из базы данных SQL Azure, SQL Server или хранилища данных SQL Azure можно настроить **SqlSource** в действии копирования, чтобы вызвать хранимую процедуру для считывания данных из исходной базы данных с помощью свойства **sqlReaderStoredProcedureName**. Дополнительные сведения см. в разделе "Свойства действия копирования" следующих статей о соединителях: [Перемещение данных в базу данных SQL Azure и из нее с помощью фабрики данных Azure](connector-azure-sql-database.md), [Перемещение данных в базу данных SQL Server и обратно на локальных компьютерах и виртуальных машинах Azure IaaS с помощью фабрики данных Azure](connector-sql-server.md), [Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure](connector-azure-sql-data-warehouse.md).          

 

## <a name="syntax-details"></a>Сведения о синтаксисе
Ниже приведен формат JSON для определения действия хранимой процедуры:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

В следующей таблице описаны эти свойства JSON:

| Свойство                  | ОПИСАНИЕ                              | Обязательно |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Имя действия.                     | Yes      |
| description               | Текст, описывающий, для чего используется действие | Нет        |
| Тип                      | Для действия хранимой процедуры используется тип действия **SqlServerStoredProcedure**. | Yes      |
| linkedServiceName         | Ссылка на **базу данных SQL Azure**, **хранилище данных SQL Azure** или сервер **SQL Server**, зарегистрированный в качестве связанной службы в фабрике данных. Дополнительные сведения об этой связанной службе см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md). | Yes      |
| storedProcedureName       | Укажите имя хранимой процедуры, которую нужно вызвать. | Yes      |
| storedProcedureParameters | Укажите значения для параметров хранимой процедуры. Используйте `"param1": { "value": "param1Value","type":"param1Type" }`, чтобы передать значения параметра и их тип, поддерживаемый источником данных. Если для параметра необходимо передать значение null, используйте синтаксис `"param1": { "value": null }` (все знаки в нижнем регистре). | Нет        |

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь со следующими ссылками, в которых описаны способы преобразования данных другими способами: 

* [Действие U-SQL](transform-data-using-data-lake-analytics.md)
* [Действие Hive](transform-data-using-hadoop-hive.md)
* [Действие Pig](transform-data-using-hadoop-pig.md)
* [Действие MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Потоковая активность Hadoop](transform-data-using-hadoop-streaming.md)
* [Действие Spark](transform-data-using-spark.md)
* [Настраиваемое действие .NET](transform-data-using-dotnet-custom-activity.md)
* [Создание прогнозирующих конвейеров с помощью службы "Машинное обучение Azure" и фабрики данных Azure](transform-data-using-machine-learning.md)
* [Действие хранимой процедуры](transform-data-using-stored-procedure.md)
