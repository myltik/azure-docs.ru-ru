---
title: "Копирование данных в хранилище BLOB-объектов с помощью фабрики данных Azure | Документация Майкрософт"
description: "Создайте фабрику данных Azure, чтобы копировать данные между двумя папками в хранилище BLOB-объектов Azure, одна из которых находится в другом расположении в пределах того же хранилища."
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
ms.date: 11/16/2017
ms.author: jingwang
ms.openlocfilehash: 254dcb6642afc19f434df837c9073d2dd7314313
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Создание фабрики данных Azure с помощью PowerShell 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Версия 2 — предварительная](quickstart-create-data-factory-powershell.md)

В этом кратком руководстве описано создание фабрики данных Azure с помощью PowerShell. Конвейер, который вы создадите в этой фабрике данных, **копирует** данные из одной папки в другую в хранилище BLOB-объектов Azure. Инструкции по **преобразованию** данных с помощью фабрики данных Azure см. в [руководстве по преобразованию данных с помощью Spark](transform-data-using-spark.md). 

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью о [начале работы со службой фабрики данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Эта статья не содержит подробный обзор службы фабрики данных. Общие сведения о службе фабрики данных Azure см. в статье [Введение в фабрику данных Azure](introduction.md).

## <a name="prerequisites"></a>Предварительные требования

### <a name="azure-subscription"></a>Подписка Azure.
Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

### <a name="azure-roles"></a>Роли Azure
Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль **участника**, **владельца** либо **администратора** подписки Azure. На портале Azure щелкните **имя пользователя** в правом верхнем углу и выберите **Разрешения**, чтобы просмотреть существующие разрешения в подписке. Если у вас есть доступ к нескольким подпискам, выберите соответствующую подписку. Примеры инструкций по назначению пользователю роли см. в статье [о добавлении ролей](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Учетная запись хранения Azure
В этом кратком руководстве в качестве **исходного** и **целевого** хранилища данных используется учетная запись хранения Azure общего назначения (в частности, хранилища BLOB-объектов). Если у вас нет учетной записи хранения Azure общего назначения, см. инструкции по [созданию учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account). 

#### <a name="get-storage-account-name-and-account-key"></a>Получение имени и ключа учетной записи хранения
В этом кратком руководстве вы будете использовать имя и ключ своей учетной записи хранения Azure. Далее описана процедура получения имени и ключа учетной записи хранения. 

1. Откройте браузер и перейдите на [портал Azure](https://portal.azure.com). Войдите, используя имя пользователя и пароль Azure. 
2. Щелкните **Больше служб >** в меню слева, отфильтруйте содержимое по ключевому слову **хранение**, затем выберите пункт **Учетные записи хранения**.

    ![Поиск учетной записи хранения](media/quickstart-create-data-factory-powershell/search-storage-account.png)
3. В списке учетных записей хранения найдите с помощью фильтра свою учетную запись хранения (при необходимости), а затем выберите **эту учетную запись**. 
4. На странице **учетной записи хранения** выберите в меню параметр **Ключи доступа**.

    ![Получение имени и ключа учетной записи хранения](media/quickstart-create-data-factory-powershell/storage-account-name-key.png)
5. Скопируйте значения полей **Имя учетной записи хранения** и **key1** в буфер обмена. Вставьте их в Блокнот или другой редактор и сохраните файл.  

#### <a name="create-input-folder-and-files"></a>Создание входной папки и файлов
В этом разделе вы создадите контейнер больших двоичных объектов с именем **adftutorial** в хранилище BLOB-объектов Azure. Затем вы создадите папку с именем **input** в контейнере и отправите в нее пример файла. 

1. На странице **Учетная запись хранения** перейдите на вкладку **Обзор** и щелкните **BLOB-объекты**. 

    ![Выбор параметра "BLOB-объекты"](media/quickstart-create-data-factory-powershell/select-blobs.png)
2. На странице **Служба BLOB-объектов** щелкните **+ Контейнер** на панели инструментов. 

    ![Кнопка добавления контейнера](media/quickstart-create-data-factory-powershell/add-container-button.png)    
3. В диалоговом окне **Создание контейнера** введите **adftutorial** в поле имени и нажмите кнопку **ОК**. 

    ![Ввод имени контейнера](media/quickstart-create-data-factory-powershell/new-container-dialog.png)
4. Щелкните **adftutorial** в списке контейнеров. 

    ![Выбор контейнера](media/quickstart-create-data-factory-powershell/seelct-adftutorial-container.png)
1. На странице **Контейнер** щелкните **Отправить** на панели инструментов.  

    ![Кнопка "Отправить"](media/quickstart-create-data-factory-powershell/upload-toolbar-button.png)
6. На странице **Отправить BLOB-объект** щелкните **Дополнительно**.

    ![Переход по ссылке "Дополнительно"](media/quickstart-create-data-factory-powershell/upload-blob-advanced.png)
7. Откройте **Блокнот** и создайте файл **emp.txt** с содержимым, приведенным ниже. Сохраните файл в папке **c:\ADFv2QuickStartPSH**. Если папка **ADFv2QuickStartPSH** отсутствует, создайте ее.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. На странице **Отправить BLOB-объект** на портале Azure найдите и выберите файл **emp.txt** в поле **Файлы**. 
9. Введите в поле **Отправить в папку** значение **input**. 

    ![Параметры отправки больших двоичных объектов](media/quickstart-create-data-factory-powershell/upload-blob-settings.png)    
10. Убедитесь, что указана папка **input** и файл **emp.txt**, а затем щелкните **Отправить**.
11. В списке должен отобразиться файл **emp.txt** с состоянием отправки. 
12. Закройте страницу **Отправить BLOB-объект**, нажав значок **X** в углу. 

    ![Закрытие страницы отправки BLOB-объекта](media/quickstart-create-data-factory-powershell/close-upload-blob.png)
1. Не закрывайте страницу **контейнера**. Она понадобится для проверки выходных данных в конце этого руководства. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Установка Azure PowerShell
Установите последнюю версию среды Azure PowerShell, если ее нет на компьютере. 

1. В веб-браузере перейдите на страницу [скачивания пакетов Azure SDK](https://azure.microsoft.com/downloads/). 
2. Нажмите кнопку **Установка Windows** в разделе **Программы командной строки** -> **PowerShell**. 
3. Чтобы установить Azure PowerShell, запустите файл **MSI**. 

Подробные инструкции см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Вход в Azure PowerShell

1. Запустите **PowerShell** на компьютере. Не закрывайте Azure PowerShell, пока выполняются описанные в этом кратком руководстве инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.

    ![Запуск PowerShell](media/quickstart-create-data-factory-powershell/search-powershell.png)
1. Выполните следующую команду и введите те же имя пользователя Azure и пароль, которые используются для входа на портал Azure:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Если у вас несколько подписок, выполните следующую команду, чтобы просмотреть все подписки для этой учетной записи:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Выполните следующую команду, чтобы выбрать подписку, с которой вы собираетесь работать. Замените значение **SubscriptionId** на идентификатор подписки Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Создать фабрику данных
1. Определите переменную для имени группы ресурсов, которую в дальнейшем можно будет использовать в командах PowerShell. Скопируйте текст следующей команды в PowerShell, укажите имя [группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md) в двойных кавычках, а затем выполните команду. Например, `"adfrg"`.
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Определите переменную для имени фабрики данных. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. Определите переменную для расположения фабрики данных: 

    ```powershell
    $location = "East US"
    ```
4. Чтобы создать группу ресурсов Azure, выполните следующую команду: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Если группа ресурсов уже существует, вы можете не перезаписывать ее. Назначьте переменной `$resourceGroupName` другое значение и еще раз выполните команду. 
5. Чтобы создать фабрику данных, выполните командлет **Set-AzureRmDataFactoryV2**. 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Обратите внимание на следующие моменты.

* Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль **участника**, **владельца** либо **администратора** подписки Azure.
* Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

## <a name="create-a-linked-service"></a>Создание связанной службы

Связанная служба в фабрике данных связывает хранилища данных и службы вычислений с фабрикой данных. В этом кратком руководстве мы создадим службу, связанную со службой хранилища Azure, которая используется и как хранилище-источник, и как хранилище-приемник. Связанная служба содержит сведения о подключении, используемые фабрикой данных для подключения к ней в среде выполнения.

1. Создайте JSON-файл с именем **AzureStorageLinkedService.json** в папке **C:\ADFv2QuickStartPSH** и добавьте в него приведенное ниже содержимое. Если папка ADFv2QuickStartPSH отсутствует, создайте ее. 

    > [!IMPORTANT]
    > Перед сохранением файла замените значения &lt;accountname&gt; и &lt;accountkey&gt; на имя вашей учетной записи хранения Azure и ее ключ.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
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
На этом этапе вы можете определить набор данных, который будет представлять данные для копирования из источника в приемник. Это набор данных типа **AzureBlob**. Он относится к **связанной службе хранилища Azure**, созданной на предыдущем этапе, и использует параметр для создания свойства **folderPath**. Для входного набора данных значением этого параметра будет входной путь, который передается при помощи действия копирования в конвейере. Аналогичным образом, для выходного набора данных значением этого параметра будет выходной путь, который передается при помощи действия копирования. 

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
  
В этом кратком руководстве мы создадим конвейер с одним действием, которое принимает два параметра: входной и выходной путь для больших двоичных объектов. Значения для этих параметров устанавливаются при активации или выполнении конвейера. Для действия копирования используется тот же набор данных большого двоичного объекта, который был создан на предыдущем шаге, в качестве входного и выходного. Если набор данных используется в качестве входного, указывается путь к входным данным. И если набор данных используется в качестве выходного, указывается путь к выходным данным. 

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

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. Выполните командлет **Invoke-AzureRmDataFactoryV2Pipeline**, чтобы создать конвейер и передать значения параметров. Командлет позволяет получить идентификатор выполнения конвейера для дальнейшего мониторинга.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>Выполнили мониторинг конвейера.

1. Запустите приведенный ниже скрипт PowerShell, чтобы проверять состояние выполнения, пока не закончится копирование данных. Скопируйте приведенный ниже скрипт в окно PowerShell и нажмите клавишу ВВОД. 

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 10
    }
    ```

    Вот результат примера выполнения:

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

    При появлении ошибки
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    сделайте следующее: 
    1. В AzureStorageLinkedService.json проверьте правильность имени и ключа учетной записи хранения Azure. 
    2. Проверьте формат строки подключения. Свойства, например AccountName и AccountKey, разделяются точкой с запятой (`;`). 
    3. Если имя и ключ учетной записи взяты в угловые скобки, удалите скобки. 
    4. Вот пример строки подключения: 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. Повторно создайте связанную службу, выполнив инструкции в разделе [Создание связанной службы](#create-a-linked-service). 
    6. Повторно запустите конвейер, выполнив инструкции в разделе [Создание конвейера](#create-a-pipeline-run). 
    7. Выполните текущую команду мониторинга еще раз, чтобы отслеживать новый запуск конвейера. 
1. Запустите следующий скрипт, извлекающий сведения о выполнении действия копирования, например размер записанных и прочитанных данных.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. Убедитесь, что это выходные данные аналогичные следующему примеру результатов выполнения действия:

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>Проверка выходных данных
Конвейер автоматически создает выходную папку в контейнере больших двоичных объектов adftutorial. Затем он копирует файл emp.txt из входной папки в выходную. 

1. На странице контейнера **adftutorial** на портале Azure нажмите кнопку **Обновить**, чтобы появилась папка output. 
    
    ![Обновить](media/quickstart-create-data-factory-powershell/output-refresh.png)
2. Щелкните **output** в списке папок. 
2. Убедитесь, что файл **emp.txt** скопирован в папку output. 

    ![Обновить](media/quickstart-create-data-factory-powershell/output-file.png)

## <a name="clean-up-resources"></a>Очистка ресурсов
Вы можете удалить ресурсы, созданные в ходе работы с этим руководством, двумя способами. Вы можете удалить [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md), которая содержит все связанные ресурсы. Если же вы хотите сохранить другие ресурсы, удалите только фабрику данных, созданную в этом руководстве.

При удалении группы ресурсов будут удалены все входящие в нее ресурсы, включая фабрики данных. Выполните следующую команду, чтобы удалить всю группу ресурсов: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Если вам нужно удалить только фабрику данных, а не всю группу ресурсов, выполните следующую команду: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Перейдите к [руководствам](tutorial-copy-data-dot-net.md), чтобы узнать об использовании фабрики данных в различных сценариях. 
