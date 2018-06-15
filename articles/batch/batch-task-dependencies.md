---
title: Использование зависимостей для выполнения задач на основе завершения других задач с помощью пакетной службы Azure | Документация Майкрософт
description: Создание задач, которые зависят от выполнения других задач, для обработки по модели MapReduce и аналогичных рабочих нагрузок больших данных в пакетной службе Azure.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ba85e075c39251b0b3d7c4b8bc3f8d53a1afadf7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316823"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Создание зависимостей для выполнения задач, которые зависят от других задач

Можно определить зависимости задач, чтобы запускать задачу или набор задач только после завершения родительской задачи. Ниже описано несколько ситуаций, в которых удобно использовать зависимости задач. Их можно использовать для:

* рабочих нагрузок MapReduce в облаке;
* заданий, задачи обработки данных которых можно представить в виде направленного ациклического графа (DAG);
* подготовительных и последующих процессов отрисовки, в которых каждая задача должна быть выполнена прежде, чем начнется выполнение следующей задачи;
* любых других заданий, в которых подчиненные задачи зависят от выходных данных вышестоящих задач.

Зависимости задач пакетной службы позволяют создавать задачи, планируемые для выполнения на вычислительных узлах после завершения одной или нескольких родительских задач. Например, можно создать задание, которое отрисовывает каждый кадр трехмерного фильма с помощью отдельных параллельных задач. Конечная задача, "задача слияния", объединяет отрисованные кадры в готовый фильм только после успешной отрисовки всех кадров.

