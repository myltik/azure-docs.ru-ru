<properties 
	pageTitle="Секционирование данных в DocumentDB с помощью пакета SDK для .NET| Microsoft Azure" 
	description="Узнайте, как использовать Azure DocumentDB SDK .NET для секционирования (сегментирования) данных и маршрутизации запросов по нескольким коллекциям" 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/05/2015" 
	ms.author="arramac"/>

# Секционирование данных в DocumentDB с помощью пакета SDK для .NET

Azure DocumentDB — это служба баз данных документов, которая позволяет легко масштабировать учетную запись за счет подготовки коллекций с помощью пакетов [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) и [интерфейсов API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) (что также называется **сегментированием**). Для облегчения разработки секционированных приложений и уменьшения объема кода, необходимого для задач секционирования, мы добавили в пакет SDK .NET функции, которые упрощают создание приложений, масштабируемых в нескольких секциях.

В этой статье мы рассмотрим классы и интерфейсы пакета SDK для .NET и расскажем, как их использовать для разработки секционированных приложений.

## Секционирование с помощью пакета DocumentDB SDK

Прежде чем углубиться в секционирование, освежим в памяти основные понятия DocumentDB, связанные с секционированием. Каждая учетная запись базы данных Azure DocumentDB состоит из набора баз данных, каждая из которых содержит несколько коллекций, каждая из которых в свою очередь содержит хранимые процедуры, триггеры, определяемые пользователем функции, документы и соответствующие вложения. Коллекции могут рассматриваться как секции в DocumentDB и имеют следующие свойства:

- Коллекции — это физические секции, а не просто логические контейнеры. Поэтому запросы и обработка документов, которые находятся в одной коллекции, позволяет повысить производительность.
- Коллекции — это границы для транзакций ACID, т. е. для хранимых процедур и триггеров.
- Коллекции не применяют схемы, поэтому их можно использовать для документов JSON одного или разных типов.

