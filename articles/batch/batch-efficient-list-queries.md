<properties
	pageTitle="Эффективные запросы списков в пакетной службе Azure | Microsoft Azure"
	description="Повысьте эффективность, уменьшив объем возвращаемых данных при запросе сущностей в пакетной службе Azure: пулов, заданий, задач и вычислительных узлов."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="10/12/2015"
	ms.author="v-marsma"/>

# Эффективные запросы к пакетной службе Azure

Из этой статьи вы узнаете, как уменьшить количество элементов и объем данных, возвращаемых при использовании API [.NET пакетной службы][api_net] для отправления запросов к пакетной службе на получение списков заданий, задач, вычислительных узлов и т. д.

Пакетная служба Azure — это серьезный вычислительный ресурс. В производственной среде такие сущности, как задания, задачи и вычислительные узлы, могут исчисляться тысячами. Поэтому при получении сведений об этих элементах может создаваться большой объем данных, которые нужно передавать по каждому запросу. Ограничение количества элементов и типов сведений, возвращаемых для каждого запроса, повысит скорость их обработки и, как следствие, производительность вашего приложения.

Отображение заданий, задач и вычислительных узлов является примером операций, которые часто выполняют практически все приложения, использующие пакетную службу Azure. Распространенным вариантом использования является мониторинг. Например, чтобы определить емкость и состояние пула, требуется отправить запросы на все вычислительные узлы в нем. Другим примером является опрос задач в задании для выявления задач, которые все еще находятся в очереди.

Этот фрагмент кода API [пакетной службы для .NET][api_net] получает все задачи, связанные с заданием, а также полный набор их свойств:

```
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks = batchClient.JobOperations.ListTasks("job-001");
```

Более эффективный запрос списка можно отправить, передав [ODATADetailLevel][odata] в метод [JobOperations.ListTasks][net_list_tasks]. Этот фрагмент кода возвращает идентификатор, командную строку и информационные свойства вычислительного узла только для завершенных задач:

