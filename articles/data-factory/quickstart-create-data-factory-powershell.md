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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 63e4c654409651f6655da1bed6ab2f544cf024dd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-azure-data-factory-and-pipeline-using-powershell"></a>Создание фабрики данных Azure и конвейера с помощью PowerShell
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Версия 2 — предварительная](quickstart-create-data-factory-powershell.md)

В этом кратком руководстве описано создание фабрики данных Azure с помощью PowerShell. В этой фабрике данных создаваемый конвейер копирует данные из одного расположения в другое расположение в хранилище BLOB-объектов Azure. Инструкции по преобразованию данных с помощью фабрики данных Azure см. в [руководстве по преобразованию данных с помощью Spark](transform-data-using-spark.md). 

Эта статья не содержит подробный обзор службы фабрики данных. Общие сведения о службе фабрики данных Azure см. в статье [Введение в фабрику данных Azure](introduction.md).

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью о [начале работы со службой фабрики данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Предварительные требования

### <a name="azure-subscription"></a>Подписка Azure.
Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

### <a name="azure-storage-account"></a>Учетная запись хранения Azure
В этом кратком руководстве в качестве **исходного** и **принимающего (целевого)** хранилища данных используется учетная запись хранения Azure общего назначения (в частности, хранилища BLOB-объектов). Если у вас нет учетной записи хранения Azure общего назначения, см. инструкции по [созданию учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account). 

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
В этом разделе вы создадите контейнер больших двоичных объектов с именем adftutorial в хранилище BLOB-объектов Azure. Затем вы создадите папку с именем input в контейнере и отправите в нее пример файла. 

1. Установите [обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/), если его нет на вашем компьютере. 
2. Запустите **обозреватель службы хранилища Microsoft Azure** на компьютере.   
3. В окне **подключения к службе хранилища Azure** выберите **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения) и нажмите кнопку **Next** (Далее). Если окно **подключения к службе хранилища Azure** не отображается, щелкните правой кнопкой мыши элемент **Storage Accounts** (Учетные записи хранения) в представлении в виде дерева, а затем выберите пункт **Connect to Azure storage** (Подключиться к хранилищу Azure). 

    ![Подключение к службе хранилища Azure](media/quickstart-create-data-factory-powershell/storage-explorer-connect-azure-storage.png)
4. В окне **Attach using Name and Key** (Присоединение с помощью имени и ключа) вставьте **имя** и **ключ учетной записи**, сохраненные на предыдущем шаге. Затем щелкните **Далее**. 
5. В окне **Connection Summary** (Сводка по подключению) нажмите кнопку **Connect** (Подключиться).
6. Убедитесь, что ваша учетная запись хранения отображается в представлении в виде дерева в разделе **(Local and Attached)** -> **Storage Accounts** ((Локальные и присоединенные) > Учетные записи хранения). 
7. Разверните узел **Blob Containers** (Контейнеры больших двоичных объектов) и убедитесь, что контейнер больших двоичных объектов **adftutorial** не существует. Если он уже существует, пропустите следующие шаги по созданию контейнера. 
8. Щелкните правой кнопкой мыши элемент **Blob Containers** (Контейнеры больших двоичных объектов) и выберите **Create Blob Container** (Создать контейнер BLOB-объектов).

    ![Создание контейнера больших двоичных объектов](media/quickstart-create-data-factory-powershell/stroage-explorer-create-blob-container-menu.png)
9. Введите **adftutorial** в качестве имени и нажмите клавишу **ВВОД**. 
10. Убедитесь, что контейнер **adftutorial** выбран в представлении в виде дерева. 
11. На панели инструментов щелкните **New Folder** (Создать папку). 

    ![Кнопка создания папки](media/quickstart-create-data-factory-powershell/stroage-explorer-new-folder-button.png)
12. В окне **Create New Virtual Directory** (Создание виртуального каталога) введите **input** в поле **имени**и нажмите кнопку **OK** (ОК). 

    ![Диалоговое окно создания каталога](media/quickstart-create-data-factory-powershell/storage-explorer-create-new-directory-dialog.png)
13. Откройте **Блокнот** и создайте файл с именем **emp.txt** и следующим содержимым: 
    
    ```
    John, Doe
    Jane, Doe
    ```    
    Сохраните его в папку **c:\ADFv2QuickStartPSH**. Создайте папку **ADFv2QuickStartPSH**, если она еще не существует. 
