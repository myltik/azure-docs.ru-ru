---
title: Добавочное копирование таблицы с помощью фабрики данных Azure | Документация Майкрософт
description: В этом руководстве вы создадите конвейер фабрики данных Azure, который пошагово копирует данные из базы данных SQL Azure в хранилище BLOB-объектов Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: a543bb1ceb14bb8f618e1cf644cc269a99719c28
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30182907"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Пошаговая загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure
В этом руководстве вы создадите фабрику данных Azure с конвейером, который загружает разностные данные из таблицы в базе данных SQL Azure в хранилище BLOB-объектов Azure. 


> [!NOTE]
> Эта статья относится к версии 2 фабрики данных Azure, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


В этом руководстве вы выполните следующие шаги:

> [!div class="checklist"]
> * Подготовите хранилище данных для хранения значений предела.
> * создадите фабрику данных;
> * создадите связанные службы; 
> * Создание наборов данных источника, приемника и предела.
> * Создали конвейер.
> * Запустили конвейер.
> * Выполнение мониторинга выполнения конвейера. 

## <a name="overview"></a>Обзор
Ниже приведена общая схема решения. 

![Пошаговая загрузка данных](media\tutorial-Incrementally-copy-powershell\incrementally-load.png)

Ниже приведены важные действия для создания этого решения. 

1. **Выберите столбец для предела.**
    Выберите один столбец в исходном хранилище данных, который можно использовать для создания среза новых или обновленных записей при каждом запуске. Как правило, данные в этом выбранном столбце (например, последнее_время_изменения или идентификатор) продолжают увеличиваться по мере создания или обновления строк. В качестве предела используется максимальное значение в этом столбце.

2. **Подготовьте хранилище данных для хранения значений предела.**   
    В этом руководстве вы сохраните значение предела в базе данных SQL.
    
