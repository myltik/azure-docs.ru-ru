---
title: Использование пакета SDK WebJobs для фоновой обработки на основе событий в Azure
description: Ознакомьтесь со сведениями о написании кода для пакета SDK WebJobs. Создавайте задания фоновой обработки на основе событий, с помощью которых можно получить доступ к данным в службах Azure и сторонних службах.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: 3adf725f76f744fd1d321668fe892b9703de25de
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
---
# <a name="how-to-use-the-webjobs-sdk-for-event-driven-background-processing"></a>Использование пакета SDK WebJobs для фоновой обработки на основе событий

В этой статье приводятся рекомендации по написанию кода для [пакета SDK WebJobs](webjobs-sdk-get-started.md). Документация относится к версиям 2.x и 3.x, если не указано иное. Основное отличие, представленное в версии 3.x, заключается в использовании класса .NET Core вместо .NET Framework.

>[!NOTE]
> [Функции Azure](../azure-functions/functions-overview.md) основаны на пакете SDK WebJobs, и эта статья содержит ссылки на документацию предложения "Функции Azure" для некоторых тем. Обратите внимание на следующие отличия между службой "Функции" и пакетом SDK WebJobs:
> * Решение "Функции Azure" версии 1.x соответствует пакету SDK WebJobs версии 2.x, а решение "Функции Azure" версии 2.x — пакету SDK WebJobs версии 3.x. Репозитории исходного кода следуют за нумерацией пакета SDK WebJobs, и у многих есть ветви v2.x, причем главная ветвь имеет в настоящее время код версии 3.x.
> * Пример кода для библиотек классов C# решения "Функции Azure" напоминает код пакета SDK WebJobs, за исключением того, что в проекте пакета SDK WebJobs не требуется атрибут `FunctionName`.
> * Некоторые типы привязки поддерживаются только в службе "Функции", например HTTP, веб-перехватчик и "Сетка событий" (на основе HTTP). 
> 
> Дополнительные сведения см. в разделе [Сравнение функций и веб-заданий](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs). 

## <a name="prerequisites"></a>предварительным требованиям

В этой статье предполагается, что вы прочитали руководство [Get started with the WebJobs SDK](webjobs-sdk-get-started.md) (Начало работы с пакетом SDK WebJobs).

## <a name="jobhost"></a>JobHost

Объект `JobHost` представляет собой контейнер среды выполнения для функций, прослушивающий триггеры и вызовы функций. Вам нужно создать объект `JobHost` в коде и написать код, чтобы настроить его работу.

Это ключевое различие между использованием пакета SDK WebJobs напрямую и косвенно с помощью решения "Функции Azure". В решении "Функции Azure" служба управляет объектом `JobHost` и ее нельзя настроить, написав код. Это решение позволяет настроить поведение узла через настройки в файле *host.json*. Эти настройки представляют собой строки, а не код, что ограничивает виды настроек, которые можно применить.

### <a name="jobhost-connection-strings"></a>Строки подключения JobHost

Пакет SDK WebJobs ищет строки подключения служебной шины и службы хранилища в файле *local.settings.json* при локальном запуске или в среде WebJob при запуске в Azure. Если вы хотите использовать свои имена для этих строк подключения или хранить их в другом месте, вы можете установить их в коде, как показано ниже:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-development-settings"></a>Параметры разработки JobHost

Класс `JobHostConfiguration` содержит метод `UseDevelopmentSettings`, который можно вызвать, чтобы реализовать более эффективную локальную разработку. Вот некоторые параметры, которые этот метод изменяет:

