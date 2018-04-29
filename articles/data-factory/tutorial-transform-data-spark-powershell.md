---
title: Преобразование данных с помощью Spark в фабрике данных Azure | Документация Майкрософт
description: В этом руководстве представлены пошаговые инструкции по преобразованию данных с использованием действия Spark в фабрике данных Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: c8676b4e7228105216f23de7c898c43c40fc2d0a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Преобразование данных в облаке с помощью действия Spark в фабрике данных Azure
В этом руководстве вы используете Azure PowerShell для создания конвейера фабрики данных, который преобразовывает данные с помощью действия Spark и служба, связанная по запросу HDInsight. В этом руководстве вы выполните следующие шаги:

> [!div class="checklist"]
> * Создадите фабрику данных. 
> * Создание и развертывание связанных служб.
> * Создание и развертывание конвейера. 
> * Запуск конвейера.
> * Выполнение мониторинга выполнения конвейера.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям
* **Учетная запись хранения Azure.** Нужно создать входной файл и скрипт Python и передать их в хранилище Azure. Выходные данные программы Spark хранятся в этой учетной записи хранения. Кластер Spark по запросу использует ту же учетную запись хранения, что и его основное хранилище.  
* **Azure PowerShell**. Следуйте инструкциям по [установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Отправка скрипта Python в учетную запись хранилища BLOB-объектов
1. Создайте файл Python с именем **WordCount_Spark.py** со следующим содержимым: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Замените свойство **storageAccountName&lt;&gt;** именем своей учетной записи хранения Azure. Затем сохраните файл. 
3. В хранилище BLOB-объектов Azure создайте контейнер с именем **adftutorial**, если он не существует. 
4. Создайте папку с именем **spark**.
5. Создайте вложенную папку с именем **script** в папке **spark**. 
6. Отправьте файл **WordCount_Spark.py** во вложенную папку **script**. 


### <a name="upload-the-input-file"></a>Отправка входного файла
1. Создайте файл с определенным текстом и назовите его **minecraftstory.txt**. Программа Spark подсчитывает количество слов в этом тексте. 
2. Создайте вложенную папку с именем `inputfiles` в папке `spark`. 
3. Отправьте файл `minecraftstory.txt` во вложенную папку `inputfiles`. 

## <a name="author-linked-services"></a>Создание связанных служб
Создайте две связанные службы в этом разделе: 
    
- Связанную службу хранилища Azure, которая связывает учетную запись хранения Azure с фабрикой данных. Это хранилище используется кластером HDInsight по запросу. В нем также содержится скрипт Spark для выполнения. 
- Связанную службу HDInsight по запросу. Фабрика данных Azure автоматически создает кластер HDInsight, запускает программу Spark, а затем удаляет кластер HDInsight после простоя в течение предварительно настроенного времени. 

### <a name="azure-storage-linked-service"></a>Связанная служба хранения Azure
Создайте файл JSON, используя предпочитаемый редактор, скопируйте следующее определение JSON связанной службы хранилища Azure и сохраните файл как **MyStorageLinkedService.json**.  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      }
    }
}
```
Обновите параметры &lt;storageAccountName&gt; и &lt;storageAccountKey&gt;, использовав имя и ключ своей учетной записи хранения Azure. 


### <a name="on-demand-hdinsight-linked-service"></a>Связанная служба HDInsight по запросу
Создайте файл JSON, используя предпочитаемый редактор, скопируйте следующее определение JSON связанной службы HDInsight Azure и сохраните файл как **MyOnDemandSparkLinkedService.json**.  

```json
{
    "name": "MyOnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "<subscriptionID> ",
        "servicePrincipalId": "<servicePrincipalID>",
        "servicePrincipalKey": {
          "value": "<servicePrincipalKey>",
          "type": "SecureString"
        },
        "tenant": "<tenant ID>",
        "clusterResourceGroup": "<resourceGroupofHDICluster>",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "MyStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
```
Обновите значения следующих свойств в определении связанных служб: 

- **hostSubscriptionId**. Замените значение &lt;&gt; на идентификатор подписки Azure. В этой подписке создается кластер HDInsight по требованию. 
- **tenant.** Замените &lt;tenantID&gt; на идентификатор своего клиента Azure. 
- **servicePrincipalId**, **servicePrincipalKey**. Замените &lt;servicePrincipalID&gt; и &lt;servicePrincipalKey&gt; на идентификатор и ключ субъекта-службы в Azure Active Directory. Этому субъекту-службе должна быть назначена роли участника подписки или группы ресурсов, в которой создается кластер. Дополнительные сведения см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../azure-resource-manager/resource-group-create-service-principal-portal.md). 
- **clusterResourceGroup.** Замените &lt;resourceGroupOfHDICluster&gt; на имя группы ресурсов, в которой необходимо создать кластер HDInsight. 

> [!NOTE]
> Azure HDInsight имеет ограничение на общее количество ядер, которые можно использовать в каждом поддерживаемом регионе Azure. Для связанной службы HDInsight по требованию будет создан кластер HDInsight в расположении хранилища Azure, используемом в качестве его основного хранилища. Убедитесь, что имеется достаточное количество квот ядра для успешного создания кластера. Дополнительные сведения см. в статье [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 


## <a name="author-a-pipeline"></a>Создание конвейера 
На этом этапе создайте конвейер с действием Spark. В действии используется пример **статистики**. Загрузите содержимое из этого расположения, если вы еще не сделали этого.

Создайте файл JSON в предпочитаемом редакторе, скопируйте следующее определение JSON определения конвейера и сохраните его как **MySparkOnDemandPipeline.json**. 

```json
{
  "name": "MySparkOnDemandPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "MyOnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
```

Обратите внимание на следующие моменты. 

- rootPath указывает на папку spark контейнера adftutorial. 
- entryFilePath указывает на файл WordCount_Spark.py во вложенной папке скрипта папки spark. 


## <a name="create-a-data-factory"></a>Создание фабрики данных 
Вы создали связанную службу и определения конвейера в файлах JSON. Теперь нужно создать фабрику данных и развернуть связанную службу и конвейер файлов JSON с помощью командлетов PowerShell. Последовательно выполните следующие команды PowerShell: 

1. По очереди задайте переменные.

    **Имя группы ресурсов**
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```

    **Имя фабрики данных (оно должно быть глобально уникальным)** 
    ```powershell
    $dataFactoryName = "MyDataFactory09102017"
    ```
    
    **Имя конвейера**
    ```powershell
    $pipelineName = "MySparkOnDemandPipeline" # Name of the pipeline
    ```
2. Запустите **PowerShell**. Не закрывайте Azure PowerShell, пока выполняются описанные в этом кратком руководстве инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды. Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

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
3. Создайте группу ресурсов ADFTutorialResourceGroup. 

    ```powershell
    New-AzureRmResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Создайте фабрику данных. 

    ```powershell
     $df = Set-AzureRmDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Чтобы просмотреть выходные данные, выполните следующую команду: 

    ```powershell
    $df
    ```
5. Перейдите в папку, где были созданы файлы JSON, и выполните следующую команду, чтобы развернуть связанную службу хранилища Azure. 
       
    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
6. Выполните следующую команду, чтобы развернуть связанную службу Spark по запросу. 
       
    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyOnDemandSparkLinkedService" -File "MyOnDemandSparkLinkedService.json"
    ```
7. Выполните следующую команду, чтобы развернуть конвейер. 
       
    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MySparkOnDemandPipeline.json"
    ```
    
## <a name="start-and-monitor-a-pipeline-run"></a>Запуск и мониторинг выполнения конвейера  

1. Запустите конвейер. Эта команда также запишет идентификатор выполнения конвейера для будущего мониторинга.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName  
    ```
2. Запустите следующий скрипт, чтобы проверять состояние выполнения, пока оно не завершится.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    
        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }

    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n" 
    ```  
3. Вот результат примера выполнения: 

    ```
    Pipeline run status: In Progress
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : 
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : 
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 
    DurationInMs      : 
    Status            : InProgress
    Error             :
    …
    
    Pipeline ' MySparkOnDemandPipeline' run finished. Result:
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : MyDataFactory09102017
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : {clusterInUse, jobId, ExecutionProgress, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 9/20/2017 6:46:30 AM
    DurationInMs      : 763466
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "clusterInUse": "https://ADFSparkSamplexxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurehdinsight.net/"
    "jobId": "0"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MySparkActivity"
    ```
4. Убедитесь, что папка с именем `outputfiles` создана в папке `spark` контейнера adftutorial с выходными данными программы Spark. 


## <a name="next-steps"></a>Дополнительная информация
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Вы научились выполнять следующие задачи: 

> [!div class="checklist"]
> * Создадите фабрику данных. 
> * Создание и развертывание связанных служб.
> * Создание и развертывание конвейера. 
> * Запуск конвейера.
> * Выполнение мониторинга выполнения конвейера.

Чтобы узнать, как преобразовать данные, запустив скрипт Hive в кластере Azure HDInsight, который находится в виртуальной сети, ознакомьтесь со следующим руководством. 

> [!div class="nextstepaction"]
> [Transform data in Azure Virtual Network using Hive activity in Azure Data Factory](tutorial-transform-data-hive-virtual-network.md) (Преобразование данных в виртуальной сети Azure с помощью действия Hive в фабрике данных Azure).





