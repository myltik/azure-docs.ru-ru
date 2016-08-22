<properties
	pageTitle="Сохранение выходных данных заданий и задач в пакетной службе Azure | Microsoft Azure"
	description="Узнайте, как использование службу хранилища Azure в качестве долговременного хранилища выходных данных задач и заданий пакетной службы. Кроме того, узнайте, как обеспечить отображение сохраненных выходных данных на портале Azure."
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
	ms.date="08/06/2016"
	ms.author="marsma" />

# Сохранение выходных данных заданий и задач пакетной службы Azure

Обычно при запуске в пакетной службе задачи создают выходные данные, которые должны быть хранены и затем использованы другими задачами в задании и (или) клиентским приложением, которое выполнило задание. Выходными данными могут быть файлы, созданные при обработке входных данных, или журналы, связанные с выполнением задачи. В этой статье описывается библиотека классов .NET, которая использует методику на основе соглашений для сохранения выходных данных таких задач в хранилище BLOB-объектов Azure, делая их доступными даже после удаления пулов, заданий и вычислительных узлов.

С помощью методики из этой статьи можно также просматривать выходные данные задачи в разделах **Saved output files** (Сохраненные выходные файлы) и **Saved logs** (Сохраненные журналы) на [портале Azure][portal].

![Селекторы сохраненных выходных файлов и журналов на портале][1]

>[AZURE.NOTE] Описанный здесь метод хранения и извлечения файлов обеспечивает функции, аналогичные управлению выходными данными задач, которое было реализовано в **пакетных приложениях** (уже устаревший компонент).

## Рекомендации по выходным данным задач

При разработке решения пакетной службы следует учесть несколько факторов, связанных с выходными данными заданий и задач.

* **Время существования вычислительного узла**. Нередко вычислительные узлы являются временными, особенно в пулах с поддержкой автомасштабирования. Выходные данные задач, которые выполняются на узле, доступны, только пока узел существует и только в течение периода хранения файлов, заданного для задачи. Поэтому, чтобы обеспечить сохранение выходных данных задачи, необходимо передать выходные файлы в долговременное хранилище, например, в службу хранилища Azure.

* **Хранение выходных данных**. Чтобы сохранить выходные данные задачи в долговременном хранилище, в коде задачи можно использовать [пакет SDK службы хранилища Azure](../storage/storage-dotnet-how-to-use-blobs.md) для передачи ее выходных данных в контейнер хранилища BLOB-объектов. В случае реализации соглашения об именовании контейнеров и файлов клиентское приложение или другие задачи в задании смогут найти и скачать эти выходные данные, используя это соглашение.

* **Извлечение выходных данных**. Выходные данные задачи можно получить непосредственно от вычислительных узлов в пуле или из службы хранилища Azure, если задача сохраняет свои выходные данные. Чтобы получить выходные данные задачи непосредственно от вычислительных узлов, необходимо знать имя файла и расположение его выходных данных на узле. Если выходные данные сохраняются в службе хранилища Azure, то подчиненной задаче или клиентскому приложению требуется полный путь к файлу в службе хранилища Azure, чтобы скачать его с помощью пакета SDK службы хранилища Azure.

* **Просмотр выходных данных.** Если перейти к задаче пакетной службы на портале Azure и щелкнуть **Files on node** (Файлы на узле), отобразятся все файлы, связанные с задачей, а не только интересующие вас выходные файлы. Опять же, файлы на вычислительных узлах доступны, только пока узел существует и только в течение периода хранения файлов, заданного для задачи. Чтобы просмотреть выходные данные задачи, которые были сохранены в службе хранилища Azure с помощью портала или такого приложения, как [Azure Storage Explorer][storage_explorer], необходимо знать их расположение и непосредственно перейти к нужному файлу.

## Справка по сохраненным выходным данным

Чтобы упростить сохранение выходных данных заданий и задач, команда разработчиков пакетной службы определила и реализовала набор соглашений об именовании и библиотеку классов .NET, [Azure Batch File Conventions][nuget_package], которую можно использовать в приложениях пакетной службы. Кроме того, на портале Azure учитываются эти соглашения об именовании, поэтому можно легко найти файлы, которые были сохранены с помощью библиотеки.

## Использование библиотеки соглашений об именовании файлов

