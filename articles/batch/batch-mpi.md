<properties
	pageTitle="Запуск приложений MPI в пакетной службе Azure с использованием задач с несколькими экземплярами | Microsoft Azure"
	description="Узнайте, как выполнять приложения с интерфейсом передачи сообщений (MPI), используя тип задачи с несколькими экземплярами в пакетной службе Azure."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/29/2016"
	ms.author="marsma" />

# Использование задач с несколькими экземплярами для запуска приложений с интерфейсом передачи сообщений в пакетной службе Azure

Многоэкземплярные задачи позволяют выполнять задачи пакетной службы Azure на нескольких вычислительных узлах одновременно. Эти задачи реализуют в пакетной службе такие сценарии высокопроизводительных вычислений, как приложения интерфейса передачи сообщений (MPI). Из этой статьи вы узнаете, как выполнять многоэкземплярные задачи с помощью библиотеки [.NET для пакетной службы][api_net].

## Общие сведения о задачах с несколькими экземплярами

В пакетной службе каждая задача обычно выполняется на одном вычислительном узле: вы отправляете несколько задач в задание, и пакетная служба планирует выполнение каждой задачи на узле. Однако можно настроить **параметры нескольких экземпляров** задачи таким образом, чтобы пакетная служба разделяла задачу на подзадачи и выполняла их на нескольких узлах.

![Общие сведения о задачах с несколькими экземплярами][1]

При отправке задачи с несколькими экземплярами в задание пакетная служба выполняет определенные действия.

1. Пакетная служба автоматически разделяет задачи на одну **основную** задачу и множество **подзадач**. Затем пакетная служба планирует выполнение основной задачи и подзадач на вычислительных узлах пула.
2. Эти задачи и их подзадачи скачивают все **общие файлы ресурсов**, указанные в параметрах для множественных экземпляров.
3. Потом они выполняют **команду координации**, указанную в параметрах для множественных экземпляров. Эта команда координации обычно используется для запуска фоновой службы (такой как [Microsoft MPI][msmpi_msdn] `smpd.exe`) и проверки готовности узлов к обработке сообщений между узлами.
4. Когда основная задача и все подзадачи выполнили команду координации, *командную строку* многоэкземплярной задачи (команду приложения) выполняет **только** **основная задача**. Например, вы можете запустить выполнение приложения с поддержкой MPI в решении на базе [MS-MPI][msmpi_msdn] с помощью команды `mpiexec.exe`.

> [AZURE.NOTE] Хотя задача с несколькими экземплярами по функциональности отличается от остальных задач, она не относится к типу таких уникальных задач, как [StartTask][net_starttask] или [JobPreparationTask][net_jobprep]. Задача с несколькими экземплярами — это всего лишь стандартная задача пакетной службы ([CloudTask][net_task] в .NET для пакетной службы) с настроенными параметрами нескольких экземпляров. В этой статье задача такого типа называется **задачей с несколькими экземплярами**.

## Требования к задачам с несколькими экземплярами

При выполнении задачи с несколькими экземплярами требуется, чтобы в пуле был **включен обмен данными между узлами** и **отключено выполнение параллельных задач**. Если запустить многоэкземплярную задачу в пуле с отключенным обменом данными между узлами или со значением параметра *maxTasksPerNode* больше 1, задача не будет запланирована. Вместо этого она будет постоянно находиться в состоянии "Активно". В этом фрагменте кода показано создание такого пула с помощью библиотеки .NET для пакетной службы.