| Свойство | Параметр разработки |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` — чтобы увеличить выходные данные журнала. |
| `Queues.MaxPollingInterval`  | Низкое значение для обеспечения немедленного запуска методов очереди.  |
| `Singleton.ListenerLockPeriod` | 15 секунд для ускорения последовательной разработки. |

В следующем примере показано, как использовать параметры разработки. Чтобы `config.IsDevelopment` вернула значение `true` при локальном запуске, установите локальную переменную среды `AzureWebJobsEnv` со значением `Development`.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>Параметры ServicePointManager JobHost

Платформа .NET Framework содержит API с именем [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit), который контролирует количество одновременных подключений к узлу. Перед запуском узла WebJobs мы рекомендуем вам увеличить это значение со значения по умолчанию 2.

Все исходящие HTTP-запросы, выполняемые из функции, используя `HttpClient`, проходят через `ServicePointManager`. Как только вы выберете `DefaultConnectionLimit`, `ServicePointManager` начнет ставить запросы в очередь, а затем отправлять их. Предположим, что для ограничения `DefaultConnectionLimit` установлено значение 2 и код включает 1000 HTTP-запросов. Первоначально в ОС фактически разрешено только 2 запроса. Остальные 998 поставлены в очередь, пока не появится место для них. Это означает, что может истечь время ожидания клиента `HttpClient`, потому что *считается*, что запрос выполнен, но на самом деле он никогда не отправлялся ОС на целевой сервер. Таким образом, вы можете столкнуться с поведением, которое не имеет смысла: локальному клиенту `HttpClient` потребуется 10 секунд, чтобы выполнить запрос, но служба будет возвращать каждый запрос за 200 мс. 

Значение по умолчанию для приложений ASP.NET — `Int32.MaxValue`, и это, вероятно, хорошо работает для WebJobs, запущенного в базовом плане службы приложений или плане более высокого уровня. WebJobs обычно требуется параметр Always On, который поддерживается только базовыми планами службы приложений или планами более высокого уровня. 

Если ваше задание WebJob работает в бесплатном или общем плане службы приложений, приложение ограничено песочницей службы приложений, которая в настоящее время имеет такое [ограничение подключений — 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). При неограниченном подключении в `ServicePointManager` более вероятно, что будет достигнут порог подключения песочницы, а сайт отключится. В этом случае установка более низкого значения параметра `DefaultConnectionLimit`, например 50 или 100, может не допустить события выше и обеспечить достаточную пропускную способность.

Параметр нужно настроить перед выполнением HTTP-запросов. Поэтому узел WebJobs не должен пытаться автоматически изменить настройки. Выполнение HTTP-запросов может произойти до запуска узла, что может привести к непредвиденному поведению. Наилучший подход — сразу же установить значение в методе `Main` перед инициализацией узла `JobHost`, как показано в следующем примере:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>триггеры;

Функции должны быть общедоступными методами и иметь один атрибут триггера или атрибут [NoAutomaticTrigger](#manual-trigger).

### <a name="automatic-trigger"></a>Автоматический триггер

Автоматические триггеры вызывают функцию в ответ на событие. Например, ознакомьтесь с триггером очереди в [обзорной статье](webjobs-sdk-get-started.md).

### <a name="manual-trigger"></a>Ручной триггер

Чтобы вызвать функцию вручную, используйте атрибут `NoAutomaticTrigger`, как показано в следующем примере:

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
    TextWriter logger,
    string value,
    [Queue("outputqueue")] out string message)
{
    message = value;
    logger.WriteLine("Creating queue message: ", message);
}
```

## <a name="input-and-output-bindings"></a>Входные и выходные привязки

Входные привязки предоставляют декларативный способ получения данных для кода из внешних служб или служб Azure. Выходные привязки обеспечивают способ обновления данных. Примеры привязок приводятся в [статье обзора](webjobs-sdk-get-started.md).

Возвращаемое значение метода можно использовать для привязки выходных данных. Для этого примените атрибут к возвращаемому значению метода. В разделе о [триггерах и привязках](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) см. пример в решении "Функции Azure".

## <a name="binding-types"></a>Типы привязки

В пакет `Microsoft.Azure.WebJobs` включены следующие типы триггеров и привязок:

* Хранилище BLOB-объектов
* Хранилище очередей
* Хранилище таблиц

