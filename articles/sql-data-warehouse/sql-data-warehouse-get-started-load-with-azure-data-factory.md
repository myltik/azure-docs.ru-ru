---
title: "Загрузка данных с помощью фабрики данных Azure | Документация Майкрософт"
description: "Сведения о том, как загружать данные с помощью фабрики данных Azure."
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: ac7ddaa7-a3a5-4e15-b3cf-c696d2d105df
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: mausher;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 960225117a1c9b0802004455353fb9015a80b07b


---
# <a name="load-data-with-azure-data-factory"></a>Загрузка данных с помощью фабрики данных Azure
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)  
> * [Фабрика данных](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
> * [PolyBase;](sql-data-warehouse-get-started-load-with-polybase.md)  
> * [BCP](sql-data-warehouse-load-with-bcp.md)  
> 
> 

В этом руководстве показано, как создать конвейер в фабрике данных Azure для перемещения данных из хранилища BLOB-объектов Azure в хранилище данных SQL Azure. С помощью описанных ниже шагов вы сможете:

* настроить демонстрационные данные в хранилище BLOB-объектов Azure;
* подключить ресурсы к фабрике данных Azure;
* создать конвейер для перемещения данных из больших двоичных объектов службы хранилища в хранилище данных SQL.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Loading-Azure-SQL-Data-Warehouse-with-Azure-Data-Factory/player]
> 
> 

## <a name="before-you-begin"></a>Перед началом работы
Чтобы изучить фабрику данных Azure, см. статью [Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке][Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке].

### <a name="create-or-identify-resources"></a>Создание или определение ресурсов
Для работы с этим руководством потребуются следующие ресурсы.

* **Хранилище BLOB-объектов Azure.** Оно используется как источник демонстрационных данных для конвейера фабрики данных Azure, поэтому его нужно создать заранее. Если у вас еще нет учетной записи хранения, узнайте, как [ее можно создать][создание учетной записи хранения].
* **Хранилище данных SQL.** В этом руководстве мы будем перемещать данные из хранилища BLOB-объектов Azure в хранилище данных SQL. Для этого в Интернете нам потребуется хранилище данных с демонстрационными данными AdventureWorksDW. Если у вас еще нет хранилища данных, [подготовьте его].[Создание хранилища данных SQL]. Если у вас уже есть хранилище данных, но в нем нет демонстрационных данных, [загрузите их вручную][Загрузка образца данных в хранилище данных SQL].
* **Фабрика данных Azure.** В ней выполняется фактическая нагрузка. Ее нужно использовать для создания конвейера перемещения данных. Если она еще не создана, см. сведения в шаге 1 статьи [Руководство. Создание первой фабрики данных Azure с помощью портала Azure][Руководство. Создание первой фабрики данных Azure с помощью портала Azure].
* **AZCopy.** Служебная программа AZCopy нужна, чтобы скопировать демонстрационные данные из локального клиента в хранилище BLOB-объектов Azure. Инструкции по установке см. в статье [Приступая к работе со служебной программой командной строки AzCopy][Приступая к работе со служебной программой командной строки AzCopy].

## <a name="step-1-copy-sample-data-to-azure-storage-blob"></a>Шаг 1. Копирование демонстрационных данных в BLOB-объект хранилища Azure
Когда все ресурсы будут готовы, скопируйте демонстрационные данные в хранилище BLOB-объектов Azure.

1. [Загрузите образец данных][Загрузить образец данных]. К демонстрационным данным AdventureWorksDW будут добавлены данные продаж еще за три года.
2. Чтобы скопировать данные о продажах в BLOB-объект хранилища Azure, выполните такую команду AZCopy:
   
    ````
    AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
    ````

## <a name="step-2-connect-resources-to-azure-data-factory"></a>Шаг 2. Подключение ресурсов к фабрике данных Azure.
Теперь, когда BLOB-объект содержит нужные нам данные, мы можем создать конвейер фабрики данных Azure, чтобы переместить их из хранилища BLOB-объектов Azure в хранилище данных SQL.

Для начала откройте [портал Azure][портал Azure] и в меню слева выберите свою фабрику данных.

### <a name="step-21-create-linked-service"></a>Шаг 2.1. Создание связанной службы
Свяжите свою учетную запись хранения Azure и хранилище данных SQL с фабрикой данных.  