```
// Configure an ODATADetailLevel specifying a subset of tasks and their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks = batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Если, как в приведенном выше примере сценария, задание состоит из тысяч задач, результаты для второго запроса обычно возвращаются быстрее, чем для первого. Дополнительные сведения об использовании ODATADetailLevel при выведении элементов с помощью API пакетной службы для .NET приведены ниже.

> [AZURE.IMPORTANT]Мы настоятельно рекомендуем **всегда** указывать ODATADetailLevel в вызовах API .NET для получения списков. Это обеспечит максимальную эффективность и производительность приложения. Задание уровня детализации помогает снизить время ответа пакетной службы, повысить эффективность использования сети и минимизировать использование памяти клиентскими приложениями.

## Средства для эффективного выполнения запроса

API [.NET][api_net] и [REST пакетной службы][api_rest] позволяют сократить число возвращаемых в списке элементов и объем сведений, возвращаемых для каждого элемента. Для этого в запросе необходимо указать строки *filter*, *select* и *expand*.

- **filter**: *строка фильтрации* является выражением, которое уменьшает число возвращаемых элементов. Например, можно вывести список только выполняющихся задач в задании или только вычислительных узлов, которые готовы к выполнению задач.
  - Строка фильтрации состоит из одного или нескольких выражений, где каждое выражение состоит из имени свойства, оператора и значения. Для каждого типа вызовов API можно указать только определенные свойства, равно как и операторы, поддерживаемые каждым свойством.
  - Несколько выражений можно объединить с помощью логических операторов `and` и `or`.
  - В приведенном ниже примере строка фильтрации выводит только запущенные задачи отрисовки: `startswith(id, 'renderTask') and (state eq 'running')`
- **select**: с помощью *строки выборки* можно ограничить значения свойств, которые требуется вернуть для каждого элемента. В строке выборки можно указать список свойств для элемента. В результатах запроса списка будут возвращаться только значения указанных свойств для каждого элемента в списке результатов запроса.
  - Строка выборки содержит список имен свойств, разделенных запятыми. Можно указать любые свойства элемента, возвращаемого операцией списка.
  - В приведенном ниже примере строки выборки указано только три свойства, которые необходимо вернуть для каждой задачи: `id, state, stateTransitionTime`
- **expand**: *строка развертывания* сокращает количество вызовов API, необходимое для получения определенных сведений. Более подробные сведения для каждого элемента списка можно получить с помощью единственного вызова операции API списка (вместо получения списка и последующего вызова для каждого элемента в списке).
  - Как и строка выборки, строка развертывания определяет, какие данные нужно включить в результаты запроса списка.
  - Строка развертывания поддерживается только для заданий, расписаний заданий, задач и пулов. В настоящее время доступна поддержка только статистических данных.
  - Далее приведен пример строки развертывания, которая указывает, что для каждого элемента нужно вернуть статистические данные: `stats`
  - Если необходимы все свойства, и строка выборки не указана, для получения статистических данных *следует* использовать строку развертывания. Если для получения подмножества свойств используется строка выборки, в ней можно указать `stats`, а строку развертывания вовсе не указывать.

> [AZURE.NOTE]При создании какого-либо из трех типов строк запроса (filter, select, expand) необходимо убедиться, что имена свойств и регистр совпадают с аналогичными элементами REST API. Например, при работе с [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) в .NET необходимо указывать **state**, а не **State**, несмотря на то что в .NET используется свойство [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Сопоставление свойств API для .NET и REST см. в таблице ниже.

### Спецификации строк фильтрации, выборки и развертывания

- Свойства, заданные в строках фильтрации, выборки и развертывания, совпадают с отображаемыми в API [REST пакетной службы][api_rest]. Это также касается библиотеки [.NET пакетной службы][api_net].
- Имена всех свойств чувствительны к регистру, но для значений свойств регистр значения не имеет.
- Строки даты и времени могут иметь один из двух форматов и должны начинаться с `DateTime`:
  - пример формата W3CDTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`;
  - пример формата RFC1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`;
- Строки с логическими выражениями должны иметь значение `true` или `false`.
- Если задано недопустимое свойство или оператор, отобразится ошибка `400 (Bad Request)`.

## Эффективное выполнение запросов в пакетной службе для .NET

В API [пакетной службы .NET][api_net] используется [ODATADetailLevel][odata] для указания строк фильтрации, выборки и развертывания для операций списка. Объект ODataDetailLevel имеет три общих свойства строки, которые можно указать в конструкторе или напрямую. Затем этот объект передается в качестве параметра в различные операции списка, например [ListPools][net_list_pools], [ListJobs][net_list_jobs] и [ListTasks][net_list_tasks].

- [ODATADetailLevel.FilterClause][odata_filter] — ограничение количества возвращаемых элементов.
- [ODATADetailLevel.SelectClause][odata_select] — задание подмножества значений свойствам, возвращаемым для каждого элемента.
- [ODATADetailLevel.ExpandClause][odata_expand] — получение данных элементов с помощью единственного вызова операции API списка (вместо вызовов для каждого элемента в списке).

Ниже приведен фрагмент кода, который с помощью API .NET пакетной службы отправляет эффективный запрос пакетной службе для получения статистики по определенному набору пулов. В этом сценарии у пользователя пакетной службы есть как тестовый, так и рабочий пулы. Идентификаторы тестовых пулов начинаются с префикса test, а рабочих — с префикса prod. В приведенном фрагменте *myBatchClient* является правильно инициализированным экземпляром [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient).

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// We want to pull only the "test" pools, so we limit the number of items returned by using a
	// FilterClause and specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();

> [AZURE.TIP]Экземпляр [ODATADetailLevel][odata] с предложениями Select и Expand также можно передать в соответствующие методы Get, например [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), для ограничения объема возвращаемых данных.

## Сопоставление API REST и .NET пакетной службы

Имена и регистр свойств в строках фильтрации, выборки и развертывания *должны* полностью соответствовать аналогичным элементам в REST API. В таблицах ниже приведено сопоставление элементов API в .NET и REST.

### Сопоставления для строк фильтрации

- **МЕТОДЫ СПИСКА .NET**. Каждый метод .NET API в этом столбце принимает объект [ODATADetailLevel][odata] в качестве параметра.
- **ЗАПРОСЫ СПИСКА REST**. Каждая страница API REST в этом столбце содержит таблицу со свойствами и операциями, разрешенными в строках *фильтрации*. Эти имена свойств и операции будут использоваться в дальнейшем при создании строки [ODATADetailLevel.FilterClause][odata_filter].

| Методы списка .NET | Запросы списка REST |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Получение списка сертификатов в учетной записи][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Получение списка файлов, связанных с задачей][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [Получение состояния подготовки задания и задачи деблокирования задания][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Получение списка заданий в учетной записи][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Получение списка файлов в узле][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Получение списка задач, связанных с заданием][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Получение расписаний для заданий в учетной записи][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Получение списка заданий, связанных с расписанием задания][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Получение списка вычислительных узлов в пуле][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [Получение списка пулов учетной записи][rest_list_pools]

### Сопоставления для строк выборки

- **ТИПЫ .NET ДЛЯ ПАКЕТНОЙ СЛУЖБЫ** — типы API .NET для пакетной службы.
- **СУЩНОСТИ API REST** —каждая страница в этом столбце содержит одну или несколько таблиц с именами свойств API REST для соответствующих типов. Эти имена свойств используются при создании строк *выборки*. Они также будут использоваться в дальнейшем при создании строки [ODATADetailLevel.SelectClause][odata_select].

| Типы .NET пакетной службы | Сущности API REST |
|---|---|
| [Сертификат][net_cert] | [Получение информации о сертификате][rest_get_cert] |
| [CloudJob][net_job] | [Получение информации о задании][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Получение информации о расписании задания][rest_get_schedule] |
| [ComputeNode][net_node] | [Получение информации об узле][rest_get_node] |
| [CloudPool][net_pool] | [Получение информации о пуле][rest_get_pool] |
| [CloudTask][net_task] | [Получение информации о задаче][rest_get_task] |

### Пример: создание строки фильтрации

Создание строки фильтрации для [ODATADetailLevel.FilterClause][odata_filter] предусматривает использование таблицы, приведенной выше в разделе *Сопоставления для строк фильтрации*. В таблице найдите страницу документации по REST API, соответствующую операции списка, которую необходимо выполнить. В первой многострочной таблице на этой странице содержатся фильтруемые свойства и соответствующие поддерживаемые ими операторы. К примеру, вы хотите получить все задачи с ненулевым кодом возврата. В строке [Вывод задач, связанных с заданием][rest_list_tasks] указана строка с соответствующим параметром и допустимые операторы.

| Свойство | Разрешенные операции | Тип |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

Соответственно, строка фильтрации для отображения списка всех задач с ненулевым кодом возврата будет выглядеть так:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

### Пример: создание строки выборки

Чтобы создать [ODATADetailLevel.SelectClause][odata_select], обратитесь к приведенной выше таблице в разделе *Сопоставления для строк выборки*. В таблице перейдите на страницу REST API, которая соответствует типу выводимой сущности. В первой многострочной таблице на этой странице содержатся выбираемые свойства и соответствующие поддерживаемые ими операторы. Например, если вы хотите получить только идентификатор и командную строку для каждой задачи в списке, эти строки вы найдете в соответствующей таблице [Получение информации о задаче][rest_get_task].

| Свойство | Тип | Примечания |
| :--- | :--- | :--- |
| `id` | `String` | `The id of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

Строка выборки для отображения только идентификатора и командной строки для каждой задачи в списке будет выглядеть так:

`id, commandLine`

## Дальнейшие действия

Ознакомьтесь с примером проекта [EfficientListQueries][efficient_query_sample] на GitHub, чтобы узнать, как эффективные запросы на получение списков могут повлиять на производительность в приложении. Это консольное приложение C# создает и добавляет большое количество задач в задание, а затем запрашивает пакетную службу, используя различные спецификации [ODATADetailLevel][odata] и отображая выходные данные указанного ниже вида.

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

Как показано в сведениях о затраченном времени, ограничение свойств и количества возвращаемых элементов может значительно снизить время отклика на запрос. Этот и другие примеры проектов находятся в репозитории [azure-batch-samples][github_samples] на веб-сайте GitHub.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
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

<!---HONumber=Oct15_HO3-->