По умолчанию выполнение зависимых задач планируются только после успешного завершения родительской задачи. Можно указать действие зависимости, чтобы переопределить поведение по умолчанию и выполнить задачи при сбое родительской задачи. Дополнительные сведения см. в разделе [Действия зависимостей](#dependency-actions).  

Можно создавать задачи, которые зависят от других задач по схеме "один к одному" или "один ко многим". Можно также создать зависимость от диапазона, при которой задача зависит от завершения группы задач, идентификаторы которых находятся в пределах определенного диапазона. Вы можете объединить эти три основные сценария, чтобы создать связь "многие ко многим".

## <a name="task-dependencies-with-batch-net"></a>Зависимости задач .NET пакетной службы
В этой статье рассматривается настройка зависимостей задач с помощью библиотеки [.NET пакетной службы][net_msdn]. Сначала в этой статье описывается, как [включить зависимость задачи](#enable-task-dependencies) в заданиях, а потом рассматривается, как [настроить задачу с зависимостями](#create-dependent-tasks). Мы также опишем, как указать действие зависимости, запускающее зависимые задачи при сбое родительской задачи. Напоследок рассматриваются поддерживаемые пакетной службой [сценарии использования зависимостей](#dependency-scenarios) .

## <a name="enable-task-dependencies"></a>Включение зависимостей задач
Чтобы использовать зависимости задач в приложении пакетной службы, сначала необходимо настроить задание для использования зависимостей задач. В .NET пакетной службы включите зависимости задач в классе [CloudJob][net_cloudjob], задав для свойства [UsesTaskDependencies][net_usestaskdependencies] значение `true`.

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Приведенный выше фрагмент кода batchClient представляет собой экземпляр класса [BatchClient][net_batchclient].

## <a name="create-dependent-tasks"></a>Создание зависимости задач
Чтобы создать задачу, зависящую от выполнения одной или нескольких родительских задач, можно указать, что эта задача "зависит" от других задач. В .NET пакетной службы укажите свойство [CloudTask][net_cloudtask].[DependsOn][net_dependson] с помощью экземпляра класса [TaskDependencies][net_taskdependencies].

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Этот фрагмент кода создает зависимую задачу с идентификатором задачи Flowers. Задача Flowers зависит от задач Rain и Sun. Выполнение задачи Flowers будет запланировано на вычислительном узле только после успешного завершения задач Rain и Sun.

> [!NOTE]
> Задача считается успешно выполненной, когда она находится в состоянии **Выполнено** и ее **код выхода** равен `0`. В .NET пакетной службы это означает, что значение свойства [CloudTask][net_cloudtask].[State][net_taskstate] — `Completed`, а значение свойства [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] класса CloudTask — `0`.
> 
> 

## <a name="dependency-scenarios"></a>сценарии использования зависимостей
Есть три основных сценария зависимостей задач, которые можно использовать в пакетной службе Azure: "один к одному", "один ко многим" и зависимость диапазона идентификаторов задач. Их можно объединить, чтобы получить четвертый сценарий — "многие ко многим".

| Сценарий&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Пример |  |
|:---:| --- | --- |
|  [Один к одному](#one-to-one) |Задача *taskB* зависит от задачи *taskA*. <p/> Выполнение задачи *taskB* не начнется, пока задача *taskA* не будет успешно выполнена. |![Схема: зависимость задач "один к одному"][1] |
|  [Один ко многим](#one-to-many) |Задача *taskC* зависит от задач *taskA* и *taskB*. <p/> Выполнение задачи *taskC* не начнется, пока задачи *taskA* и *taskB* не будут успешно выполнены). |![Схема: зависимость задач "один ко многим"][2] |
|  [Диапазон идентификаторов задач](#task-id-range) |Задача *taskD* зависит от ряда задач. <p/> Выполнение задачи *taskD* не начнется, пока не будут успешно выполнены задачи с идентификаторами от *1* до *10*. |![Схема: зависимость диапазона идентификаторов задач][3] |

> [!TIP]
> Вы можете создать связь **многие ко многим**, например, когда каждая задача C, D, E и F зависит от задач A и B. Это удобно, например, в сценариях распараллеленной предварительной обработки, где подчиненные задачи зависят от выходных данных нескольких вышестоящих задач.
> 
> В примерах в этом разделе зависимая задача выполняется только после успешного завершения родительской задачи. Это поведение по умолчанию для зависимой задачи. Можно запустить зависимую задачу после сбоя родительской задачи, указав действие зависимости, чтобы переопределить поведение по умолчанию. Дополнительные сведения см. в разделе [Действия зависимостей](#dependency-actions).

### <a name="one-to-one"></a>Один к одному
При взаимосвязи "один к одному" задача зависит от успешного выполнения одной родительской задачи. Чтобы создать зависимость, укажите один идентификатор задачи в статическом методе [TaskDependencies][net_taskdependencies].[OnId][net_onid] при заполнении свойства [DependsOn][net_dependson] элемента [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Один ко многим
При взаимосвязи "один ко многим" задача зависит от успешного выполнения нескольких родительских задач. Чтобы создать зависимость, укажите набор идентификаторов задач в статическом методе [TaskDependencies][net_taskdependencies].[OnIds][net_onids] при заполнении свойства [DependsOn][net_dependson] элемента [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>Диапазон идентификаторов задач
При зависимости от диапазона родительских задач задача зависит от выполнения задач, идентификаторы которых находятся в указанном диапазоне.
Чтобы создать зависимость, укажите первый и последний идентификаторы задач в статическом методе [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange] при заполнении свойства [DependsOn][net_dependson] элемента [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Если вы используете диапазоны идентификаторов задач для зависимостей, то идентификаторы задач в диапазоне *должны* быть строковыми представлениями целых чисел.
> 
> Каждая задача в диапазоне должна выполнить условие зависимости, завершившись успешно или сбоем, который сопоставлен с действием зависимости со значением **Satisfy**. Дополнительные сведения см. в разделе [Действия зависимостей](#dependency-actions).
>
>

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Действия зависимостей

По умолчанию зависимая задача или набор задач выполняется только после успешного завершения родительской задачи. В некоторых случаях может потребоваться запустить зависимые задачи даже в случае сбоя родительской задачи. Поведение по умолчанию можно переопределить, указав действие зависимости. Действие зависимости указывает, следует ли запускать зависимую задачу в случае успешного завершения или сбоя родительской задачи. 

Например предположим, что зависимая задача ожидает данные из восходящей задачи. При сбое восходящей задачи зависимая задача все равно может быть выполнена с использованием старых данных. В этом случае с помощью действия зависимости можно указать, что зависимая задача может быть выполнена в случае сбоя родительской задачи.

Действие зависимости основано на условии выхода родительской задачи. Можно указать действие зависимости для любого из приведенных ниже условий выхода. Чтобы получить сведения для .NET, ознакомьтесь с классом [ExitConditions][net_exitconditions].

- Если возникла ошибка предварительной обработки.
- Если возникла ошибка отправки файла. Если задача завершается с кодом выхода, который был задан с помощью свойства **exitCodes** или **exitCodeRanges**, а затем возникает ошибка отправки файла, действие, указанное в коде выхода, имеет более высокий приоритет.
- Когда задача завершается с кодом выхода, определенным в свойстве **ExitCodes**.
- Когда задача завершается с кодом выхода, который попадает в диапазон, заданный свойством **ExitCodeRanges**.
- По умолчанию, если задача завершается с кодом выхода, не указанным в свойстве **ExitCodes** или **ExitCodeRanges**, или если задача завершается с ошибкой предварительной обработки и свойство **PreProcessingError** не задано, или если происходит сбой выполнения задачи с ошибкой отправки файла и свойство **FileUploadError** не задано. 

Чтобы указать действие зависимости в .NET, задайте свойство [ExitOptions][net_exitoptions].[DependencyAction][net_dependencyaction] для условия выхода. Свойство **DependencyAction** принимает одно из двух значений:

- Если свойству **DependencyAction** присвоить значение **Satisfy**, то зависимые задачи смогут быть выполнены, если родительская задача завершается с указанной ошибкой.
- Если свойству **DependencyAction** присвоить значение **Block** то, зависимые задачи не будут выполнены.

По умолчанию для свойства **DependencyAction** заданы значение **Satisfy** для кода выхода 0 и значение **Block** для всех остальных условий выхода.

В следующем фрагменте кода настраивается свойство **DependencyAction** родительской задачи. Если родительская задача завершается ошибкой предварительной обработки или ошибкой с заданным кодом, то зависимая задача блокируется. Если родительская задача завершается с любой другой ошибкой, код которой не равен 0, то зависимая задача выполняется.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Пример кода
[TaskDependencies][github_taskdependencies] — это один из [примеров кода пакетной службы Azure][github_samples] на портале GitHub. Это решение Visual Studio демонстрирует следующее:

- как включить зависимость задачи для задания;
- как создать задачи, которые зависят от других задач;
- как выполнить эти задачи в пуле вычислительных узлов.

## <a name="next-steps"></a>Дополнительная информация
### <a name="application-deployment"></a>Развертывание приложения
Функция [пакетов приложения](batch-application-packages.md) в пакетной службе дает возможность очень легко развернуть приложения, которые задачи выполняют на вычислительных узлах, и управлять их версиями.

### <a name="installing-applications-and-staging-data"></a>Установка приложений и промежуточных данных
Ознакомьтесь с публикацией [Installing applications and staging data on Batch compute nodes][forum_post] (Установка приложений и промежуточное размещение данных на вычислительных узлах пакетной службы) на форуме по пакетной службе Azure. Из нее вы узнаете о различных способах подготовки узлов для выполнения задач. Эта публикация написана одним из участников команды разработчиков пакетной службы Azure, и она дает хорошее базовое понимание разных способов копирования приложений, входных данных для задач и других файлов на вычислительные узлы.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions#Microsoft_Azure_Batch_ExitOptions_DependencyAction
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Схема: зависимость "один к одному""
[2]: ./media/batch-task-dependency/02_one_to_many.png "Схема: зависимость "один ко многим""
[3]: ./media/batch-task-dependency/03_task_id_range.png "Схема: зависимость диапазона идентификаторов задач"
