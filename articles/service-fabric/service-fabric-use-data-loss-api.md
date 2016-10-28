<properties
   pageTitle="Как вызвать потерю данных в службах Service Fabric | Microsoft Azure"
   description="Описывается, как использовать API потери данных."
   services="service-fabric"
   documentationCenter=".net"
   authors="LMWF"
   manager="rsinha"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="lemai"/>
   
# Как вызвать потерю данных в службах

>[AZURE.WARNING] В этом документе описывается, как вызвать потерю данных в службах, поэтому он должен использоваться с осторожностью.

## Введение
Можно вызвать потерю данных в секции службы Service Fabric, вызвав StartPartitionDataLossAsync(). Этот API использует службу внесения ошибок и анализа для выполнения работы, чтобы создать условия для потери данных.

## Использование службы внесения ошибок и анализа

В настоящее время служба внесения ошибок и анализа поддерживает следующие интерфейсы API, перечисленные в таблице ниже. В правой части диаграммы приведен соответствующий командлет PowerShell. Чтобы получить дополнительные сведения о каждом API, обратитесь к соответствующей документации на сайте MSDN.

| API C# | Командлет PowerShell |
|-------------------------------------|-----------------------------------------------:|
|[StartPartitionDataLossAsync][dl] |[Start-ServiceFabricPartitionDataLoss][psdl] |
|[StartPartitionQuorumLossAsync][ql] |[Start-ServiceFabricPartitionQuorumLoss][psql] |
|[StartPartitionRestartAsync][rp] |[Start-ServiceFabricPartitionRestart][psrp] |

## Принцип выполнения команды

Служба внесения ошибок и анализа использует асинхронную модель, в которой вы начинаете с выполнения команды в одном API (т. н. API Start в этом документе), а затем проверяете ход выполнения этой команды с помощью API GetProgress, пока она не достигнет конечного состояния или не будет отменена вами. Чтобы выполнить команду, вызовите API Start для соответствующего API. Этот API возвращается, если служба внесения ошибок и анализа приняла запрос. Однако это не указывает, насколько выполнена команда или было ли вообще начато ее выполнение. Чтобы проверить ход выполнения команды, вызовите API GetProgress, который соответствует ранее вызванному API Start. API GetProgress вернет объект, указывающий в своем свойстве State текущее состояние команды. Команда выполняется неограниченное время, пока не будет выполнено одно из следующих условий.

1.	Она полностью выполнена. При вызове GetProgress в этом случае свойство State объекта хода выполнения будет иметь значение Completed.
2.	При выполнении возникла неустранимая ошибка. При вызове GetProgress в этом случае свойство State объекта хода выполнения будет иметь значение Faulted.
3.	Вы отменили команд с помощью API [CancelTestCommandAsync][cancel] или командлета PowerShell [Stop ServiceFabricTestCommand][cancelps]. При вызове GetProgress в этом случае свойство State объекта хода выполнения будет иметь значение Cancelled или ForceCancelled, в зависимости от аргумента данного API. Обратитесь к документации по [CancelTestCommandAsync][cancel] для получения дополнительных сведений.


## Подробное описание выполнения команды

Чтобы начать выполнение команды, вызовите API Start с ожидаемыми аргументами. У всех API Start есть аргумент Guid, называемый operationId. Данный аргумент нужно отслеживать, так как он используется для отслеживания хода выполнения этой команды. Его нужно передать в API GetProgress, чтобы отслеживать ход выполнения команды. Значение operationId должно быть уникальным.

После успешного вызова API Start вызов API GetProgress должен осуществляться циклически, пока свойство State возвращаемого объекта хода выполнения не примет значение Completed. Все исключения [FabricTransientException][fte] и OperationCanceledException должны быть обработаны путем повтора операции. Когда команда достигнет конечного состояния (Completed, Faulted или Cancelled), свойство Result возвращаемого объекта хода выполнения будет содержать дополнительные сведения. Если состояние — Completed, то Result.SelectedPartition.PartitionId будет содержать идентификатор секции, которая была выбрана. Result.Exception будет иметь значение NULL. Если состояние — Faulted, то Result.Exception будет содержать причину, по которой служба внесения ошибок и анализа присвоила это состояние выполнения команды. Result.SelectedPartition.PartitionId будет содержать идентификатор секции, которая была выбрана. В некоторых ситуациях команда может быть выполнена недостаточно, чтобы можно было выбрать секцию. В этом случае значение PartitionId будет равно 0. Если состояние — Cancelled, то Result.Exception будет иметь значение NULL. Как и в случае состояния Faulted, Result.SelectedPartition.PartitionId будет содержать идентификатор секции, которая была выбрана, но если команда была выполнена недостаточно для этого, этот идентификатор будет равен 0. Ознакомьтесь также с примером ниже.

В этом примере кода показано, как начать, а затем проверять выполнение команды, которая приводит к потере данных в определенном разделе.

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

В примере ниже показано, как использовать PartitionSelector для выбора случайной секции заданной службы.

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## Журнал и усечение

После достижения командой конечного состояния ее метаданные на какое-то время остаются в службе внесения ошибок и анализа, прежде чем будут удалены для экономии места. Если после их удаления вызвать GetProgress с operationId этой команды, то будет возвращено исключение FabricException с кодом ErrorCode, равным KeyNotFound.

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx

<!---HONumber=AcomDC_0921_2016-->