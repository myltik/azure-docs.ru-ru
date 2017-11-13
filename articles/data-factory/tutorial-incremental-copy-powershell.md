---
title: "Пошаговое копирование данных с помощью фабрики данных Azure | Документация Майкрософт"
description: "В этом руководстве вы создадите конвейер фабрики данных Azure, который пошагово копирует данные из базы данных SQL Azure в хранилище BLOB-объектов Azure."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/06/2017
ms.author: shlo
ms.openlocfilehash: f352f46f2d4c23124f4ee7e886cae9bdd8d5d2c9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2017
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage"></a>Пошаговая загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure

[!INCLUDE [data-factory-what-is-include-md](../../includes/data-factory-what-is-include.md)]

#### <a name="this-tutorial"></a>В этом руководстве рассматривается:

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Во время процесса интеграции данных одним из широко используемых сценариев является пошаговая загрузка данных для обновления результатов обновленного анализа после начальной загрузки данных и их анализа. В этом руководстве мы рассмотрим загрузку лишь новых или обновленных записей из источников данных в приемники. Такая загрузка работает более эффективно по сравнению с полной загрузкой, особенно для больших наборов данных.    

Фабрику данных можно использовать для создания решений верхнего предела, чтобы получить пошаговую загрузку данных с помощью действий поиска, копирования и хранимой процедуры в конвейере.  

В этом руководстве вы выполните следующие шаги:

> [!div class="checklist"]
> * Подготовите хранилище данных для хранения значений предела.   
> * Создадите фабрику данных.
> * создание связанных служб. 
> * Создадите наборы данных источника, приемника и предела.
> * Создадите конвейер.
> * Запустили конвейер.
> * Выполнение мониторинга выполнения конвейера. 

## <a name="overview"></a>Обзор
Ниже приведена общая схема решения: 

![Пошаговая загрузка данных](media\tutorial-Incrementally-load-data-from-azure-sql-to-blob\incrementally-load.png)

Ниже приведены важные действия для создания этого решения. 

1. **Выберите столбец для предела.**
    Выберите один столбец в исходном хранилище данных, который можно использовать для создания среза новых или обновленных записей при каждом запуске. Как правило, данные в этом выбранном столбце (например, последнее_время_изменения или идентификатор) продолжают увеличиваться по мере создания или обновления строк. В качестве предела используется максимальное значение в этом столбце.
2. **Подготовьте хранилище данных для хранения значений предела.**   
    В этом руководстве сохраните значение предела в базе данных SQL Azure.
