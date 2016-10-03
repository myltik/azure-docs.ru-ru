<properties
   pageTitle="Вызов Chaos в кластерах Service Fabric | Microsoft Azure"
   description="Управление Chaos в кластере с помощью интерфейсов API службы внесения ошибок и анализа кластера."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/>

# Вызов контролируемого хаоса в кластерах Service Fabric
Крупномасштабные распределенные системы, такие как облачные инфраструктуры, ненадежны по своей сути. Azure Service Fabric позволяет разработчикам создавать надежные службы на основе ненадежной инфраструктуры. Чтобы создавать надежные службы, разработчики должны иметь возможность вызывать сбои в таких ненадежных инфраструктурах, чтобы проверить стабильность работы своих служб.

Служба внесения ошибок и анализа кластера (FAS) позволяет разработчикам вызывать действия, порождающие сбой, для тестирования работы служб. Однако на этом возможности целевого моделирования ошибок заканчиваются. Для углубленного тестирования можно применить Chaos. Chaos моделирует на кластере непрерывные ошибки, чередуя как нормальные, так и ненормальные ошибки, на протяжении долгого периода времени. Настроив частоту и тип ошибок в Chaos, его можно запустить через интерфейсы API C# или PowerShell, чтобы создавать сбои в работе кластера и службы. Во время выполнения Chaos создает различные события, которые записывают состояние выполнения в определенный момент времени. Например, ExecutingFaultsEvent содержит все ошибки, которые выполняются в этой итерации. ValidationFailedEvent содержит сведения о сбое, обнаруженном в ходе проверки кластера. Для получения отчета о выполнении Chaos можно вызывать API GetChaosReportAsync.

## Ошибки, вызываемые Chaos
Chaos генерирует ошибки во всем кластере Service Fabric и вмещает ошибки за месяцы или годы работы всего в несколько часов тестирования. Сочетание ошибок с чередованием с высокой частотой возникновения сбоев позволяет находить проблемы, которые в противном случае были бы упущены. Это упражнение Chaos обеспечивает значительное улучшение качества кода службы. Chaos вызывает ошибки следующих категорий:

 - Перезапуск узла
 - Перезапуск развернутого пакета кода
 - Удаление реплики
 - Перезапуск реплики
 - перемещение первичной реплики (настраивается);
 - перемещение вторичной реплики (настраивается).

Chaos выполняет несколько итераций. Каждая из них включает в себя сбои и проверку кластера в течение указанного периода времени. Вы можете настроить время, затрачиваемое на стабилизацию работы и проверку кластера для успешной его работы. В случае обнаружения сбоя при проверке кластера Chaos создает и сохраняет событие ValidationFailedEvent с меткой времени в формате UTC и подробными сведениями об ошибке.

Например, рассмотрим экземпляр Chaos, для которого настроено выполнение в течение одного часа и не более трех одновременных сбоев. Chaos вызывает три сбоя и проверяет работоспособность кластера. Затем он выполняет итерацию предыдущего шага, пока выполнение не будет явным образом остановлено посредством API StopChaosAsync или через один час. Если на какой-либо итерации кластер становится неработоспособен, то есть не стабилизируется в течение заданного времени, Chaos создает событие ValidationFailedEvent, которое указывает, что произошла ошибка и может потребоваться дальнейшее изучение.

Текущая версия Chaos вызывает только безопасные ошибки, то есть при отсутствии внешних ошибок исключена вероятность потери кворума или данных.

## Важные параметры конфигурации
 - **TimeToRun**. Общее время выполнения Chaos до успешного завершения. До истечения периода TimeToRun вы можете остановить выполнение Chaos с помощью API StopChaos.
 - **MaxClusterStabilizationTimeout**. Максимальное время ожидания возвращения кластера в работоспособное состояние перед повторной проверкой. Этот период ожидания позволяет уменьшить нагрузку на кластер во время его восстановления. Выполняемые проверки:
    - работоспособен ли кластер;
    - работоспособна ли служба;
    - достигнут ли заданный размер целевого набора реплик для секции службы;
    - отсутствуют ли реплики InBuild.
 - **MaxConcurrentFaults**. Максимальное количество одновременных ошибок, вызываемых при каждой итерации. Чем больше это число, тем более активнее действует Chaos, что приведет к более сложным отработкам отказа и переходам. При отсутствии внешних ошибок Chaos исключает вероятность потери кворума или данных, независимо от значения этого параметра.
 - **EnableMoveReplicaFaults**. Включает или отключает ошибки, приводящие к перемещению первичных или вторичных реплик. Эти ошибки отключены по умолчанию.
 - **WaitTimeBetweenIterations**. Время ожидания между итерациями, то есть после цикла ошибок и соответствующей проверки.
 - **WaitTimeBetweenFaults**. Время ожидания между двумя последовательными ошибками в итерации.

## Выполнение Chaos
Пример на языке C#

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
                // If StoppedEvent is found, exit the loop.
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
PowerShell

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

<!---HONumber=AcomDC_0921_2016-->