14. На панели инструментов нажмите кнопку **Upload** (Отправить) и выберите **Upload Files** (Отправка файлов). 

    ![Кнопка "Отправить"](media/quickstart-create-data-factory-powershell/storage-explorer-upload-button.png)
15. В окне **отправки файлов** для элемента **Files** (Файлы) выберите `...`. 
16. В окне **Select folder to upload** (Выбор папки для отправки) перейдите в папку с файлом **emp.txt** и выберите его. 

    ![Диалоговое окно отправки файлов](media/quickstart-create-data-factory-powershell/storage-explorer-upload-files-dialog.png)
17. В окне **Upload files** (Отправка файлов) нажмите кнопку **Upload** (Отправить). 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Установка Azure PowerShell
Установите последнюю версию среды Azure PowerShell, если ее нет на компьютере. 

1. В веб-браузере перейдите на страницу [скачивания пакетов Azure SDK](https://azure.microsoft.com/downloads/). 
2. Нажмите кнопку **Установка Windows** в разделе **Программы командной строки** -> **PowerShell**. 
3. Чтобы установить Azure PowerShell, запустите файл **MSI**. 

Подробные инструкции см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Вход в Azure PowerShell
Запустите **PowerShell** на компьютере. Не закрывайте Azure PowerShell, пока выполняются описанные в этом кратком руководстве инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.

1. Выполните следующую команду и введите имя пользователя Azure и пароль, которые используются для входа на портал Azure.
       
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
1. Определите переменную для имени группы ресурсов, которую в дальнейшем можно будет использовать в командах PowerShell. Скопируйте текст следующей команды в PowerShell, укажите имя [группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md) в двойных кавычках, а затем выполните команду. 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Определите переменную для имени фабрики данных, которую в дальнейшем можно будет использовать в командах PowerShell. 

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
    Если группа ресурсов уже существует, вы можете не перезаписывать ее. Назначьте переменной `$resourceGroupName` другое значение и повторите попытку. Если вы хотите совместно использовать группу ресурсов с другими пользователями, перейдите к следующему шагу. 
5. Чтобы создать фабрику данных, выполните командлет **Set-AzureRmDataFactoryV2**. 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Обратите внимание на следующие моменты.

* Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Чтобы создавать экземпляры фабрики данных, вы должны быть **участником** или **администратором** подписки Azure.
* Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

## <a name="create-a-linked-service"></a>Создание связанной службы

Связанная служба в фабрике данных связывает хранилища данных и службы вычислений с фабрикой данных. В этом руководстве необходимо создать одну связанную служба хранилища Azure, используемую как в качестве источника, так и приемника, который в примере называется AzureStorageLinkedService.

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

Вы можете определить набор данных, который будет представлять данные для копирования из источника в приемник. В этом примере набор данных большого двоичного объекта относится к связанным службам хранилища Azure, созданным на предыдущем шаге. Набор данных принимает параметр, значение которого задается в действии, использующем набор данных. Параметр используется для создания пути **folderPath**, указывающего, где находятся или хранятся данные.

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
  
В этом примере этот конвейер содержит одно действие и принимает два параметра: путь входного и выходного большого двоичного объекта. Значения для этих параметров устанавливаются при активации или выполнении конвейера. Для действия копирования используется тот же набор данных большого двоичного объекта, который был создан на предыдущем шаге, в качестве входного и выходного. Если набор данных используется в качестве входного, указывается путь к входным данным. И если набор данных используется в качестве выходного, указывается путь к выходным данным. 

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

    Замените значения **inputPath** и **outputPath** своими путями к исходному и целевому большому двоичному объекту, если используются другие контейнеры и папки.

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
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
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
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

2. Запустите следующий скрипт, извлекающий сведения о выполнении действия копирования, например размер записанных и прочитанных данных.

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
Конвейер автоматически создает выходную папку в контейнере больших двоичных объектов adftutorial. Затем он копирует файл emp.txt из входной папки в выходную. Воспользуйтесь [обозревателем службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/), чтобы проверить, скопированы ли большие двоичные объекты из inputBlobPath в outputBlobPath. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Вы можете удалить ресурсы, созданные в ходе работы с этим руководством, двумя способами. Вы можете удалить [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md), которая содержит все связанные ресурсы. Если же вы хотите сохранить другие ресурсы, удалите только фабрику данных, созданную в этом руководстве.

Выполните следующую команду, чтобы удалить всю группу ресурсов: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Выполните следующую команду для удаления только фабрики данных: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Перейдите к [руководствам](tutorial-copy-data-dot-net.md), чтобы узнать об использовании фабрики данных в различных сценариях. 