3. **Создайте конвейер, используя следующий рабочий процесс:** 
    
    Конвейер в этом решении содержит следующие действия:
  
    1. Создание двух действий **поиска**. Используйте первое действие поиска для получения последнего значения предела, а второе для получения нового значения предела. Эти значения предела передаются в действие копирования. 
    2. Создание **действия копирования**, копирующего строки из исходного хранилища данных со значениями столбцов предела, которые выше значений старого предела и меньше значений нового. Затем оно копирует разностные данные из исходного хранилища данных в хранилище BLOB-объектов в качестве нового файла. 
    3. Создание **действия хранимой процедуры**, которое обновляет значения предела для конвейера при последующем выполнении. 


Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования
* **База данных SQL Azure**. Используйте базу данных как **исходное** хранилище данных. Если у вас нет базы данных SQL Azure, вы можете создать ее, выполнив шаги из статьи [Создание базы данных SQL Azure на портале Azure](../sql-database/sql-database-get-started-portal.md).
* **Учетная запись хранения Azure.** В этом руководстве в качестве **приемника** будет использоваться хранилище BLOB-объектов. Если у вас нет учетной записи хранения Azure, ознакомьтесь с разделом [Создание учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account). Создайте контейнер с именем **adftutorial**. 
* **Azure PowerShell**. Следуйте инструкциям по [установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Создание таблицы источника данных в базе данных SQL Azure
1. Откройте **SQL Server Management Studio** в **обозревателе серверов**, щелкните правой кнопкой мыши базу данных и выберите **Создать запрос**.
2. Выполните указанную ниже команду SQL для базы данных SQL Azure, чтобы создать таблицу с именем `data_source_table` в качестве хранилища источника данных.  
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    В этом руководстве в качестве столбца **предела** мы будем использовать **LastModifytime**.  В следующей таблице показаны данные в хранилище источника данных:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-sql-database-to-store-the-high-watermark-value"></a>Создание дополнительной таблицы в базе данных SQL для хранения значения верхнего предела
1. Выполните указанную ниже команду SQL для базы данных SQL Azure, чтобы создать таблицу с именем `watermarktable` для хранения значения предела.  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
3. Задайте **значение по умолчанию** верхнего предела, используя имя таблицы исходного хранилища данных.  (В этом руководстве используется таблица с именем **data_source_table**.)

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
4. Просмотрите данные в таблице `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Выходные данные: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-azure-sql-database"></a>Создание хранимой процедуры в базе данных SQL Azure 

Выполните указанную ниже команду, чтобы создать хранимую процедуру в базе данных SQL Azure.

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Создать фабрику данных

1. Запустите **PowerShell**. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.

    Выполните следующую команду и введите имя пользователя и пароль, которые используются для входа на портал Azure.
        
    ```powershell
    Login-AzureRmAccount
    ```        
    Чтобы просмотреть все подписки для этой учетной записи, выполните следующую команду:

    ```powershell
    Get-AzureRmSubscription
    ```
    Выполните следующую команду, чтобы выбрать подписку, с которой вы собираетесь работать. Замените значение **SubscriptionId** на идентификатор подписки Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
2. Выполните командлет **Set-AzureRmDataFactoryV2**, чтобы создать фабрику данных. Перед выполнением команды замените заполнители собственными значениями.

    ```powershell
    Set-AzureRmDataFactoryV2 -ResourceGroupName "<your resource group to create the factory>" -Location "East US" -Name "<specify the name of data factory to create. It must be globally unique.>" 
    ```

    Обратите внимание на следующие моменты.

    * Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

        ```
        The specified Data Factory name '<data factory name>' is already in use. Data Factory names must be globally unique.
        ```

    * Чтобы создавать экземпляры фабрики данных, вы должны быть участником или администратором подписки Azure.
    * Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.


## <a name="create-linked-services"></a>Создание связанных служб
Связанная служба в фабрике данных связывает хранилища данных и службы вычислений с фабрикой данных. В этом разделе вы создадите связанные службы для учетной записи хранения Azure и базы данных SQL Azure. 

### <a name="create-azure-storage-linked-service"></a>Создание связанной службы хранилища Azure
1. Создайте JSON-файл с именем **AzureStorageLinkedService.json** в папке **C:\ADF** и добавьте в него приведенное ниже содержимое. Если папка ADF отсутствует, создайте ее. Перед сохранением файла замените значения `<accountName>` и `<accountKey>` на имя вашей учетной записи хранения Azure и ее ключ.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. В **Azure PowerShell** перейдите в папку **ADF**.
3. Выполните командлет **Set-AzureRmDataFactoryV2LinkedService**, чтобы создать связанную службу: **AzureStorageLinkedService**. В указанном ниже примере вы передадите значения для параметров **ResourceGroupName** и **DataFactoryName**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Пример выходных данных:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Создание связанной службы базы данных SQL Azure
1. Создайте JSON-файл с именем **AzureSQLDatabaseLinkedService.json** в папке **C:\ADF** и добавьте в него приведенное ниже содержимое. Если папка ADF отсутствует, создайте ее. Замените значения **&lt;server&gt;, &lt;user id&gt; и &lt;password&gt;** значениями сервера, идентификатора пользователя и пароля базы данных SQL Azure, прежде чем сохранить файл. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. В **Azure PowerShell** перейдите в папку **ADF**.
3. Выполните командлет **Set-AzureRmDataFactoryV2LinkedService**, чтобы создать связанную службу **AzureSQLDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Пример выходных данных:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Создание наборов данных
На этом шаге вы создадите наборы данных, которые представляют данные источника и приемника. 

### <a name="create-a-source-dataset"></a>Создание исходного набора данных

1. Создайте файл JSON с именем SourceDataset.json в той же папке со следующим содержимым: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```
    В этом руководстве используется таблица с именем **data_source_table**. Замените ее, если используется таблица с другим именем. 
2.  Выполните командлет Set-AzureRmDataFactoryV2Dataset, чтобы создать набор данных SourceDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Вот пример выходных данных командлета:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Создание набора данных приемника

1. Создайте файл JSON с именем SinkDataset.json в той же папке со следующим содержимым: 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')", 
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > В этом фрагменте кода предполагается, что у вас есть контейнер больших двоичных объектов с именем **adftutorial** в хранилище BLOB-объектов Azure. Создайте контейнер (если его еще нет) или присвойте ему имя имеющегося контейнера. Выходная папка `incrementalcopy` создается автоматически, если она не существует в контейнере. В этом руководстве имя файла создается динамически с помощью выражения `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.
2.  Выполните командлет Set-AzureRmDataFactoryV2Dataset, чтобы создать набор данных SinkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Вот пример выходных данных командлета:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-watermark"></a>Создание набора данных для предела
На этом шаге вы создадите набор данных для хранения значения верхнего предела. 

1. Создайте файл JSON с именем WatermarkDataset.json в той же папке со следующим содержимым: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Выполните командлет Set-AzureRmDataFactoryV2Dataset, чтобы создать набор данных WatermarkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Вот пример выходных данных командлета:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Создание конвейера
В этом руководстве вы создадите конвейер с двумя действиями поиска, одним действием копирования и одним действием хранимой процедуры, связанными в одном конвейере. 


1. Создайте файл JSON IncrementalCopyPipeline.json в той же папке со следующим содержимым. 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },
    
                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },
    
                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                
                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],
    
                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },
    
                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
    
                        "storedProcedureName": "sp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },
    
                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
    
                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
            
        }
    }
    ```
    

2. Выполните командлет Set-AzureRmDataFactoryV2Pipeline для создания конвейера IncrementalCopyPipeline.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Пример выходных данных: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```
 
## <a name="run-the-pipeline"></a>Запуск конвейера

1. Запустите конвейер **IncrementalCopyPipeline**, выполнив командлет **Invoke-AzureRmDataFactoryV2Pipeline**. Замените заполнители собственными именами группы ресурсов и фабрики данных.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup "<your resource group>" -dataFactoryName "<your data factory name>"
    ``` 
2. Проверьте состояние конвейера, выполняя командлет Get-AzureRmDataFactoryV2ActivityRun, пока вы не увидите, что все действия выполняются успешно. Замените заполнители нужными значениями времени для параметров RunStartedAfter и RunStartedBefore.  В этом руководстве мы используем -RunStartedAfter "2017/09/14" и -RunStartedBefore "2017/09/15".

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Пример выходных данных:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Просмотр результатов

1. В хранилище BLOB-объектов Azure (хранилище приемника) вы должны увидеть, что данные скопированы в файл, заданный в SinkDataset.  В этом руководстве в качестве имени файла используется `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`.  Открыв файл, вы увидите, что записи в файле идентичны данным в базе данных SQL Azure.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ``` 
2. Проверив последнее значение из `watermarktable`, вы увидите, что значение предела обновлено.

    ```sql
    Select * from watermarktable
    ```
    
    Пример выходных данных:
 
    TableName | WatermarkValue
    --------- | --------------
    data_source_table 2017-09-05 8:06:00.000

### <a name="insert-data-into-data-source-store-to-verify-delta-data-loading"></a>Добавление данных в хранилище данных для проверки загрузки разностных данных

1. Добавьте новые данные в базу данных SQL Azure (хранилище источника данных):

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')
    
    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ``` 

    Обновленные данные в базе данных SQL Azure выглядят следующим образом:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. Повторно запустите конвейер **IncrementalCopyPipeline**, выполнив командлет **Invoke-AzureRmDataFactoryV2Pipeline**. Замените заполнители собственными именами группы ресурсов и фабрики данных.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup "<your resource group>" -dataFactoryName "<your data factory name>"
    ```
3. Проверьте состояние конвейера, выполняя командлет **Get-AzureRmDataFactoryV2ActivityRun**, пока вы не увидите, что все действия выполняются успешно. Замените заполнители нужными значениями времени для параметров RunStartedAfter и RunStartedBefore.  В этом руководстве мы используем -RunStartedAfter "2017/09/14" и -RunStartedBefore "2017/09/15".

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Пример выходных данных:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4.  В хранилище BLOB-объектов Azure вы увидите, что в нем был создан другой файл. В этом руководстве новый файл имеет имя `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`.  Открыв файл, вы увидите записи 2 строк:
5.  Проверив последнее значение из `watermarktable`, вы увидите, что значение предела снова обновлено.

    ```sql
    Select * from watermarktable
    ```
    Пример выходных данных: 
    
    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000

     
## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве вы выполнили следующие шаги: 

> [!div class="checklist"]
> * Определили столбец **предела** и сохранили его в базе данных SQL Azure.  
> * Создадите фабрику данных.
> * Создали связанные службы для базы данных SQL и хранилища BLOB-объектов. 
> * Создали наборы данных приемника и источника.
> * Создали конвейер.
> * Запустили конвейер.
> * Выполнили мониторинг выполнения конвейера. 

Перейдите к следующему руководству, чтобы узнать о преобразовании данных с помощью кластера Spark в Azure:

> [!div class="nextstepaction"]
>[Transform data in the cloud by using Spark activity in Azure Data Factory](tutorial-transform-data-spark-powershell.md) (Преобразование данных в облаке с помощью действий Spark в фабрике данных Azure)



