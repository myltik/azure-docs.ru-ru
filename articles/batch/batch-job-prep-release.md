<properties
	pageTitle="Подготовка и очистка задания в пакетной службе | Microsoft Azure"
	description="Используйте задачи подготовки на уровне задания для минимизации передачи данных на вычислительные узлы пакетной службы Azure и задачи снятия для очистки узла после завершения задания."
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
	ms.date="10/15/2015"
	ms.author="v-marsma"/>

# Выполнение задач подготовки и завершения заданий на вычислительных узлах пакетной службы Azure

Для заданий пакетной службы Azure часто требуется определенная настройка перед выполнением, а также определенные действия после завершения задач задания. Пакетная служба предоставляет механизмы для такой подготовки и обслуживания в виде необязательных задач *подготовки задания* и *снятия задания*.

Задача подготовки задания выполняется на всех вычислительных узлах, на которых запланировано выполнение задач, до выполнения задач задания. Когда задание завершается, на каждом узле в пуле, на котором была выполнена хотя бы одна задача, выполняется задача снятия задания. Для задач подготовки и снятия задания можно указать командную строку, запускаемую при вызове задачи. Задачи предоставляют такие возможности, как загрузка файла, выполнение с повышенными правами, пользовательские переменные среды, максимальная продолжительность выполнения, число повторных попыток и время хранения файла.

В следующих разделах вы узнаете, как использовать эти задачи с помощью класса [JobPreparationTask][net_job_prep] и класса [JobReleaseTask][net_job_release] в интерфейсе API [пакетной службы .NET][api_net].

> [AZURE.TIP]Задачи подготовки и снятия задания особенно полезны в средах с «общим пулом», то есть таких средах, в которых пул вычислительных узлов сохраняется между запусками заданий и может использоваться совместно различными заданиями.

## Когда используются задачи подготовки и снятия заданий

Задачи подготовки и снятия заданий удобны в целом ряде ситуаций. Вот некоторые из них.

- **Перенос общих данных задач**. Для задач пакетных заданий часто требуется общий набор входных данных. Например, в ежедневных расчетах по анализу рисков рыночные данные относятся к конкретному заданию, но являются общими для всех задач внутри этого задания. Эти рыночные данные, зачастую объемом в несколько гигабайт, нужно загрузить на каждый вычислительный узел только один раз, после чего их сможет использовать любая задача, которая выполняется на этом узле. *Задача подготовки задания* позволяет загрузить данные на все узлы перед выполнением других задач.
- **Удаление данных задания**. В среде общего пула, в котором узлы пула вычислительных узлов не удаляются между заданиями, удаление данных задания между запусками может требоваться для экономии дискового пространства на узлах или в соответствии с политиками безопасности организации. С помощью *задачи снятия задания* можно удалить данные, загруженные задачей подготовки задания или созданные во время выполнения задачи.
- **Хранение журнала**. Может возникнуть необходимость хранить копию файлов журнала, создаваемых задачами, или, например, файлы аварийных дампов, создаваемые приложениями, в которых возникает сбой. В таких случаях с помощью *задачи снятия задания* можно сжать и передать эти данные в учетную запись [хранения Azure][azure_storage].

## Задача подготовки задания

Перед выполнением задач задания на каждом вычислительном узле, на котором запланировано выполнение задач, выполняется задача подготовки задания. По умолчанию пакетная служба ожидает завершения задачи подготовки задания и только после этого запускает запланированные задачи на узле. Это ожидание можно отключить в настройках службы. Задача подготовки задания запускается на вычислительном узле снова при перезапуске узла, но это поведение также можно отключить.

Задача подготовки задания выполняется только на узлах, на которых запланировано выполнение задач. Благодаря этому на узлах, которым не назначены задачи, ненужная задача подготовки выполняться не будет. Это позволяет, например, снизить затраты на передачу данных. Такая ситуация возникает, когда число задач в задании меньше количества узлов в пуле. Она также действует, когда включено [параллельное выполнение](batch-parallel-node-tasks.md), в результате чего некоторые узлы бездействуют, если число задач оказывается меньше, чем общее число возможных параллельных задач.

> [AZURE.NOTE] [JobPreparationTask]Задача [net\_job\_prep\_cloudjob] отличается от [CloudPool.StartTask][pool_starttask] тем, что задача JobPreparationTask выполняется в начале каждого задания, тогда как StartTask выполняется только при первом присоединении вычислительного узла к пулу или при его перезапуске.

## Задача снятия задания

Когда задание завершается, на каждом узле в пуле, на котором была выполнена хотя бы одна задача, выполняется задача снятия задания. Чтобы пометить задание как завершенное, нужно направить запрос прекращения. После этого пакетная служба устанавливает состояние задания *Прекращение*, прекращает все активные или запущенные задачи, связанные с заданием, и запускает задачу снятия задания. Затем состояние задания меняется на *Завершено*.

> [AZURE.NOTE]Задача снятия задания также выполняется при удалении задания. Однако если задание было ранее прекращено, задача снятия задания при последующем удалении задания вторично не выполняется.

