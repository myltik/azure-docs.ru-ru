---
title: Использование задач с несколькими экземплярами для выполнения приложений MPI с помощью пакетной службы Azure | Документация Майкрософт
description: Узнайте, как выполнять приложения с интерфейсом передачи сообщений (MPI), используя тип задачи с несколькими экземплярами в пакетной службе Azure.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 5/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0fb5ea21c6403369cbcb60df58c0f70a57a61d4e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30160762"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Использование задач с несколькими экземплярами для запуска приложений с интерфейсом передачи сообщений в пакетной службе

Многоэкземплярные задачи позволяют выполнять задачи пакетной службы Azure на нескольких вычислительных узлах одновременно. Эти задачи реализуют в пакетной службе такие сценарии высокопроизводительных вычислений, как приложения интерфейса передачи сообщений (MPI). Из этой статьи вы узнаете, как выполнять задачи с несколькими экземплярами с помощью библиотеки [.NET пакетной службы][api_net].

> [!NOTE]
> Примеры в этой статье посвящены вычислительным узлам Batch .NET, MS-MPI и Windows. Рассматриваемые здесь принципы выполнения многоэкземплярных задач применимы для других платформ и технологий (Python и Intel MPI на узлах Linux, например).
>
>

## <a name="multi-instance-task-overview"></a>Общие сведения о задачах с несколькими экземплярами
В пакетной службе каждая задача обычно выполняется на одном вычислительном узле: вы отправляете несколько задач в задание, и пакетная служба планирует выполнение каждой задачи на узле. Тем не менее, если настроить **параметры использования нескольких экземпляров** задачи, вы сообщите пакетной службе вместо этого создать одну основную задачу и несколько подзадач, которые выполняются на нескольких узлах.

![Общие сведения о задачах с несколькими экземплярами][1]

При отправке задачи с несколькими экземплярами в задание пакетная служба выполняет определенные действия.

1. Пакетная служба создает одну **основную** задачу и несколько **подзадач** на основе параметров использования нескольких экземпляров. Общее число задач (основная плюс все подзадачи) соответствует количеству **экземпляров** (вычислительных узлов), указанному в параметрах использования нескольких экземпляров.
2. Пакетная служба назначает один из вычислительных узлов **главным** и планирует выполнение основной задачи на нем. Выполнение подзадач она планирует на остальных вычислительных узлах, выделенных для многоэкземплярной задачи, размещая по одной подзадаче на каждом узле.
3. Основная задача и все подзадачи скачивают **общие файлы ресурсов**, указанные в параметрах использования нескольких экземпляров.
4. Потом они выполняют **команду координации** , указанную в параметрах для множественных экземпляров. Команда координации обычно используется для подготовки узлов к выполнению задачи. Подготовка может включать в себя запуск фоновых служб (таких как `smpd.exe` [Microsoft MPI][msmpi_msdn]) и проверку готовности узлов к обработке сообщений, передаваемых между узлами.
5. *После* того, как основная задача и все подзадачи успешно выполнят команду координации, основная задача выполнит **команду приложения** на главном узле. Команда приложения — это командная строка, указанная для многоэкземплярной задачи. Ее выполняет только основная задача. Например, вы можете запустить выполнение приложения с поддержкой MPI в решении на базе [MS-MPI][msmpi_msdn] с помощью команды `mpiexec.exe`.

