---
title: Создание триггеров расписания в фабрике данных Azure | Документация Майкрософт
description: Узнайте, как создать в фабрике данных Azure триггер, который запускает конвейер по расписанию.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: shlo
ms.openlocfilehash: 5df6633d40a3a361e551d1bea6caa2606a661a52
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618928"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Создание триггера, который запускает конвейер по расписанию
В этой статье описан триггер расписания и шаги по его созданию, запуску и мониторингу. Сведения о других типах триггеров см. в статье [Выполнение конвейера и триггеры в фабрике данных Azure](concepts-pipeline-execution-triggers.md).

При создании триггера расписания нужно указать для него расписание (дату начала, интервал повторения, дату окончания и т. д.) и связать его с конвейером. Конвейеры и триггеры имеют связь "многие ко многим". Несколько триггеров могут запускать один конвейер. Один триггер может запускать несколько конвейеров.

> [!NOTE]
> Эта статья относится к фабрике данных Azure версии 2, которая в настоящее время доступна в предварительной версии. Если вы используете фабрику данных Azure версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Руководство. Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

В следующих разделах приведены шаги по созданию триггера расписания различными методами. 

## <a name="data-factory-ui"></a>Пользовательский интерфейс фабрики данных
Вы можете создать **триггер расписания** для периодического запуска конвейера (ежечасно, ежедневно и т. д.). 

> [!NOTE]
> Полное пошаговое руководство по созданию конвейера и триггера расписания, связыванию их, а также по выполнению и мониторингу конвейера см. в статье [Создание фабрики данных с помощью пользовательского интерфейса службы "Фабрика данных Azure"](quickstart-create-data-factory-portal.md).