## Задачи подготовки и снятия задания в API .NET пакетной службы

Чтобы указать задачу подготовки задания, создайте и настройте объект [JobPreparationTask][net_job_prep] и назначьте его свойству [CloudJob.JobPreparationTask][net_job_prep_cloudjob]. Аналогичным образом инициализируйте объект [JobReleaseTask][net_job_release] и назначьте его свойству [CloudJob.JobReleaseTask][net_job_prep_cloudjob] задания, чтобы настроить задачу снятия задания.

В этом фрагменте кода `myBatchClient` представляет собой полностью инициализированный экземпляр [BatchClient][net_batch_client], а `myPool` является существующим пулом в учетной записи пакетной службы.

		// Create the CloudJob for CloudPool "myPool"
		CloudJob myJob = myBatchClient.JobOperations.CreateJob("JobPrepReleaseSampleJob",
															   new PoolInformation() { PoolId = "myPool" });

		// Specify the command lines for the job preparation and release tasks
		string jobPrepCmdLine = "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
		string jobReleaseCmdLine = "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

		// Assign the job preparation task to the job
		myJob.JobPreparationTask = new JobPreparationTask { CommandLine = jobPrepCmdLine };

		// Assign the job release task to the job
		myJob.JobReleaseTask = new JobPreparationTask { CommandLine = jobReleaseCmdLine };

		await myJob.CommitAsync();

Как упоминалось выше, задача снятия выполняется, когда задание прекращается или удаляется. Завершение работы с API .NET пакетной службы выполняется путем вызова [PoolOperations.TerminateJobAsync][net_job_terminate]. Удаление задания выполняется с помощью [PoolOperations.DeleteJobAsync][net_job_delete]. Обычно оба эти действия выполняются после завершения задач задания или по истечении времени ожидания, которое было определено.

		// Terminate the job to mark it as Completed; this will initiate the Job Release Task on any node
		// that executed job tasks. Note that the Job Release Task is also executed when a job is deleted,
		// thus you need not call Terminate if you typically delete your jobs upon task completion.
		await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");

## Дальнейшие действия

### Пример проекта на сайте GitHub

В примере проекта [JobPrepRelease][job_prep_release_sample] с сайта GitHub можно ознакомиться с действием задач подготовки и снятия задания. Это консольное приложение выполняет следующее.

1. Создает пул с двумя "небольшими" узлами.
2. Создает задание с задачами подготовки задания, снятия задания и стандартными задачами.
3. Запускает задачу подготовки задания, которая сначала записывает идентификатор узла в текстовый файл в общем каталоге узла.
4. Запускает на каждом узле задачу, которая записывает свой идентификатор в тот же текстовый файл.
5. После завершения всех задач (или по истечении времени ожидания) выводит содержимое текстового файла каждого узла на консоль.
6. После завершения задания запускает задачу снятия задания для удаления файла с узла.
6. Выводит коды завершения задач подготовки и снятия задания для каждого узла, на котором они выполнялись.
7. Приостанавливает выполнение, чтобы подтвердить удаление задания или пула.

Выходные данные в этом примере приложения будут аналогичны следующему примеру:

```
Attempting to create pool: JobPrepReleaseSamplePool
The pool already existed when we tried to create it
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-3105992504_1-20151015t150030z:
-------------------------------------------
tvm-3105992504_1-20151015t150030z tasks:
  task001
  task002
  task006
  task007

Contents of shared\job_prep_and_release.txt on tvm-3105992504_2-20151015t150030z:
-------------------------------------------
tvm-3105992504_2-20151015t150030z tasks:
  task003
  task005
  task004
  task008

Waiting for job JobPrepReleaseSampleJob to reach state Completed
....

tvm-3105992504_1-20151015t150030z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-3105992504_2-20151015t150030z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
no

Sample complete, hit ENTER to exit...
```

### Просмотр задач подготовки и завершения задания с помощью обозревателя пакетной службы

[Обозреватель пакетной службы][batch_explorer_article], который также имеется в [репозитории образцов кода][batch_explorer_project] пакетной службы на сайте GitHub, очень удобен при разработке решений с использованием пакетной службы Azure. При выполнении примера приложения выше, например с помощью обозревателя пакетной службы, можно просмотреть свойства задания и его задачи или даже загрузить общий текстовый файл, измененный задачами задания.

На следующем снимке экрана выделены свойства задач подготовки и снятия задания, показанные на вкладке **Сведения о задании** при выборе задания *JobPrepReleaseSampleJob* на вкладке **Задания**.

![Обозреватель пакетной службы][1]

*Снимок экрана обозревателя пакетной службы с задачами подготовки и снятия задания*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_article]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net\_job\_prep\_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

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

[1]: ./media/batch-job-prep-release/batchexplorer-01.png
[2]: ./media/batch-job-prep-release/batchexplorer-02.png

<!---HONumber=AcomDC_0114_2016-->