1. Сначала начните процесс регистрации, щелкнув в колонке фабрики данных раздел "Связанные службы" и выбрав пункт "Новое хранилище данных". Выберите имя, под которым будет зарегистрирована ваша служба хранилища Azure, выберите тип "Служба хранилища Azure", а затем введите имя и ключ своей учетной записи.
2. Чтобы зарегистрировать хранилище данных SQL, перейдите в раздел "Создать и развернуть" и последовательно щелкните "Новое хранилище данных" и "Хранилище данных SQL Azure". Скопируйте этот шаблон и добавьте в него свои данные.
   
    ```JSON
    {
        "name": "<Linked Service Name>",
        "properties": {
            "description": "",
            "type": "AzureSqlDW",
            "typeProperties": {
                 "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
             }
        }
    }
    ```

### <a name="step-22-define-the-dataset"></a>Шаг 2.2. Определение набора данных
После создания связанных служб следует определить наборы данных.  Это означает, что нужно задать структуру данных, перемещаемых из вашего хранилища в хранилище данных.  Вы можете прочитать об этом подробнее.

1. Сначала перейдите к разделу фабрики данных "Разработка и развертывание".
2. Чтобы привязать хранилище к фабрике данных, щелкните элемент "Создать набор данных", а затем — "Хранилище BLOB-объектов Azure".  Для определения данных в хранилище BLOB-объектов Azure вы можете использовать приведенный ниже сценарий.
   
    ```JSON
    {
        "name": "<Dataset Name>",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "<linked storage name>",
            "typeProperties": {
                "folderPath": "<containter name>",
                "fileName": "FactInternetSales.csv",
                "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }
    ```
3. Теперь мы настроим набор данных для хранилища данных SQL. Начнем так же, то есть щелкнем элемент "Создать набор данных", а затем — "Хранилище данных SQL Azure".
   
    ```JSON
    {
        "name": "DWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": "AzureSqlDWLinkedService",
            "typeProperties": {
                "tableName": "FactInternetSales"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

## <a name="step-3-create-and-run-your-pipeline"></a>Шаг 3. Создание и запуск конвейера
Теперь мы настроим и запустим конвейер в фабрике данных Azure.  Эта операция будет выполнять фактическое перемещение данных.  Полный список операций, которые можно выполнять в хранилище данных SQL и фабрике данных Azure, см. [здесь][Перемещение данных в хранилище SQL Azure и из него с помощью фабрики данных Azure].

В разделе "Создать и развернуть" щелкните "Дополнительные команды", а затем — "Создать конвейер".  Создав конвейер, вы сможете передавать данные в хранилище данных с помощью приведенного ниже кода.

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
              }
            ],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения можно найти в следующих статьях.

* [Схема обучения по фабрике данных Azure][Схема обучения по фабрике данных Azure].
* [Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure][Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure]. Это основная справочная статья, посвященная использованию фабрики данных Azure с хранилищем данных SQL Azure.

В приведенных ниже статьях содержатся подробные сведения о фабрике данных Azure. В них речь идет о базах данных SQL Azure и HDinsight, но эти сведения также относятся к хранилищу данных SQL Azure.

* [Руководство. Создание первого конвейера для обработки данных с помощью кластера Hadoop][Руководство. Создание первого конвейера для обработки данных с помощью кластера Hadoop]. Это основное руководство по обработке данных с помощью фабрики данных Azure. Из этого руководства вы узнаете, как создать конвейер, в котором веб-журналы ежемесячно преобразовываются и анализируются с помощью HDInsight. Обратите внимание, что в этом руководстве копирование не используется.
* [Копирование данных из хранилища больших двоичных объектов Azure в Базу данных SQL с помощью фабрики данных][Копирование данных из хранилища больших двоичных объектов Azure в Базу данных SQL с помощью фабрики данных]. В этом руководстве вы создадите конвейер в фабрике данных Azure, чтобы скопировать данные из хранилища BLOB-объектов Azure в базу данных SQL Azure.

<!--Image references-->

<!--Article references-->
[Документация по AZCopy]: ../storage/storage-use-azcopy.md
[Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Создание хранилища данных SQL Azure]: sql-data-warehouse-get-started-provision.md
[Создание учетной записи хранения]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Фабрика данных]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Руководство. Создание первой фабрики данных Azure с помощью портала Azure]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке]: ../data-factory/data-factory-introduction.md
[Загрузка образца данных в хранилище данных SQL]: sql-data-warehouse-load-sample-databases.md
[Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase;]: sql-data-warehouse-get-started-load-with-polybase.md
[Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Руководство по началу работы с фабрикой данных Azure]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Схема обучения по фабрике данных Azure]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[портал Azure]: https://portal.azure.com
[Загрузите демонстрационные данные]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv



<!--HONumber=Nov16_HO2-->


