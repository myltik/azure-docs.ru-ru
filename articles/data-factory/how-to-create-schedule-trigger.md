---
title: "Как создать триггеры в фабрике данных Azure | Документы Microsoft"
description: "Узнайте, как создать триггер в фабрике данных Azure, выполняется по расписанию конвейера."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: eed286c01604ab0e9ac2113d56cbce268503668d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Как создать триггер, который выполняет конвейера по расписанию
Эта статья содержит шаги для создания, запуска и наблюдения за триггера. Основные сведения о триггерах см. в разделе [конвейера выполнения и триггеры](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью о [начале работы со службой фабрики данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="use-azure-powershell"></a>Использование Azure PowerShell
В этом разделе показано, как использовать Azure PowerShell для создания, запуска и наблюдения за триггера. Чтобы увидеть работу этого образца следует пройти [краткое руководство: создание фабрики данных с помощью Azure PowerShell](quickstart-create-data-factory-powershell.md). Затем добавьте следующий код в методе main, который создает и запускает запланированный триггер, который выполняется каждые 15 минут. Триггер связан с конвейером (**Adfv2QuickStartPipeline**), создаваемых в рамках краткого руководства.

1. Создайте JSON-файл с именем MyTrigger.json в папке C:\ADFv2QuickStartPSH\ со следующим содержимым: 

    > [!IMPORTANT]
    > Задать **startTime** текущее время в формате UTC и **endTime** на один час после текущего времени в формате UTC, время до сохранения файла JSON.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```
    
    Фрагмент JSON: 
    - **Тип** триггер имеет значение **ScheduleTrigger**. 
    - **Частоты** равно **минуту** и **интервал** равно **15**. Таким образом триггер выполняется конвейера каждые 15 минут между значениями времени начала и окончания. 
    - **EndTime** составляет один час после **startTime**, поэтому выполняется триггер конвейера 15 минут, 30 минут и 45 минут позже времени начала. Не забудьте обновить свойство startTime текущее время в формате UTC и endTime в один час после времени начала.  
    - Триггер, связанные с **Adfv2QuickStartPipeline** конвейера. Чтобы связать несколько конвейеров с триггера, добавить дополнительные **pipelineReference** разделы. 
    - Конвейер в кратком руководстве принимает два **параметры**. Таким образом можно передавать значения для этих параметров из триггера. 
2. Создание триггера с помощью **AzureRmDataFactoryV2Trigger набор** командлета.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. Убедитесь, что состояние триггера **остановлена** с помощью **Get AzureRmDataFactoryV2Trigger** командлета. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" 
    ```
4. Запуск триггера с помощью **AzureRmDataFactoryV2Trigger начала** командлета: 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" 
    ```
5. Убедитесь, что состояние триггера **Started** с помощью **Get AzureRmDataFactoryV2Trigger** командлета.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" 
    ```
6.  Получить триггер запускается с помощью PowerShell с помощью **Get AzureRmDataFactoryV2TriggerRun** командлета. Чтобы получить сведения о запускается триггер, выполните следующую команду периодически: обновление **TriggerRunStartedAfter** и **TriggerRunStartedBefore** значения в соответствии со значениями в определении триггера . 

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

    Мониторинге триггер запускается/конвейера выполняется на портале Azure см. в разделе [запускается монитор конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-net-sdk"></a>Использование пакета SDK для .NET
В этом разделе показано, как использовать пакет SDK .NET для создания, запуска и наблюдения за триггера. Если вы хотите просмотреть этот код работает, сначала выполните [краткое руководство по созданию фабрики данных, с помощью пакета SDK .NET](quickstart-create-data-factory-dot-net.md). Затем добавьте следующий код в методе main, который создает и запускает запланированный триггер, который выполняется каждые 15 минут. Триггер связан с конвейером (**Adfv2QuickStartPipeline**), создаваемых в рамках краткого руководства. 

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method. 
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

Для мониторинга выполнения триггера, добавьте следующий код перед удалением последней `Console.WriteLine` инструкции: 

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Мониторинге триггер запускается/конвейера выполняется на портале Azure см. в разделе [запускается монитор конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-python-sdk"></a>С помощью пакета SDK для Python
В этом разделе показано, как использовать пакет SDK для Python для создания, запуска и наблюдения за триггера. Если вы хотите просмотреть этот код работает, сначала выполните [краткое руководство по созданию фабрики данных, с помощью пакета SDK для Python](quickstart-create-data-factory-python.md). Затем добавьте следующий блок кода после «мониторинг выполнения конвейера» блок кода в сценарий python. Этот код создает запланированный триггер, который выполняется каждые 15 минут между указанными начала и окончания. Обновите start_time в текущее время в формате UTC и end_time на один час после текущего времени UTC. 

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC') 
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
Мониторинге триггер запускается/конвейера выполняется на портале Azure см. в разделе [запускается монитор конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-resource-manager-template"></a>Использование шаблона Resource Manager
Шаблон диспетчера ресурсов Azure можно использовать для создания триггера. Пошаговые инструкции см. в разделе [создания фабрики данных Azure с помощью шаблона диспетчера ресурсов](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Время начала триггер передается в конвейер
В версии 1 фабрика данных Azure поддерживала чтение или запись секционированных данных с использованием системных переменных SliceStart/SliceEnd/WindowStart/WindowEnd. В версии 2 это достигается с помощью параметра конвейера и времени начала или запланированного времени запуска триггера в качестве значения параметра. В следующем примере триггера запланированного времени передается как значение параметра scheduledRunTime конвейера. 

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
Дополнительные сведения см. в разделе [как для чтения или записи секционированных данных](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Дальнейшие действия
Подробные сведения о триггерах см. в разделе [конвейера выполнения и триггеры](concepts-pipeline-execution-triggers.md#triggers).