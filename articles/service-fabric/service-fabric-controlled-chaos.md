---
title: "Вызов Chaos в кластерах Service Fabric | Документация Майкрософт"
description: "Управление Chaos в кластере с помощью интерфейсов API службы внесения ошибок и анализа кластера."
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: motanv
ms.openlocfilehash: 3b3b93bc9ec5ecdcfc289e5b62e84de6aa4172ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Вызов контролируемого хаоса в кластерах Service Fabric
Крупномасштабные распределенные системы, такие как облачные инфраструктуры, ненадежны по своей сути. Azure Service Fabric позволяет разработчикам создавать надежные распределенные службы на основе ненадежной инфраструктуры. Чтобы писать надежные распределенные службы на основе ненадежной инфраструктуры, разработчики должны иметь возможность проверять стабильность работы своих служб, когда для базовой ненадежной инфраструктуры выполняются сложные переходы состояния из-за ошибок.

[Служба внесения ошибок и анализа кластера](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-testability-overview) (также называемая службой анализа ошибок) позволяет разработчикам вызывать ошибки для тестирования работы служб. Эти целевые имитируемые ошибки, например [перезапуск секции](https://docs.microsoft.com/en-us/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), помогают выполнять самые распространенные переходы между состояниями. При этом целевым имитируемым ошибкам по определению свойственна погрешность, поэтому могут происходить ошибки, которые появляются только в трудной для прогнозирования, длинной и сложной последовательности переходов между состояниями. Для неискаженного тестирования без можно использовать Chaos.

Chaos моделирует в кластере периодические сбои, чередуя как нормальные, так и ненормальные ошибки, на протяжении долгого периода времени. Настроив частоту и тип ошибок в инструменте Chaos, его можно запускать или останавливать через API-интерфейсы C# или PowerShell, чтобы создавать сбои в работе кластера и службы. Можно настроить запуск Chaos в течение указанного периода времени (например, на один час), после чего Chaos останавливается автоматически. Или можно вызывать API StopChaos (C# или Powershell) для остановки в любое время.

> [!NOTE]
> Текущая версия Chaos вызывает только безопасные ошибки, то есть при отсутствии внешних ошибок исключена вероятность потери кворума или данных.
>

Во время выполнения Chaos создает различные события, которые записывают состояние выполнения в определенный момент времени. Например, ExecutingFaultsEvent содержит все сбои, которые Chaos выполняет в этой итерации. ValidationFailedEvent содержит сведения о сбое проверки (проблемы работоспособности и стабильности), обнаруженном во время проверки кластера. Для получения отчета о выполнении Chaos можно вызывать API GetChaosReportAsync (C# или Powershell). Эти события сохраняются в [надежном словаре](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections), для которого настроена политика усечения, диктуемая двумя конфигурациями: **MaxStoredChaosEventCount** (значение по умолчанию — 25 000) и **StoredActionCleanupIntervalInSeconds** (значение по умолчанию — 3600). Каждые *StoredActionCleanupIntervalInSeconds* Chaos выполняет проверку. В результате проверки определенное число последних событий (*MaxStoredChaosEventCount*) удаляется из надежного словаря.

## <a name="faults-induced-in-chaos"></a>Ошибки, вызываемые Chaos
Chaos генерирует сбои во всем кластере Service Fabric и вмещает сбои за месяцы или годы работы всего в несколько часов тестирования. Сочетание ошибок с чередованием с высокой частотой возникновения сбоев позволяет находить проблемы, которые иначе были бы упущены. Это упражнение Chaos обеспечивает значительное улучшение качества кода службы.

Chaos вызывает ошибки следующих категорий:

* Перезапуск узла
* Перезапуск развернутого пакета кода
* Удаление реплики
* Перезапуск реплики
* перемещение первичной реплики (настраивается);
* перемещение вторичной реплики (настраивается).

Chaos выполняет несколько итераций. Каждая из них включает в себя сбои и проверку кластера в течение указанного периода времени. Вы можете настроить время, затрачиваемое на стабилизацию работы и проверку кластера для успешной его работы. В случае обнаружения сбоя при проверке кластера Chaos создает и сохраняет событие ValidationFailedEvent с меткой времени в формате UTC и подробными сведениями об ошибке. Например, рассмотрим экземпляр Chaos, для которого настроено выполнение в течение одного часа и не более трех одновременных сбоев. Chaos вызывает три сбоя и проверяет работоспособность кластера. Затем он выполняет итерацию предыдущего шага, пока выполнение не будет явным образом остановлено посредством API StopChaosAsync или через один час. Если при какой-либо итерации кластер становится неработоспособным, то есть его работа не стабилизируется в течение заданного времени (MaxClusterStabilizationTimeout), Chaos создает событие ValidationFailedEvent. Это событие указывает на наличие ошибки, которую необходимо дополнительно изучить.

Чтобы узнать какие ошибки созданы инструментом Chaos, можно использовать API GetChaosReport (PowerShell или C#). API возвращает следующий сегмент отчета Chaos, основанного на переданном маркере продолжения или диапазоне времени. Можно указать ContinuationToken для получения следующего сегмента отчета Chaos или указать диапазон времени с помощью параметров StartTimeUtc и EndTimeUtc, но нельзя указать ContinuationToken и диапазон времени одновременно. При наличии более 100 событий Chaos отчет Chaos возвращается по сегментам, каждый из которых содержит не более 100 событий.

## <a name="important-configuration-options"></a>Важные параметры конфигурации
* **TimeToRun**. Общее время выполнения Chaos до успешного завершения. Вы можете остановить выполнение Chaos до истечения периода TimeToRun с помощью API StopChaos.

* **MaxClusterStabilizationTimeout**. Максимальный период ожидания возврата кластера в работоспособное состояние до создания события ValidationFailedEvent. Это нужно, чтобы уменьшить нагрузку на кластер, пока его работоспособность восстанавливается. Выполняемые проверки:
  * работоспособен ли кластер;
  * работоспособна ли служба;
  * достигнут ли заданный размер целевого набора реплик для секции службы;
  * отсутствуют ли реплики InBuild.
* **MaxConcurrentFaults**. Максимальное количество одновременных сбоев, вызываемых при каждой итерации. Чем выше число, тем более агрессивно ведет себя Chaos, а комбинации отработок отказа и переходов состояния, которые проходит кластер, становятся более сложными. 

> [!NOTE]
> Независимо от значения *MaxConcurrentFaults* при отсутствии внешних ошибок Chaos исключает вероятность потери кворума или данных.
>

* **EnableMoveReplicaFaults**. Включает или отключает сбои, приводящие к перемещению первичных или вторичных реплик. Эти ошибки отключены по умолчанию.
* **WaitTimeBetweenIterations**. Время ожидания между итерациями. То есть время, на которое Chaos приостанавливает работу после выполнения цикла ошибок и завершения соответствующей проверки работоспособности кластера. Чем выше значение этого параметра, тем ниже средняя скорость внедрения ошибки.
* **WaitTimeBetweenFaults**. Время ожидания между двумя последовательными сбоями в одной итерации. Чем выше значение, тем меньше степень параллелизма (или перекрывания) в ошибках.
* **ClusterHealthPolicy**. Политика работоспособности кластера используется для проверки работоспособности кластера между итерациями Chaos. Если работоспособность кластера находится в состоянии ошибки или непредвиденного исключения во время выполнения ошибки, Chaos ожидает 30 минут до следующей проверки работоспособности, чтобы дать кластеру время восстановить работу.
* **Context**. Коллекция (string, string) пар "ключ —значение". Для записи сведений о запуске Chaos можно использовать карту. Максимальное число таких пар — 100. Каждая строка (ключ или значение) может содержать не более 4095 символов. Эту карту настраивает стартер запуска Chaos, чтобы при необходимости сохранять контекст конкретного выполнения.

## <a name="how-to-run-chaos"></a>Выполнение Chaos

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
