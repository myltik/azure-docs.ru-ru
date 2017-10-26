---
title: "Копирование локальных данных в облако с помощью фабрики данных Azure | Документация Майкрософт"
description: "Узнайте, как копировать данные из локального хранилища данных в облако Azure с помощью локальной среды выполнения интеграции в фабрике данных Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/14/2017
ms.author: jingwang
ms.openlocfilehash: 9aac9c9bcc609a91415438279419d4cc8e237fcb
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2017
---
# <a name="copy-data-between-on-premises-and-cloud"></a>Копирование данных между локальной средой и облаком
Фабрика данных Azure — это облачная служба интеграции данных, которая позволяет создавать управляемые данными рабочие процессы в облаке для оркестрации и автоматизации перемещения и преобразования данных. С помощью фабрики данных Azure можно создавать и включать в расписание управляемые данными рабочие процессы (конвейеры), которые могут принимать данные из разнородных хранилищ данных, обрабатывать и преобразовывать эти данные с помощью служб вычислений (например, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics и машинного обучения Azure), а также публиковать выходные данные в хранилища данных (например, хранилище данных SQL Azure) для использования приложениями бизнес-аналитики.

В этом руководстве будет использоваться Azure PowerShell для создания конвейера фабрики данных, который копирует данные из локальной базы данных SQL Server в хранилище BLOB-объектов Azure. Вы создадите и будете использовать локальную среду выполнения интеграции (IR) фабрики данных Azure, которая обеспечивает интеграцию локальных и облачных хранилищ данных.  Сведения об использовании других средств или пакетов SDK для создания фабрики данных см. в [этом кратком руководстве](quickstart-create-data-factory-dot-net.md).

В этом руководстве вы выполните следующие шаги:

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создадите локальную среду выполнения интеграции.
> * Создадите и зашифруете локальную связанную службу SQL Server в локальной среде выполнения интеграции.
> * Создадите связанную службу хранилища Azure.
> * Создавать наборы данных SQL Server и службы хранилища Azure.
> * Создадите конвейер с действием копирования для перемещения данных.
> * Запуск конвейера.
> * Выполнять мониторинг выполнения конвейера и действия.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* **SQL Server.** В этом руководстве используйте локальную базу данных SQL Server в качестве **исходного** хранилища данных.
* **Учетная запись хранения Azure.** В этом руководстве в качестве **места назначения и приемника** будет использоваться хранилище BLOB-объектов Azure. в статье [Об учетных записях хранения Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) .
* **Azure PowerShell**. Следуйте инструкциям по [установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps).

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
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $df = Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Обратите внимание на следующие моменты.

    * Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

        ```
        Data factory name "<data factory name>" is not available.
        ```

    * Чтобы создавать экземпляры фабрики данных, вы должны быть участником или администратором подписки Azure.
    * Сейчас фабрика данных позволяет создавать фабрики данных только в восточной части США и восточной части США 2. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

## <a name="create-a-self-hosted-ir"></a>Создание локальной среды IR

В этом разделе вы создадите локальную среду выполнения интеграции и свяжите ее с локальным узлом (компьютером).