1. Откройте вкладку **Правка**. 

    ![Открытие вкладки "Правка"](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. Выберите пункт меню **Триггер** и щелкните **Создать/изменить**. 

    ![Меню создания триггера](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. На странице **Add Triggers** (Добавление триггеров) щелкните **Choose trigger...** (Выбор триггера...) и нажмите **Создать**. 

    ![Добавление триггеров — новый триггер](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. На странице **Новый триггер** выполните следующие действия: 

    1. Убедитесь, что в качестве параметра **Тип** выбрано **Расписание**. 
    2. Укажите значение даты и времени запуска триггера в поле **Дата начала (UTC)**. По умолчанию в нем установлено текущее время. 
    3. Задайте **интервал повторения** для триггера. Выберите одно из значений из раскрывающегося списка (каждую минуту, ежечасно, ежедневно, еженедельно и ежемесячно). Введите множитель в текстовом поле. Например, если требуется, чтобы триггер запускался каждые 15 минут, выберите **Каждую минуту** и введите **15** в текстовом поле. 
    4. В поле **Окончание** выберите **Нет конца**, если вы не хотите указывать дату и время окончания для триггера. Чтобы задать дату и время окончания, выберите **Дата**, укажите дату и время окончания и нажмите кнопку **Применить**. Каждое выполнение конвейера оплачивается. Если выполняется тестирование, настройте конвейер таким образом, чтобы он активировался всего несколько раз. Но при этом обязательно оставьте достаточно времени, чтобы конвейер успел запуститься между временем публикации и временем окончания. Триггер вступит в силу только после публикации решения в службе "Фабрика данных", а не сразу при сохранении триггера в пользовательском интерфейсе.

        ![Настройки триггера](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. В окне **Новый триггер** установите флажок **Активировано** и нажмите кнопку **Далее**. С помощью этого флажка позже можно деактивировать этот триггер. 

    ![Настройки триггера — кнопка "Далее"](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. На странице **Новый триггер** просмотрите предупреждающее сообщение и щелкните **Готово**.

    ![Настройки триггера — кнопка "Готово"](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. Щелкните **Опубликовать**, чтобы перенести изменения в службу "Фабрика данных". Пока вы не опубликуете изменения в фабрике данных, триггер не будет активировать выполнения конвейера. 

    ![Кнопка "Опубликовать"](./media/how-to-create-schedule-trigger/publish-2.png)
8. Перейдите на вкладку **Мониторинг** слева. Щелкните **Refresh** (Обновить), чтобы обновить этот список. Вы увидите выполнения конвейера, активированные запланированным триггером. Обратите внимание на значения в столбце **Активировано**. При использовании варианта **Trigger Now** (Активировать сейчас) в списке появится выполнение, активированное триггером вручную. 

    ![Мониторинг запусков по триггеру](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. Щелкните стрелку вниз рядом с областью **Pipeline Runs** (Запуски конвейера), чтобы перейти к представлению **Trigger Runs** (Запуски триггеров). 

    ![Мониторинг запусков триггеров](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell
В этом разделе показано, как использовать Azure PowerShell для создания, запуска и мониторинга триггера расписания. Чтобы этот образец работал, сначала выполните действия в статье [Создание фабрики данных Azure с помощью PowerShell](quickstart-create-data-factory-powershell.md). Затем добавьте следующий код в метод Main, который создает и запускает триггер расписания, выполняемый каждые 15 минут. Триггер связан с конвейером с именем **Adfv2QuickStartPipeline**, созданным в рамках работы с кратким руководством.

1. Создайте файл JSON с именем **MyTrigger.json** в папке C:\ADFv2QuickStartPSH со следующим содержимым:

    > [!IMPORTANT]
    > Прежде чем сохранить файл JSON, установите значение элемента **startTime** на текущее время в формате UTC. Установите значение элемента **endTime** на один час после текущего времени UTC.

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

    Во фрагменте кода JSON:
    - Для элемента **type** триггера задано значение "ScheduleTrigger".
    - Для элемента **frequency** триггера задано значение "Minute", а для элемента **interval** — значение 15. Таким образом, триггер запускает конвейер каждые 15 минут в период между временем начала и окончания.
    - Значение элемента **endTime** равно одному часу после элемента **startTime**. Таким образом, триггер запускает конвейер через 15, 30 и 45 минут после времени начала. Не забудьте установить время начала на текущее время в формате UTC, а время окончания на один час после времени начала. 
    - Триггер связан с конвейером **Adfv2QuickStartPipeline**. Чтобы связать несколько конвейеров с триггером, добавьте дополнительные разделы **pipelineReference**.
    - Конвейер в кратком руководстве принимает два значения **параметров**: **inputPath** и **outputPath**. Таким образом можно передавать значения этих параметров из триггера.

2. Создайте триггер с помощью командлета **Set-AzureRmDataFactoryV2Trigger**:

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. Убедитесь, что состояние триггера — **Stopped** (Остановлено), используя командлет **Get AzureRmDataFactoryV2Trigger**:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Запустите триггер с помощью командлета **Start-AzureRmDataFactoryV2Trigger**:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Убедитесь, что состояние триггера — **Started** (Запущено), используя командлет **Get AzureRmDataFactoryV2Trigger**:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6.  Получайте сведения о выполнениях триггера в Azure PowerShell с помощью командлета **Get-AzureRmDataFactoryV2TriggerRun**. Для этого следующую команду нужно выполнять периодически. Обновите значения **TriggerRunStartedAfter** и **TriggerRunStartedBefore** в соответствии со значениями в определении триггера:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Сведения о том, как отслеживать выполнения триггера и конвейера на портале Azure, см. в разделе [Мониторинг конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="net-sdk"></a>ПАКЕТ SDK .NET
В этом разделе показано, как использовать пакет SDK для .NET для создания, запуска и мониторинга триггера. Чтобы этот образец работал, сначала выполните действия в статье [Создание фабрики данных и конвейера с помощью пакета SDK .NET](quickstart-create-data-factory-dot-net.md). Затем добавьте следующий код в метод Main, который создает и запускает триггер расписания, выполняемый каждые 15 минут. Триггер связан с конвейером с именем **Adfv2QuickStartPipeline**, созданным в рамках работы с кратким руководством.

Чтобы создать и запустить триггер расписания, который выполняется каждые 15 минут, добавьте следующий код в метод Main:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Для мониторинга выполнения триггера добавьте следующий код перед последней инструкцией `Console.WriteLine` в этом примере:

```csharp
            // Check that the trigger runs every 15 minutes
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

Сведения о том, как отслеживать выполнения триггера и конвейера на портале Azure, см. в разделе [Мониторинг конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Пакет SDK для Python
В этом разделе показано, как использовать пакет SDK для Python для создания, запуска и мониторинга триггера. Чтобы этот образец работал, сначала выполните действия в статье [Создание фабрики данных и конвейера с помощью Python](quickstart-create-data-factory-python.md). Затем добавьте следующий блок кода после блока кода "мониторинг выполнения конвейера" в скрипте Python. Этот код создает триггер расписания, который выполняется каждые 15 минут между указанным временем начала и окончания. Задайте для переменной **start_time** текущее время в формате UTC, а для переменной **end_time** значение на один час после текущего времени в формате UTC.

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

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Сведения о том, как отслеживать выполнения триггера и конвейера на портале Azure, см. в разделе [Мониторинг конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Шаблон диспетчера ресурсов Azure
Для создания триггера можно использовать шаблон Azure Resource Manager. Пошаговые инструкции см. в статье [Руководство. Создание фабрики данных Azure с помощью шаблона Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Передача времени запуска триггера в конвейер
В фабрике данных Azure версии 1 поддерживается чтение или запись секционированных данных с использованием системных переменных **SliceStart**, **SliceEnd**, **WindowStart** и **WindowEnd**. В фабрике данных Azure версии 2 этого поведения можно добиться с помощью параметра конвейера. Время начала и запланированное время для триггера задаются в качестве значения для параметра конвейера. В следующем примере запланированное время для триггера передается в качестве значения параметру конвейера **scheduledRunTime**:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    

Дополнительные сведения см. в статье [Чтение или запись секционированных данных в фабрике данных Azure версии 2](how-to-read-write-partitioned-data.md).

## <a name="json-schema"></a>Схема JSON
Следующее определение JSON показывает, как создать триггер расписания с указанием расписания и повторения:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  Свойство **parameters** является обязательным свойством для элемента **pipelines**. Если конвейер не принимает никаких параметров, необходимо включить пустое определение JSON для свойства **parameters**.


### <a name="schema-overview"></a>Общее представление схемы
Таблица ниже содержит обзор основных элементов схемы, связанных с периодичностью выполнения и расписанием триггера:

| Свойство JSON | ОПИСАНИЕ |
|:--- |:--- |
| **startTime** | Значение даты и времени. Для простых расписаний значение свойства **startTime** применяется к первому выполнению. В сложных расписаниях триггер не запускается раньше, чем определяется значением **startTime**. |
| **endTime** | Дата и время завершения триггера. После указанной даты и времени триггер перестает выполняться. Значение свойства не может быть в прошлом. Это необязательное свойство. |
| **timeZone** | Часовой пояс. В настоящее время поддерживается только часовой пояс UTC. |
| **recurrence** | Объект recurrence указывает правила повторения для триггера. Объект recurrence поддерживает следующие элементы: **frequency**, **interval**, **endTime**, **count** и **schedule**. Если определен объект recurrence, элемент **frequency** является обязательным. Другие элементы объекта recurrence являются необязательными. |
| **frequency** | Единица частоты, с которой выполняется триггер. Поддерживаются следующие значения: "минута", "час", "день", "неделя" и "месяц". |
| **interval** | Положительное целое число, указывающее интервал для значения **frequency**, которое определяет, как часто выполняется триггер. Например, если **interval** имеет значение 3, а для элемента **frequency** выбран вариант week (неделя), триггер выполняется один раз каждые 3 недели. |
| **schedule** | Расписание повторения для триггера. Триггер с указанным значением **частоты** выполняется по расписанию. Свойство **schedule** содержит изменения для повторения, основанного на минутах, часах, неделях, днях месяца и номере недели.


### <a name="schema-defaults-limits-and-examples"></a>Параметры схемы по умолчанию, ограничения и примеры

| Свойство JSON | type | Обязательно | Значение по умолчанию | Допустимые значения | Пример |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Строка | Yes | None | Дата и время по спецификации ISO-8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Объект. | Yes | None | Объект recurrence | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Number | Нет  | 1 | 1–1000 | `"interval":10` |
| **endTime** | Строка | Yes | None | Значение даты и времени, представляющее время в будущем. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | Объект. | Нет  | None | Объект schedule | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Свойство startTime
В следующей таблице показано, как свойство **startTime** управляет запуском триггера:

| Значение startTime | Повторение без расписания | Повторение с расписанием |
|:--- |:--- |:--- |
| Время начала в прошлом | Вычисляется время первого выполнения, относящееся к будущему времени, после указанного времени начала.<br/><br/>Последующие выполнения вычисляются с учетом времени предыдущего выполнения.<br/><br/>Пример представлен после этой таблицы. | Триггер выполняется _не раньше_ указанного времени начала. Первое выполнение производится по расписанию, которое отсчитывается от времени начала.<br/><br/>Последующие выполнения производятся по расписанию повторов. |
| Время начала в будущем или в настоящем | Выполняется первый раз в указанное время начала.<br/><br/>Последующие выполнения вычисляются с учетом времени предыдущего выполнения. | Триггер выполняется _не раньше_ указанного времени начала. Первое выполнение производится по расписанию, которое отсчитывается от времени начала.<br/><br/>Последующие выполнения производятся по расписанию повторов. |

Рассмотрим, как работает триггер, для которого время начала установлено в прошлом, указан параметр recurrence и отсутствует параметр schedule. Предположим, что текущее время — `2017-04-08 13:00`, время начала — `2017-04-07 14:00`, а повторение — каждые два дня. (Значение **recurrence** определяется путем указания для свойства **frequency** значения "день", а для свойства **interval** значение 2.) Обратите внимание, что значение **startTime** находится в прошлом, то есть наступает раньше текущего времени.

В этих условиях первое выполнение происходит `2017-04-09 at 14:00`. От времени начала ядро планировщика отсчитывает время повторных выполнений. Выполнения, которые приходятся на прошлое, игнорируются. Ядро берет очередной случай выполнения, который приходится на будущее. В данном сценарии время начала имеет значение `2017-04-07 at 2:00pm`, поэтому следующее выполнение состоится через 2 дня от этого времени начала, то есть `2017-04-09 at 2:00pm`.

Время первого выполнения останется тем же, даже если **startTime** имеет значение `2017-04-05 14:00` или `2017-04-01 14:00`. Все последующие выполнения после первого вычисляются по расписанию (свойство schedule). Таким образом, последующие выполнения проходят `2017-04-11 at 2:00pm`, затем `2017-04-13 at 2:00pm`, затем `2017-04-15 at 2:00pm` и т. д.

Наконец, когда часы или минуты не установлены в расписании триггера, часы или минуты первого выполнения используются как значения по умолчанию.

### <a name="schedule-property"></a>Свойство schedule
С одной стороны, использование свойства schedule может ограничивать число выполнений триггера. Например, если триггеру назначена ежемесячная частота и параметр schedule, который запускает триггер только в 31-й день месяца, он будет выполняться только в те месяцы, в которых есть 31 день.

Обратите внимание, что параметр schedule может увеличивать число выполнений триггера. Например, триггер с ежемесячной частотой и параметром schedule, который запускает триггер в 1 и 2 день месяца, будет выполняться в первый и второй дни месяца, а не раз в месяц.

Если задано несколько элементов **schedule**, они применяются в порядке от большего к меньшему: номер недели, число месяца, день недели, час и, наконец, минута.

В следующей таблице элементы свойства **schedule** описаны подробно.


| Элемент JSON | ОПИСАНИЕ | Допустимые значения |
|:--- |:--- |:--- |
| **minutes** | Минуты часа, в которые будет выполняться триггер. | <ul><li>Целое число </li><li>массив целых чисел</li></ul>
| **hours** | Часы дня, в которые будет выполняться триггер. | <ul><li>Целое число </li><li>массив целых чисел</li></ul> |
| **weekDays** | Дни недели, по которым выполняется триггер. Значение можно указать только с еженедельной частотой. | <ul><li>Понедельник, вторник, среда, четверг, пятница, суббота, воскресенье</li><li>Массив значений дня (максимальный размер массива — 7)</li><li>Значения дня можно вводить в любом регистре</li></ul> |
| **monthlyOccurrences** | Дни месяца, по которым выполняется триггер. Значение можно указать только с ежемесячной частотой. | <ul><li>Массив объектов **monthlyOccurence**: `{ "day": day,  "occurrence": occurence }`.</li><li>Атрибут **day** является днем недели, в который выполняется триггер. Например, свойство **monthlyOccurrences** со значением **day** равным `{Sunday}` означает каждое воскресенье месяца. Атрибут **day** является обязательным.</li><li>Атрибут **occurrence** является частотой появления определенного **дня** на протяжении месяца. Например, свойство **monthlyOccurrences** со значением **day** и **occurrence** со значением `{Sunday, -1}` означает последнее воскресенье месяца. Атрибут **occurrence** не является обязательным.</li></ul> |
| **monthDays** | Дни месяца, по которым выполняется триггер. Значение можно указать только с ежемесячной частотой. | <ul><li>Любое значение <= -1 и >= -31</li><li>Любое значение >= 1 и <= 31</li><li>Массив значений</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Примеры расписаний повторения триггеров
Этот раздел содержит примеры расписания повторений с применением объекта **schedule** и его элементов.

В примерах предполагается, что **interval** имеет значение равное 1, а значение **frequency** является правильным согласно определению расписания. Например, у вас не может быть значения **frequency** "день" и изменение monthDays в объекте **schedule**. Такие ограничения указаны в таблице в предыдущем разделе.

| Пример | ОПИСАНИЕ |
|:--- |:--- |
| `{"hours":[5]}` | Выполняется каждый день в 05:00. |
| `{"minutes":[15], "hours":[5]}` | Выполняется каждый день в 05:15. |
| `{"minutes":[15], "hours":[5,17]}` | Выполняется каждый день в 05:15 и 16:15. |
| `{"minutes":[15,45], "hours":[5,17]}` | Выполняется каждый день в 05:15, 05:45, 16:15 и 16:45. |
| `{"minutes":[0,15,30,45]}` | Выполнение каждые 15 минут. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Выполнение каждый час. Этот триггер запускается каждый час. Минуты зависят от значения **startTime**, если оно указано. Если значение не указано, управление минутами осуществляется в соответствии с временем создания. Например, если триггер запущен или создан (в зависимости от ситуации) в 12:25, он будет запускаться в 00:25, 01:25, 02:25 и т. д. и 23:25.<br/><br/>Это расписание работает так же, как триггер со значением **frequency** равным "hour", значением 1 для параметра **interval** и без параметра **schedule**.  Это расписание можно использовать с различными значениями **частоты** и **интервала** для создания других триггеров. Например, если значение **частоты** — "месяц", расписание выполняется один раз в месяц, а не каждый день, когда **частота** имеет значение "день". |
| `{"minutes":[0]}` | Задание выполняется с наступлением каждого часа. Этот триггер выполняется с наступлением каждого часа, начиная с 12:00, 13:00, 14:00 и т. д.<br/><br/>Это расписание является эквивалентом триггера со значением **frequency** равным "hour" и значением **startTime** без минут или без объекта **schedule**, но со значением **frequency** "day". Если значение **frequency** равно "week" или "month", расписание выполняется только один раз в неделю или один раз в месяц соответственно. |
| `{"minutes":[15]}` | Задание выполняется через 15 минут после наступления каждого часа. Этот триггер выполняется через 15 минут после наступления каждого часа, начиная с 00:15, 01:15, 02:15 и т. д. и заканчивая в 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Задание выполняется в 17:00 каждую субботу. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Задание выполняется в 17:00 в каждые понедельник, среду и пятницу. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Задание выполняется в 17:15 и 17:45 в каждые понедельник, среду и пятницу. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Задание выполняется каждые 15 минут в каждый рабочий день. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Задание выполняется каждые 15 минут в период с 09:00 до 16:45 в каждый рабочий день. |
| `{"weekDays":["tuesday", "thursday"]}` | Задание выполняется по вторникам и четвергам в указанное время начала. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Задание выполняется в 06:00 28 числа каждого месяца (при условии, что значение **frequency** — "month"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Задание выполняется в 06:00 в последний день месяца. Чтобы триггер запускался в последний день месяца, используйте "-1" вместо значения 28, 29, 30 или 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Задание выполняется в 06:00 в первый и последний день каждого месяца. |
| `{monthDays":[1,14]}` | Задание выполняется в первый и 14-й день каждого месяца в указанное время начала. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Задание выполняется в первую пятницу каждого месяца в 05:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Задание выполняется в первую пятницу каждого месяца в указанное время начала. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Задание выполняется в третью пятницу с конца каждого месяца в указанное время начала. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Задание выполняется в первую и последнюю пятницу каждого месяца в 05:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Задание выполняется в первую и последнюю пятницу каждого месяца в указанное время начала. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Задание выполняется в пятую пятницу каждого месяца в указанное время начала. Если пятой пятницы в месяце нет, конвейер не выполняется, так как по расписанию задание должно выполняться только по пятым пятницам. Чтобы триггер выполнялся в последнюю пятницу месяца, используйте вместо значения **повторения** 5 значение –1. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Задание выполняется каждые 15 минут в последнюю пятницу месяца. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Задание выполняется в 05:15, 05:45, 17:15, 17:45 в третью среду каждого месяца. |


## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения см. в руководстве по [выполнению конвейера и использованию триггеров](concepts-pipeline-execution-triggers.md#triggers).
