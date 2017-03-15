---
title: "Использование зависимостей для выполнения задач на основе завершения других задач с помощью пакетной службы Azure | Документация Майкрософт"
description: "Создание задач, которые зависят от выполнения других задач, для обработки по модели MapReduce и аналогичных рабочих нагрузок больших данных в пакетной службе Azure."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6b6c548ca1001587e2b40bbe9ee2fcb298f40d72
ms.openlocfilehash: d7cca5d71d3db45599b47328755c53a023e9c4ae
ms.lasthandoff: 02/28/2017


---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Создание зависимостей для выполнения задач, которые зависят от других задач

Функция зависимости задач пакетной службы Azure хорошо подходит для обработки:

* рабочих нагрузок MapReduce в облаке;
* заданий, задачи обработки данных которых можно представить в виде направленного ациклического графа (DAG);
* любых других заданий, в которых подчиненные задачи зависят от выходных данных вышестоящих задач.

Зависимости задач пакета позволяют создавать задачи, запланированные для выполнения на вычислительных узлах только после успешного завершения одной или нескольких задач. Например, можно создать задание, которое отрисовывает каждый кадр трехмерного фильма с помощью отдельных параллельных задач. Конечная задача, "задача слияния", объединяет отрисованные кадры в готовый фильм только после успешной отрисовки всех кадров.

Можно создавать задачи, которые зависят от других задач по схеме "один к одному" или "один ко многим". Можно даже создать зависимость от диапазона, при которой задача зависит от успешного завершения группы задач, идентификаторы которых находятся в пределах определенного диапазона. Вы можете объединить эти три основные сценария, чтобы создать связь "многие ко многим".

## <a name="task-dependencies-with-batch-net"></a>Зависимости задач .NET пакетной службы
В этой статье рассматривается настройка зависимостей задач с помощью библиотеки [.NET пакетной службы][net_msdn]. Сначала в этой статье описывается, как [включить зависимость задачи](#enable-task-dependencies) в заданиях, а потом рассматривается, как [настроить задачу с зависимостями](#create-dependent-tasks). Напоследок рассматриваются поддерживаемые пакетной службой [сценарии использования зависимостей](#dependency-scenarios) .

## <a name="enable-task-dependencies"></a>Включение зависимостей задач
Чтобы использовать зависимости задачи в приложении пакетной службы, сначала необходимо сообщить пакетной службе об использовании в задании зависимостей задач. В .NET пакетной службы включите зависимости задач в классе [CloudJob][net_cloudjob], задав для свойства [UsesTaskDependencies][net_usestaskdependencies] значение `true`.

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Приведенный выше фрагмент кода batchClient представляет собой экземпляр класса [BatchClient][net_batchclient].

## <a name="create-dependent-tasks"></a>Создание зависимости задач
Чтобы создать задачу, зависящую от успешного выполнения одной или нескольких других задач, сообщите пакетной службе, что задача "зависит" от других задач. В .NET пакетной службы укажите свойство [CloudTask][net_cloudtask].[DependsOn][net_dependson] с помощью экземпляра класса [TaskDependencies][net_taskdependencies].

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Этот фрагмент кода создает задачу с идентификатором "Цветы", запланированную для выполнения на вычислительном узле только после успешного выполнения задач с идентификатором "Дождь" и "Солнце".

> [!NOTE]
> Задача считается выполненной, когда она находится в состоянии **Выполнено** и ее **код выхода** равен `0`. В .NET пакетной службы это означает, что значение свойства [CloudTask][net_cloudtask].[State][net_taskstate] — `Completed`, а значение свойства [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] класса CloudTask — `0`.
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
> 

### <a name="one-to-one"></a>Один к одному
Чтобы создать задачу, зависящую от успешного выполнения какой-либо другой задачи, укажите идентификатор одной задачи для статического метода [TaskDependencies][net_taskdependencies].[OnId][net_onid], когда заполните свойство [DependsOn][net_dependson] класса [CloudTask][net_cloudtask].

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
Чтобы создать задачу, зависящую от успешного выполнения нескольких задач, укажите набор идентификаторов задач для статического метода [TaskDependencies][net_taskdependencies].[OnIds][net_onids], когда заполните свойство [DependsOn][net_dependson] класса [CloudTask][net_cloudtask].

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
Чтобы создать задачу, зависящую от успешного выполнения группы задач, идентификаторы которых находятся в диапазоне, укажите первый и последний идентификаторы задач в диапазоне для статического метода [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange], когда заполните свойство [DependsOn][net_dependson] класса [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Если вы используете диапазоны идентификаторов задач для зависимостей, то идентификаторы задач в диапазоне *должны* быть строковыми представлениями целых чисел. Кроме того, каждая задача в диапазоне должна завершиться успешно, чтобы сделать возможным планирование выполнения зависимой задачи.
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

## <a name="code-sample"></a>Пример кода
[TaskDependencies][github_taskdependencies] — это один из [примеров кода пакетной службы Azure][github_samples] на портале GitHub. В этом решении Visual Studio 2015 показано, как включить зависимость задач для задания, создать задачи, зависящие от других задач, а также выполнять эти задачи в пуле вычислительных узлов.

## <a name="next-steps"></a>Дальнейшие действия
### <a name="application-deployment"></a>Развертывание приложения
Функция [пакетов приложения](batch-application-packages.md) в пакетной службе дает возможность очень легко развернуть приложения, которые задачи выполняют на вычислительных узлах, и управлять их версиями.

### <a name="installing-applications-and-staging-data"></a>Установка приложений и промежуточных данных
Ознакомьтесь с публикацией [Installing applications and staging data on Batch compute nodes][forum_post] (Установка приложений и промежуточное размещение данных на вычислительных узлах пакетной службы) на форуме по пакетной службе Azure. Из нее вы узнаете о различных способах подготовки узлов для выполнения задач. Эта публикация написана одним из участников команды разработчиков пакетной службы Azure, и она дает хорошее базовое понимание особенностей разных способов загрузки файлов (в том числе файлов приложений и входных данных для задач) на вычислительные узлы.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
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

