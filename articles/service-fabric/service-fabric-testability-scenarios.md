---
title: Создание тестов хаотичных сбоев и отработки отказа для микрослужб Azure | Документация Майкрософт
description: Использование сценариев тестов на хаос и отработку отказа Service Fabric для провоцирования ошибок и проверки надежности служб.
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 8eee7e89-404a-4605-8f00-7e4d4fb17553
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: d9c05ba2b98af5ef26ef5b5a7ae0995512df4e75
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208759"
---
# <a name="testability-scenarios"></a>Сценарии Testability
Крупные распределенные системы, такие как облачные инфраструктуры, ненадежные по своей сути. Azure Service Fabric предоставляет разработчикам возможность создавать службы, которые работают в ненадежных инфраструктурах. Чтобы создать высококачественные службы, разработчики должны иметь возможность проверить стабильность работы своих служб в таких ненадежных инфраструктурах.

Служба анализа сбоев позволяет разработчикам вызывать действия, порождающие сбой, для тестирования работы служб в условиях сбоя. Однако на этом преимущества целевого моделирования ошибок заканчиваются. Для дальнейшего развития тестирования вы можете использовать сценарии тестирования в Service Fabric: хаотический тест и тест отработки отказа. Они моделируют на кластере непрерывные ошибки с чередованием, как нормальные, так и ненормальные, на протяжении долгого периода времени. Настроив частоту и тип ошибок для теста, его можно запустить через интерфейсы API C# или PowerShell, создавая сбои в кластере и службе.

> [!WARNING]
> ChaosTestScenario заменен более устойчивым Chaos на основе службы. Для получения дополнительных сведений ознакомьтесь с новой статьей [Вызов контролируемого хаоса в кластерах Service Fabric](service-fabric-controlled-chaos.md) .
> 
> 

## <a name="chaos-test"></a>Хаотический тест
Сценарий хаотического тестирования создает ошибки в масштабах всего кластера Service Fabric. Этот сценарий позволяет всего за несколько часов проработать ошибки, которые обычно наблюдаются в течение многих месяцев или лет. Сочетание ошибок с чередованием с высокой частотой возникновения сбоев позволяет находить проблемы, которые в противном случае были бы упущены. Это обеспечивает значительное улучшение качества кода службы.

### <a name="faults-simulated-in-the-chaos-test"></a>Ошибки, моделируемые в хаотическом тесте
* Перезапуск узла
* Перезапуск развернутого пакета кода
* Удаление реплики
* Перезапуск реплики
* Перемещение первичной реплики (необязательно)
* Перемещение вторичной реплики (необязательно)

Хаотический тест выполняет несколько итераций сбоев и проверок кластера в течение указанного периода времени. Кроме того, вы можете настроить время, затрачиваемое на стабилизацию работы и проверку кластера для успешной его работы. Сценарий завершается ошибкой, если при проверке кластера обнаруживается сбой.

Рассмотрим тест, запланированный на выполнение в течение одного часа и включающий не более трех одновременных сбоев. В ходе теста будут вызваны три ошибки, а затем проверена работоспособность кластера. Во время теста предыдущий шаг будет повторяться, пока кластер не станет неработоспособным или не пройдет один час. Если при любой итерации кластер становится неработоспособным, т. е. его работа не стабилизируется в течение заданного времени, тест завершится ошибкой с исключением. Это исключение указывает, что возникли проблемы, которые необходимо дополнительно изучить.

В своей текущей форме механизм создания ошибок при хаотическом тесте вызывает только безопасные сбои. Это означает, что при отсутствии внешних ошибок исключена вероятность потери кворума или данных.

### <a name="important-configuration-options"></a>Важные параметры конфигурации
* **TimeToRun.** Общее время выполнения теста до успешного завершения. Тест может завершиться ранее вместо ошибки проверки.
* **MaxClusterStabilizationTimeout.** Максимальный период ожидания возврата кластера в работоспособное состояние до завершения теста с ошибкой. Проверяется, работоспособны ли кластер и службы, соответствует ли размер набора целевых реплик разделу службы, а также отсутствуют ли реплики InBuild.
* **MaxConcurrentFaults.** Максимальное количество одновременных ошибок, вызываемых при каждой итерации. Чем больше это число, тем более интенсивно проводится тест, что приведет к более сложным отработкам отказа и переходам. Во время этого теста при отсутствии внешних ошибок исключена вероятность потери кворума или данных, независимо от значения этого параметра.
* **EnableMoveReplicaFaults**: включает или отключает ошибки, что приводит к перемещению первичных или вторичных реплик. Эти ошибки отключены по умолчанию.
* **WaitTimeBetweenIterations**: время ожидания между итерациями, то есть после цикла ошибок и соответствующей проверки.

### <a name="how-to-run-the-chaos-test"></a>Выполнение хаотического теста
Пример на языке C#

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
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

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Тест Failover Test
Сценарий тестирования отработки отказа — это версия сценария хаотического тестирования, предназначенная для конкретного раздела службы. В ходе этого теста проверяется влияние отработки отказа на определенный раздел службы (другие службы не затрагиваются). Если ввести сведения о целевом разделе и других параметрах, тест выполняется как клиентское средство, которое использует API C# или PowerShell, чтобы создавать ошибки для раздела службы. В ходе сценария повторяется последовательность моделируемых сбоев и проверка службы, в то время как бизнес-логика обеспечивает рабочую нагрузку. Ошибка при проверке службы указывает на проблему, которую необходимо дополнительно изучить.

### <a name="faults-simulated-in-the-failover-test"></a>Ошибки, моделируемые во время теста отработки отказа
* Перезапуск развернутого пакета кода в расположении раздела
* Удаление первичной или вторичной реплики либо экземпляра без отслеживания состояния
* Перезапуск первичной или вторичной реплики (в материализованной службе)
* Перемещение первичной реплики
* Перемещение вторичной реплики
* Перезапуск раздела

В ходе теста отработки отказа вызывается выбранная ошибка, а затем выполняется проверка службы для обеспечения ее стабильной работы. Во время теста отработки отказа одновременно вызывается только одна ошибка в отличие от хаотического теста, в ходе которого может инициироваться несколько сбоев. Если после каждого сбоя раздел службы не стабилизируется в течение заданного времени ожидания, тест не пройден. Этот тест вызывает только безопасные ошибки. Это означает, что при отсутствии внешних ошибок исключена вероятность потери кворума или данных.

### <a name="important-configuration-options"></a>Важные параметры конфигурации
* **PartitionSelector**— объект выбора, указывающий целевой раздел.
* **TimeToRun**— общее время выполнения теста до завершения.
* **MaxServiceStabilizationTimeout**— максимальный период ожидания возврата кластера в работоспособное состояние до завершения теста с ошибкой. Проверяется, работоспособна ли служба, соответствует ли размер набора целевых реплик всем разделам, а также отсутствуют ли реплики InBuild.
* **WaitTimeBetweenFaults**— время ожидания между каждым циклом сбоя и проверки.

### <a name="how-to-run-the-failover-test"></a>Выполнение теста отработки отказа
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
