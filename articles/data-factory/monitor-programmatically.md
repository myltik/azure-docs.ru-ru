---
title: Отслеживание фабрики данных Azure с помощью программных средств | Документация Майкрософт
description: Узнайте, как отслеживать конвейер в фабрике данных с помощью различных пакетов средств разработки программного обеспечения (пакетов SDK).
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 5b7b473bdc411e11d6d1c90c63c4f05c6dac3280
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619608"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Отслеживание фабрики данных Azure с помощью программных средств
В этой статье описывается как отслеживать конвейер в фабрике данных с помощью различных пакетов средств разработки программного обеспечения (пакетов SDK). 

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, прочитайте статью [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью приложения для мониторинга и управления](v1/data-factory-monitor-manage-pipelines.md).

## <a name="data-range"></a>Срок хранения данных

В фабрике данных данные запуска конвейеров сохраняются только в течение 45 дней. Если вы выполняете программный запрос на сведения о запуске для конвейера фабрики данных (например, с помощью команды PowerShell `Get-AzureRmDataFactoryV2PipelineRun`), максимальные значения дат для необязательных параметров `LastUpdatedAfter` и `LastUpdatedBefore` не указываются. Если вы выполняете запрос на данные, к примеру, за прошедший год, запрос выполняется, но возвращаются данные запуска конвейера за последние 45 дней.

Если вам нужно, чтобы данные запуска конвейера хранились более 45 дней, настройте собственный журнал ведения диагностики с использованием [Azure Monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Полное пошаговое руководство по созданию и отслеживанию конвейера с помощью пакета SDK для .NET приведено в разделе [Создание фабрики данных и конвейера с помощью пакета SDK .NET](quickstart-create-data-factory-dot-net.md).

1. Добавьте следующий код, чтобы постоянно проверять состояние выполнения конвейера до завершения копирования данных.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Добавьте следующий код, извлекающий сведения о выполнении действия копирования, например размер записанных и прочитанных данных.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Полная документация по пакету SDK для .NET приведена в [справочнике по пакету SDK для .NET для фабрики данных](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Полное пошаговое руководство по созданию и отслеживанию конвейера с помощью пакета SDK для Python приведено в разделе [Создание фабрики данных и конвейера с помощью Python](quickstart-create-data-factory-python.md).

Для отслеживания работы конвейера добавьте следующий код.

```python
#Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Полная документация по пакету SDK для Python приведена в [справочнике по пакету SDK для Python для фабрики данных](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>ИНТЕРФЕЙС REST API
Полное пошаговое руководство по созданию и отслеживанию конвейера с помощью REST API приведено в разделе [Создание фабрики данных Azure и конвейера с помощью REST API](quickstart-create-data-factory-rest-api.md).
 
1. Запустите следующий скрипт, чтобы проверять состояние выполнения, пока не закончится копирование данных.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Запустите следующий скрипт, извлекающий сведения о выполнении действия копирования, например размер записанных и прочитанных данных.

    ```PowerShell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Полная документация по REST API приведена в [справочнике по REST API фабрики данных](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Полное пошаговое руководство по созданию и отслеживанию конвейера с помощью PowerShell приведено в разделе [Создание фабрики данных и конвейера с помощью пакета PowerShell](quickstart-create-data-factory-powershell.md).

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

Полная документация по командлетам PowerShell приведена в [справочнике по командлетам PowerShell для фабрики данных](/powershell/module/azurerm.datafactoryv2/?view=azurermps-4.4.1).

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об использовании Azure Monitor для отслеживания конвейеров фабрики данных см. в статье [Monitor data factories using Azure Monitor](monitor-using-azure-monitor.md) (Отслеживание фабрик данных с помощью Azure Monitor). 

