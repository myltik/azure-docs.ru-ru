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
ms.openlocfilehash: 8aabe45a1627b1a897ca9fe4bda581c7a3f6bc03
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Вызвать пакет служб SSIS с помощью хранимой процедуры действия в фабрике данных Azure
В этой статье описывается вызов пакета служб SSIS из конвейера фабрики данных Azure с помощью действия хранимой процедуры. 

> [!NOTE]
> В этой статье относится к версии 1 для фабрики данных, который обычно доступен. Если вы используете версию 2 службы фабрики данных, которая находится в общедоступной предварительной версии, см. раздел [пакетов служб SSIS вызова неуправляемого кода, с помощью действие хранимой процедуры в версии 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Технические условия

### <a name="azure-sql-database"></a>Базы данных SQL Azure 
В этой статье пошаговом базы данных Azure SQL, на котором размещен каталог служб SSIS. Можно также использовать Azure управляемый экземпляр SQL (личной предварительной версии).

### <a name="create-an-azure-ssis-integration-runtime"></a>Создание среды выполнения интеграции Azure SSIS.
Создание среды выполнения служб SSIS Azure integration, если у вас его нет, выполнив пошаговые инструкции в [учебника: пакеты служб SSIS, развертывание](../tutorial-deploy-ssis-packages-azure.md). Необходимо создать фабрику данных версии 2 для создания среды выполнения служб SSIS Azure интеграции. 

### <a name="azure-powershell"></a>Azure PowerShell
Чтобы установить модули Azure PowerShell, выполните инструкции из статьи [Установка и настройка Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Создание фабрики данных
Далее описаны действия по созданию фабрики данных. Для создания конвейера в действие хранимой процедуры в этой фабрике данных. Действие хранимая процедура выполняет хранимую процедуру в базе данных SSISDB, чтобы выполнить пакет служб SSIS.

1. Определите переменную для имени группы ресурсов, которую в дальнейшем можно будет использовать в командах PowerShell. Скопируйте текст следующей команды в PowerShell, укажите имя [группы ресурсов Azure](../../azure-resource-manager/resource-group-overview.md) в двойных кавычках, а затем выполните команду. Например, `"adfrg"`. 
   
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

5. Чтобы создать фабрику данных, выполните следующую **New AzureRmDataFactory** командлета, используя свойство расположение и ResourceGroupName из переменной $ResGrp: 
    
    ```powershell       
    $df = New-AzureRmDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Обратите внимание на следующие моменты.

* Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль **участника**, **владельца** либо **администратора** подписки Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Создание связанной службы базы данных SQL Azure
Создайте связанную службу для связывания базы данных Azure SQL, на котором размещен каталог служб SSIS для фабрики данных. Фабрика данных использует сведения в этой связанной службе для подключения к базе данных SSISDB и выполняет хранимую процедуру для запуска пакета служб SSIS. 

1. Создайте JSON-файл с именем **AzureSqlDatabaseLinkedService.json** в **C:\ADF\RunSSISPackage** папку со следующим содержимым: 

    > [!IMPORTANT]
    > Замените &lt;servername&gt;, &lt;username&gt;@&lt;servername&gt; и &lt;пароль&gt; со значениями базы данных SQL Azure перед Сохранение файла.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. В **Azure PowerShell**, переключитесь в **C:\ADF\RunSSISPackage** папки.
3. Запустите **New AzureRmDataFactoryLinkedService** для создания связанной службы: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

## <a name="create-an-output-dataset"></a>Создание выходного набора данных
Этот выходной набор данных является пустой набор данных, вызывающее расписание конвейера. Обратите внимание, что задана час и интервал равен 1. Таким образом в конвейере выполняется после часа в конвейере время начала и окончания. 

1. Создание файла OuputDataset.json со следующим содержимым: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Запустите **New AzureRmDataFactoryDataset** для создания набора данных. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

## <a name="create-a-pipeline-with-stored-procedure-activity"></a>Создать конвейер с действиями хранимой процедуры 
На этом шаге создания конвейера с действие хранимой процедуры. Действие вызывает хранимой процедуры sp_executesql Чтобы выполнить пакет служб SSIS. 

1. Создайте JSON-файл с именем **MyPipeline.json** в **C:\ADF\RunSSISPackage** папку со следующим содержимым:

    > [!IMPORTANT]
    > Замените &lt;имя папки&gt;, &lt;имя проекта&gt;, &lt;имя пакета&gt; с именами папки, проекта и пакета в каталоге служб SSIS до сохранения файла.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Чтобы создать конвейер: **RunSSISPackagePipeline**, запустите **New AzureRmDataFactoryPipeline** командлета.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

## <a name="monitor-the-pipeline-run"></a>Выполнили мониторинг конвейера.

2. Запустите **Get AzureRmDataFactorySlice** для получения сведений о всех фрагментов из выходного набора данных **, которая является таблицей конвейера выходные данные.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Обратите внимание, здесь указывается то же значение StartDateTime, что и в JSON конвейера. 
3. Выполните командлет **Get-AzureRmDataFactoryRun** , чтобы получить сведения о действиях, выполняемых для конкретного среза.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Вы можете выполнять этот командлет до тех пор, пока не увидите срез с состоянием **Готово** или **Сбой**. 

    Запуском следующего запроса к базе данных SSISDB в сервер Azure SQL, чтобы убедиться, что пакет выполнен. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Дальнейшие действия
Сведения об активности хранимую процедуру в разделе [действия хранимой процедуры](data-factory-stored-proc-activity.md) статьи.

