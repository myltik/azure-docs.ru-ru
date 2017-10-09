---
title: "Создание фабрики данных Azure с помощью PowerShell | Документация Майкрософт"
description: "Создайте фабрику данных Azure для копирования данных из одного расположения в другое в хранилище BLOB-объектов Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 92f798244db1f69d01f46d0c0bcce9fe139bef05
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="create-a-data-factory-and-pipeline-using-powershell"></a>Создание фабрики данных и конвейера с помощью пакета PowerShell
Фабрика данных Azure — это облачная служба интеграции данных, которая позволяет создавать управляемые данными рабочие процессы в облаке для оркестрации и автоматизации перемещения и преобразования данных. С помощью фабрики данных Azure можно создавать и включать в расписание управляемые данными рабочие процессы (конвейеры), которые могут принимать данные из разнородных хранилищ данных, обрабатывать и преобразовывать эти данные с помощью служб вычислений (например, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics и машинного обучения Azure), а также публиковать выходные данные в хранилища данных (например, хранилище данных SQL Azure) для использования приложениями бизнес-аналитики. 

В этом кратком руководстве описано создание фабрики данных Azure с помощью PowerShell. В этой фабрике данных конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* **Учетная запись хранения Azure.** Хранилище BLOB-объектов используется как хранилища данных, являющиеся **источником** и **приемником**. Если у вас нет учетной записи хранения Azure, ознакомьтесь с разделом "Создание учетной записи хранения" в статье "Об учетных записях хранения Azure" (../storage/common/storage-create-storage-account.md#create-a-storage-account).
* Создайте **контейнер больших двоичных объектов** в хранилище BLOB-объектов, в контейнере создайте входную **папку** и отправьте несколько файлов в нее. 
* **Azure PowerShell**. Следуйте инструкциям по [установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps).
* [Обозреватель хранилищ Azure](https://azure.microsoft.com/features/storage-explorer/). Это средство можно использовать для подключения к службе хранилища больших двоичных объектов Azure, создания контейнера больших двоичных объектов, отправки входного файла и проверки выходного файла. 

## <a name="create-a-data-factory"></a>Создать фабрику данных

1. Запустите **PowerShell**. Не закрывайте Azure PowerShell, пока выполняются описанные в этом кратком руководстве инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.

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
2. Выполните командлет **Set-AzureRmDataFactoryV2**, чтобы создать фабрику данных. Перед выполнением команды замените заполнители собственными значениями. Замените **заполнители** своими значениями. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>";
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    Set-AzureRmDataFactoryV2 -ResourceGroupName "<your resource group to create the factory>" -Location "East US" -Name "<specify the name of data factory to create. It must be globally unique.>" 
    ```

    Обратите внимание на следующие моменты.

    * Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Чтобы создавать экземпляры фабрики данных, вы должны быть участником или администратором подписки Azure.
    * Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

## <a name="create-a-linked-service"></a>Создание связанной службы

Связанная служба в фабрике данных связывает хранилища данных и службы вычислений с фабрикой данных. В этом руководстве необходимо создать одну связанную служба хранилища Azure, используемую как в качестве источника, так и приемника, который в примере называется AzureStorageLinkedService.

1. Создайте JSON-файл с именем **AzureStorageLinkedService.json** в папке **C:\ADFv2QuickStartPSH** и добавьте в него приведенное ниже содержимое. Если папка ADFv2QuickStartPSH отсутствует, создайте ее. Перед сохранением файла замените значения &lt;accountname&gt; и &lt;accountkey&gt; на имя вашей учетной записи хранения Azure и ее ключ.

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

2. В **Azure PowerShell** перейдите в папку **ADFv2QuickStartPSH**.

3. Выполните командлет **Set-AzureRmDataFactoryV2LinkedService**, чтобы создать связанную службу: **AzureStorageLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Пример выходных данных:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>Создание набора данных

Вы можете определить набор данных, который будет представлять данные для копирования из источника в приемник. В этом примере набор данных большого двоичного объекта относится к связанным службам хранилища Azure, созданным на предыдущем шаге. Набор данных принимает параметр, значение которого задается в действии, использующем набор данных. Параметр используется для создания folderPath, указывающего, где находятся или хранятся данные.

1. Создайте файл JSON с именем **BlobDataset.json** в папке **C:\ADFv2QuickStartPSH** со следующим содержимым:

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": {
                    "value": "@{dataset().path}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Чтобы создать набор данных **BlobDataset** выполните командлет**Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    Пример выходных данных:

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Создание конвейера

В этом примере этот конвейер содержит одно действие и принимает два параметра: путь входного и выходного большого двоичного объекта. Значения для этих параметров устанавливаются при активации или выполнении конвейера. Действие копирования ссылается на тот же набор данных большого двоичного объекта, который был создан на предыдущем шаге, в качестве входного и выходного. Если набор данных используется в качестве входного, указывается путь к входным данным. И если набор данных используется в качестве выходного, указывается путь к выходным данным. 

1. Создайте файл JSON с именем **Adfv2QuickStartPipeline.json** в папке **C:\ADFv2QuickStartPSH** со следующим содержимым:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                        "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Чтобы создать конвейер **Adfv2QuickStartPipeline**, выполните командлет **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    Пример выходных данных:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>Создание конвейера

На этом шаге задайте значения параметров конвейера **inputPath** и **outputPath** с фактическими значениями путей большого двоичного объекта источника и приемника. Затем можно создать конвейер с использованием этих аргументов. 

1. Создайте файл JSON с именем **PipelineParameters.json** в папке **C:\ADFv2QuickStartPSH** со следующим содержимым:

    Замените значения inputPath и outputPath своими путями к большому двоичному объекту источника и приемника для копирования данных до сохранения файла.

    ```json
    {
        "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/foldername>",
        "outputPath": "<the blob path to copy data to, e.g. containername/foldername>"
    }
    ```

2. Выполните командлет **Invoke-AzureRmDataFactoryV2Pipeline**, чтобы создать конвейер и передать значения параметров. Эта команда также запишет идентификатор выполнения конвейера для будущего мониторинга.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-a-pipeline-run"></a>Мониторинг выполнения конвейера

1. Запустите следующий скрипт, чтобы проверять состояние выполнения, пока не закончится копирование данных.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
        }

        Write-Host  "Pipeline is running...status: " $run.Status -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    ```

    Вот результат примера выполнения:

    ```
    Key                  : 00000000-0000-0000-0000-000000000000
    Timestamp            : 9/7/2017 8:31:26 AM
    RunId                : 000000000-0000-0000-0000-000000000000
    DataFactoryName      : <dataFactoryname>
    PipelineName         : Adfv2QuickStartPipeline
    Parameters           : {inputPath: <inputBlobPath>, outputPath: <outputBlobPath>}
    ParametersCount      : 2
    ParameterNames       : {inputPath, outputPath}
    ParameterNamesCount  : 2
    ParameterValues      : {<inputBlobPath>, <outputBlobPath>}
    ParameterValuesCount : 2
    RunStart             : 9/7/2017 8:30:45 AM
    RunEnd               : 9/7/2017 8:31:26 AM
    DurationInMs         : 41291
    Status               : Succeeded
    Message              :
    ```

2. Запустите следующий скрипт, извлекающий сведения о выполнении действия копирования, например размер записанных и прочитанных данных.

    ```powershell
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. Убедитесь, что это выходные данные аналогичные следующему примеру результатов выполнения действия:

    ```json
    Activity run details:
    ResourceGroupName : adf
    DataFactoryName   : <dataFactoryname>
    ActivityName      : CopyFromBlobToBlob
    Timestamp         : 9/7/2017 8:24:06 AM
    PipelineRunId     : 9b362a1d-37b5-449f-918c-53a8d819d83f
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityStart     : 9/7/2017 8:23:30 AM
    ActivityEnd       : 9/7/2017 8:24:06 AM
    Duration          : 36331
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 331452208
    "dataWritten": 331452208
    "copyDuration": 23
    "throughput": 14073.209
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    ```

## <a name="verify-the-output"></a>Проверка выходных данных
Воспользуйтесь такими средствами, как [обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/), чтобы проверить скопирован ли большой двоичный объект из inputBlobPath в outputBlobPath.

## <a name="clean-up-resources"></a>Очистка ресурсов
Вы можете удалить ресурсы, созданные в ходе работы с этим руководством, двумя способами. Вы можете удалить [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md), которая содержит все связанные ресурсы. Если же вы хотите сохранить другие ресурсы, удалите только фабрику данных, созданную в этом руководстве.

Выполните следующую команду, чтобы удалить всю группу ресурсов: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Выполните следующую команду для удаления только фабрики данных: 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Дальнейшие действия
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Перейдите к [руководствам](tutorial-copy-data-dot-net.md), чтобы узнать об использовании фабрики данных в различных сценариях. 