[Azure Batch File Conventions][nuget_package] — это библиотека классов .NET, которая позволяет приложениям .NET пакетной службы легко сохранять выходные данные задач в службе хранилища Azure и извлекать их из нее. Она предназначена для использования в коде клиента и задачи: в коде задачи — для сохранения файлов, а в коде клиента — для получения списка файлов и их извлечения. Задачи можно также использовать для получения выходных данных вышестоящих задач, например, как в сценарии [зависимостей задач](batch-task-dependencies.md).

Библиотека соглашений гарантирует, что контейнеры хранилища и выходные файлы задач именуются в соответствии с соглашением и передаются в нужное расположение при сохранении в службе хранилища Azure. При извлечении выходных данных их можно легко найти для заданного задания или задачи, получив список или извлекая выходные данные по идентификатору и назначению вместо того, чтобы указывать имена файлов и их пути в службе хранилища.

Например, можно использовать библиотеку, чтобы "вывести список всех промежуточных файлов для задачи 7" или "получить предварительные эскизы для задания *mymovie*" без необходимости знать имена или расположения файлов в своей учетной записи хранения.

### Получение библиотеки

Вы можете получить библиотеку, которая содержит новые классы и расширяет классы [CloudJob][net_cloudjob] и [CloudTask][net_cloudtask] за счет новых методов, на сайте [NuGet][nuget_package]. Ее можно добавить в проект Visual Studio с помощью [диспетчера пакетов библиотек NuGet][nuget_manager].

>[AZURE.TIP] [Исходный код][github_file_conventions] библиотеки соглашений об именовании файлов для пакетной службы Azure доступен на сайте GitHub в репозитории пакетов SDK Microsoft Azure для .NET.

## Требование: связанная учетная запись хранения

