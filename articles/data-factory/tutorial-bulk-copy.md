---
title: Копирование данных в пакетном режиме с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как использовать фабрику данных Azure и действие копирования для копирования данных из исходного хранилища данных в хранилище данных назначения в пакетном режиме.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 28128e07b4beb03b1a8ecdd8546d54c19145689f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31593701"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Копирование нескольких таблиц в пакетном режиме с помощью фабрики данных Azure
В этом руководстве показано **копирование нескольких таблиц из базы данных SQL Azure в хранилище данных SQL Azure**. Этот подход можно применить и в других сценариях. Например, копирование таблиц из SQL Server или Oracle в базу данных SQL Azure, хранилище данных или большой двоичный объект Azure, копирование различных путей из большого двоичного объекта в таблицы базы данных SQL Azure.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

В целом это руководство включает следующие шаги:

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создание связанных служб базы данных SQL Azure, хранилища данных SQL Azure и службы хранилища Azure.
> * Создание наборов данных базы данных SQL Azure и хранилища данных SQL.
> * Создание конвейера для поиска таблиц, которые нужно скопировать, и конвейера для выполнения операции копирования. 
> * Запуск конвейера.
> * Мониторинг конвейера и выполнения действий.

В этом руководстве используется Azure PowerShell. Сведения об использовании других средств или пакетов SDK для создания фабрики данных см. в [этом кратком руководстве](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Комплексный рабочий процесс
В этом сценарии есть несколько таблиц базы данных SQL Azure, которые необходимо скопировать в хранилище данных SQL. Вот логическая последовательность действий рабочего процесса в конвейерах:

![Рабочий процесс](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* Первый конвейер ищет список таблиц, который необходимо скопировать в хранилище данных-приемник.  В качестве альтернативы можно создать таблицу метаданных, в которой перечислены все таблицы, которые нужно скопировать в хранилище данных-приемник. Затем конвейер активирует другой конвейер, который выполняет итерацию по каждой таблице базы данных и выполняет операцию копирования данных.
* Второй конвейер выполняет фактическое копирование. Он принимает список таблиц в качестве параметра. Чтобы добиться лучшей производительности, для каждой таблицы в списке скопируйте определенную таблицу из базы данных SQL Azure в соответствующую таблицу хранилища данных SQL Azure, используя [промежуточное копирование с помощью хранилища BLOB-объектов и PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). В этом примере первый конвейер передает список таблиц в качестве значения параметра. 

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

* **Azure PowerShell**. Следуйте инструкциям по [установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps).
* **Учетная запись хранения Azure.** Учетная запись хранения Azure используется в качестве промежуточного хранилища больших двоичных объектов в операции массового копирования. 
* **База данных SQL Azure**. Эта база данных содержит исходные данные. 
* **Хранилище данных Azure SQL.** Это хранилище данных содержит данные, копируемые из базы данных SQL. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Подготовка базы данных SQL и хранилища данных SQL

**Подготовка исходной базы данных SQL Azure:**

Создайте базу данных SQL Azure, используя пример данных Adventure Works LT, представленный в статье [Создание базы данных SQL Azure на портале Azure](../sql-database/sql-database-get-started-portal.md). В этом руководстве копируются все таблицы из этого примера базы данных в хранилище данных SQL.

**Подготовка хранилища данных-приемника SQL Azure:**

1. Если у вас нет хранилища данных SQL Azure, выполните шаги из раздела [Создание хранилища данных SQL](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).

2. Создайте соответствующие схемы таблиц в хранилище данных SQL Azure. [Служебную программу переноса](https://www.microsoft.com/download/details.aspx?id=49100) можно использовать для **миграции схемы** из базы данных SQL Azure в хранилище данных SQL Azure. Фабрика данных Azure используется для миграции и копирования данных на более поздних этапах.

## <a name="azure-services-to-access-sql-server"></a>Доступ служб Azure к серверу SQL Server

В базе данных SQL и хранилище данных SQL Azure предоставьте службам Azure доступ к серверу SQL Server. Убедитесь, что параметр **Разрешить доступ к службам Azure** имеет значение **ВКЛ** для вашего сервера Azure SQL. Этот параметр позволяет службе фабрики данных читать данные из базы данных SQL Azure и записывать данные в хранилище данных SQL Azure. Чтобы проверить и при необходимости включить этот параметр, сделайте следующее.

1. Щелкните **Все службы** слева и выберите **Серверы SQL**.
2. Выберите сервер и щелкните **Брандмауэр** в разделе **Параметры**.
3. На странице **Параметры брандмауэра** щелкните **ВКЛ** для параметра **Разрешить доступ к службам Azure**.

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. Запустите **PowerShell**. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.

    Выполните следующую команду и введите имя пользователя и пароль, которые используются для входа на портал Azure.
        
    ```powershell
    Connect-AzureRmAccount
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
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Обратите внимание на следующие моменты.

    * Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Чтобы создавать экземпляры фабрики данных, вы должны быть участником или администратором подписки Azure.
    * Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

## <a name="create-linked-services"></a>Создание связанных служб

В этом руководстве создаются три связанные службы для источника, приемника и промежуточного большого двоичного объекта соответственно, включая подключения к хранилищам данных:

### <a name="create-the-source-azure-sql-database-linked-service"></a>Создание исходной связанной службы базы данных SQL Azure

1. Создайте файл JSON с именем **AzureSqlDatabaseLinkedService.json** в папке **C:\ADFv2TutorialBulkCopy** и добавьте в него приведенное ниже содержимое. Если папка ADFv2TutorialBulkCopy отсутствует, создайте ее.

    > [!IMPORTANT]
    > Перед сохранением файла замените &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; и &lt;password&gt; значениями своей базы данных SQL Azure.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. В **Azure PowerShell** переключитесь в папку **ADFv2TutorialBulkCopy**.

3. Выполните командлет **Set-AzureRmDataFactoryV2LinkedService**, чтобы создать связанную службу **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Пример выходных данных:

    ```json
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Создание связанной службы-приемника хранилища данных SQL Azure

1. Создайте файл JSON с именем **AzureSqlDWLinkedService.json** в папке **C:\ADFv2TutorialBulkCopy** со следующим содержимым:

    > [!IMPORTANT]
    > Перед сохранением файла замените &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; и &lt;password&gt; значениями своей базы данных SQL Azure.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
            }
        }
    }
    ```

2. Чтобы создать связанную службу **AzureSqlDWLinkedService**, выполните командлет **Set-AzureRmDataFactoryV2LinkedService**.

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Пример выходных данных:

    ```json
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Создание промежуточной связанной службы хранилища Azure

