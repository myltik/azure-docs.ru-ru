---
title: "Как создать триггеры переворачивающиеся окна в фабрике данных Azure | Документы Microsoft"
description: "Узнайте, как создать триггер в фабрике данных Azure, работающей на «переворачивающегося» окна конвейера."
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
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: a3b056ae4bb4eda26fec58ca3b6bed7f0744e36e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Как создать триггер, который запускает конвейера на «переворачивающегося» окна
Эта статья содержит шаги для создания, запуска и отслеживать триггер переворачивающиеся окна. Общие сведения о триггеры и типы, мы поддерживаем см. в разделе [конвейера выполнения и триггеры](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью о [начале работы со службой фабрики данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

«Переворачивающееся» окно триггеры — это тип триггер, срабатывающий периодических интервалом с указанным временем запуска, сохраняя состояние. Переворачивающиеся окна представляют собой ряд из фиксированного размера, без перекрытия последовательных временных интервалов. «Переворачивающееся» окно триггер имеет отношение 1:1 с конвейером и может ссылаться только на единственном числе конвейера.

## <a name="tumbling-window-trigger-type-properties"></a>"Переворачивающееся" окно свойств типа триггера
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

Ниже приводится общий обзор основных элементов, связанных с повторения и планирования в триггере переворачивающиеся окна.

| **Имя JSON** | **Описание** | **Допустимые значения** | **Обязательный** |
|:--- |:--- |:--- |:--- |
| **type** | Тип триггера. Эта возможность предусмотрена как «TumblingWindowTrigger». | Строка | Yes |
| **runtimeState** | <readOnly>Возможные значения: Запущена, остановлена, отключена | Строка | Да, только для чтения |
| **frequency** |*Частоты* строка, представляющая Частотная единица повторном выполнении появляется триггер. Поддерживаемыми значениями являются «минуты» и «час». Если время начала частей даты, которые являются более детализированными, чем частота, они будут учитывать для вычисления границ окна. Для ex: Если частота — каждый час, время начала 2016-04-01T10:10:10Z, первое окно является (2017 г-09-01T10:10:10Z 2017 г-09-01T11:10:10Z.)  | Строка. Поддерживаемые типы «minute», «час» | Yes |
| **interval** |*Интервал* является положительным целым числом и обозначает интервал для *частоты* , определяющее, как часто будет выполняться триггер. Например если *интервал* 3 и *частоты* — «час» триггер повторяется каждые 3 часа. | Целое число  | Yes |
| **startTime**|*startTime* — это дата и время. *время начала* является первым вхождением и может быть в прошлом. Первый интервал триггер будет (время начала, время начала + интервал). | Datetime | Yes |
| **endTime**|*время окончания* даты-времени. *время окончания* является последнее вхождение и может быть в прошлом. | Datetime | Yes |
| **Задержка** | Указывает задержку до обработки данных начнется окна. Запустите конвейера запускается после ожидаемое время выполнения + задержка. Задержка определяет, как время ожидания триггер просроченных времени до активации нового запуска. Он не изменяет время запуска окна. | TimeSpan (пример: 00:10:00 означает задержки, составляющим 10 мин.) |  Нет. Значение по умолчанию — «00: 00:00» |
| **Max параллелизма** | Число запусков одновременных триггера, которые срабатывают для windows, которые готовы. Пример: если мы пытаемся задним числом каждый час для вчерашнего дня, который бы 24 windows. Если параллелизма = 10, триггер события запускаются только для первых 10 windows (01 00:00:00 - 09:00-10:00). После выполнения первых 10 запусков триггеру конвейера запускается триггер запускаются Далее 10 (10:00 до 11:00-19:00 до 20:00). Примере параллелизма = 10, если windows 10, все будет готово, будет выполнен 10 конвейера. Если есть только 1 окно готовности, будут существовать только 1 выполнения конвейера. | Целое число  | Да. Возможные значения 1 – 50 |
| **retryPolicy: числа** | Число повторных попыток перед запуском конвейера помечен как «Сбой»  | Целое число  |  Нет. Значение по умолчанию — 0 повторные попытки |
| **retryPolicy: intervalInSeconds** | Задержка между повторными попытками (в секундах) | Целое число  |  Нет. По умолчанию — 30 секунд. |

### <a name="using-system-variables-windowstart-and-windowend"></a>Использование системных переменных: WindowStart и WindowEnd

Если вы хотите использовать WindowStart и WindowEnd переворачивающиеся окна триггера в вашей **конвейера** определение (т. е. для части запроса), необходимо передавать переменные в качестве параметров в ваш конвейер **триггер**определение, следующим образом:
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

Затем в определении конвейера, чтобы использовать значения WindowStart и WindowEnd, используйте параметры соответствующим образом «MyWindowStart» и «MyWindowEnd»

### <a name="notes-on-backfill"></a>Замечания о задним числом
Если есть несколько окон для выполнения (esp. в сценарии задним числом), порядок выполнения windows является детерминированным и от старых к новым интервалы. Нет возможности для изменения этого поведения сейчас.

### <a name="updating-an-existing-triggerresource"></a>Обновление существующего TriggerResource
* При изменении частоты (или размера окна), триггера, состояние windows уже обработано будет *не* требуется сбросить. Триггер будет продолжать активироваться для windows после последнего выполнения, с помощью нового размера окна.
* Если время окончания триггер изменения (добавлены или обновлены), состояние windows уже обработано будет *не* требуется сбросить. Триггер будет поддерживать просто новое время окончания. Если время окончания предшествует windows уже выполнена, перестанут триггер. В противном случае он останавливается при обнаружении новое время окончания.

## <a name="sample-using-azure-powershell"></a>Образец с помощью Azure PowerShell
В этом разделе показано, как использовать Azure PowerShell для создания, запуска и наблюдения за триггера.

1. Создайте JSON-файл с именем MyTrigger.json в папке C:\ADFv2QuickStartPSH\ со следующим содержимым:

   > [!IMPORTANT]
   > Задать **startTime** текущее время в формате UTC и **endTime** на один час после текущего времени в формате UTC, время до сохранения файла JSON.

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
2. Создание триггера с помощью **AzureRmDataFactoryV2Trigger набор** командлета.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    
3. Confirm that the status of the trigger is **Stopped** by using the **Get-AzureRmDataFactoryV2Trigger** cmdlet.

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

## <a name="next-steps"></a>Дальнейшие действия
Подробные сведения о триггерах см. в разделе [конвейера выполнения и триггеры](concepts-pipeline-execution-triggers.md#triggers).