3. **Создайте конвейер, используя следующий рабочий процесс**: 
    
    Конвейер в этом решении содержит следующие действия:
  
    * Создайте два действия поиска. Используйте первое действие поиска для получения последнего значения предела, а второе для получения нового значения предела. Эти значения передаются в действие копирования. 
    * Создайте действие копирования, копирующее строки из исходного хранилища данных со значениями столбцов предела, которые выше значений старого предела и меньше значений нового. Затем оно копирует разностные данные из исходного хранилища данных в хранилище BLOB-объектов в качестве нового файла. 
    * Создайте действие хранимой процедуры, которое обновляет значение предела для конвейера при последующем выполнении. 


Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям
* **База данных SQL Azure**. Используйте базу данных как исходное хранилище данных. Если у вас нет базы данных SQL, создайте ее, следуя указаниям в статье [Создание базы данных SQL Azure на портале Azure](../sql-database/sql-database-get-started-portal.md).
* **Хранилище Azure.** В этом руководстве в качестве приемника будет использоваться хранилище BLOB-объектов. Если у вас нет учетной записи хранения, создайте ее, следуя действиям в разделе [Создание учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account). Создайте контейнер с именем adftutorial. 
* **Azure PowerShell**. Следуйте инструкциям в статье [Установка и настройка Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-source-table-in-your-sql-database"></a>Создание таблицы источника данных в базе данных SQL
1. Откройте среду SQL Server Management Studio. В **обозревателе сервера** щелкните правой кнопкой мыши базу данных и выберите **Создать запрос**.

2. Выполните указанную ниже команду SQL для базы данных SQL, чтобы создать таблицу с именем `data_source_table` в качестве хранилища источника данных. 
    
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
    В этом руководстве в качестве столбца предела мы будем использовать LastModifytime. В следующей таблице показаны данные в хранилище источника данных.

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Создание дополнительной таблицы в базе данных SQL для хранения значения верхнего предела
1. Выполните указанную ниже команду SQL для базы данных SQL, чтобы создать таблицу с именем `watermarktable` для хранения значения предела.  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Задайте значение по умолчанию верхнего предела, используя имя таблицы исходного хранилища данных. В этом руководстве используется таблица с именем data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Просмотрите данные в таблице `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Выходные данные: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Создание хранимой процедуры в базе данных SQL 

Выполните указанную ниже команду, чтобы создать хранимую процедуру в базе данных SQL.

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
1. Определите переменную для имени группы ресурсов, которую в дальнейшем можно будет использовать в командах PowerShell. Скопируйте текст следующей команды в PowerShell, укажите имя [группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md) в двойных кавычках, а затем выполните команду. Например, `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Если группа ресурсов уже имеется, вы можете не перезаписывать ее. Назначьте переменной `$resourceGroupName` другое значение и еще раз выполните команду.

2. Определите переменную для расположения фабрики данных. 

    ```powershell
    $location = "East US"
    ```
3. Чтобы создать группу ресурсов Azure, выполните следующую команду: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Если группа ресурсов уже имеется, вы можете не перезаписывать ее. Назначьте переменной `$resourceGroupName` другое значение и еще раз выполните команду.

4. Определите переменную для имени фабрики данных. 

    > [!IMPORTANT]
    >  Измените имя фабрики данных, чтобы сделать его глобально уникальным. Например, укажите ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncCopyTutorialFactory";
    ```
5. Чтобы создать фабрику данных, выполните командлет **Set-AzureRmDataFactoryV2**. 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Обратите внимание на следующие моменты.

* Имя фабрики данных должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль участника, владельца либо администратора подписки Azure.
* Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища, база данных SQL и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.


## <a name="create-linked-services"></a>Создание связанных служб
Связанная служба в фабрике данных связывает хранилища данных и службы вычислений с фабрикой данных. В этом разделе вы создадите связанные службы для учетной записи хранения и базы данных SQL. 

### <a name="create-a-storage-linked-service"></a>Создание связанной службы хранилища
1. Создайте JSON-файл с именем AzureStorageLinkedService.json в папке C:\ADF со следующим содержимым. (Если папка ADF отсутствует, создайте ее.) Замените `<accountName>` и `<accountKey>` именем и ключом учетной записи хранения, прежде чем сохранять файл.

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
2. В PowerShell перейдите в папку ADF.

3. Выполните командлет **Set-AzureRmDataFactoryV2LinkedService**, чтобы создать связанную службу AzureStorageLinkedService. В указанном ниже примере вы передадите значения для параметров *ResourceGroupName* и *DataFactoryName*. 

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

### <a name="create-a-sql-database-linked-service"></a>Создание связанной службы базы данных SQL
1. Создайте в папке C:\ADF файл JSON с именем AzureSQLDatabaseLinkedService.json со следующим содержимым. (Если папка ADF отсутствует, создайте ее.) Вместо значений &lt;server&gt;, &lt;database&gt;, &lt;user id&gt; и &lt;password&gt; укажите имя сервера, имя базы данных, идентификатор пользователя и пароль, прежде чем сохранить файл. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database>; Persist Security Info=False; User ID=<user> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. В PowerShell перейдите в папку ADF.

3. Выполните командлет **Set-AzureRmDataFactoryV2LinkedService**, чтобы создать связанную службу AzureSQLDatabaseLinkedService. 

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
    В этом руководстве используется таблица с именем data_source_table. Замените ее, если используется таблица с другим именем.

2. Выполните командлет **Set-AzureRmDataFactoryV2Dataset**, чтобы создать набор данных SourceDataset.
    
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
    > В этом фрагменте кода предполагается, что у вас есть контейнер BLOB-объектов с именем adftutorial в хранилище BLOB-объектов. Создайте контейнер (если его еще нет) или присвойте ему имя имеющегося контейнера. Выходная папка `incrementalcopy` создается автоматически, если она не существует в контейнере. В этом руководстве имя файла создается динамически с помощью выражения `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

2. Выполните командлет **Set-AzureRmDataFactoryV2Dataset**, чтобы создать набор данных SinkDataset.
    
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

## <a name="create-a-dataset-for-a-watermark"></a>Создание набора данных для предела
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
2.  Выполните командлет **Set-AzureRmDataFactoryV2Dataset**, чтобы создать набор данных WatermarkDataset.
    
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


1. Создайте JSON-файл IncrementalCopyPipeline.json в той же папке со следующим содержимым: 

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
    

2. Выполните командлет **Set-AzureRmDataFactoryV2Pipeline** для создания конвейера IncrementalCopyPipeline.
    
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

1. Запустите конвейер IncrementalCopyPipeline, выполнив командлет **Invoke-AzureRmDataFactoryV2Pipeline**. Замените заполнители собственными именами группы ресурсов и фабрики данных.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ``` 
2. Проверьте состояние конвейера, выполняя командлет **Get-AzureRmDataFactoryV2ActivityRun**, пока вы не увидите, что все действия выполняются успешно. Замените заполнители нужными значениями времени для параметров *RunStartedAfter* и *RunStartedBefore*. В этом руководстве вы используете *-RunStartedAfter "2017/09/14"* и *-RunStartedBefore "2017/09/15"*.

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

1. В хранилище BLOB-объектов (хранилище приемника) вы должны увидеть, что данные скопированы в файл, заданный в SinkDataset. В этом руководстве в качестве имени файла используется `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`. Открыв файл, вы увидите, что записи в файле идентичны данным в базе данных SQL.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ``` 
2. Проверьте последнее значение из `watermarktable`. Вы увидите, что значение предела было обновлено.

    ```sql
    Select * from watermarktable
    ```
    
    Пример выходных данных:
 
    TableName | WatermarkValue
    --------- | --------------
    data_source_table 2017-09-05 8:06:00.000

### <a name="insert-data-into-the-data-source-store-to-verify-delta-data-loading"></a>Добавление данных в хранилище источника данных для проверки загрузки разностных данных

1. Добавьте новые данные в базу данных SQL (хранилище источника данных).

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')
    
    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ``` 

    Обновленные данные в базе данных SQL выглядят следующим образом:

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
2. Повторно запустите конвейер IncrementalCopyPipeline, выполнив командлет **Invoke-AzureRmDataFactoryV2Pipeline**. Замените заполнители собственными именами группы ресурсов и фабрики данных.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
3. Проверьте состояние конвейера, выполняя командлет **Get-AzureRmDataFactoryV2ActivityRun**, пока вы не увидите, что все действия выполняются успешно. Замените заполнители нужными значениями времени для параметров *RunStartedAfter* и *RunStartedBefore*. В этом руководстве вы используете *-RunStartedAfter "2017/09/14"* и *-RunStartedBefore "2017/09/15"*.

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
4. В хранилище BLOB-объектов вы увидите, что был создан другой файл. В этом руководстве новый файл имеет имя `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`. Открыв этот файл, вы увидите записи двух строк.

5. Проверьте последнее значение из `watermarktable`. Вы увидите, что значение предела было обновлено снова.

    ```sql
    Select * from watermarktable
    ```
    Пример выходных данных: 
    
    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000

     
## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы выполнили следующие шаги: 

> [!div class="checklist"]
> * Подготовите хранилище данных для хранения значений предела. 
> * создадите фабрику данных;
> * создадите связанные службы; 
> * Создание наборов данных источника, приемника и предела.
> * Создали конвейер.
> * Запустили конвейер.
> * Выполнение мониторинга выполнения конвейера. 

В этом руководстве с помощью конвейера мы скопировали данные из одной таблицы в базе данных SQL в хранилище BLOB-объектов. Перейдите к следующему руководству, чтобы узнать о копировании данных из нескольких таблиц в локальной базе данных SQL Server в базу данных SQL. 

> [!div class="nextstepaction"]
>[Добавочная загрузка данных из нескольких таблиц в SQL Server в базу данных SQL Azure](tutorial-incremental-copy-multiple-tables-powershell.md)