1. Создайте локальную среду выполнения интеграции. Используйте уникальное имя в случае, если существует другая среда выполнения интеграции с тем же именем.

   ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Пример выходных данных:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Выполните следующую команду, чтобы получить состояние созданной среды выполнения интеграции.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Пример выходных данных:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Выполните следующую команду, чтобы получить ключи проверки подлинности для регистрации локальной среды выполнения интеграции в службе фабрики данных в облаке. Скопируйте один из ключей для регистрации локальной среды выполнения интеграции.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Пример выходных данных:

   ```json
   {
       "AuthKey1":  "IR@8437c862-d6a9-4fb3-87dd-7d4865a9e845@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@8437c862-d6a9-4fb3-85dd-7d4865a9e845@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

4. [Скачайте](https://www.microsoft.com/download/details.aspx?id=39717) локальную среду выполнения интеграции на локальный компьютер Windows и используйте ключ проверки подлинности, полученный на предыдущем шаге, чтобы зарегистрировать локальную среду выполнения интеграции вручную.

   ![Регистрация среды выполнения интеграции](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

   Когда локальная среда выполнения интеграции будет успешно зарегистрирована, вы увидите следующее сообщение:

   ![Успешно зарегистрирована](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

   Когда узел будет подключен к облачной службе, отобразится следующая страница:

   ![Узел подключен](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Создание связанных служб

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Создание связанных служб хранилища Azure (место назначения и приемник)

1. Создайте JSON-файл с именем **AzureStorageLinkedService.json** в папке **C:\ADFv2Tutorial** со следующим содержимым. Создайте папку ADFv2Tutorial, если она еще не существует.  Замените значения &lt;accountname&gt; и &lt;accountkey&gt; на имя вашей учетной записи хранения Azure и ее ключ.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. В **Azure PowerShell** перейдите в папку **ADFv2Tutorial**.

   Выполните командлет **Set-AzureRmDataFactoryV2LinkedService**, чтобы создать связанную службу: **AzureStorageLinkedService**. Командлеты, используемые в этом руководстве, принимают значения параметров **ResourceGroupName** и **DataFactoryName**. Кроме того, вы можете передать объект **DataFactory**, возвращенный командлетом Set-AzureRmDataFactoryV2, не вводя параметры ResourceGroupName и DataFactoryName при каждом запуске командлета.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Пример выходных данных:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Создание и шифрование связанной службы SQL Server (источник)

1. Создайте JSON-файл с именем **SqlServerLinkedService.json** в папке **C:\ADFv2Tutorial** с указанным ниже содержимым. Замените **&lt;servername>**, **&lt;databasename>**, **&lt;username>** и **&lt;password>** собственными именами сервера, базы данных, пользователя и пароля SQL Server, прежде чем сохранить файл. Замените **&lt;integration** **runtime** **name>** именем своей среды выполнения интеграции.

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. Чтобы зашифровать конфиденциальные данные в полезных данных JSON в локальной среде выполнения интеграции, можно выполнить командлет **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** и передать указанные выше полезные данные JSON. Шифрование гарантирует, что учетные данные будут зашифрованы с помощью программного интерфейса защиты данных (API защиты данных) и сохранены локально в узле локальной среды выполнения интеграции. Полезные выходные данные можно перенаправить в другой JSON-файл (в этом случае encryptedLinkedService.json), содержащий зашифрованные учетные данные.

    Замените **&lt;integration runtime name&gt;** именем собственной среды выполнения интеграции, прежде чем выполнить команду.

   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName <integration runtime name> -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Выполните следующую команду с помощью JSON-файла из предыдущего шага для создания **SqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Создание наборов данных

### <a name="prepare-an-on-premises-sql-server-for-the-tutorial"></a>Подготовка локальной службы SQL Server для руководства

На этом этапе вы создадите наборы входных и выходных данных, которые представляют собой входные и выходные данные для операции копирования (из локальной базы данных SQL Server в хранилище BLOB-объектов Azure). Прежде чем создавать наборы данных, необходимо сделать следующее (подробное описание шагов приводится далее):

- В базе данных SQL Server, добавленной в фабрику данных в качестве связанной службы, создайте таблицу с именем **emp** и вставьте в нее пару записей в качестве примера.
- В учетной записи хранения BLOB-объектов Azure, которую вы добавили в качестве связанной службы в фабрику данных, создайте контейнер BLOB-объектов с именем **adftutorial** .


1. В базе данных SQL Server, которую вы указали для локальных связанных служб (**SqlServerLinkedService**), используйте следующий сценарий SQL, чтобы создать в базе данных таблицу **emp**.

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Вставьте несколько образцов в таблицу:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```



### <a name="create-a-dataset-for-source-sql-database"></a>Создание набора данных для исходной базы данных SQL

1. Создайте JSON-файл с именем **SqlServerDataset.json** в папке **C:\ADFv2Tutorial** со следующим содержимым:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Чтобы создать набор данных **SqlServerDataset**, выполните командлет **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Пример выходных данных:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Создание набора данных для хранилища BLOB-объектов Azure (приемника)

1. Создайте JSON-файл с именем **AzureBlobDataset.json** в папке **C:\ADFv2Tutorial** со следующим содержимым:

    > [!IMPORTANT]
    > В этом образце кода предполагается, что у вас есть контейнер с именем **adftutorial** в хранилище BLOB-объектов Azure.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Чтобы создать набор данных **AzureBlobDataset**, выполните командлет **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Пример выходных данных:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>Создание конвейеров

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>Создание конвейера SqlServerToBlobPipeline

1. Создайте JSON-файл с именем **SqlServerToBlobPipeline.json** в папке **C:\ADFv2Tutorial** со следующим содержимым:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Чтобы создать конвейер **SQLServerToBlobPipeline**, выполните командлет **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Пример выходных данных:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>Запуск и мониторинг выполнения конвейера

1. Запустите выполнение конвейера SQLServerToBlobPipeline и запишите идентификатор выполнения конвейера для будущего мониторинга.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. Выполните указанный ниже скрипт, чтобы постоянно проверять состояние выполнения конвейера **SQLServerToBlobPipeline** и вывести конечный результат.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Вот результат примера выполнения:

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Вы можете получить идентификатор выполнения конвейера **SQLServerToBlobPipeline** и просмотреть подробный результат выполнения действия, как показано ниже.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Вот результат примера выполнения:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 4,
      "throughput": 0.01,
      "errors": []
    }
    ```
4. Подключитесь к хранилищу BLOB-объектов Azure (приемнику) и подтвердите, что данные из базы данных SQL Azure были скопированы надлежащим образом.

## <a name="next-steps"></a>Дальнейшие действия
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создавать локальную среду выполнения интеграции.
> * Создавать и шифровать локальную связанную службу SQL Server в локальной среде выполнения интеграции.
> * Создавать связанную службу хранилища Azure.
> * Создавать наборы данных SQL Server и службы хранилища Azure.
> * Создадите конвейер с действием копирования для перемещения данных.
> * Запуск конвейера.
> * Выполнять мониторинг выполнения конвейера и действия.

Список хранилищ данных, поддерживаемых фабрикой данных Azure в качестве источников и приемников, см. в разделе о [поддерживаемых хранилищах данных и форматах](copy-activity-overview.md#supported-data-stores-and-formats).

Перейдите к следующему руководству, чтобы узнать о копировании данных в пакетном режиме из источника в место назначения:

> [!div class="nextstepaction"]
>[Копирование нескольких таблиц в пакетном режиме с помощью фабрики данных Azure](tutorial-bulk-copy.md)