Для сохранения выходных данных в долговременном хранилище с помощью библиотеки соглашений об именовании файлов и просмотра этих выходных данных на портале Azure необходимо [связать учетную запись хранения Azure](batch-application-packages.md#link-a-storage-account) с учетной записью пакетной службы. Если вы этого еще не сделали, свяжите учетную запись хранения с учетной записью пакетной службы с помощью портала Azure.

Щелкните колонку **Учетная запись пакетной службы** > **Параметры** > **Учетная запись хранения** > **Учетная запись хранения** (нет) и выберите учетную запись хранения в своей подписке.

Более подробное пошаговое руководство по связыванию учетной записи хранения см. в разделе [Развертывание приложения с помощью пакетов приложений пакетной службы Azure](batch-application-packages.md).

## Сохранение выходных данных

Существует два основных действия, необходимых для сохранения выходных данных заданий и задач с помощью библиотеки соглашений об именовании файлов: создание контейнера хранилища и сохранение выходных данных в него.

>[AZURE.WARNING] Так как все выходные данные заданий и задач хранятся в одном контейнере, то на случай, если большое число задач попытается одновременно сохранить файлы, следует задать [пределы для регулирования хранилища](../storage/storage-performance-checklist.md#blobs).

### Создание контейнера хранилища

Прежде чем ваши задачи начнут сохранение выходных данных в хранилище, необходимо создать контейнер хранилища BLOB-объектов, в который они будут передавать свои выходные данные. Это можно сделать с помощью вызова [CloudJob][net_cloudjob].[PrepareOutputStorageAsync][net_prepareoutputasync]. Этот метод расширения принимает объект [CloudStorageAccount][net_cloudstorageaccount] в качестве параметра и создает контейнер с именем, позволяющим обнаружить его содержимое с помощью портала Azure и методов получения, описанных далее в этой статье.

Обычно этот код помещается в клиентское приложение — то есть приложение, которое создает пулы, задания и задачи.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
	"myJob",
	new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
	new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### Хранение выходных данных задач

После подготовки контейнера в хранилище BLOB-объектов задачи могут сохранять выходные данные в этот контейнер с помощью класса [TaskOutputStorage][net_taskoutputstorage], находящегося в библиотеке соглашений об именовании файлов.

В коде задачи сначала создайте объект [TaskOutputStorage][net_taskoutputstorage], и затем по завершении задачи вызовите метод [TaskOutputStorage][net_taskoutputstorage].[SaveAsync][net_saveasync], чтобы сохранить выходные данные в службе хранилища Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
	linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

Параметр output kind позволяет классифицировать сохраненные файлы. Существуют четыре стандартных типа [TaskOutputKind][net_taskoutputkind]\: TaskOutput, TaskPreview, TaskLog и TaskIntermediate. Можно также определить пользовательские типы, если они будут нужны в вашем рабочем процессе.

Эти типы позволяют указать тип выходных данных, отображаемых в списке, когда вы запрашиваете у пакетной службы сохраненные выходные данные какой-либо определенной задачи. Другими словами, при выводе списка выходных данных их можно отфильтровать по одному из типов выходных данных. Например, "Выдайте мне версию *preview* выходных данных для задачи *109*". Дополнительные сведения о выводе списка и извлечении выходных данных приведены далее, в разделе [Извлечение выходных данных](#retrieve-output) этой статьи.

>[AZURE.TIP] Тип выходных данных определяет также, где на портале Azure будет отображен каждый конкретный файл: файлы из категории *TaskOutput* — в разделе "Task output files" (Выходные файлы задач), а файлы из категории *TaskLog* — в разделе "Task logs" (Журналы задач).

### Хранения выходных данных заданий

Помимо хранения выходных данных задач, можно сохранять выходные данные, связанные со всем заданием. Например, для задачи слияния в задании отрисовки фильма можно сохранить полностью отрисованный фильм в качестве выходных данных задания. По завершении задания клиентское приложение может просто получить список и извлечь выходные данные этого задания, не отправляя запросы к отдельным задачам.

Для сохранения выходных данных задания используйте вызов метода [JobOutputStorage][net_joboutputstorage].[SaveAsync][net_joboutputstorage_saveasync] и укажите [JobOutputKind][net_joboutputkind] и имя файла.

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

В качестве значения TaskOutputKind для выходных данных задачи используйте параметр [JobOutputKind][net_joboutputkind], чтобы классифицировать сохраняемые файлы задания. Позже это позволит запросить (получить список) определенный тип выходных данных. JobOutputKind включает в себя типы output и preview, а также позволяет создавать пользовательские типы.

### Хранение журналов задач

Помимо сохранения файла в долговременном хранилище при завершении задачи или задания, может возникнуть потребность сохранять файлы, которые обновляются во время выполнения задачи: например, файлы журнала или `stdout.txt` и `stderr.txt`. Для этой цели библиотека Azure Batch File Conventions предоставляет метод [TaskOutputStorage][net_taskoutputstorage].[SaveTrackedAsync][net_savetrackedasync]. С помощью метода [SaveTrackedAsync][net_savetrackedasync] можно отслеживать обновления файла на узле (с указанным интервалом) и сохранять эти обновления в службе хранилища Azure.

В приведенном ниже фрагменте кода [SaveTrackedAsync][net_savetrackedasync] используется для обновления `stdout.txt` в службе хранилища Azure во время выполнения задачи через каждые 15 секунд.

```csharp
string logFilePath = Path.Combine(
	Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

using (ITrackedSaveOperation stdout =
		taskStorage.SaveTrackedAsync(
		TaskOutputKind.TaskLog,
		logFilePath,
		"stdout.txt",
		TimeSpan.FromSeconds(15)))
{
	/* Code to process data and produce output file(s) */
}
```

`Code to process data and produce output file(s)` — это просто заполнитель для обычного кода задачи. Например, вам может понадобиться код, который скачивает данные из службы хранилища Azure и выполняет с ними какую-либо операцию преобразования или вычисления. В данном фрагменте кода важно обратить внимание на то, как такой код был помещен в блок `using` для периодического обновления файла с помощью метода [SaveTrackedAsync][net_savetrackedasync].

>[AZURE.NOTE] При включении отслеживания файла с помощью метода SaveTrackedAsync в службе хранилища Azure будут сохраняться только данные, *добавляемые* в отслеживаемый файл. Этот метод следует использовать только для отслеживания не участвующих в ротации файлов журнала или других файлов, в которые добавляются данные. То есть при обновлении файла данные должны добавляться только в его конец.

## Извлечение выходных данных

Извлечь сохраненные выходные данные с помощью библиотеки Azure Batch File Conventions можно в контексте задачи и задания. Можно запросить выходные данные определенной задачи или задания без необходимости знать ни путь к ним в хранилище BLOB-объектов, ни даже имя файла. Можно просто сказать: "Дайте мне выходные файлы задачи *109*".

Приведенный ниже фрагмент кода перебирает все задачи в задании, выводит некоторые сведения о выходных файлах для задачи и затем скачивает их файлы из службы хранилища.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
		task.OutputStorage(storageAccount).ListOutputs(
			TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

		output.DownloadToFileAsync(
			$"{jobId}-{output.FilePath}",
			System.IO.FileMode.Create).Wait();
    }
}
```

## Выходные данные задач и портал Azure

Портал Azure будет отображать выходные данные и журналы задачи, сохраненные в связанной учетной записи хранения Azure с использованием соглашений об именовании, указанных в [файле сведений о библиотеке Azure Batch File Conventions][github_file_conventions_readme]. Вы можете самостоятельно реализовать эти соглашения на выбранном языке или использовать библиотеку соглашений об именовании файлов в приложениях .NET.

### Обеспечение отображения на портале

Чтобы выходные данные отображались на портале, необходимо выполнить следующие требования:

 1. [Свяжите учетную запись хранения Azure](#requirement-linked-storage-account) со своей учетной записью пакетной службы.
 2. При сохранении выходных данных нужно соблюдать предварительно определенные соглашения об именовании контейнеров хранилища и файлов. Определение этих соглашений можно найти в [файле сведений][github_file_conventions_readme] о библиотеке соглашений об именовании файлов. Если для сохранения выходных данных вы используете библиотеку [Azure Batch File Conventions][nuget_package], то это требование уже выполнено.

### Просмотр выходных данных на портале

Чтобы просмотреть выходные данные и журналы задачи на портале Azure, перейдите к задаче, выходные данные которой вас интересуют, затем щелкните **Saved output files** (Сохраненные выходные файлы) или **Saved logs** (Сохраненные журналы). На приведенном рисунке показан раздел **Saved output files** (Сохраненные выходные файлы) для задачи с идентификатором 007.

.![Колонка выходных данных задачи на портале Azure][2]

## Пример кода

Пример проекта [PersistOutputs][github_persistoutputs] — это один из [примеров кода пакетной службы Azure][github_samples] на портале GitHub. Это решение Visual Studio 2015 демонстрирует использование библиотеки Azure Batch File Conventions для сохранения выходных данных задачи в долговременном хранилище. Чтобы запустить пример приложения, выполните следующее.

1. Откройте проект в **Visual Studio 2015**.
2. Добавьте свои **данные** учетной записи пакетной службы и учетной записи хранения в **AccountSettings.settings** в проекте Microsoft.Azure.Batch.Samples.Common.
3. Выполните **сборку** решения (но не запускайте его). Восстановите необходимые пакеты NuGet в случае появления соответствующего запроса.
4. С помощью портала Azure передайте [пакет приложения](batch-application-packages.md) для **PersistOutputsTask**. Добавьте в ZIP-файл пакета `PersistOutputsTask.exe` и его зависимые сборки , задайте для приложения идентификатор PersistOutputsTask, а для версии пакета приложения — значение "1.0".
5. **Запустите** (выполните) проект **PersistOutputs**.

## Дальнейшие действия

### Развертывание приложения

Функция [пакетов приложения](batch-application-packages.md) в пакетной службе дает возможность очень легко развернуть приложения, которые задачи выполняют на вычислительных узлах, и управлять их версиями.

### Установка приложений и промежуточных данных

Ознакомьтесь с публикацией [Installing applications and staging data on Batch compute nodes][forum_post] (Установка приложений и промежуточное размещение данных на вычислительных узлах пакетной службы) на форуме по пакетной службе Azure. Из нее вы узнаете о различных способах подготовки узлов для выполнения задач. Эта публикация написана одним из членов команды пакетной службы Azure, и она дает хорошее базовое понимание особенностей разных способов загрузки файлов (в том числе файлов приложений и входных данных для задач) на вычислительные узлы. Также в ней описан ряд моментов, которые необходимо учитывать при выборе каждого из этих способов.

[forum_post]: https://social.msdn.microsoft.com/Forums/ru-RU/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Селекторы сохраненных выходных файлов и журналов на портале"
[2]: ./media/batch-task-output/task-output-02.png "Колонка выходных данных задачи на портале Azure"

<!---HONumber=AcomDC_0810_2016-->