Начиная с версии [1\.1.0 пакета Azure DocumentDB SDK для .NET ](http://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) можно выполнять операции с документами непосредственно в базе данных. [DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) внутренне использует PartitionResolver, указанный вами для базы данных, чтобы перенаправлять запросы в соответствующую коллекцию.

Каждый класс PartitionResolver — это конкретная реализация интерфейса [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx), у которого три метода: [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx), [ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx) и [ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx). Запросы LINQ и итераторы ReadFeed используют метод ResolveForRead внутренне для перебора всех коллекций, соответствующих ключу секции для запроса. Аналогичным образом операции создания используют метод ResolveForCreate для перенаправления в нужную секцию. Для операций замены, удаления и чтения изменения не требуются, так как они используют документы, которые уже содержат ссылку на соответствующую коллекцию.

Пакет SDK также содержит два класса, поддерживающие два канонических метода секционирования, хэширование и поиск диапазона, с помощью [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) и [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx). Эти классы можно использовать, чтобы легко добавить логику секционирования в приложение.

## Добавление логики секционирования и регистрация PartitionResolver 

Ниже приведен фрагмент кода, показывающий, как создать [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) и зарегистрироваться в DocumentClient для базы данных.

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## Создание документов в секции  

После регистрации PartitionResolver вы можете выполнять операции создания и запросы непосредственно для базы данных, как показано ниже. В этом примере пакет SDK использует PartitionResolver для извлечения и хэширования UserId, а затем это значение используется для направления операции создания в правильную коллекцию.

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## Создание запросов к секциям  

Вы можете создавать запросы с помощью метода [CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx), передавая базу данных и ключ секции. Запрос возвращает один результирующий набор по всем коллекциям в базе данных, которые сопоставляются с ключом секции.

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## Создание запросов ко всем коллекциям в базе данных 

Можно также запрашивать все коллекции в базе данных и перечислять результаты, как показано ниже, пропуская аргумент ключа секции.

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## Хэширование сопоставителя секции
При секционировании по хэшу секции назначаются на основе значения хэш-функции, что позволяет равномерно распределить запросы и данные по нескольким разделам. Этот подход часто применяется для секционирования данных, создаваемых или используемых большим количеством уникальных клиентов, и очень удобен для хранения профилей пользователей, элементов каталога и данных телеметрии Интернета вещей.

**Секционирование по хэшу:** ![Диаграмма, на которой показано, как хэш-секционирование равномерно распределяет запросы по секциям](media/documentdb-sharding/partition-hash.png)

Простая схема секционирования по хэшу в *N* коллекциях: взять любой документ и вычислить *хэш(d) mod N*, чтобы определить, в какую коллекцию он помещается. Однако проблема с этим простым способом заключается в том, что он не работает при добавлении новых коллекций или удалении коллекций, так как для этого потребуется заново обработать почти все данные. [Согласованное хэширование](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738) — это хорошо известный алгоритм, который решает эту проблему, используя схему хэширования, которая сводит к минимуму объем перемещаемых данных, необходимых при добавлении или удалении коллекций.

Класс [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) реализует логику для построения согласованного хэш-кольца по хэш-функции, указанной в интерфейсе [IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx). По умолчанию HashPartitionResolver использует хэш-функцию MD5, но ее заменить на собственную реализацию хэширования. HashPartitionResolver создает 16 хэшей или "виртуальных узлов" в хэш-кольце для каждой коллекции, чтобы добиться более равномерного распределения документов по коллекциям, но это число можно изменить, чтобы справиться с асимметрией данных вычислений на стороне клиента.

**Согласованное хэширование с помощью HashPartitionResolver:** ![Диаграмма, на которой показано, как HashPartitionResolver создает хэш-кольцо](media/documentdb-sharding/HashPartitionResolver.JPG)

## Сопоставитель секций диапазона

При секционировании по диапазону разделы назначаются в зависимости от того, входит ли ключ раздела в определенный диапазон. Это часто используется для секционирования со свойствами метки времени (например, eventTime от 1 апреля 2015 г. до 14 апреля 2015 г.). Класс [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) помогает поддерживать сопоставление между Range<T> и ссылкой на коллекцию.

[Range<T>](https://msdn.microsoft.com/library/azure/mt126048.aspx) — это простой класс, который управляет диапазонами типов, реализующими интерфейс IComparable<T> and IEquatable<T> как строки или числа. Для операций чтения и создания можно передать любой произвольный диапазон, а сопоставитель определит все коллекции-кандидаты, получив диапазоны секций, которые пересекаются с запрошенным диапазоном. Это полезно при выполнении запросов временного ряда данных в диапазоне.

**Секционирование по диапазону:**

![Диаграмма, на которой показано, как секционирование по диапазону равномерно распределяет запросы по секциям](media/documentdb-sharding/partition-range.png)

Особый случай секционирования по диапазону — диапазон из всего одного значения, что иногда называют "уточняющим секционированием". Оно часто используется для секционирования по региону (например, секция для Скандинавии содержит Норвегию, Данию и Швецию) или клиентов секционирования в мультитенантном приложении.

## Примеры 

Рассмотрим [проект примеров секционирование DocumentDB на портале Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning), содержащий фрагменты кода, в которых показано, как использовать эти сопоставители и расширить их для реализации собственных сопоставителей в соответствии с вашими требованиям, например как в следующем случае:

* Как указать произвольное лямбда-выражение для GetPartitionKey и использовать его для реализации составных ключей секционирования или для секционирования различных типов объектов по-разному.
* Как создать простой сопоставитель [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs), который использует ручную таблицу уточняющих запросов для секционирования. Этот шаблон часто используется для секционирования на основе дискретных значений, таких как регион, идентификатор клиента или имя приложения.
* Как создать сопоставитель [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs), который автоматически создает коллекции на основе шаблона, определяющего схему именования, IndexingPolicy и хранимые процедуры, которые должны быть зарегистрированы для новых коллекций.
* Как создать [SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) без схемы, который просто создает новые коллекции при заполнении старых.
* Как сериализовывать и десериализовывать состояние PartitionResolver в формате JSON, так, чтобы совместно использовать его для разных процессов и между выключениями. Эти данные можно сохранить в файлах конфигурации или даже в коллекции DocumentDB.
* Класс [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) для динамического добавления и удаления секций в базе данных, секционированной на основе согласованного хэширования. Внутренне [TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) используется для маршрутизации операций чтения и записи во время миграции в одном из четырех режимов — чтение из старой схемы секционирования (ReadCurrent), новой (ReadNext), объединение результатов из обеих схем (ReadBoth) или недоступность во время миграции (None).

Образцы содержат открытый исходный код, и мы рекомендуем вам отправлять запросы с материалами, которые могут принести пользу другим разработчикам DocumentDB. Инструкции о том, как принять участие, см. в разделе [Рекомендации по участию](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md).

>[AZURE.NOTE] Операции создания коллекции ограничены DocumentDB по скорости, поэтому для выполнения некоторых примеров методов, показанных здесь, может потребоваться несколько минут.

##Часто задаваемые вопросы
**Почему DocumentDB поддерживает клиентское, а не серверное секционирование?**

DocumentDB поддерживает клиентское секционирование по двум причинам.

- Очень трудно абстрагироваться от концепции коллекции без ущерба для согласованного индексирования или запросов, высокой доступности или гарантий транзакций ACID. 
- Базам данных документов часто требуется гибкость при определении стратегий секционирования, которую серверный подход может не давать. 

**Почему секционирование не поддерживается на других платформах (Node.js, Java или Python)?**

Мы будем постепенно развертывать поддержку секционирование для других платформ на основе отзывов клиентов SDK для .NET.

**Как добавить или удалить коллекцию в схеме секционирования?**

Взгляните реализацию DocumentClientHashPartitioningManager в проекте примеров, чтобы узнать, как можно реализовать повторное секционирование.

**Как сохранить или передать конфигурацию секционирования другим клиентам?**

Вы можете сериализовать состояние модуля секционирования в формате JSON и сохранить его в файлах конфигурации или даже в коллекции DocumentDB. Изучите метод RunSerializeDeserializeSample в проекте примеров в качестве примера.

**Как связать различные методы секционирования?**

Вы можете связать сопоставители PartitionResolvers, реализуя собственный IPartitionResolver, который использует один или несколько существующих сопоставителей. Изучите TransitionHashPartitionResolver в проекте примеров.

##Ссылки
* [Примеры кода секционирования на Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning)
* [Основные понятия секционирования данных с помощью DocumentDB](documentdb-partition-data.md)
* [Коллекции и уровни производительности DocumentDB](documentdb-performance-levels.md)
* [Документацию для DocumentDB .NET SDK на портале MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Примеры DocumentDB .NET](https://github.com/Azure/azure-documentdb-net)
* [Ограничения DocumentDB](documentdb-limits.md)
* [Блог с советами по повышению производительности DocumentDB](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
 

<!---HONumber=AcomDC_0128_2016-->