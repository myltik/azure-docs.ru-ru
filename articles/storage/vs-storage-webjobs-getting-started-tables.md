<properties
	pageTitle="Приступая к работе с подключенными службами хранилища Azure и Visual Studio (проекты веб-заданий)"
	description="Как приступить к работе, используя табличное хранилище Azure в проекте веб-задания Azure в Visual Studio после подключения к учетной записи хранения с помощью подключенных служб Visual Studio."
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/30/2016"
	ms.author="tarcher"/>

# Начало работы со службой хранилища Azure (проекты веб-заданий Azure)

## Обзор

Эта статья содержит примеры кода C#, в которых показано, как использовать пакет SDK для веб-заданий Azure версии 1.x со службой хранилища таблиц Azure. В примерах кода используется [пакет SDK для веб-заданий](/app-service-web/websites-dotnet-webjobs-sdk.md) версии 1.x.

В службе хранилища таблиц Azure можно хранить большие объемы структурированных данных. Эта служба — хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных. Дополнительные сведения см. в статье [Как использовать хранилище таблиц из .NET](storage-dotnet-how-to-use-tables.md/#create-a-table "Использование табличного хранилища из .NET").


В некоторых фрагментах кода демонстрируется использование атрибута **Table** в функциях, [вызванных вручную](vs-storage-webjobs-getting-started-blobs.md#manual), т. е. без использования атрибутов запуска.

## Как добавить сущность в таблицу

Чтобы добавить сущности в таблицу, используйте атрибут **Table** с параметром **ICollector<T>** или **IAsyncCollector<T>**, где **T** обозначает схему сущностей, которые нужно добавить. Конструктор атрибута принимает строковый параметр, который указывает имя таблицы.

Следующий пример кода добавляет сущности **Person** в таблицу с именем *Ingress*.

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() {
		                PartitionKey = "Test",
		                RowKey = i.ToString(),
		                Name = "Name" }
		            );
		    }
		}

Как правило, тип, используемый с **ICollector**, является производным от **TableEntity** или реализует **ITableEntity**, но не во всех случаях. Один из следующих классов **Person** работает с кодом, показанным в предыдущем методе **Ingress**.

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

При работе непосредственно с API хранилища Azure можно также добавить параметр **CloudStorageAccount** в сигнатуру метода.

## Мониторинг в реальном времени

Поскольку функции входящих данных часто обрабатывают тома данных большого размера, на панели мониторинга пакета SDK для заданий WebJob доступны данные мониторинга в реальном времени. В разделе **Журнал вызова** указывается, запущена ли еще функция.

![Запуск функции входа](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

На странице **Подробности о вызове** отображаются данные о ходе выполнения функции (количество записанных сущностей) и предоставляется возможность ее прервать.

![Запуск функции входа](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

По завершении выполнения функции на странице **Подробности о вызове** отображается количество записанных строк.

![Функция входа выполнена](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## Как выполнять чтение нескольких сущностей из таблицы

Для чтения таблиц используйте атрибут **Table** с параметром **IQueryable<T>**, где тип **T** является производным от **TableEntity** или реализует **ITableEntity**.

Следующий пример кода считывает и записывает все строки таблицы **Ingress**.

		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

### Как выполнять чтение одной сущности из таблицы

Конструктор атрибута **Table** с двумя дополнительными параметрами позволяет указать ключ раздела и ключ строки, если необходимо выполнить привязку к одной сущности таблицы.

Следующий пример кода считывает строку таблицы для сущности **Person** на основе значений ключа раздела и ключа строки, полученных в сообщении очереди.

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


В этом примере класс **Person** не должен реализовывать **ITableEntity**.

## Как использовать API службы хранения .NET непосредственно для работы с таблицей

Чтобы сделать работу с таблицей более гибкой, можно также использовать атрибут **Table** для объекта **CloudTable**.

В следующем примере кода объект **CloudTable** используется для добавления одной сущности в таблицу *Ingress*.

		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

Дополнительную информацию об использовании объекта **CloudTable** см. в статье [Использование табличного хранилища из .NET](./storage-dotnet-how-to-use-tables.md).

## Связанные разделы, которые описаны в практическом руководстве по работе с очередями

Дополнительную информацию об обработке таблиц, которая инициируется сообщением очереди, а также несвязанные с обработкой таблиц сценарии для пакета SDK для веб-заданий см. в статье [Использование пакета SDK веб-заданий для работы с хранилищем очередей Azure](vs-storage-webjobs-getting-started-queues.md).



## Дальнейшие действия

В этой статье предоставлены примеры кода обработки обычных сценариев для работы с таблицами Azure. Дополнительную информацию об использовании веб-заданий Azure и пакета SDK для веб-заданий см. в статье [Рекомендуемые ресурсы по веб-заданиям Azure](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=AcomDC_0204_2016-->