В этом руководстве хранилища BLOB-объектов Azure используются в качестве области промежуточного хранения, чтобы включить PolyBase для повышения производительности копирования.

1. Создайте файл JSON с именем **AzureStorageLinkedService.json** в папке **C:\ADFv2TutorialBulkCopy** со следующим содержимым:

    > [!IMPORTANT]
    > Перед сохранением файла замените значения &lt;accountname&gt; и &lt;accountkey&gt; на имя вашей учетной записи хранения Azure и ее ключ.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
                }
            }
        }
    }
    ```

2. Чтобы создать связанную службу **AzureStorageLinkedService.json**, выполните командлет **Set-AzureRmDataFactoryV2LinkedService**.

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

## <a name="create-datasets"></a>Создание наборов данных

В этом руководстве создаются наборы данных источника и приемника, в которых указывается место хранения данных:

### <a name="create-a-dataset-for-source-sql-database"></a>Создание набора данных для исходной базы данных SQL

1. Создайте файл JSON с именем **AzureSqlDatabaseDataset.json** в папке **C:\ADFv2TutorialBulkCopy** со следующим содержимым. TableName является фиктивным,так как позже в действии копирования будет использован SQL-запрос для извлечения данных.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. Чтобы создать набор данных **AzureSqlDatabaseDataset**, выполните командлет **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Пример выходных данных:

    ```json
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Создание набора данных для хранилища данных-приемника SQL

1. Создайте файл JSON с именем **AzureSqlDWDataset.json** в папке **C:\ADFv2TutorialBulkCopy** со следующим содержимым: tableName задается в качестве параметра, позже действие копирования, которое ссылается на этот набор данных, передает фактическое значение в набор данных.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. Чтобы создать набор данных **AzureSqlDWDataset**, выполните командлет **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Пример выходных данных:

    ```json
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Создание конвейеров

В этом руководстве создается два конвейера:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Создание конвейера IterateAndCopySQLTables

Этот конвейер принимает список таблиц в качестве параметра. Для каждой таблицы в списке он копирует данные из таблицы в базе данных SQL Azure в хранилище данных SQL Azure с помощью промежуточного копирования и PolyBase.

1. Создайте файл JSON с именем **IterateAndCopySQLTables.json** в папке **C:\ADFv2TutorialBulkCopy** со следующим содержимым:

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from SQL database to SQL DW",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. Чтобы создать конвейер **IterateAndCopySQLTables**, выполните командлет **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Пример выходных данных:

    ```json
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Создание конвейера GetTableListAndTriggerCopyData

Этот конвейер выполняет два действия:

* Ищет системную таблицу базы данных SQL Azure, чтобы получить список таблиц для копирования.
* Активирует конвейер IterateAndCopySQLTables для выполнения копирования данных.

1. Создайте файл JSON с именем **GetTableListAndTriggerCopyData.json** в папке **C:\ADFv2TutorialBulkCopy** со следующим содержимым:

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
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

2. Чтобы создать конвейер **GetTableListAndTriggerCopyData**, выполните командлет **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Пример выходных данных:

    ```json
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>Запуск и мониторинг выполнения конвейера

1. Запустите выполнение главного конвейера GetTableListAndTriggerCopyData и запишите идентификатор выполнения конвейера для будущего мониторинга. В рамках конвейера запускается выполнение конвейера IterateAndCopySQLTables, как указано в действии ExecutePipeline.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  Выполните следующий скрипт, чтобы постоянно проверять состояние выполнения конвейера **GetTableListAndTriggerCopyData** и вывести окончательный результат выполнения конвейера и действия выполнения.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Вот результат примера выполнения:

    ```json
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Вы можете получить идентификатор выполнения конвейера **IterateAndCopySQLTables** и просмотреть подробный результат выполнения действия, как показано ниже.

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Вот результат примера выполнения:

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Подключитесь к хранилищу данных-приемнику SQL Azure и подтвердите, что данные из базы данных SQL Azure скопированы надлежащим образом.

## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы выполнили следующие шаги: 

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создание связанных служб базы данных SQL Azure, хранилища данных SQL Azure и службы хранилища Azure.
> * Создание наборов данных базы данных SQL Azure и хранилища данных SQL.
> * Создание конвейера для поиска таблиц, которые нужно скопировать, и конвейера для выполнения операции копирования. 
> * Запуск конвейера.
> * Мониторинг конвейера и выполнения действий.

Перейдите к следующему руководству, чтобы узнать о копировании данных по шагам из источника в место назначения:
> [!div class="nextstepaction"]
>[Пошаговая загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure](tutorial-incremental-copy-powershell.md)