Чтобы использовать другие типы триггеров и привязок, установите пакет NuGet, который их содержит, и вызовите метод `Use<binding>` для объекта `JobHostConfiguration`. Например, если вы хотите использовать триггер таймера, установите `Microsoft.Azure.WebJobs.Extensions` и вызовите `UseTimers` в методе `Main`, как показано в этом примере:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Вы можете найти пакет для установки для определенного типа привязки в разделе **пакетов** [справочной статьи](#binding-reference-information) по типу привязки для решения "Функции Azure". Исключением является триггер и привязка файлов (для локальной файловой системы), которая не поддерживается решением "Функции Azure". Чтобы использовать привязку файлов, установите `Microsoft.Azure.WebJobs.Extensions` и вызовите `UseFiles`.

### <a name="usecore"></a>UseCore

Пакет `Microsoft.Azure.WebJobs.Extensions`, упомянутый ранее, также предоставляет специальный тип привязки, который вы можете зарегистрировать, вызвав метод `UseCore`. Эта привязка позволяет определить параметр [ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) в сигнатуре функции. Объект контекста дает вам доступ к идентификатору вызова, который вы можете использовать для корреляции всех журналов, созданных данным вызовом функции. Ниже приведен пример:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

## <a name="binding-configuration"></a>Конфигурация привязки

Многие типы триггеров и привязок позволяют настроить их поведение, задав свойства в объекте конфигурации, который вы передаете на узел `JobHost`.

### <a name="queue-trigger-configuration"></a>Конфигурация триггера очереди

Параметры, которые вы можете настроить для триггера очереди службы хранилища, объясняются в справочной документации файла [host.json](../azure-functions/functions-host-json.md#queues) в решении "Функции Azure". Как установить их в проекте пакета SDK WebJobs, показано в следующем примере:

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configuration-for-other-bindings"></a>Конфигурация для других привязок

Некоторые типы триггеров и привязок определяют собственный настраиваемый тип конфигурации. Например, триггер файла позволяет указать корневой путь для мониторинга:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Выражения привязки

В параметрах конструктора атрибутов вы можете использовать выражения, которые разрешают значения из разных источников. Например, в следующем коде путь для атрибута `BlobTrigger` создает выражение с именем `filename`. При использовании для выходной привязки `filename` разрешается к имени активирующего большого двоичного объекта.
 
```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Дополнительные сведения о выражениях привязки см. в разделе [Выражения привязки и шаблоны](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) в документации по решению "Функции Azure".

### <a name="custom-binding-expressions"></a>Настраиваемые выражения привязки

Иногда требуется указать в коде имя очереди, имя большого двоичного объекта, контейнера или таблицы, а не жестко задавать их. Например, в некоторых случаях в файле конфигурации или в переменной среды необходимо указывать имя очереди для атрибута `QueueTrigger`.

Вы можете сделать это, передав объект `NameResolver` объекту `JobHostConfiguration`. В параметрах конструктора атрибута привязки и триггера нужно указать специальные заполнители, тогда код `NameResolver` будет указывать фактические значения, которые следует использовать вместо этих заполнителей. Заполнители идентифицируются путем использования знаков процента (%) с обеих сторон, как показано в примере ниже:
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Этот код позволяет использовать очередь с именем logqueuetest в тестовой среде и очередь с именем logqueueprod в производственной среде. Вместо фиксированного имени очереди требуется указать имя записи в коллекции `appSettings`. 

Есть стандартное средство NameResolver, которое работает, если нет никакого другого. Стандартное средство получает значения из настроек приложения или переменных среды.

Класс `NameResolver` получает имя очереди из `appSettings`, как показано в следующем примере:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Передайте класс `NameResolver` в объект `JobHost`, как показано в следующем примере.

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Решение "Функции Azure" реализует `INameResolver`, чтобы получить значения из настроек приложения, как показано в этом примере. Когда вы используете пакет SDK WebJobs напрямую, вы можете написать специальную реализацию, которая получает значения замены заполнителя из любого выбранного источника. 

## <a name="binding-at-runtime"></a>Привязка во время выполнения

Если перед использованием атрибута привязки для веб-заданий, например `Queue`, `Blob` или `Table`, необходимо выполнить какие-либо действия с функцией, можно использовать интерфейс `IBinder`.

В следующем примере в выходной очереди создается новое сообщение с тем же содержимым, что и в сообщении входной очереди. Имя очереди вывода определяется кодом в теле функции.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

Дополнительные сведения см. в разделе [Привязка во время выполнения](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) в документации по решению "Функции Azure".

## <a name="binding-reference-information"></a>Справочная информация о привязке

Справочная информация о каждом типе привязки содержится в документации по решению "Функции Azure". Используя в качестве примера очередь службы хранилища, в каждой справочной статье о привязке вы найдете следующие сведения:

* [Пакеты](../azure-functions/functions-bindings-storage-queue.md#packages) — сведения о том, какой пакет установить, чтобы включить поддержку привязки в проекте пакета SDK WebJobs.
* [Пример триггера](../azure-functions/functions-bindings-storage-queue.md#trigger---example) — пример библиотеки классов C# применяется к пакету SDK WebJobs. Просто опустите атрибут `FunctionName`.
* [Атрибуты триггера](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) — атрибуты, используемые для типа привязки.
* [Конфигурация триггера](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) — объяснения свойств атрибута и параметров конструктора.
* [Использование триггера](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) — сведения о том, к каким типам можно применить привязку и как она работает. Например: алгоритм опроса, обработка подозрительной очереди.
  
Чтобы ознакомиться со списком справочных статей о привязке, просмотрите сведения в разделе о **поддерживаемых привязках** в статье [Основные понятия триггеров и привязок в Функциях Azure](../azure-functions/functions-triggers-bindings.md#supported-bindings). В этом списке привязки HTTP, веб-перехватчика и службы "Сетка событий" поддерживаются только с помощью решения "Функции Azure", а не пакета SDK WebJobs.

## <a name="disable-attribute"></a>Атрибут Disable 

Атрибут [Disable](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) позволяет вам управлять запуском функции. 

В следующем примере, если параметр приложения `Disable_TestJob` имеет значение "1" или True (без учета регистра), функция не будет работать. В этом случае среда выполнения создает сообщение журнала о том, что *функция Functions.TestJob отключена*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Когда вы изменяете значения параметра приложения на портале Azure, нужно повторно запустить WebJob с помощью нового параметра.

Атрибут может объявляться на уровне класса, метода или параметра. Имя параметра также может содержать выражения привязки.

## <a name="timeout-attribute"></a>Атрибут Timeout

Атрибут [Timeout](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) требует отмены функции, если она не завершена в течение определенного периода времени. В следующем примере функция будет работать в течение одного дня без использования времени ожидания. С функцией времени ожидания функция будет отменена через 15 секунд.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Вы можете применить атрибут Timeout на уровне класса или метода и указать глобальное время ожидания, используя `JobHostConfiguration.FunctionTimeout`. Время ожидания на уровне класса или метода переопределяет глобальное время ожидания.

## <a name="singleton-attribute"></a>Атрибут Singleton

Используйте атрибут [Singleton](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs), чтобы обеспечить выполнение только одного экземпляра функции, даже при наличии нескольких экземпляров несущего веб-приложения. Такое выполнение обеспечивается с помощью реализации [распределенного блокирования](#viewing-lease-blobs).

В следующем примере только один экземпляр функции `ProcessImage` запускается в любой момент времени:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

У некоторых триггеров есть встроенная поддержка для управления параллелизмом:

* **QueueTrigger** — задайте значение 1 для параметра `JobHostConfiguration.Queues.BatchSize`.
* **ServiceBusTrigger** — задайте значение 1 для параметра `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls`.
* **FileTrigger** — задайте значение 1 для параметра `FileProcessor.MaxDegreeOfParallelism`.

Эти параметры можно использовать для обеспечения отдельного выполнения функции в одном экземпляре. Чтобы гарантировать, что запущен только один экземпляр функции, если веб-приложение масштабируется до нескольких экземпляров, примените блокировку Singleton для уровня прослушивателя в функции (`[Singleton(Mode = SingletonMode.Listener)]`). Блокировки прослушивателя инициируются при запуске узла JobHost. Если все три горизонтально масштабированные экземпляры запускаются одновременно, блокировки требует только один из экземпляров, а запускается только один прослушиватель.

### <a name="singletonscopehost"></a>SingletonScope.Host

Областью по умолчанию для блокировки является `SingletonScope.Function`. Это означает, что область блокировки (путь аренды большого двоичного объекта) привязана к полному имени функции. Чтобы заблокировать функции, укажите узел `SingletonScope.Host` и используйте имя идентификатора области, одинаковое для всех функций, которые не нужно запускать одновременно. В следующем примере выполняется только один экземпляр `AddItem` или `RemoveItem`:

```charp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation
}
```

### <a name="viewing-lease-blobs"></a>Просмотр арендуемых больших двоичных объектов

Пакет SDK WebJobs использует аренды [больших двоичных объектов Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) для реализации распределенной блокировки. Арендуемые большие двоичные объекты, используемые Singleton, можно найти в контейнере `azure-webjobs-host` в учетной записи хранения `AzureWebJobsStorage` по пути locks. Например, путь к арендуемому большому двоичному объекту для первого примера `ProcessImage`, рассмотренного ранее, должен быть таким: `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Все пути включают идентификатор JobHost, в данном случае 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Асинхронные функции

Информацию о том, как закодировать асинхронные функции, см. в документации по решению "Функции Azure" в разделе [Асинхронный режим](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Токены отмены

Информацию о том, как обрабатывать маркеры отмены, см. в документации по решению "Функции Azure" в разделе [Токены отмены](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Выполнение на нескольких экземплярах

Если ваше веб-приложение работает в нескольких экземплярах, на каждом экземпляре запускается непрерывный WebJob, который прослушивает триггеры и вызывает функции. Различные привязки триггера предназначены для эффективной совместной работы в экземплярах, чтобы масштабирование до большего количества экземпляров позволяло обрабатывать большую нагрузку.

Триггеры очереди и большого двоичного объекта автоматически препятствуют тому, чтобы функция обрабатывала сообщение очереди или большой двоичный объект более одного раза. Функции не должны быть идемпотентными.

Триггер таймера автоматически гарантирует, что запускается только один экземпляр таймера, поэтому вы не получаете больше одного экземпляра функции, запущенного в заданное время.

Если вы хотите, чтобы выполнялся только один экземпляр функции, даже если существует несколько экземпляров несущего веб-приложения, можно использовать атрибут [Singleton](#singleton).
    
## <a name="filters"></a>Фильтры 

Функциональные фильтры (предварительная версия) обеспечивают способ настройки конвейера выполнения WebJobs с помощью вашей собственной логики. Фильтры напоминают [фильтры ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Их можно реализовать как декларативные атрибуты, которые применяются к функциям или классам. Дополнительные сведения см. на странице о [фильтрах функции](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

Мы рекомендуем применить платформу ведения журналов, разработанную для ASP.NET, а также ознакомиться со статьей [обзора](webjobs-sdk-get-started.md), в которой объясняется, как использовать эту платформу. 

### <a name="log-filtering"></a>Фильтрация журнала

Каждый журнал, созданный экземпляром `ILogger`, имеет связанные параметры `Category` и `Level`. Параметр [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) (Уровень ведения журнала) является перечислением целочисленных значений, которые обозначают относительную важность.

|LogLevel    |Код|
|------------|---|
|Трассировка       | 0 |
|Отладка       | 1 |
|Информация | 2 |
|Предупреждение     | 3 |
|Ошибка       | 4. |
|критические ошибки.    | 5 |
|None        | 6 |

Каждую категорию можно независимо отфильтровать до определенного уровня [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel). Например, вы можете просмотреть все журналы для обработки триггера большого двоичного объекта, но только `Error` и выше для всего остального.

Чтобы упростить определение правил фильтрации, пакет SDK WebJobs предоставляет фильтр `LogCategoryFilter`, который можно передать многим имеющимся поставщикам ведения журналов, включая Application Insights и Console.

Фильтр `LogCategoryFilter` имеет свойство `Default` с начальным значением `Information`, что означает, что все сообщения с уровнями `Information`, `Warning`, `Error` или `Critical` регистрируются, а любые сообщения с уровнями `Debug` или `Trace` фильтруются.

Свойство `CategoryLevels` позволяет указать уровни журналов для определенных категорий, чтобы вы могли точно настроить выходные данные журнала. Если в словаре `CategoryLevels` не найдено совпадение, фильтр возвращается к значению `Default` при принятии решения о фильтрации сообщения.

В следующем примере создается фильтр, который по умолчанию фильтрует все журналы на уровне `Warning`. Категории `Function` или `Host.Results` фильтруются на уровне `Error`. Фильтр `LogCategoryFilter` сравнивает текущую категорию со всеми зарегистрированными уровнями `CategoryLevels` и выбирает самое длинное совпадение. Это означает, что уровень `Debug`, зарегистрированный для `Host.Triggers`, будет соответствовать `Host.Triggers.Queue` или `Host.Triggers.Blob`. Это позволит вам управлять более широкими категориями, не добавляя их.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Пользовательские данные телеметрии для Application Insights

Изнутри клиент `TelemetryClient`, созданный поставщиком Application Insights для пакета SDK WebJobs, использует канал [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Когда конечная точка Application Insights недоступна или регулирует входящие запросы, этот канал [сохраняет запросы в файловой системе веб-приложения и позже повторно отправляет их](http://apmtips.com/blog/2015/09/03/more-telemetry-channels).

Клиент `TelemetryClient` создается классом, который реализует `ITelemetryClientFactory`. По умолчанию это [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Если вы хотите изменить любую часть конвейера Application Insights, вы можете указать свою фабрику `ITelemetryClientFactory`, и узел будет использовать ваш класс для создания клиента `TelemetryClient`. Например, этот код переопределяет фабрику `DefaultTelemetryClientFactory`, чтобы изменить свойство `ServerTelemetryChannel`:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // change the default from 30 seconds to 15 seconds
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

Объект SamplingPercentageEstimatorSettings настраивает [адаптивную выборку](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). Это означает, что в некоторых сценариях с большим количеством операций App Insights отправляет выбранное подмножество данных телеметрии на сервер.

После создания фабрики данных телеметрии ее нужно передать поставщику ведения журнала Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a>Дальнейшие действия

В этом руководстве приведены фрагменты кода, демонстрирующие, как обрабатывать общие сценарии для работы с пакетом SDK WebJobs. Полные примеры см. на странице с примерами [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples).