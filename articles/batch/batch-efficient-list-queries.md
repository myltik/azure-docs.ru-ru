---
title: Проектирование эффективных запросов списков с помощью пакетной службы Azure | Документация Майкрософт
description: 'Сведения о повышении производительности за счет фильтрации запросов, а именно при запросе сведений о ресурсах пакетной службы: пулах, заданиях, задачах и вычислительных узлах.'
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 330350d6ac6838ea5b09763fe1f73fab1934710c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Эффективное создание запросов на вывод списка ресурсов пакетной службы

В этой статье вы узнаете, как повысить производительность приложения пакетной службы Azure, уменьшив объем данных, возвращаемых службой при запросе заданий, задач и вычислительных узлов с помощью библиотеки [.NET пакетной службы][api_net].

Почти все приложения, использующие пакетную службу, должны выполнять определенный тип операций мониторинга или других операций, отправляющих запросы в пакетную службу (часто на регулярной основе). Например, чтобы определить, находятся ли в очереди задачи задания, необходимо получить данные о каждой задаче в задании. Чтобы определить состояние узлов в пуле, нужно получить данные о каждом из этих узлов. В этой статье объясняется, как выполнять такие запросы наиболее эффективно.

> [!NOTE]
> Пакетная служба поддерживает API для распространенных сценариев подсчета задач в задании. Вместо использования запроса списка для этого можно вызвать операцию [Получить количество задач][rest_get_task_counts]. Эта операция указывает количество задач, ожидающих выполнения, запущенных, завершенных, успешно или неудачно выполненных задач. Это операция эффективнее запроса списка. Дополнительные сведения см. в статье [Подсчет задач по состоянию для мониторинга хода выполнения задания (предварительная версия)](batch-get-task-counts.md). 
>
> Операция получения количества задач недоступна в версиях пакетной службы до 2017-06-01.5.1. Если вы используете более старую версию службы, используйте запрос списка для подсчета задач в задании.
>
> 

## <a name="meet-the-detaillevel"></a>Знакомство с DetailLevel
В рабочем приложении пакетной службы такие сущности, как задания, задачи и вычислительные узлы, могут исчисляться тысячами. При запросе сведений об этих ресурсах потенциально большой объем данных нужно передать из пакетной службы в приложение по каждому запросу. Ограничение количества элементов и типов сведений, возвращаемых запросом, повышает скорость их обработки и, как следствие, производительность вашего приложения.

Этот фрагмент кода API [.NET пакетной службы][api_net] перечисляет *все* задачи, связанные с заданием, а также выводит *полный* набор их свойств.

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Однако можно выполнить более эффективный запрос списка, применив к запросу уровень детализации. Для этого необходимо передать объект [ODATADetailLevel][odata] методу [JobOperations.ListTasks][net_list_tasks]. Этот фрагмент кода возвращает только идентификатор, командную строку и информационные свойства вычислительного узла только для завершенных задач:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Если, как в примере сценария, задание состоит из тысяч задач, результаты для второго запроса обычно возвращаются быстрее, чем для первого. [Ниже](#efficient-querying-in-batch-net)приведены дополнительные сведения об использовании ODATADetailLevel при получении списка элементов с помощью API пакетной службы для .NET.

> [!IMPORTANT]
> Мы настоятельно рекомендуем *всегда* передавать объект ODATADetailLevel в вызовы API .NET для получения списков. Это обеспечит максимальную эффективность и производительность приложения. Задание уровня детализации помогает снизить время ответа пакетной службы, повысить эффективность использования сети и минимизировать использование памяти клиентскими приложениями.
> 
> 

## <a name="filter-select-and-expand"></a>Фильтрация, выборка и развертывание
Интерфейсы API [.NET пакетной службы][api_net] и [REST API пакетной службы][api_rest] позволяют сократить количество возвращаемых в списке элементов и объем сведений, возвращаемых для каждого элемента. Для этого в запросе необходимо указать строки **фильтрации**, **выборки** и **развертывания**.

### <a name="filter"></a>Фильтр
Строка фильтра (filter) — это выражение, которое уменьшает число возвращаемых элементов. Например, можно вывести список только выполняющихся задач в задании или только вычислительных узлов, которые готовы к выполнению задач.

* Строка фильтра состоит из одного или нескольких выражений, каждое из которых состоит из имени свойства, оператора и значения. Для каждого опрашиваемого типа сущности можно указать только определенные свойства, а также только определенные операторы, поддерживаемые каждым свойством.
* Несколько выражений можно объединить с помощью логических операторов `and` и `or`.
* В этом примере строка фильтра выводит только запущенные задачи отрисовки: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Выберите пункт
Строка выборки (select) позволяет ограничить значения свойств, которые требуется вернуть для каждого элемента. Вы указываете список имен свойств, после чего в результатах запроса возвращаются только значения указанных свойств для соответствующих элементов.

* Строка выборки содержит список имен свойств, разделенных запятыми. Для запрашиваемого типа сущности можно указать любые свойства.
* В этом примере строка выборки указывает, что для каждой задачи необходимо вернуть значения только трех свойств: `id, state, stateTransitionTime`.

### <a name="expand"></a>Разверните
Строка развертывания сокращает количество вызовов API, необходимое для получения определенных сведений. При использовании строки развертывания дополнительные сведения о каждом элементе можно получить с помощью одного вызова API. Вместо того, чтобы получать список записей и запрашивать сведения о каждом включенном в него элементе, вы применяете строку развертывания и получаете те же самые сведения за один вызов API. Чем меньше вызовов API, тем выше производительность.

* Как и строка выборки, строка развертывания определяет, какие данные нужно включить в результаты запроса списка.
* Строки развертывания поддерживаются только при использовании для получения списка заданий, расписаний заданий, задач и пулов. В настоящее время они поддерживают только статистические данные.
* Если необходимы все свойства, и строка выборки не указана, для получения статистических данных *следует* использовать строку развертывания. Если для получения подмножества свойств используется строка выборки, то в ней можно указать `stats` и не указывать строку развертывания.
* Далее приведен пример строки развертывания, которая предписывает, что для каждого элемента в списке необходимо вернуть статистические данные: `stats`.

> [!NOTE]
> При создании какого-либо из трех типов строк запроса (filter, select или expand) необходимо убедиться, что имена свойств и регистр совпадают с аналогичными элементами REST API. Например, при работе с классом [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) в .NET необходимо указывать свойство **state**, а не **State**, несмотря на то что в .NET используется свойство [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Сопоставление свойств API для .NET и REST см. в таблице ниже.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Правила строк фильтрации, выборки и развертывания
* Имена свойств в строках фильтрации, выборки и развертывания должны отобразиться в [REST API пакетной службы][api_rest] даже при использовании [.NET пакетной службы][api_net] или других пакетов SDK для пакетной службы.
* Имена всех свойств чувствительны к регистру, но для значений свойств регистр значения не имеет.
* Строки даты и времени могут иметь один из двух форматов и должны начинаться с `DateTime`.
  
  * Пример формата W3C-DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Пример формата RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Строки с логическими выражениями должны иметь значение `true` или `false`.
* Если задано недопустимое свойство или оператор, отобразится ошибка `400 (Bad Request)` .

## <a name="efficient-querying-in-batch-net"></a>Эффективное выполнение запросов в пакетной службе для .NET
В API [.NET пакетной службы][api_net] для указания строк фильтрации, выборки и развертывания операций списка используется класс [ODATADetailLevel][odata]. Объект ODataDetailLevel имеет три открытых строковых свойства, которые можно указать в конструкторе или установить непосредственно в объекте. Затем объект ODataDetailLevel передается в качестве параметра различных операций получения списка, таких как [ListPools][net_list_pools], [ListJobs][net_list_jobs] и [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[FilterClause][odata_filter]: ограничивает количество возвращаемых элементов.
* [ODATADetailLevel][odata].[SelectClause][odata_select]: определяет, значения каких свойств необходимо вернуть для каждого элемента.
* [ODATADetailLevel][odata].[ExpandClause][odata_expand]: извлекает данные по всем элементам за один вызов API вместо того, чтобы выполнять отдельные вызовы для каждого элемента.

Ниже приведен фрагмент кода, который с помощью API пакетной службы для .NET отправляет эффективный запрос пакетной службе для получения статистики по определенному набору пулов. В этом сценарии у пользователя пакетной службы есть как тестовые, так и рабочие пулы. Идентификаторы тестовых пулов имеют префикс "test", а рабочих пулов — "prod". В приведенном фрагменте *myBatchClient* является правильно инициализированным экземпляром класса [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) .

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Экземпляр [ODATADetailLevel][odata] с предложениями Select и Expand также можно передать в соответствующие методы Get для ограничения объема возвращаемых данных, например в метод [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx).
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Сопоставление API REST и .NET пакетной службы
Имена и регистр свойств в строках фильтрации, выборки и развертывания *должны* полностью соответствовать аналогичным элементам в REST API. В таблицах ниже приведено сопоставление элементов API в .NET и REST.

### <a name="mappings-for-filter-strings"></a>Сопоставления для строк фильтрации
* **Методы списка .NET** — каждый из методов API для .NET в этом столбце принимает объект [ODATADetailLevel][odata] в качестве параметра.
* **Запросы списка REST**— каждая страница REST API, связанная с этим столбцом, содержит таблицу со свойствами и операциями, разрешенными в строках *фильтрации* . Эти имена свойств и операции будут использоваться при создании строки [ODATADetailLevel.FilterClause][odata_filter].

| Методы списка .NET | Запросы списка REST |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Получение списка сертификатов в учетной записи][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Получение списка файлов, связанных с задачей][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Получение состояния подготовки задания и задачи деблокирования задания][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Получение списка заданий в учетной записи][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Получение списка файлов в узле][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Получение списка задач, связанных с заданием][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Получение расписаний для заданий в учетной записи][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Получение списка заданий, связанных с расписанием задания][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Получение списка вычислительных узлов в пуле][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Получение списка пулов учетной записи][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Сопоставления для строк выборки
* **Типы пакетной службы для .NET**— типы API пакетной службы для .NET.
* **Сущности REST API**— каждая страница в этом столбце содержит одну или несколько таблиц с именами свойств API REST для соответствующих типов. Эти имена свойств используются при создании строк *выборки* . Они также будут использоваться при создании строки [ODATADetailLevel.SelectClause][odata_select].

| Типы пакетной службы для .NET | Сущности REST API |
| --- | --- |
| [Certificate][net_cert] |[Получение информации о сертификате][rest_get_cert] |
| [CloudJob][net_job] |[Получение информации о задании][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Получение информации о расписании задания][rest_get_schedule] |
| [ComputeNode][net_node] |[Получение информации об узле][rest_get_node] |
| [CloudPool][net_pool] |[Получение информации о пуле][rest_get_pool] |
| [CloudTask][net_task] |[Получение информации о задаче][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Пример. Создание строки фильтрации
При создании строки фильтрации для [ODATADetailLevel.FilterClause][odata_filter] обратитесь к таблице, приведенной выше в разделе "Сопоставления для строк фильтрации", чтобы найти страницу документации по REST API, соответствующей операции списка, которую необходимо выполнить. В первой многострочной таблице на этой странице содержатся фильтруемые свойства и соответствующие поддерживаемые ими операторы. К примеру, вы хотите получить все задачи с ненулевым кодом возврата. В строке [Вывод задач, связанных с заданием][rest_list_tasks] указана строка с соответствующим параметром и допустимые операторы.

| Свойство | Разрешенные операции | type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Соответственно, строка фильтрации для отображения списка всех задач с ненулевым кодом возврата будет выглядеть так:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Пример. Создание строки выборки
Для создания [ODATADetailLevel.SelectClause][odata_select] обратитесь к приведенной выше таблице в разделе "Сопоставления для строк выборки" и перейдите на страницу REST API, которая соответствует типу выводимой сущности. В первой многострочной таблице на этой странице содержатся выбираемые свойства и соответствующие поддерживаемые ими операторы. Например, если вы хотите получить только идентификатор и командную строку для каждой задачи в списке, эти строки вы найдете в соответствующей таблице [Получение информации о задаче][rest_get_task].

| Свойство | type | Заметки |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Строка выборки для отображения только идентификатора и командной строки для каждой задачи в списке будет выглядеть так:

`id, commandLine`

## <a name="code-samples"></a>Примеры кода
### <a name="efficient-list-queries-code-sample"></a>Пример кода с эффективными запросами списков
Ознакомьтесь с примером проекта [EfficientListQueries][efficient_query_sample] на GitHub, чтобы узнать, как эффективные запросы на получение списков могут повлиять на производительность в приложении. Это консольное приложение C# создает и добавляет большое количество задач в задание. Затем оно выполняет несколько вызовов метода [JobOperations.ListTasks][net_list_tasks] и передает объекты [ODATADetailLevel][odata], настроенные с различными значениями свойств для изменения объема возвращаемых данных. Вывод приложения должен быть похож на приведенный ниже:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Как показано в сведениях о затраченном времени, путем ограничения свойств и количества возвращаемых элементов можно значительно уменьшить время отклика на запрос. Этот и другие примеры проектов находятся в репозитории [azure-batch-samples][github_samples] на веб-сайте GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Библиотека BatchMetrics и пример кода
Помимо примера кода EfficientListQueries, приведенного выше, можно использовать проект [BatchMetrics][batch_metrics], содержащийся в репозитории [azure-batch-samples][github_samples] на GitHub. Пример проекта BatchMetrics демонстрирует, как эффективно отслеживать ход выполнения задания пакетной службы Azure с помощью API пакетной службы.

Пример [BatchMetrics][batch_metrics] включает в себя проект библиотеки классов .NET, который можно внедрить в собственные проекты, а также простую программу командной строки для проверки и демонстрации использования библиотеки.

В примере приложения проекта демонстрируются следующие операции:

1. Выбор определенных атрибутов для скачивания только необходимых свойств.
2. Фильтрация времени смены состояния для скачивания изменений, отправленных с момента последнего запроса.

Например, в библиотеке BatchMetrics используется приведенный ниже метод. Он возвращает объект ODATADetailLevel, указывающий, что для сущностей, к которым обращен запрос, нужно получить только свойства `id` и `state`. Он также указывает, что должны быть возвращены только те сущности, состояние которых изменилось с момента, указанного в значении параметра `DateTime` .

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Дополнительная информация
### <a name="parallel-node-tasks"></a>Параллельные задачи узла
Еще одна статья, посвященная производительности приложения пакетной службы — [Повышение эффективности вычислительных ресурсов в пакетной службе Azure благодаря параллельному выполнению задач на узлах](batch-parallel-node-tasks.md). Для эффективной обработки некоторых типов рабочих нагрузок можно применять параллельное выполнение задач на меньшем количестве более крупных вычислительных узлов. Дополнительные сведения о таком сценарии см. [здесь](batch-parallel-node-tasks.md#example-scenario).

### <a name="batch-forum"></a>Форум по Пакетной службе
На [форуме по пакетной службе Azure][forum] на сайте MSDN можно обсудить пакетную службу и задать вопросы о ней. Изучайте полезные «прикрепленные» сообщения и задавайте вопросы, возникающие во время сборки пакетных решений.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job