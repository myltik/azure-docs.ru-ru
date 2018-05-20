---
title: Вызов пакета SSIS из фабрики данных Azure с помощью действия хранимой процедуры | Документация Майкрософт
description: В этой статье рассматривается вызов пакета SQL Server Integration Services (SSIS) из конвейера базы данных Azure с помощью действия хранимой процедуры.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: article
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 309a64c47eb95ca2ca05bb82bc1c41256305823d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Вызов пакета SSIS с помощью действия хранимой процедуры в фабрике данных Azure
В этой статье описывается, как вызвать пакет SSIS из конвейера фабрики данных Azure, используя действие хранимой процедуры. 

> [!NOTE]
> Статья относится к фабрике данных версии 1, которая является общедоступной. Если вы используете фабрику данных версии 2, которая является общедоступной предварительной, ознакомьтесь с [этой статьей](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>предварительным требованиям

### <a name="azure-sql-database"></a>Базы данных SQL Azure 
В этих пошаговых инструкциях используется база данных SQL Azure, в которой размещен каталог SSIS. Вы также можете использовать управляемый экземпляр SQL Azure (предварительная версия).

### <a name="create-an-azure-ssis-integration-runtime"></a>Создание среды выполнения интеграции Azure SSIS.
Создайте среду выполнения интеграции Azure SSIS, если у вас ее нет. Для этого выполните пошаговую инструкцию в статье [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](../tutorial-create-azure-ssis-runtime-portal.md). Для создания среды выполнения интеграции Azure SSIS необходимо создать фабрику данных версии 2. 

## <a name="azure-portal"></a>Портал Azure
В этом разделе с помощью портала Azure вы создадите конвейер фабрики данных с действием хранимой процедуры, которое вызывает пакет SSIS.

### <a name="create-a-data-factory"></a>Создание фабрики данных
Сначала нужно создать фабрику данных с помощью портала Azure. 

1. Перейдите на [портал Azure](https://portal.azure.com). 
2. В меню слева щелкните **Создать**, выберите **Данные+аналитика** и щелкните **Фабрика данных**. 
   
   ![Создать -> Фабрика данных](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. На странице **Новая фабрика данных** введите **ADFTutorialDataFactory** в поле **Имя**. 
      
     ![Страница "Новая фабрика данных"](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Имя фабрики данных Azure должно быть **глобально уникальным**. Если вы увидите следующую ошибку для поля имени, введите другое имя фабрики данных (например, ваше_имя_ADFTutorialBulkCopyDF). Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](data-factory-naming-rules.md), чтобы узнать правила именования для артефактов службы "Фабрика данных".

    `Data factory name ADFTutorialDataFactory is not available`
3. Выберите **подписку** Azure, в рамках которой вы хотите создать фабрику данных. 
4. Для **группы ресурсов** выполните одно из следующих действий.
     
      - Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке. 
      - Выберите **Создать новую**и укажите имя группы ресурсов.   
         
    Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../../azure-resource-manager/resource-group-overview.md).  
4. Выберите **V1** в качестве **версии**.
5. Укажите **расположение** фабрики данных. В этом раскрывающемся списке отображаются только сведения о расположениях, поддерживаемых службой "Фабрика данных". Хранилища данных (служба хранилища Azure, служба "База данных SQL Azure" и т. д.) и вычислительные ресурсы (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.
6. Кроме того, установите флажок **Закрепить на панели мониторинга**.     
7. Нажмите кнопку **Создать**.
8. На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных). 

    ![Элемент Deploying data factory (Развертывание фабрики данных)](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Когда завершится создание, откроется страница **Фабрика данных**, как показано на рисунке ниже.
   
    ![Домашняя страница фабрики данных](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Щелкните плитку **Создать и развернуть**, чтобы открыть редактор фабрики данных.

    ![Редактор фабрики данных](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Создание связанной службы базы данных SQL Azure
Создайте связанную службу, которая свяжет базу данных SQL Azure, содержащую каталог SSIS, с вашей фабрикой данных. Фабрика данных использует информацию связанной службы для подключения к базе данных SSISDB и выполняет хранимую процедуру для запуска пакета SSIS. 

1. В редакторе фабрики данных щелкните в меню пункт **Новое хранилище данных** и выберите **База данных SQL Azure**. 

    ![Новое хранилище данных -> База данных SQL Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. В области справа выполните следующие действия:

    1. Замените `<servername>` именем сервера Azure SQL. 
    2. Замените `<databasename>` именем базы данных каталога SSIS (**SSISDB**). 
    3. Замените `<username@servername>` именем пользователя, у которого есть доступ к кластеру серверу Azure SQL. 
    4. Замените `<password>` паролем этого пользователя. 
    5. Разверните связанную службу, нажав кнопку **Развернуть** на панели инструментов. 

        ![Связанная служба базы данных SQL Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Создание пустого набора данных для вывода
Этот пустой выходной набор данных используется для выполнения расписания конвейера. Обратите внимание, что частота составляет час, а интервал — 1. Это значит, что конвейер выполняется раз в час на протяжении всего периода между временем начала и окончания конвейера. 

1. Слева на странице редактора фабрики данных щелкните **... Дополнительно** -> **Новый набор данных** -> **Azure SQL**.

    ![Дополнительно -> Новый набор данных](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. Скопируйте следующий фрагмент кода JSON в правую панель редактора JSON. 
    
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
3. На панели инструментов щелкните **Развернуть** . Это действие развертывает набор данных в службе фабрики данных Azure. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Создание конвейера с действием хранимой процедуры 
На этом этапе создается конвейер с действием хранимой процедуры. Это действие вызывает хранимую процедуру sp_executesql для запуска пакета SSIS. 

1. В левой области щелкните **... Дополнительно** и **Новый конвейер**.
2. Скопируйте следующий фрагмент кода JSON в редактор JSON. 

    > [!IMPORTANT]
    > Прежде чем сохранять файл, замените заполнители &lt;folder name&gt;, &lt;project name&gt;, &lt;package name&gt; именами папки, проекта и пакета в каталоге SSIS соответственно.

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
            "start": "2018-01-19T00:00:00Z",
            "end": "2018-01-19T05:00:00Z",
            "isPaused": false
        }
    }    
    ```
3. На панели инструментов щелкните **Развернуть** . Это действие развертывает конвейер в службе фабрики данных Azure. 

### <a name="monitor-the-pipeline-run"></a>Выполнили мониторинг конвейера.
Для выходного набора данных определено расписание ежечасного выполнения. Время окончания конвейера отстоит от времени запуска на пять часов. Таким образом, отображается пять запусков конвейера. 

1. Закройте окно редактора, чтобы увидеть домашнюю страницу фабрики данных. Щелкните плитку **Мониторинг и управление**. 

    ![Плитка "Схема"](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Измените **время начала** и **время окончания**, указав значения **01/18/2018 08:30** и **01/20/2018 08:30** соответственно, и щелкните **Применить**. Вы увидите **окна действий**, связанные с выполнением конвейера. 

    ![Окна действий](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Дополнительные сведения о мониторинге конвейеров см в статье [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью нового приложения по мониторингу и управлению](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
В этом разделе с помощью Azure PowerShell вы создадите конвейер фабрики данных с действием хранимой процедуры, которое вызывает пакет SSIS.

Чтобы установить модули Azure PowerShell, выполните инструкции из статьи [Установка и настройка Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-factory"></a>Создание фабрики данных
В следующей процедуре представлены шаги для создания фабрики данных. Вы создадите конвейер с действием хранимой процедуры в фабрике данных. Действие хранимой процедуры выполняет хранимую процедуру в базе данных SSISDB для запуска вашего пакета SSIS.

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

5. Чтобы создать фабрику данных, выполните командлет **New-AzureRmDataFactory**, используя свойства Location и ResourceGroupName из переменной $ResGrp: 
    
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
Создайте связанную службу, которая свяжет базу данных SQL Azure, содержащую каталог SSIS, с вашей фабрикой данных. Фабрика данных использует информацию связанной службы для подключения к базе данных SSISDB и выполняет хранимую процедуру для запуска пакета SSIS. 

1. В папке **C:\ADF\RunSSISPackage** создайте файл JSON с именем **AzureSqlDatabaseLinkedService.json** и следующим содержимым: 

    > [!IMPORTANT]
    > Перед сохранением файла замените заполнители &lt;servername&gt;, &lt;username&gt;@&lt;servername&gt; и &lt;password&gt; значениями для используемой базы данных SQL Azure.

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
2. В **Azure PowerShell** перейдите в папку **C:\ADF\RunSSISPackage**.
3. Выполните командлет **New-AzureRmDataFactoryLinkedService**, чтобы создать связанную службу **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Создание выходного набора данных
Этот пустой выходной набор данных используется для выполнения расписания конвейера. Обратите внимание, что частота составляет час, а интервал — 1. Это значит, что конвейер выполняется раз в час на протяжении всего периода между временем начала и окончания конвейера. 

1. Создайте файл OuputDataset.json со следующим содержимым: 
    
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
2. Выполните командлет **New-AzureRmDataFactoryDataset**, чтобы создать набор данных. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Создание конвейера с действием хранимой процедуры 
На этом этапе создается конвейер с действием хранимой процедуры. Это действие вызывает хранимую процедуру sp_executesql для запуска пакета SSIS. 

1. Создайте файл JSON с именем **MyPipeline.json** в папке **C:\ADF\RunSSISPackage** со следующим содержимым:

    > [!IMPORTANT]
    > Прежде чем сохранять файл, замените заполнители &lt;folder name&gt;, &lt;project name&gt;, &lt;package name&gt; именами папки, проекта и пакета в каталоге SSIS соответственно.

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

2. Чтобы создать конвейер **RunSSISPackagePipeline**, выполните командлет **New-AzureRmDataFactoryPipeline**.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Выполнили мониторинг конвейера.

2. Выполните командлет **Get-AzureRmDataFactorySlice**, чтобы получить сведения обо всех срезах выходного набора данных\*\*, то есть созданной конвейером выходной таблицы.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Обратите внимание, здесь указывается то же значение StartDateTime, что и в JSON конвейера. 
3. Выполните командлет **Get-AzureRmDataFactoryRun** , чтобы получить сведения о действиях, выполняемых для конкретного среза.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Вы можете выполнять этот командлет до тех пор, пока не увидите срез с состоянием **Готово** или **Сбой**. 

    Вы можете запустить следующий запрос к базе данных SSISDB на своем сервере SQL Server Azure, чтобы убедиться, что пакет выполнен. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Дополнительная информация
См дополнительные сведения о [действии хранимой процедуры](data-factory-stored-proc-activity.md).

