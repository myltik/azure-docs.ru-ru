---
title: Запуск пакета SSIS с помощью действия SSIS в фабрике данных Azure | Документация Майкрософт
description: В этой статье рассматривается запуск пакета SQL Server Integration Services (SSIS) из конвейера базы данных Azure с помощью действия SSIS.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: article
ms.date: 04/17/2018
ms.author: douglasl
ms.openlocfilehash: 6c8bbe7ef7f74638b978cdad5b59a89fd81d12a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="run-an-ssis-package-using-the-ssis-activity-in-azure-data-factory"></a>Запуск пакета SSIS с помощью действия SSIS в фабрике данных Azure
В этой статье описывается, как запустить пакет SSIS из конвейера фабрики данных Azure, используя действие SSIS. 

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Действие SSIS недоступно в службе фабрики данных версии 1, которая является общедоступной. Альтернативный метод запуска пакета SSIS с помощью службы фабрики данных версии 1 описывается в разделе [Вызов пакета SSIS с помощью действия хранимой процедуры в фабрике данных Azure](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>предварительным требованиям

### <a name="azure-sql-database"></a>Базы данных SQL Azure 
В этих пошаговых инструкциях используется база данных SQL Azure, в которой размещен каталог SSIS. Вы также можете использовать управляемый экземпляр SQL Azure (предварительная версия).

## <a name="create-an-azure-ssis-integration-runtime"></a>Создание среды выполнения интеграции Azure SSIS.
Создайте среду выполнения интеграции Azure SSIS, если у вас ее нет. Для этого выполните пошаговую инструкцию в статье [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Пользовательский интерфейс фабрики данных (портал Azure)
В этом разделе для создания конвейера фабрики данных с действием SSIS, которое запускает пакет SSIS, используется пользовательский интерфейс фабрики данных.

### <a name="create-a-data-factory"></a>Создание фабрики данных
Сначала нужно создать фабрику данных с помощью портала Azure. 

1. Запустите веб-браузер **Microsoft Edge** или **Google Chrome**. Сейчас только эти браузеры поддерживают пользовательский интерфейс фабрики данных.
2. Перейдите на [портал Azure](https://portal.azure.com). 
3. В меню слева щелкните **Создать**, выберите **Данные+аналитика** и щелкните **Фабрика данных**. 
   
   ![Создать -> Фабрика данных](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. На странице **Новая фабрика данных** введите **ADFTutorialDataFactory** в поле **Имя**. 
      
     ![Страница "Новая фабрика данных"](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Имя фабрики данных Azure должно быть **глобально уникальным**. Если вы увидите следующую ошибку для поля имени, введите другое имя фабрики данных (например, ваше_имя_ADFTutorialBulkCopyDF). Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](naming-rules.md), чтобы узнать правила именования для артефактов службы "Фабрика данных".
  
     ![Ошибка, связанная с недоступностью имени](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Выберите **подписку** Azure, в рамках которой вы хотите создать фабрику данных. 
4. Для **группы ресурсов** выполните одно из следующих действий.
     
      - Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке. 
      - Выберите **Создать новую**и укажите имя группы ресурсов.   
         
    Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/resource-group-overview.md).  
4. Выберите **V2 (Preview)** (V2 (предварительная версия)) для **версии**.
5. Укажите **расположение** фабрики данных. В этом раскрывающемся списке отображаются только сведения о расположениях, поддерживаемых службой "Фабрика данных". Хранилища данных (служба хранилища Azure, служба "База данных SQL Azure" и т. д.) и вычислительные ресурсы (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.
6. Кроме того, установите флажок **Закрепить на панели мониторинга**.     
7. Нажмите кнопку **Создать**.
8. На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных). 

    ![Элемент Deploying data factory (Развертывание фабрики данных)](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Когда завершится создание, откроется страница **Фабрика данных**, как показано на рисунке ниже.
   
    ![Домашняя страница фабрики данных](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Щелкните плитку **Создание и мониторинг**, чтобы открыть на отдельной вкладке приложение пользовательского интерфейса службы "Фабрика данных Azure". 

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Создание конвейера с помощью действия SSIS
На этом этапе вы создадите конвейер, используя пользовательский интерфейс фабрики данных. Вы добавите действие SSIS в конвейер и настроите его для запуска пакета SSIS. 

1. На странице "Начало работы" щелкните **Create pipeline** (Создать конвейер). 

    ![Страница "Начало работы"](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. На панели элементов **Действия** разверните элемент **Общие** и перетащите действие **Выполнить пакет SSIS** в область конструктора конвейера. 

   ![Перетаскивание действия SSIS в область конструктора](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. На вкладке **Общие** свойств действия SSIS введите имя и описание действия. Укажите необязательные значения времени ожидания и повторных попыток.

    ![Настройка свойств на вкладке "Общие"](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. На вкладке **Параметры** свойств действия SSIS выберите связанную среду выполнения интеграции Azure-SSIS с базой данных `SSISDB`, в которой развернут пакет. Укажите путь к пакету в базе данных `SSISDB` в формате `<folder name>/<project name>/<package name>.dtsx`. При необходимости укажите выполнение в 32-разрядной среде и предварительно определенный или пользовательский уровень ведения журнала. Затем укажите путь к среде в формате `<folder name>/<environment name>`.

    ![Настройка свойств на вкладке "Параметры"](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. Чтобы проверить конфигурацию конвейера, щелкните **Проверка** на панели инструментов. Чтобы закрыть **отчет о проверке конвейера**, нажмите кнопку **>>**.

6. Опубликуйте конвейер в фабрике данных, нажав кнопку **Опубликовать все**. 

### <a name="run-and-monitor-the-pipeline"></a>Запуск и мониторинг конвейера
В этом разделе вы активируете выполнение конвейера, а затем будете отслеживать его. 

1. Чтобы активировать конвейер, щелкните **Триггер** на панели инструментов, а затем **Trigger Now** (Активировать сейчас). 

    ![Trigger Now (Активировать сейчас)](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. На странице **Запуск конвейера** нажмите кнопку **Готово**. 
3. Перейдите на вкладку **Мониторинг** слева. На ней отображается выполнение конвейера и его состояние вместе с другой информацией (например, время начала выполнения). Чтобы обновить это представление, щелкните **Refresh** (Обновить).

    ![Запуски конвейера](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Щелкните ссылку **View Activity Runs** (Просмотр выполнений действий) в столбце **Actions** (Действия). Вы видите выполнение только одного действия, так как конвейер содержит одно действие (действие SSIS).

    ![Выполнение действия](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

4. Вы можете запустить следующий **запрос** к базе данных SSISDB на своем сервере SQL Server Azure, чтобы убедиться, что пакет выполнен. 

    ```sql
    select * from catalog.executions
    ```

    ![Проверка выполнения пакета](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Вы также можете создать запланированный триггер для запуска конвейера по расписанию (ежечасно, ежедневно и т. д.). Пример см. в разделе [Запуск конвейера по расписанию](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell
В этом разделе для создания конвейера фабрики данных с действием SSIS, которое запускает пакет SSIS, используется Azure PowerShell. 

Чтобы установить модули Azure PowerShell, выполните инструкции из статьи [Установка и настройка Azure PowerShell](/powershell/azure/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Создание фабрики данных
Вы можете использовать ту же фабрику данных, в которой есть среда выполнения интеграции Azure SSIS, или создать отдельную. В следующей процедуре представлены шаги для создания фабрики данных. Вы создадите в этой фабрике данных конвейер с действием SSIS. Данное действие SSIS запускает пакет SSIS. 

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
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

Обратите внимание на следующие моменты.

* Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль **участника**, **владельца** либо **администратора** подписки Azure.
* Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2, Западной Европе и Юго-Восточной Азии. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Создание конвейера с помощью действия SSIS 
На этом этапе вы создадите конвейер с действием SSIS. Это действие запускает пакет SSIS. 

1. Создайте JSON-файл **RunSSISPackagePipeline.json** в папке **C:\ADF\RunSSISPackage** с содержимым, как в приведенном ниже примере.

    > [!IMPORTANT]
    > Замените имена объектов, описания и пути, свойства и значения параметров, пароли и другие значения переменных перед сохранением файла. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [{
                "name": "mySSISActivity",
                "description": "My SSIS package/activity description",
                "type": "ExecuteSSISPackage",
                "typeProperties": {
                    "connectVia": {
                        "referenceName": "myAzureSSISIR",
                        "type": "IntegrationRuntimeReference"
                    },
                    "runtime": "x64",
                    "loggingLevel": "Basic",
                    "packageLocation": {
                        "packagePath": "FolderName/ProjectName/PackageName.dtsx"            
                    },
                    "environmentPath":   "FolderName/EnvironmentName",
                    "projectParameters": {
                        "project_param_1": {
                            "value": "123"
                        }
                    },
                    "packageParameters": {
                        "package_param_1": {
                            "value": "345"
                        }
                    },
                    "projectConnectionManagers": {
                        "MyAdonetCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "abc"
                                }
                            }
                        }
                    },
                    "packageConnectionManagers": {
                        "MyOledbCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "def"
                                }
                            }
                        }
                    },
                    "propertyOverrides": {
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
                            "value": 8,
                            "isSensitive": false
                        }
                    }
                },
                "policy": {
                    "timeout": "0.01:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30
                }
            }]
        }
    }
    ```

2.  В Azure PowerShell перейдите в папку `C:\ADF\RunSSISPackage`.

3. Чтобы создать конвейер **RunSSISPackagePipeline**, выполните командлет **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Пример выходных данных:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Создание конвейера
Используйте командлет **Invoke-AzureRmDataFactoryV2Pipeline** для запуска конвейера. Командлет позволяет получить идентификатор выполнения конвейера для дальнейшего мониторинга.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Выполнили мониторинг конвейера.

Запустите приведенный ниже скрипт PowerShell, чтобы проверять состояние выполнения, пока не закончится копирование данных. Скопируйте приведенный ниже скрипт в окно PowerShell и нажмите клавишу ВВОД. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

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

### <a name="create-a-trigger"></a>Создание триггера
На предыдущем шаге вы запустили конвейер по запросу. Вы также можете создать триггер расписания для запуска конвейера по расписанию (ежечасно, ежедневно и т. д.).

1. Создайте файл JSON с именем **MyTrigger.json** в папке **C:\ADF\RunSSISPackage** со следующим содержимым: 

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
2. В **Azure PowerShell** перейдите в папку **C:\ADF\RunSSISPackage**.
3. Выполните командлет **Set-AzureRmDataFactoryV2Trigger**, чтобы создать триггер. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. По умолчанию триггер находится в остановленном состоянии. Запустите триггер с помощью командлета **Start-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. Убедитесь, что триггер запущен, с помощью командлета **Get-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. Через час выполните следующую команду. Например, если текущее время 15:25 (UTC), запустите команду в 16:00 (UTC). 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    Вы можете запустить следующий запрос к базе данных SSISDB на своем сервере SQL Server Azure, чтобы убедиться, что пакет выполнен. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Дополнительная информация
Вы также можете отслеживать конвейер с помощью портала Azure. Пошаговые инструкции см. в разделе [Мониторинг конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
