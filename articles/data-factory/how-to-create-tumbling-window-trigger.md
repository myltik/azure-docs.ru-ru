---
title: Создание триггеров "переворачивающегося" окна в фабрике данных Azure | Документация Майкрософт
description: Узнайте, как создать триггер в фабрике данных Azure, который запускает конвейер в "переворачивающемся" окне.
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
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: 61d53e0d5f32f40b67f5b2d4ce888b047f8c4cea
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619717"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Создание триггера, который запускает конвейер в "переворачивающемся" окне
Эта статья содержит шаги по созданию, запуску и мониторингу триггера "переворачивающегося" окна. Дополнительные сведения о триггерах и поддерживаемых типах см. в статье [Выполнение конвейера и триггеры в фабрике данных Azure](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Эта статья относится к фабрике данных Azure версии 2, которая в настоящее время доступна в предварительной версии. Если вы используете фабрику данных Azure версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Руководство. Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Триггер "переворачивающегося" окна — это тип триггера, который активируется с определенным интервалом начиная с указанного времени запуска, сохраняя состояние. "Переворачивающиеся" окна — это ряд неперекрывающихся и несоприкасающихся интервалов времени фиксированного размера. Триггер "переворачивающегося" окна имеет связь "один к одному" с конвейером и может ссылаться только на один конвейер.

## <a name="tumbling-window-trigger-type-properties"></a>Свойства типа триггера "переворачивающегося" окна
"Переворачивающееся" окно имеет следующие свойства типа триггеров:

```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "parameter1": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter2": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
                }
            }
      }    
}
```  

Таблица ниже содержит обзор основных элементов JSON, связанных с периодичностью выполнения и расписанием триггера "переворачивающегося" окна.

| Элемент JSON | ОПИСАНИЕ | type | Допустимые значения | Обязательно |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Тип триггера. Тип — это фиксированное значение "TumblingWindowTrigger". | Строка | "TumblingWindowTrigger" | Yes |
| **runtimeState** | Текущее состояние времени выполнения триггера.<br/>**Примечание.** Этот элемент — \<readOnly>. | Строка | "Started," "Stopped," "Disabled" | Yes |
| **frequency** | Строка, представляющая единицу частоты (минуты или часы), с которой выполняется триггер. Если значения даты **startTime** являются более детализированными, чем значение **частоты**, даты **startTime** учитываются при вычислении границ окна. Например, если значение **частоты** соответствует ежечасному выполнению, а значение **startTime** — 2016-04-01T10:10:10Z, первое окно будет (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Строка | "minute," "hour"  | Yes |
| **interval** | Положительное целое число, указывающее интервал для значения **frequency**, которое определяет, как часто выполняется триггер. Например, если **interval** имеет значение 3, а для элемента **frequency** выбран вариант "hour", триггер будет выполняться один раз каждые 3 часа. | Целое число  | Положительное целое число. | Yes |
| **startTime**| Первое возникновение, которое может быть в прошлом. Первым интервалом триггера является (**startTime**, **startTime** + **interval**). | Datetime | Значение даты и времени. | Yes |
| **endTime**| Последнее возникновение, которое может быть в прошлом. | Datetime | Значение даты и времени. | Yes |
| **delay** | Время задержки до начала обработки данных окна. Запуск конвейера начинается после истечения ожидаемого времени выполнения плюс время **задержки**. **Задержка** определяет, как долго триггер ожидает, прежде чем начать новое выполнение по окончании предыдущего. **Задержка** не изменяет окно **startTime**. Например, значение **задержки** 00:10:00 подразумевает задержку длительностью 10 минут. | Timespan  | Значение времени, где время по умолчанию — 00:00:00. | Нет  |
| **maxConcurrency** | Количество одновременных выполнений триггеров, запущенных в окнах, которые готовы. Например, чтобы заполнить ежечасные запуски для вчерашних результатов в 24 окнах. Если **maxConcurrency** равно 10, события триггера активируются только для первых 10 окон (00:00–01:00 — 09:00–10:00). После завершения первых 10 активированных выполнений конвейера выполнения триггер запускается для следующих 10 окон (10:00–11:00 — 19:00–20:00). Продолжая пример с **maxConcurrency** равным 10, если 10 окон готовы, значит есть всего 10 выполнений конвейера. Если готово только одно окно, значит готово только 1 выполнение конвейера. | Целое число  | Целое число от 1 до 50. | Yes |
| **retryPolicy: Count** | Число повторных попыток, после которых выполнение конвейера будет помечено как "Failed".  | Целое число  | Целое число, в котором значение по умолчанию — 0 (повторы отсутствуют). | Нет  |
| **retryPolicy: intervalInSeconds** | Задержка между повторными попытками (в секундах). | Целое число  | Количество секунд, где значение по умолчанию — 30. | Нет  |

### <a name="windowstart-and-windowend-system-variables"></a>Системные переменные WindowStart и WindowEnd

Системные переменные **WindowStart** и **WindowEnd** триггера "переворачивающегося" окна можно использовать в определении **конвейера** (то есть для части запроса). Передайте системные переменные в качестве параметров конвейера в определении **триггера**. В следующем примере показано, как передавать эти переменные в качестве параметров:

```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "MyWindowStart": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "MyWindowEnd": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    }
                }
            }
      }    
}
```  

Чтобы использовать значения системных переменных **WindowStart** и **WindowEnd** в определении конвейера, используйте параметры "MyWindowStart" и "MyWindowEnd" соответственно.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Порядок выполнения окон в сценарии с обратным заполнением
Если есть несколько окон для выполнения (особенно в сценарии с обратным заполнением), порядок выполнения окон является детерминированным: от старых интервалов к новым. В настоящее время это поведение изменить невозможно.

### <a name="existing-triggerresource-elements"></a>Имеющиеся элементы TriggerResource
Следующие пункты относятся к имеющимся элементам **TriggerResource**:

* Если значение для элемента **frequency** (или размера окна) триггера изменяется, состояние окон, которые уже обрабатываются, *не* сбрасывается. Триггер продолжает запускать окна из последнего окна, которое выполнено с использованием нового размера окна.
* Если значение для элемента **endTime** триггера изменяется (добавляется или обновляется), состояние окон, которые уже обрабатываются, *не* сбрасывается. Триггер использует новое значение **endTime**. Триггер останавливается, если новое значение **endTime** предшествует окну, которое уже выполняется. В противном случае триггер останавливается, когда встречается новое значение **endTime**.

## <a name="sample-for-azure-powershell"></a>Пример для Azure PowerShell
В этом разделе показано, как использовать Azure PowerShell для создания, запуска и мониторинга триггера.

1. Создайте файл JSON с именем **MyTrigger.json** в папке C:\ADFv2QuickStartPSH со следующим содержимым:

   > [!IMPORTANT]
   > Прежде чем сохранить файл JSON, установите значение элемента **startTime** на текущее время в формате UTC. Установите значение элемента **endTime** на один час после текущего времени UTC.

    ```json   
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```  

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

6. Получайте сведения о выполнениях триггера в Azure PowerShell с помощью командлета **Get-AzureRmDataFactoryV2TriggerRun**. Чтобы получить сведения о выполнениях триггера, периодически выполняйте следующую команду. Обновите значения **TriggerRunStartedAfter** и **TriggerRunStartedBefore** в соответствии со значениями в определении триггера:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Сведения о том, как отслеживать выполнения триггера и конвейера на портале Azure, см. в разделе [Мониторинг конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения см. в руководстве по [выполнению конвейера и использованию триггеров](concepts-pipeline-execution-triggers.md#triggers).