> [!NOTE]
> Хотя задача с несколькими экземплярами по функциональности отличается от остальных задач, она не относится к типу таких уникальных задач, как [StartTask][net_starttask] или [JobPreparationTask][net_jobprep]. Задача с несколькими экземплярами — это всего лишь стандартная задача пакетной службы ([CloudTask][net_task] в .NET пакетной службы) с настроенными параметрами нескольких экземпляров. В этой статье задача такого типа называется **задачей с несколькими экземплярами**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Требования к задачам с несколькими экземплярами
При выполнении многоэкземплярной задачи требуется, чтобы в пуле был **включен обмен данными между узлами** и **отключено параллельное выполнение задач**. Чтобы отключить параллельное выполнение задач, задайте для свойства [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool#Microsoft_Azure_Batch_CloudPool_MaxTasksPerComputeNode) значение 1.

> [!NOTE]
> Пакетная служба [ограничит](batch-quota-limit.md#other-limits) размера пула, для которого включен обмен данными между узлами.


В этом фрагменте кода показано, как создать пул для многоэкземплярных задач, используя библиотеку .NET для пакетной службы.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> Если запустить многоэкземплярную задачу в пуле с отключенным обменом данными между узлами или со значением параметра *maxTasksPerNode* больше 1, задача не будет запланирована. Вместо этого она будет постоянно находиться в состоянии "Активно". 
>
> Многоэкземплярные задачи могут выполняться только на узлах в пулах, созданных после 14 декабря 2015 года.
>
>

### <a name="use-a-starttask-to-install-mpi"></a>Установка MPI с помощью StartTask
Для выполнения приложений MPI с помощью задачи с несколькими экземплярами на вычислительных узлах в пуле сначала необходимо установить реализацию MPI (например, MS-MPI или Intel MPI). Это хорошая возможность воспользоваться задачей [StartTask][net_starttask], которая выполняется каждый раз при присоединении узла к пулу или его перезапуске. В этом фрагменте кода создается StartTask, указывающий пакет установки MS-MPI в качестве [файла ресурсов][net_resourcefile]. Командная строка задачи запуска выполняется после скачивания файла ресурсов на узел. В этом случае командная строка выполняет автоматическую установку MS-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Удаленный доступ к памяти (RDMA)
Если для пула пакетной службы выбран [размер с поддержкой RDMA](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), например А9, то приложение MPI может воспользоваться преимуществами сети RDMA Azure с удаленным доступом к памяти, обеспечивающей высокую производительность и низкие задержки.

Сведения о размерах, указанных в качестве "С поддержкой RDMA", см. в следующих статьях:

* Пулы **CloudServiceConfiguration**

  * [Размеры для облачных служб](../cloud-services/cloud-services-sizes-specs.md) (только для Windows)
* Пулы **VirtualMachineConfiguration**

  * [Размеры виртуальных машин в Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Размеры виртуальных машин в Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Чтобы воспользоваться преимуществами RDMA на [вычислительных узлах Linux](batch-linux-nodes.md), на узлах необходимо использовать **Intel MPI**. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Создание задачи с несколькими экземплярами с помощью .NET для пакетной службы
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

## <a name="primary-task-and-subtasks"></a>Основная задача и подзадачи
При создании параметров для множественных экземпляров для задачи нужно указать число вычислительных узлов, которые должны выполнять задачу. При отправке задачи в задание пакетная служба создает одну **основную** задачу и такое количество **подзадач**, которое вместе с основной задачей совпадает с указанным количеством узлов.

Этим задачам назначается целочисленный идентификатор в диапазоне от 0 до *numberOfInstances* – 1. Задача с идентификатором 0 — это основная задача, а все остальные идентификаторы назначаются подзадачам. Например, при создании следующих параметров нескольких экземпляров для задачи основная задача будет иметь идентификатор 0, а подзадачи — в диапазоне от 1 до 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Главный узел
Когда вы отправляете многоэкземплярную задачу, пакетная служба назначает один из вычислительных узлов главным и планирует выполнение основной задачи на нем. Выполнение подзадач она планирует на остальных вычислительных узлах, выделенных для многоэкземплярной задачи.

## <a name="coordination-command"></a>команду координации
**Команду координации** выполняют основная задача и подзадачи.

Вызов команды координации блокируется: пакетная служба не выполнит команду приложения, пока команда координации не будет успешно возвращена для всех подзадач. Таким образом, команда координации должна запустить все необходимые фоновые службы. Убедитесь, что они готовы к использованию, а затем выполните выход. К примеру, эта команда координации для решения с использованием MS-MPI версии 7 запускает службу SMPD на узле, а затем завершает работу:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Обратите внимание, что в этой команде координации используется `start` . Это необходимо, так как приложение `smpd.exe` не возвращается сразу после выполнения. Если не использовать команду [start][cmd_start], то команда координации не возвращается и поэтому выполнение команды приложения блокируется.

## <a name="application-command"></a>Команда приложения
Если основная задача и все подзадачи выполнили команду координации, командную строку многоэкземплярной задачи выполняет *только*основная задача. Назовем эту командную строку **командой приложения** , чтобы отличать ее от команды координации.

Для приложений MS-MPI используйте команду приложения, чтобы выполнить приложение с поддержкой MPI с использованием `mpiexec.exe`. В качестве примера ниже приведена команда приложения для решения с использованием MS-MPI версии 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Так как команда `mpiexec.exe` приложений MS-MPI использует переменную `CCP_NODES` по умолчанию (см. раздел [Переменные среды](#environment-variables)), в примере командной строки приложения выше она исключена.
>
>

## <a name="environment-variables"></a>Переменные среды
Пакетная служба создает несколько [переменных среды][msdn_env_var] для конкретных задач с несколькими экземплярами на вычислительных узлах, выделенных для каждой задачи с несколькими экземплярами. Командные строки координации и приложения могут ссылаться на эти переменные среды, как и сценарии и программы, выполняемые с их помощью.

Приведенные ниже переменные среды создаются пакетной службой для использования многоэкземплярными задачами.

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Полные сведения об этих и других переменных среды вычислительных узлов пакетной службы, включая их содержимое и видимость, доступны в разделе [Azure Batch compute node environment variables][msdn_env_var] (Переменные среды вычислительных узлов пакетной службы Azure).

> [!TIP]
> Фрагмент кода Linux MPI для пакетной службы содержит пример использования некоторых из этих переменных среды. Сценарий Bash [coordination-cmd][coord_cmd_example] скачивает общее приложение и входные файлы из службы хранилища Azure, открывает доступ к общему ресурсу NFS на главном узле и настраивает другие узлы, выделенные для задачи с несколькими экземплярами, как NFS-клиенты.
>
>

## <a name="resource-files"></a>Файлы ресурсов
Для многоэкземплярных задач есть два набора файлов ресурсов: **общие файлы ресурсов**, которые скачивают *все* задачи (основная задача и подзадачи), и **файлы ресурсов**, указанные непосредственно для многоэкземплярной задачи, которые скачивает *только основная* задача.

В параметрах для задачи с множественными экземплярами можно указать один или несколько **общих файлов ресурсов** . Основная задача и все подзадачи скачивают эти общие файлы ресурсов из [службы хранилища Azure](../storage/common/storage-introduction.md) в **общий каталог задач** каждого узла. Доступ к общему каталогу задачи можно получить из приложения и командной строки координации с помощью переменной среды `AZ_BATCH_TASK_SHARED_DIR` . Пути `AZ_BATCH_TASK_SHARED_DIR` идентичны для каждого узла, выделенного многоэкземплярной задаче, поэтому можно использовать общую команду координации для основной задачи и всех подзадач. Пакетная служба не предоставляет общий доступ к каталогу, как при удаленном доступе, но его можно использовать в качестве подключаемого каталога или общего ресурса, как упоминалось ранее в подсказке к переменным среды.

Файлы ресурсов, указываемые для самой многоэкземплярной задачи, по умолчанию скачиваются в ее рабочий каталог, `AZ_BATCH_TASK_WORKING_DIR`. Как уже упоминалось, в отличие от общих файлов ресурсов, только основная задача скачивает файлы ресурсов, указанные для многоэкземплярной задачи.

> [!IMPORTANT]
> Для создания ссылок на эти каталоги в командной строке всегда используйте переменные среды `AZ_BATCH_TASK_SHARED_DIR` и `AZ_BATCH_TASK_WORKING_DIR`. Не пытайтесь создать пути вручную.
>
>

## <a name="task-lifetime"></a>Срок действия задачи
Срок действия основной задачи определяет срок действия всей задачи с несколькими экземплярами. При выходе из основной задачи завершаются все подзадачи. Код выхода основной задачи — это код выхода самой задачи с несколькими экземплярами. На его основе можно определить, как завершилась задача (успешно или со сбоем), т. е. нужно ли выполнять повторные попытки.

Если любая из подзадач завершается неудачно, например, выполняется выход с ненулевым кодом возврата, происходит сбой всей задачи с несколькими экземплярами. Затем задача с несколькими экземплярами завершается и выполняется повторно, пока не будет достигнут предел повтора.

При удалении задачи с несколькими экземплярами пакетная служба удаляет основную задачу и все подзадачи. Все каталоги и файлы подзадач удаляются из вычислительных узлов также, как и при стандартной задаче.

Свойства [TaskConstraints][net_taskconstraints] для задачи с несколькими экземплярами (включая [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock] и [RetentionTime][net_taskconstraint_retention]), обрабатываются как свойства для стандартной задачи и применяются к основной задаче и всем подзадачам. Но если изменить свойство [RetentionTime][net_taskconstraint_retention] после добавления задачи с несколькими экземплярами в задание, это изменение будет применено только к основной задаче. Для всех подзадач и дальше будет использоваться исходное значение свойства [RetentionTime][net_taskconstraint_retention].

Если последняя задача является частью многоэкземплярной задачи, в списке последних задач вычислительного узла отображается идентификатор подзадачи.

## <a name="obtain-information-about-subtasks"></a>Получение сведений о подзадачах
Чтобы получить сведения о подзадачах с помощью библиотеки .NET пакетной службы, вызовите метод [CloudTask.ListSubtasks][net_task_listsubtasks]. Этот метод возвращает сведения о всех подзадачах и вычислительном узле, на котором выполняются задачи. Руководствуясь полученной информацией, можно определить корневой каталог каждой подзадачи, идентификатор пула, его текущее состояние, код выхода и многое другое. Эти сведения можно использовать в сочетании с методом [PoolOperations.GetNodeFile][poolops_getnodefile], чтобы получить файлы подзадачи. Обратите внимание, что этот метод не возвращает сведения для основной задачи (идентификатор — 0).

> [!NOTE]
> Если не указано иное, методы .NET пакетной службы, которые используются для задачи с несколькими экземплярами [CloudTask][net_task], применяются *только* к основной задаче. Например, при вызове метода [CloudTask.ListNodeFiles][net_task_listnodefiles] для задачи с несколькими экземплярами возвращаются только файлы основной задачи.
>
>

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

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
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

## <a name="code-sample"></a>Пример кода
В примере кода [MultiInstanceTasks][github_mpi] в GitHub демонстрируется использование задачи с несколькими экземплярами для запуска приложения [MS-MPI][msmpi_msdn] на вычислительных узлах пакетной службы. Чтобы запустить пример, следуйте указаниям, приведенным в разделах [Подготовка](#preparation) и [Выполнение](#execution).

### <a name="preparation"></a>Подготовка
1. Выполните первые два шага в статье [How to compile and run a simple MS-MPI program][msmpi_howto] (Как скомпилировать и выполнить простую программу MS-MPI). Это поможет выполнить предварительные требования к следующему шагу.
2. Создайте версию *Выпуск* примера программы MPI [MPIHelloWorld][helloworld_proj]. Это программа, которую будет выполнять на вычислительных узлах задача с несколькими экземплярами.
3. Создайте ZIP-файл, содержащий файл `MPIHelloWorld.exe` (созданный на шаге 2) и `MSMpiSetup.exe` (скачанный на шаге 1). Вы отправите этот ZIP-файл как пакет приложения на следующем шаге.
4. Используйте [портал Azure][portal] для создания [приложения](batch-application-packages.md) пакетной службы с именем MPIHelloWorld и укажите ZIP-файл, созданный на предыдущем шаге, в качестве версии 1.0 пакета приложения. Дополнительные сведения см. в разделе [Передача приложений и управление ими](batch-application-packages.md#upload-and-manage-applications).

> [!TIP]
> Создайте версию *Выпуск* файла `MPIHelloWorld.exe`, чтобы в пакет приложения не нужно было включать какие-либо дополнительные зависимости (например, `msvcp140d.dll` или `vcruntime140d.dll`).
>
>

### <a name="execution"></a>Выполнение
1. Скачайте файл [azure-batch-samples][github_samples_zip] из GitHub.
2. Откройте **решение** MultiInstanceTasks в Visual Studio 2015 или более поздней версии. Файл решения `MultiInstanceTasks.sln` находится в следующем расположении:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Введите данные своей учетной записи пакетной службы и учетной записи хранения в `AccountSettings.settings` в проекте **Microsoft.Azure.Batch.Samples.Common**.
4. **Создайте и запустите** решение MultiInstanceTasks, чтобы выполнить пример приложения MPI на вычислительных узлах в пуле пакетной службы.
5. *Необязательно.* Используйте [портал Azure][portal] или [BatchLabs][batch_labs], чтобы проверить пример пула, задания и задачи (MultiInstanceSamplePool, MultiInstanceSampleJob, MultiInstanceSampleTask), прежде чем удалить ресурсы.

> [!TIP]
> Вы можете скачать [Visual Studio Community][visual_studio] бесплатно, если у вас нет Visual Studio.
>
>

Результат `MultiInstanceTasks.exe` аналогичен приведенному ниже:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Дополнительная информация
* В блоге команды разработчиков для Microsoft HPC и пакетной службы Azure обсуждается [поддержка MPI для Linux в пакетной службе Azure][blog_mpi_linux] и предоставляются сведения об использовании [OpenFOAM][openfoam] с пакетной службой. Фрагменты кода Python для [примера OpenFOAM можно найти на сайте GitHub][github_mpi].
* Узнайте, как [создавать пулы на вычислительных узлах Linux](batch-linux-nodes.md) для использования в решениях MPI пакетной службы Azure.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchLabs/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

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

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Общие сведения о нескольких экземплярах"
