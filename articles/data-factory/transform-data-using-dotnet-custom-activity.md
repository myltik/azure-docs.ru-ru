---
title: "Использование настраиваемых действий в конвейере фабрики данных Azure"
description: "Узнайте, как создавать пользовательские действия и использовать их в конвейере фабрики данных Azure."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shengc
ms.openlocfilehash: 24f15168fd716cf317087b8a2ad19b66574ce569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Использование настраиваемых действий в конвейере фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-use-custom-activities.md)
> * [Версия 2 — предварительная](transform-data-using-dotnet-custom-activity.md)

Существует два типа действий, которые можно использовать в конвейере фабрики данных Azure.

- [Действия перемещения данных](copy-activity-overview.md) для перемещения данных между [поддерживаемыми исходными хранилищами данных и хранилищами данных-приемниками](copy-activity-overview.md#supported-data-stores-and-formats).
- [Действия преобразования данных](transform-data.md) для преобразования данных с помощью служб вычислений, например: в Azure HDInsight, пакетной службе Azure и Машинном обучении Azure. 

Чтобы переместить данные в хранилище данных, которое не поддерживает фабрика данных, и обратно, или чтобы преобразовать или обработать данные способом, который не поддерживается фабрикой данных Azure, создайте **пользовательское действие** с собственной логикой перемещения или преобразования данных и используйте это действие в конвейере. Настраиваемые действия запускают настраиваемую логику кода в пуле **пакетной службы Azure** виртуальных машин.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая сейчас доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Использование настраиваемых действий в конвейере фабрики данных Azure](v1/data-factory-use-custom-activities.md).
 

Если вы еще не знакомы с пакетной службой Azure, см. следующие статьи.

* [Основные сведения о пакетной службе Azure](../batch/batch-technical-overview.md) — общие сведения о пакетной службе Azure.
* Статья о командлете [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) со сведениями о создании учетной записи пакетной службы Azure или статья о [портале Azure](../batch/batch-account-create-portal.md) со сведениями о создании учетной записи пакетной службы Azure с помощью портала Azure. Подробные инструкции по использованию этого командлета см. в статье [Using PowerShell to manage Azure Batch Account](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) (Использование PowerShell для управления учетной записью пакетной службы Azure).
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) со сведениями о создании пула пакетной службы Azure.

## <a name="azure-batch-linked-service"></a>Связанная пакетная служба Azure 
Ниже приведен фрагмент кода JSON, определяющий пример связанной пакетной службы Azure. Дополнительные сведения см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md).

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 Дополнительные сведения о связанной пакетной службе Azure см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md). 

## <a name="custom-activity"></a>Настраиваемое действие

В следующем фрагменте кода JSON определяется конвейер с простым настраиваемым действием. Определение действия содержит ссылку на связанную пакетную службу Azure. 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

В этом примере helloworld.exe является пользовательским приложением, которое хранится в папке customactv2/helloworld учетной записи хранения Azure, используемой в resourceLinkedService. Настраиваемое действие отправляет пользовательское приложение для выполнения в пакетной службе Azure. Вы можете заменить команду на любое предпочитаемое приложение, которое можно выполнить в целевой операционной системе узлов пула пакетной службы Azure. 

В следующей таблице описаны имена и описания свойств, относящихся к этому действию. 

| Свойство              | Описание                              | Обязательно |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Имя действия в конвейере.     | Да      |
| description           | Описание действия.  | Нет       |
| type                  | Для пользовательского действия используется тип действия **Custom**. | Да      |
| linkedServiceName (имя связанной службы)     | Связанная служба пакетной службы Azure. Дополнительные сведения об этой связанной службе см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md).  | Да      |
| command               | Команда для выполнения пользовательского приложения. Если приложение уже находится в узле пула пакетной службы Azure, resourceLinkedService и folderPath могут быть пропущены. Например, можно указать команду `cmd /c dir`, которая изначально поддерживается узлом пула пакетной службы Windows. | Да      |
| resourceLinkedService | Связанная служба хранилища Azure учетной записи хранения, в которой хранится пользовательское приложение. | Нет       |
| folderPath            | Путь к папке пользовательского приложения и всех его зависимостей. | Нет       |
| referenceObjects      | Массив имеющихся связанных служб и наборов данных. Указанные связанные службы и наборы данных передаются в пользовательское приложение в формате JSON. Пользовательский код может использовать ресурсы фабрики данных. | Нет       |
| extendedProperties    | Определенные пользователем свойства, которые могут быть переданы в пользовательское приложение в формате JSON. Пользовательский код может использовать дополнительные свойства. | Нет       |

## <a name="passing-objects-and-properties"></a>Передача объектов и свойств