```csharp
CloudPool myCloudPool =
	myBatchClient.PoolOperations.CreatePool(
		poolId: "MultiInstanceSamplePool",
		targetDedicated: 3
		virtualMachineSize: "small",
		cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

Кроме того, многоэкземплярные задачи могут выполняться *только* на узлах в **пулах, созданных после 14 декабря 2015 года**.

> [AZURE.TIP] Если в пуле пакетной службы используются [вычислительные узлы A8 или A9](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md), приложения MPI могут воспользоваться преимуществами сети RDMA Azure с высокой производительностью и низкой задержкой. Полный список размеров вычислительных узлов, доступных для пулов пакетной службы, см. в статье [Размеры для облачных служб](./../cloud-services/cloud-services-sizes-specs.md).

### Использование StartTask для установки приложений MPI

Перед запуском приложений MPI с использованием задачи с несколькими экземплярами на вычислительных узлах в пуле сначала необходимо установить программное обеспечение MPI. Это отличная возможность воспользоваться задачей [StartTask][net_starttask], которая выполняется каждый раз при присоединении узла к пулу или его перезапуске. Этот фрагмент кода создает задачу StartTask, которая определяет пакет установки MS-MPI в качестве [файла ресурсов][net_resourcefile], а также командную строку, которая выполняется после скачивания файла ресурсов на узел.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

> [AZURE.NOTE] В пакетной службе при реализации решения MPI с помощью задач с несколькими экземплярами можно использовать и другие методы, помимо MS-MPI. Вы можете использовать любой метод реализации стандарта MPI, совместимый с операционной системой, указанной для вычислительных узлов в пуле.

## Создание задачи с несколькими экземплярами с помощью .NET для пакетной службы

Теперь после рассмотрения требований к пулу и установки пакета MPI самое время перейти к созданию задачи с несколькими экземплярами. В этом фрагменте кода мы создадим стандартную задачу [CloudTask][net_task], а затем настроим ее свойство [MultiInstanceSettings][net_multiinstance_prop]. Как упоминалось выше, многоэкземплярная задача не относится к особому типу задач, а является стандартной задачей пакетной службы, настраиваемой с помощью параметров для множественных экземпляров.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## Основная задача и подзадачи

При создании параметров для множественных экземпляров для задачи нужно указать число вычислительных узлов, которые должны выполнять задачу. При отправке задачи в задание пакетная служба создает одну **основную** задачу и такое количество **подзадач**, которое вместе с основной задачей совпадает с указанным количеством узлов.

Этим задачам назначается целочисленный идентификатор в диапазоне от 0 до *numberOfInstances –1*. Задача с идентификатором 0 — это основная задача, а все остальные идентификаторы назначаются подзадачам. Например, при создании следующих параметров нескольких экземпляров для задачи основная задача будет иметь идентификатор 0, а подзадачи — в диапазоне от 1 до 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

## Команда координации

**Команду координации** выполняют основная задача и подзадачи.

Вызов команды координации блокируется: пакетная служба не выполнит команду приложения, пока команда координации не будет успешно возвращена для всех подзадач. Таким образом, команда координации должна запустить все необходимые фоновые службы. Убедитесь, что они готовы к использованию, а затем выполните выход. К примеру, эта команда координации для решения с использованием MS-MPI версии 7 запускает службу SMPD на узле, а затем завершает работу:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Обратите внимание, что в этой команде координации используется `start`. Это необходимо, так как приложение `smpd.exe` не возвращается сразу после выполнения. Если не использовать команду [start][cmd_start], то команда координации не возвращается и поэтому выполнение команды приложения блокируется.

## Команда приложения

Если основная задача и все подзадачи выполнили команду координации, командную строку многоэкземплярной задачи выполняет *только* основная задача. Назовем эту командную строку **командой приложения**, чтобы отличать ее от команды координации.

Для приложений MS-MPI используйте команду приложения, чтобы выполнить приложение с поддержкой MPI с использованием `mpiexec.exe`. В качестве примера ниже приведена команда приложения для решения с использованием MS-MPI версии 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

>[AZURE.NOTE] Так как команда `mpiexec.exe` приложений MS-MPI использует переменную `CCP_NODES` по умолчанию (см. раздел [Переменные среды](#environment-variables)), в примере командной строки приложения выше она исключена.

## Переменные среды

Эти переменные среды доступны на вычислительном узле для приложений и скриптов, которые выполняет команда приложения основной задачи:

* `CCP_NODES`

  * **Доступность**: пулы CloudServiceConfiguration и VirtualMachineConfiguration.
  * **Формат**: `numNodes<space>nodeIP<space>numCores<space>`.
  * **Пример**: `2 10.0.0.4 1 10.0.0.5 1`.

* `AZ_BATCH_NODE_LIST`

  * **Доступность**: пулы CloudServiceConfiguration и VirtualMachineConfiguration.
  * **Формат**: `nodeIP;nodeIP`.
  * **Пример**: `10.0.0.4;10.0.0.5`.

* `AZ_BATCH_HOST_LIST`

  * **Доступность**: пулы VirtualMachineConfiguration.
  * **Формат**: `nodeIP,nodeIP`.
  * **Пример**: `10.0.0.4,10.0.0.5`.

## Файлы ресурсов

Для многоэкземплярных задач есть два набора файлов ресурсов: **общие файлы ресурсов**, которые скачивают *все* задачи (основная задача и подзадачи), и **файлы ресурсов**, указанные непосредственно для многоэкземплярной задачи, которые скачивает *только основная* задача.

В параметрах для задачи с множественными экземплярами можно указать один или несколько **общих файлов ресурсов**. Основная задача и все подзадачи скачивают эти общие файлы ресурсов из [службы хранилища Azure](./../storage/storage-introduction.md) в общий каталог задачи каждого узла. Доступ к общему каталогу задачи можно получить из приложения и командной строки координации с помощью переменной среды `AZ_BATCH_TASK_SHARED_DIR`.

Файлы ресурсов, указанные для многоэкземплярной задачи, скачивает в рабочий каталог задачи (`AZ_BATCH_TASK_WORKING_DIR`) *только* основная задача, так как подзадачи не скачивают эти файлы ресурсов.

Содержимое каталога `AZ_BATCH_TASK_SHARED_DIR` доступно основной задаче и всем подзадачам, выполняемым на узле. Пример общего каталога задачи — `tasks/mybatchjob/job-1/mymultiinstancetask/`. Кроме того, у основной задачи и каждой подзадачи есть рабочий каталог, доступный только для этой задачи. Получить доступ к нему можно с помощью переменной среды `AZ_BATCH_TASK_WORKING_DIR`.

Обратите внимание, что в примерах кода в этой статье мы указываем файлы ресурсов не для самой многоэкземплярной задачи, а только для задачи StartTask пула и свойства [CommonResourceFiles][net_multiinsance_commonresfiles] параметров для множественных экземпляров.

> [AZURE.IMPORTANT] Для создания ссылок на эти каталоги в командной строке всегда используйте переменные среды `AZ_BATCH_TASK_SHARED_DIR` и `AZ_BATCH_TASK_WORKING_DIR`. Не пытайтесь создать пути вручную.

## Срок действия задачи

Срок действия основной задачи определяет срок действия всей задачи с несколькими экземплярами. При выходе из основной задачи завершаются все подзадачи. Код выхода основной задачи — это код выхода самой задачи с несколькими экземплярами. На его основе можно определить, как завершилась задача (успешно или со сбоем), т. е. нужно ли выполнять повторные попытки.

Если любая из подзадач завершается неудачно, например, выполняется выход с ненулевым кодом возврата, происходит сбой всей задачи с несколькими экземплярами. Затем задача с несколькими экземплярами завершается и выполняется повторно, пока не будет достигнут предел повтора.

При удалении задачи с несколькими экземплярами пакетная служба удаляет основную задачу и все подзадачи. Все каталоги и файлы подзадач удаляются из вычислительных узлов также, как и при стандартной задаче.

Свойства [TaskConstraints][net_taskconstraints] для многоэкземплярной задачи (включая [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock] и [RetentionTime][net_taskconstraint_retention]), обрабатываются как свойства для стандартной задачи и применяются к основной задаче и всем подзадачам. Но если изменить свойство [RetentionTime][net_taskconstraint_retention] после добавления многоэкземплярной задачи в задание, это изменение будет применено только к основной задаче. Для всех подзадач и дальше будет использоваться исходное значение свойства [RetentionTime][net_taskconstraint_retention].

Если последняя задача является частью многоэкземплярной задачи, в списке последних задач вычислительного узла отображается идентификатор подзадачи.

## Получение сведений о подзадачах

Чтобы получить сведения о подзадачах с помощью библиотеки .NET для пакетной службы, вызовите метод [CloudTask.ListSubtasks][net_task_listsubtasks]. Этот метод возвращает сведения о всех подзадачах и вычислительном узле, на котором выполняются задачи. Руководствуясь полученной информацией, можно определить корневой каталог каждой подзадачи, идентификатор пула, его текущее состояние, код выхода и многое другое. Эти сведения можно использовать в сочетании с методом [PoolOperations.GetNodeFile][poolops_getnodefile], чтобы получить файлы подзадачи. Обратите внимание, что этот метод не возвращает сведения для основной задачи (идентификатор — 0).

> [AZURE.NOTE] Если не указано иное, методы .NET для пакетной службы, которые используются для многоэкземплярной задачи [CloudTask][net_task], применяются *только* к основной задаче. Например, при вызове метода [CloudTask.ListNodeFiles][net_task_listnodefiles] для многоэкземплярной задачи возвращаются только файлы основной задачи.

В следующем фрагменте кода показано, как получить сведения о подзадачах, а также запросить содержимое файла из узлов, на которых выполняются эти подзадачи.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
			await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## Дальнейшие действия

- Вам может потребоваться создать простое приложение MS-MPI, которое можно использовать при тестировании задач с несколькими экземплярами в пакетной службе. Пошаговые инструкции по созданию простого приложения MPI с помощью MS-MPI см. в записи блога рабочей группы пакетной службы Azure и Microsoft HPC [How to compile and run a simple MS-MPI program][msmpi_howto] \(Как скомпилировать и запустить простую программу MS-MPI).

- Актуальные сведения о MS-MPI см. на странице [Microsoft MPI][msmpi_msdn] на сайте MSDN.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Общие сведения о нескольких экземплярах"

<!---HONumber=AcomDC_0831_2016-->