<properties
	pageTitle="Эффективные запросы списков в пакетной службе Azure | Microsoft Azure"
	description="Узнайте, как уменьшить объем возвращаемых данных и повысить эффективность запросов пулов, заданий, задач и вычислительных узлов в пакетной службе Azure."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="Batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/27/2015"
	ms.author="davidmu;v-marsma"/>

# Эффективные запросы списков в пакетной службе

Пакетная служба Azure — это серьезный вычислительный ресурс. В производственной среде такие сущности, как задания, задачи и вычислительные узлы, могут исчисляться тысячами. Поэтому при получении сведений об этих элементах может создаваться большой объем данных, которые нужно передавать по каждому запросу. Ограничение количества элементов и типов сведений, возвращаемых для каждого запроса, повысит скорость их обработки и, как следствие, производительность вашего приложения.

Следующие методы API [пакетной службы для .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) являются примерами операций, которые часто выполняют практически все приложения, использующие пакетную службу Azure:

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx)
- [ListComputeNodes](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx).

Распространенным вариантом использования является мониторинг. Например, чтобы определить емкость и состояние пула, требуется опросить все вычислительные узлы (виртуальные машины) в нем. Другим примером является опрос задач в задании для выявления тех из них, которые все еще находятся в очереди. В некоторых случаях необходим широкий набор данных, а в других — только общее количество элементов или набор элементов в определенном состоянии.

Важно отметить, что и количество элементов, которое может быть возвращено, и объем данных, необходимый для представления этих элементов, могут быть очень велики. Простой опрос большого количества элементов, возвращающий множество результатов, может привести к ряду проблем.

- Время ответа API пакетной службы может стать слишком большим. Чем большее количество элементов, тем больше пакетной службе нужно времени на обработку запроса. Большое количество элементов необходимо разбивать на фрагменты, поэтому для получения всех элементов одного списка клиентской библиотеке, возможно, потребуется выполнить несколько вызовов API.
- Обработка API приложением, которое вызывает пакетную службу, займет тем больше времени, чем больше элементов нужно обработать.
- Увеличение количества и размера элементов приводит к росту потребления памяти приложением, вызывающим пакетную службу,
- а также к увеличению сетевого трафика. Передача займет больше времени и, в зависимости от архитектуры приложения, может привести к увеличению расходов на Интернет для данных, передаваемых за пределы области учетной записи пакетной службы.

Ниже приведена информация, которая касается всех интерфейсов API пакетной службы.