В этом примере показано, как использовать свойства referenceObjects и extendedProperties для передачи объектов фабрики данных и определенных пользователем свойств в пользовательское приложение. 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            }           
        }
      }
    }]
  }
}
```

При выполнении действия referenceObjects и extendedProperties сохраняются в указанных ниже файлах. Эти файлы будут развернуты в той же папке выполнения SampleApp.exe. 

- activity.json

  Хранит свойство extendedProperties и свойства настраиваемых действий. 

- linkedServices.json

  Хранит массив связанных служб, определенных в свойстве referenceObjects. 

- datasets.json

  Хранит массив наборов данных, определенных в свойстве referenceObjects. 

В следующем примере кода показано, как SampleApp.exe может получить доступ к необходимой информации в JSON-файлах. 

```C#
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

####<a name="retrieve-execution-outputs"></a>Извлечение выходных данных выполнения

Вы можете запустить выполнение примера конвейера и отслеживать его результат, выполнив следующие команды PowerShell: 

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -dataFactoryName "factoryName" -PipelineName "pipelineName" 
$result = Get-AzureRmDataFactoryV2ActivityRun -dataFactoryName "factoryName" -PipelineRunId $runId -RunStartedAfter "2017-09-06" -RunStartedBefore "2017-12-31"
$result.output -join "`r`n" 
$result.Error -join "`r`n" 
```

Свойства **stdout** и **stderr** пользовательского приложения сохраняются в контейнер **adfjobs** в связанной службе хранилища Azure, определенной при создании связанной пакетной службы Azure с помощью GUID задания. Вы можете получить подробный путь из выходных данных выполнения действия, как показано в следующем фрагменте кода: 

```shell
"exitcode": 0
"outputs": [
    "https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/output/stdout.txt",
    "https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/output/stderr.txt"
]
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

> [!IMPORTANT]
> - Свойства activity.json, linkedServices.json и datasets.json хранятся в папке среды выполнения пакетной задачи. Для этого примера свойства activity.json, linkedServices.json и datasets.json хранятся по адресу https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/runtime/. При необходимости их можно очистить отдельно. 
> - Для связанных служб, использующих локальную среду выполнения интеграции, конфиденциальная информация, например ключи или пароли, шифруется локальной средой выполнения интеграции. Это гарантирует, что учетные данные останутся в пределах частных сетевых сред, определенных клиентами. Некоторые поля с конфиденциальными данными, на которые таким образом ссылается пользовательский код приложения, могут отсутствовать. При необходимости в extendedProperties используйте SecureString, а не ссылку на связанную службу. 



## <a name="auto-scaling-of-azure-batch"></a>Автомасштабирование пакетной службы Azure
Можно также создать пул пакетной службы Azure с использованием функции **автомасштабирования** . Например, можно создать пул пакетной службы Azure с нулем выделенных виртуальных машин и формулой автоматического масштабирования на основе числа ожидающих задач. 

Приведенный здесь пример формулы обеспечивает следующее поведение: при создании пула он изначально содержит одну виртуальную машину. Метрика $PendingTasks определяет количество задач в состоянии выполнения и активном состоянии (в очереди).  Формула находит среднее число ожидающих выполнения задач за последние 180 секунд и соответствующим образом задает значение TargetDedicated. Благодаря этому значение TargetDedicated никогда не превысит 25 виртуальных машин. Таким образом, по мере поступления новых задач пул автоматически увеличивается, а по мере их завершения виртуальные машины высвобождаются по одной, и функция автоматического масштабирования уменьшает пул. Значения startingNumberOfVMs и maxNumberofVMs можно настроить в соответствии со своими потребностями.

Формула автоматического масштабирования:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Дополнительные сведения см. в статье [Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure](../batch/batch-automatic-scaling.md).

Если в пуле используется [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)(значение по умолчанию), пакетной службе может потребоваться 15–30 минут на подготовку виртуальной машины перед выполнением настраиваемого действия.  Если пул использует другое значение autoScaleEvaluationInterval, пакетная служба может затрачивать autoScaleEvaluationInterval плюс 10 минут.


## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими ссылками, в которых описаны способы преобразования данных другими способами: 

* [Действие U-SQL](transform-data-using-data-lake-analytics.md)
* [Действие Hive](transform-data-using-hadoop-hive.md)
* [Действие Pig](transform-data-using-hadoop-pig.md)
* [Действие MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Действие потоковой передачи Hadoop](transform-data-using-hadoop-streaming.md)
* [Действие Spark](transform-data-using-spark.md)
* [Создание прогнозирующих конвейеров с помощью машинного обучения Azure и фабрики данных Azure](transform-data-using-machine-learning.md)
* [Действие хранимой процедуры](transform-data-using-stored-procedure.md)
