---
title: "Вызов Chaos в кластерах Service Fabric | Документация Майкрософт"
description: "Управление Chaos в кластере с помощью интерфейсов API службы внесения ошибок и анализа кластера."
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2017
ms.author: motanv
translationtype: Human Translation
ms.sourcegitcommit: 1a9bec270650223cd40b3b60e5bc9fc7e212a207
ms.openlocfilehash: ea8f76d146bd630cc8cb0a417d8c090b656150a4


---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Вызов контролируемого хаоса в кластерах Service Fabric
Крупномасштабные распределенные системы, такие как облачные инфраструктуры, ненадежны по своей сути. Azure Service Fabric позволяет разработчикам создавать надежные службы на основе ненадежной инфраструктуры. Чтобы создавать надежные службы, разработчики должны иметь возможность вызывать сбои в таких ненадежных инфраструктурах, чтобы проверить стабильность работы своих служб.

Служба внесения ошибок и анализа кластера (также называемая службой анализа ошибок) позволяет разработчикам вызывать действия, порождающие сбой, для тестирования работы служб. Однако на этом возможности целевого моделирования ошибок заканчиваются. Для углубленного тестирования можно применить Chaos.

Chaos моделирует в кластере непрерывные сбои, чередуя как нормальные, так и ненормальные ошибки, на протяжении долгого периода времени. Настроив частоту и тип сбоев в инструменте Chaos, его можно запустить или остановить через интерфейсы API C# или PowerShell, чтобы создавать сбои в работе кластера и службы.

Во время выполнения Chaos создает различные события, которые записывают состояние выполнения в определенный момент времени. Например, ExecutingFaultsEvent содержит все сбои, которые выполняются в этой итерации. ValidationFailedEvent содержит сведения об ошибке, обнаруженной во время проверки кластера. Для получения отчета о выполнении Chaos можно вызывать API GetChaosReportAsync.

## <a name="faults-induced-in-chaos"></a>Ошибки, вызываемые Chaos
Chaos генерирует сбои во всем кластере Service Fabric и вмещает сбои за месяцы или годы работы всего в несколько часов тестирования. Сочетание ошибок с чередованием с высокой частотой возникновения сбоев позволяет находить проблемы, которые в противном случае были бы упущены. Это упражнение Chaos обеспечивает значительное улучшение качества кода службы.

Chaos вызывает ошибки следующих категорий:

* Перезапуск узла
* Перезапуск развернутого пакета кода
* Удаление реплики
* Перезапуск реплики
* перемещение первичной реплики (настраивается);
* перемещение вторичной реплики (настраивается).

Chaos выполняет несколько итераций. Каждая из них включает в себя сбои и проверку кластера в течение указанного периода времени. Вы можете настроить время, затрачиваемое на стабилизацию работы и проверку кластера для успешной его работы. В случае обнаружения сбоя при проверке кластера Chaos создает и сохраняет событие ValidationFailedEvent с меткой времени в формате UTC и подробными сведениями об ошибке.

Например, рассмотрим экземпляр Chaos, для которого настроено выполнение в течение одного часа и не более трех одновременных сбоев. Chaos вызывает три сбоя и проверяет работоспособность кластера. Затем он выполняет итерацию предыдущего шага, пока выполнение не будет явным образом остановлено посредством API StopChaosAsync или через один час. Если при какой-либо итерации кластер становится неработоспособным, т. е. его работа не стабилизируется в течение заданного времени, то Chaos создает событие ValidationFailedEvent. Это событие указывает на наличие ошибки, которую необходимо дополнительно изучить.

В текущей версии Chaos вызывает только безопасные сбои. То есть при отсутствии внешних ошибок исключена вероятность потери кворума или данных.

## <a name="important-configuration-options"></a>Важные параметры конфигурации
* **TimeToRun**. Общее время выполнения Chaos до успешного завершения. Вы можете остановить выполнение Chaos до истечения периода TimeToRun с помощью API StopChaos.
* **MaxClusterStabilizationTimeout**. Максимальный период ожидания возврата кластера в работоспособное состояние до повторной проверки. Это нужно, чтобы уменьшить нагрузку на кластер, пока его работоспособность восстанавливается. Выполняемые проверки:
  * работоспособен ли кластер;
  * работоспособна ли служба;
  * достигнут ли заданный размер целевого набора реплик для секции службы;
  * отсутствуют ли реплики InBuild.
* **MaxConcurrentFaults**. Максимальное количество одновременных сбоев, вызываемых при каждой итерации. Чем выше число, тем интенсивнее Chaos вызывает ошибки. Это позволит проверить более сложные комбинации отработок отказа и смены состояний. При отсутствии внешних ошибок Chaos исключает вероятность потери кворума или данных, независимо от значения этого параметра.
* **EnableMoveReplicaFaults**. Включает или отключает сбои, приводящие к перемещению первичных или вторичных реплик. Эти ошибки отключены по умолчанию.
* **WaitTimeBetweenIterations**. Время ожидания между итерациями, то есть после цикла сбоев и соответствующей проверки.
* **WaitTimeBetweenFaults**. Время ожидания между двумя последовательными сбоями в итерации.

## <a name="how-to-run-chaos"></a>Выполнение Chaos
**C#:**

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
                    if (eventSet.add(chaosEvent))
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
**PowerShell:**

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



<!--HONumber=Nov16_HO3-->