- Имя каждого свойства — это строка, соответствующая свойству объекта.
- Имена всех свойств чувствительны к регистру, но для значений свойств регистр значения не имеет.
- Имена свойств и регистры соответствуют элементам в API REST пакетной службы.
- Строки даты и времени начинаются с DateTime и указываются в одном из двух форматов:
	- W3CDTF (например, *creationTime gt DateTime'2011-05-08T08:49:37Z'*);
	- RFC1123 (например, *creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'*).
- Строки с логическими выражениями должны иметь значение True или False.
- Если указано неверное свойство или оператор, возвращается ошибка с кодом «400 (недопустимый запрос)».

## Эффективное выполнение запросов в пакетной службе для .NET

Интерфейс API пакетной службы для .NET позволяет снижать как количество возвращаемых элементов в списке, так и объем сведений, возвращаемых для каждого элемента. Для этого в запросе указывается уровень детализации ([DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx)). DetailLevel — это абстрактный базовый класс. Фактически нужно создать и передать объект [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) в качестве параметра в соответствующие методы.

Объект ODataDetailLevel имеет три открытых строковых свойства, которые можно указать в конструкторе или напрямую:

- [FilterClause](#filter). Фильтрация и потенциальное уменьшение числа возвращаемых элементов.
- [SelectClause](#select). Выбор подмножества значений свойств, возвращаемых для каждого элемента. Позволяет уменьшить размер элемента и ответа.
- [ExpandClause](#expand). Возвращение всех необходимых данных за один, а не несколько вызовов.

> [AZURE.TIP]Экземпляр DetailLevel с предложениями Select и Expand также можно передать в соответствующие методы Get, такие как [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), для ограничения объема возвращаемых данных.

### <a id="filter"></a> FilterClause

Количество возвращаемых элементов можно уменьшить с помощью строки фильтра. Чтобы убедиться, что возвращаются только элементы, относящиеся к запросу, можно задать одно или несколько значений свойств с квалификаторами. Например, можно вывести список только выполняющихся задач в задании или только вычислительных узлов, которые готовы к выполнению задач.

 [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) — это строка, состоящая из одного или нескольких выражений, где каждое выражение состоит из *имени свойства*, *оператора* и *значения*. Для каждого вызова API можно указать только определенные свойства, равно как и операторы, поддерживаемые каждым свойством. Несколько выражений можно объединить с помощью логических операторов **and** и **or**.

Например, эта строка фильтра возвращает только выполняющиеся задачи, параметр *displayName* которых начинается с MyTask:

	startswith(displayName, 'MyTask') and (state eq 'Running')

В перечисленных ниже статьях по API REST пакетной службы приведены таблицы поддерживаемых свойств и операций с ними.

- [Получение списка пулов учетной записи](https://msdn.microsoft.com/library/azure/dn820101.aspx)
- [Получение списка вычислительных узлов в пуле](https://msdn.microsoft.com/library/azure/dn820159.aspx)
- [Получение списка заданий в учетной записи](https://msdn.microsoft.com/library/azure/dn820117.aspx)
- [Получение состояния подготовки задания и задачи деблокирования задания](https://msdn.microsoft.com/library/azure/mt282170.aspx)
- [Получение расписаний для заданий в учетной записи](https://msdn.microsoft.com/library/azure/mt282174.aspx)
- [Получение списка заданий, связанных с расписанием задания](https://msdn.microsoft.com/library/azure/mt282169.aspx)
- [Получение списка задач, связанных с заданием](https://msdn.microsoft.com/library/azure/dn820187.aspx)
- [Получение списка файлов, связанных с задачей](https://msdn.microsoft.com/library/azure/dn820142.aspx)
- [Получение списка сертификатов в учетной записи](https://msdn.microsoft.com/library/azure/dn820154.aspx)
- [Получение списка файлов в узле](https://msdn.microsoft.com/library/azure/dn820151.aspx)

> [AZURE.IMPORTANT]Указывая свойства в каком-либо из трех типов предложений, убедитесь, что имя свойства и регистр соответствуют аналогичному элементу в API REST пакетной службы. Например, при работе с [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) в .NET необходимо указывать **state**, а не **State**, несмотря на то что в .NET используется свойство [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Чтобы проверить имя и регистр свойства **state**, следует найти имя элемента в разделе [Получение сведений о задаче](https://msdn.microsoft.com/library/azure/dn820133.aspx) в документации по API REST пакетной службы.

### <a id="select"></a> SelectClause

Значения свойств, которые возвращаются для каждого элемента, можно ограничить с помощью строки выбора. Для элемента можно указать список свойств, после чего будут возвращаться только значения этих свойств.

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) — это строка, содержащая список имен свойств, разделенных запятыми. Можно указать любое сочетание свойств элемента, возвращаемых операцией list.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

Количество вызовов API можно уменьшить с помощью предложения expand. Подробные сведения о каждом элементе списка можно получить с помощью одного вызова API (вместо получения списка и последовательного вызова каждого элемента в нем).

Предложение [ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) похоже на предложение select тем, что оно тоже управляет возвращаемыми в результатах данными. Предложение expand поддерживается только для списка заданий, списка задач и списка пулов. В настоящее время оно поддерживает только статистические данные. Если необходимы все свойства и предложение select не указано, для получения статистических сведений следует использовать предложение expand. Если для получения подмножества свойств используется предложение select, можно указать статистику в нем, а для предложения expand оставить значение null.

## Пример эффективного запроса

Ниже приведен фрагмент кода, который с помощью API пакетной службы для .NET выполняет эффективный запрос к пакетной службе для получения статистики по определенному набору пулов. В этом сценарии у пользователя пакетной службы есть как тестовый, так и рабочий пулы. Идентификаторы тестовых пулов начинаются с префикса test, а рабочих — с префикса prod. В приведенном фрагменте *myBatchClient* — это должным образом инициализированный экземпляр [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient).

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// We want to pull only the "test" pools, so we limit the items returned by using a Filterclause and
	// specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = myBatchClient.PoolOperations.ListPools(detailLevel).ToList();

> [AZURE.TIP]Мы советуем *всегда* использовать предложения фильтрации и выбора в вызовах API для получения списков. Это обеспечит максимальную эффективность и производительность приложения.

## Дальнейшие действия

1. Обязательно ознакомьтесь с документацией по API пакетной службы, относящейся к вашим сценариям разработки.
    - [Пакетная служба (REST)](https://msdn.microsoft.com/library/azure/dn820158.aspx)
    - [Пакетная служба (.NET)](https://msdn.microsoft.com/library/azure/dn865466.aspx)
2. Скачайте [образцы кода, связанные с использованием пакетной службы Azure](https://github.com/Azure/azure-batch-samples), на GitHub и изучите их.

<!---HONumber=September15_HO1-->