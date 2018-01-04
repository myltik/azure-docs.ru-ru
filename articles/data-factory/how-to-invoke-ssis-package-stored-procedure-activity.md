---
title: "Пакет служб SSIS, с помощью фабрики данных Azure - действия хранимой процедуры вызова | Документы Microsoft"
description: "В этой статье описывается вызов пакета SQL Server Integration Services (SSIS) из конвейера фабрики данных Azure с помощью действия хранимой процедуры."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 12/07/2017
ms.author: jingwang
ms.openlocfilehash: 713e9ad7a76c15cbde912954e00991a80b995683
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Вызвать пакет служб SSIS с помощью хранимой процедуры действия в фабрике данных Azure
В этой статье описывается вызов пакета служб SSIS из конвейера фабрики данных Azure с помощью действия хранимой процедуры. 

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется версия 1 служба фабрики данных, который является общедоступной версии (GA), см. раздел [пакетов служб SSIS вызова неуправляемого кода, с помощью действие хранимой процедуры в версии 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Технические условия

### <a name="azure-sql-database"></a>Базы данных SQL Azure 
В этой статье пошаговом базы данных Azure SQL, на котором размещен каталог служб SSIS. Можно также использовать Azure управляемый экземпляр SQL (личной предварительной версии).

## <a name="create-an-azure-ssis-integration-runtime"></a>Создание среды выполнения интеграции Azure SSIS.
Создание среды выполнения служб SSIS Azure integration, если у вас его нет, выполнив пошаговые инструкции в [учебника: пакеты служб SSIS, развертывание](tutorial-deploy-ssis-packages-azure.md).

### <a name="azure-powershell"></a>Azure PowerShell
Чтобы установить модули Azure PowerShell, выполните инструкции из статьи [Установка и настройка Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="create-a-data-factory"></a>Создание фабрики данных
Можно использовать ту же фабрику данных, имеющий IR Azure SSIS или создать фабрику данных. Далее описаны действия по созданию фабрики данных. Для создания конвейера в действие хранимой процедуры в этой фабрике данных. Действие хранимая процедура выполняет хранимую процедуру в базе данных SSISDB, чтобы выполнить пакет служб SSIS. 

1. Определите переменную для имени группы ресурсов, которую в дальнейшем можно будет использовать в командах PowerShell. Скопируйте текст следующей команды в PowerShell, укажите имя [группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md) в двойных кавычках, а затем выполните команду. Например, `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Если группа ресурсов уже существует, вы можете не перезаписывать ее. Назначьте переменной `$ResourceGroupName` другое значение и еще раз выполните команду.
2. Чтобы создать группу ресурсов Azure, выполните следующую команду: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Если группа ресурсов уже существует, вы можете не перезаписывать ее. Назначьте переменной `$ResourceGroupName` другое значение и еще раз выполните команду. 
3. Определите переменную для имени фабрики данных. 

    > [!IMPORTANT]
    >  Измените имя фабрики данных, чтобы оно было глобально уникальным. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Чтобы создать фабрику данных, выполните следующий командлет **Set-AzureRmDataFactoryV2**, используя свойства Location и ResourceGroupName из переменной $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Обратите внимание на следующие моменты.

* Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль **участника**, **владельца** либо **администратора** подписки Azure.
* Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

### <a name="create-an-azure-sql-database-linked-service"></a>Создание связанной службы базы данных SQL Azure
Создайте связанную службу для связывания базы данных Azure SQL, на котором размещен каталог служб SSIS для фабрики данных. Фабрика данных использует сведения в этой связанной службе для подключения к базе данных SSISDB и выполняет хранимую процедуру для запуска пакета служб SSIS. 

1. Создайте JSON-файл с именем **AzureSqlDatabaseLinkedService.json** в **C:\ADF\RunSSISPackage** папку со следующим содержимым: 

    > [!IMPORTANT]
    > Замените &lt;servername&gt;, &lt;username&gt;, и &lt;пароль&gt; со значениями базы данных SQL Azure до сохранения файла.

    ```json
    {
        "name": "AzureSqlDbLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. В **Azure PowerShell**, переключитесь в **C:\ADF\RunSSISPackage** папки.

3. Выполните командлет **Set-AzureRmDataFactoryV2LinkedService**, чтобы создать связанную службу **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

## <a name="create-a-pipeline-with-stored-procedure-activity"></a>Создать конвейер с действиями хранимой процедуры 
На этом шаге создания конвейера с действие хранимой процедуры. Действие вызывает хранимой процедуры sp_executesql Чтобы выполнить пакет служб SSIS. 

1. Создайте JSON-файл с именем **RunSSISPackagePipeline.json** в **C:\ADF\RunSSISPackage** папку со следующим содержимым:

    > [!IMPORTANT]
    > Замените &lt;имя папки&gt;, &lt;имя проекта&gt;, &lt;имя ПАКЕТА&gt; с именами папки, проекта и пакета в каталоге служб SSIS до сохранения файла. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDbLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Чтобы создать конвейер: **RunSSISPackagePipeline**, запустите **AzureRmDataFactoryV2Pipeline набор** командлета.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
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
Используйте **Invoke AzureRmDataFactoryV2Pipeline** для выполнения в конвейер. Командлет позволяет получить идентификатор выполнения конвейера для дальнейшего мониторинга.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

## <a name="monitor-the-pipeline-run"></a>Выполнили мониторинг конвейера.

Запустите приведенный ниже скрипт PowerShell, чтобы проверять состояние выполнения, пока не закончится копирование данных. Скопируйте приведенный ниже скрипт в окно PowerShell и нажмите клавишу ВВОД. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

## <a name="create-a-trigger"></a>Создание триггера
На предыдущем шаге вызывается конвейера по запросу. Можно также создать триггер расписание для выполнения в конвейер по расписанию (каждый час, день, и т. д.).

1. Создайте JSON-файл с именем **MyTrigger.json** в **C:\ADF\RunSSISPackage** папку со следующим содержимым: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. В **Azure PowerShell**, переключитесь в **C:\ADF\RunSSISPackage** папки.
3. Запустите **AzureRmDataFactoryV2Trigger набор** командлет для создания триггера. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. По умолчанию триггер находится в остановленном состоянии. Запуск триггера, запустив **AzureRmDataFactoryV2Trigger начала** командлета. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Убедитесь, что триггер запускается путем запуска **Get AzureRmDataFactoryV2Trigger** командлета. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Выполните следующую команду, после наступления следующего часа. Например если текущее время — 15:25 по UTC, выполните команду в 4: 00 UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Запуском следующего запроса к базе данных SSISDB в сервер Azure SQL, чтобы убедиться, что пакет выполнен. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Дальнейшие действия
Также можно отслеживать с помощью портала Azure конвейера. Пошаговые инструкции см. в разделе [отслеживать конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
