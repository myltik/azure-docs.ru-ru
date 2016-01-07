<properties
   pageTitle="Использование счетчиков производительности в Azure | Microsoft Azure"
   description="Использование счетчиков производительности в облачных службах Azure или на виртуальной машине для обнаружения и решения проблем с производительностью."
   services="cloud-services"
   documentationCenter=".net"
   authors="rboucher"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/25/2015"
   ms.author="robb" />

# Создание и использование счетчиков производительности в приложении Azure

В этой статье рассматриваются преимущества счетчиков производительности, а также описан способ их добавления в приложения Azure. Счетчики можно использовать для сбора данных, а также обнаружения и устранения проблем с производительностью системы и приложений.

Кроме этого, данные счетчиков производительности для Windows Server, IIS и ASP.NET можно собирать и использовать для определения работоспособности веб-ролей, рабочих ролей Azure и виртуальных машин. Вы также можете создавать и использовать настраиваемые счетчики производительности.

Данные счетчиков производительности можно просмотреть несколькими способами.
1. Непосредственно на узле приложения с помощью системного монитора через удаленный рабочий стол.
2. Используя службу System Center Operations Manager с помощью пакета управления Azure.
3. С помощью других средств мониторинга, которые получают доступ к диагностическим данным, передаваемым в хранилище Azure. Дополнительные сведения см. в статье [Хранение и просмотр диагностических данных в службе хранилища Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx).

Сведения о мониторинге производительности приложений на [классическом портале Azure](http://manage.azure.com/) см. в статье [Мониторинг облачных служб](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Дополнительную информацию о создании стратегии ведения журналов и трассировки, а также об использовании средств диагностики и других методик для устранения неполадок и оптимизации приложений Azure см. в статье [Советы и рекомендации по устранению неполадок при разработке приложений Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx).


## Включение мониторинга счетчиков производительности

Счетчики производительности по умолчанию отключены. Приложение или задача при запуске должны изменить конфигурацию агента диагностики по умолчанию, чтобы включить определенные счетчики производительности, которые необходимо отслеживать для каждого экземпляра роли.

### Счетчики производительности, доступные в Microsoft Azure

Azure предлагает набор счетчиков производительности для Windows Server, IIS и стека ASP.NET. В следующей таблице перечислены некоторые счетчики производительности для приложений Azure.

|Категория счетчика: объект (экземпляр)|Имя счетчика |Справочные материалы|
|---|---|---|
|Исключения .NET CLR (_глобально_)|Число исключений/с |Счетчики производительности исключений|
|Память .NET CLR (_глобально_) |Время на сборку мусора, % |Счетчики производительности памяти|
|ASP.NET: |Перезапуски приложения |Счетчики производительности для ASP.NET|
|ASP.NET: |Время выполнения запроса |Счетчики производительности для ASP.NET|
|ASP.NET: |Прерванные запросы |Счетчики производительности для ASP.NET|
|ASP.NET: |Перезапуски рабочего процесса |Счетчики производительности для ASP.NET|
|Приложения ASP.NET (__всего__)|Общее число запросов |Счетчики производительности для ASP.NET|
|Приложения ASP.NET (__всего__)|Число запросов в секунду |Счетчики производительности для ASP.NET|
|ASP.NET v4.0.30319 |Время выполнения запроса |Счетчики производительности для ASP.NET|
|ASP.NET v4.0.30319 |Время ожидания запроса |Счетчики производительности для ASP.NET|
|ASP.NET v4.0.30319 |Текущие запросы |Счетчики производительности для ASP.NET|
|ASP.NET v4.0.30319 |Запросы в очереди |Счетчики производительности для ASP.NET|
|ASP.NET v4.0.30319 |Отклоненные запросы |Счетчики производительности для ASP.NET|
|Память |Доступный объем в МБ |Счетчики производительности памяти|
|Память |Число байтов выделенной памяти |Счетчики производительности памяти|
|Процессор(\_всего) |Загруженность процессора, % |Счетчики производительности для ASP.NET|
|TCPv4 |Сбои соединения |Объект TCP|
|TCPv4 |Установленные подключения |Объект TCP|
|TCPv4 |Сбросы подключений |Объект TCP|
|TCPv4 |Отправлено сегментов в секунду |Объект TCP|
|Сетевой интерфейс (*) |Получено байт в секунду |Объект сетевого интерфейса|
|Сетевой интерфейс (*) |Отправлено байт в секунду |Объект сетевого интерфейса|
|Сетевой интерфейс (сетевой адаптер шины виртуальной машины Microsoft \_2) |Получено байт в секунду| Объект сетевого интерфейса|
|Сетевой интерфейс (сетевой адаптер шины виртуальной машины Microsoft \_2) |Отправлено байт в секунду|Объект сетевого интерфейса|
|Сетевой интерфейс (сетевой адаптер шины виртуальной машины Microsoft \_2) |Всего байт в секунду|Объект сетевого интерфейса|

## Создание пользовательских счетчиков производительности и добавление их в приложение

В Azure можно создавать и изменять пользовательские счетчики производительности для веб-ролей и рабочих ролей. Счетчики могут использоваться для отслеживания определенного поведения приложения. Вы можете создавать пользовательские категории и описатели счетчиков производительности для задачи при запуске, веб-роли или рабочей роли с повышенными разрешениями, а также удалять их.

>[AZURE.NOTE]Для выполнения кода, который вносит изменения в пользовательские счетчики производительности, нужны разрешения более высокого уровня. Если код относится к веб-роли или рабочей роли, для правильной инициализации такая роль должна содержать тег <Runtime executionContext="elevated" /> в файле ServiceDefinition.csdef.

Данные пользовательских счетчиков производительности можно отправить в хранилище Azure с помощью агента диагностики.

Стандартные данные счетчиков производительности создаются процессами Azure. Данные пользовательских счетчиков производительности должны создаваться приложением веб-роли или рабочей роли. Сведения о типах данных, которые могут храниться в пользовательских счетчиках производительности, см. в статье [Типы счетчиков производительности](https://msdn.microsoft.com/library/z573042h.aspx). Пример создания и установки данных пользовательских счетчиков производительности в веб-роли см. в статье [Пример PerformanceCounters](http://code.msdn.microsoft.com/azure/).

## Сохранение и просмотр данных счетчика производительности

Azure кэширует данные счетчиков производительности вместе с другими диагностическими сведениями. Эти данные можно отслеживать удаленно во время выполнения экземпляра роли, используя удаленный доступ к рабочему столу для запуска таких средств, как системный монитор. Чтобы сохранить данные за пределами экземпляра роли, агент диагностики должен передать их в хранилище Azure. Ограничение размера кэшированных данных счетчиков производительности можно задать в мониторе диагностики. Это ограничение также можно настроить как часть общего ограничения для всех диагностических данных. Дополнительные сведения о задании размера буфера см. в статьях о свойствах [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) и [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Общие сведения о настройке агента диагностики для передачи данных в учетную запись хранения см. в статье [Хранение и просмотр диагностических данных в хранилище Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx).

Каждый настроенный экземпляр счетчика производительности записывается с указанной частотой выборки. При этом данные передаются в учетную запись хранения в результате запланированного запроса на передачу или по требованию. Автоматическую передачу можно запланировать с периодичностью раз в минуту. Данные счетчиков производительности, переданные агентом диагностики, хранятся в таблице WADPerformanceCountersTable в учетной записи хранения. К этой таблице можно обращаться с помощью запросов в рамках стандартных методов API хранилища Azure. Пример запроса и отображения данных счетчиков производительности из таблицы WADPerformanceCountersTable см. в статье [Пример PerformanceCounters Microsoft Azure](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9).

>[AZURE.NOTE]В зависимости от частоты передачи агента диагностики и задержки очереди самые последние данные счетчиков производительности в учетной записи хранения могут быть устаревшими на несколько минут.

## Включение счетчиков производительности с помощью файла конфигурации диагностики

Чтобы включить счетчики производительности в приложении Azure, сделайте следующее.

## Предварительные требования

При работе с этой статьей предполагается, что вы импортировали монитор диагностики в свое приложение и добавили файл конфигурации в решение Visual Studio (diagnostics.wadcfg в пакете SDK 2.4 и ниже или diagnostics.wadcfgx в пакете SDK 2.5 и выше). Дополнительные сведения описаны в шагах 1 и 2 статьи [Включение диагностики в облачных службах и виртуальных машинах Azure](./cloud-services-dotnet-diagnostics.md).

## Шаг 1. Сбор и хранение данных счетчиков производительности

После добавления файла диагностики в решение Visual Studio можно настроить сбор и хранение данных счетчиков производительности в приложении Azure. Это можно сделать, добавив счетчики производительности в файл диагностики. Данные диагностики, включая счетчики производительности, сначала собираются на уровне экземпляра. Затем данные сохраняются в таблице WADPerformanceCountersTable службы Azure, поэтому необходимо также указать в своем приложении учетную запись хранения. При проверке приложения локально в эмуляторе вычислений можно также сохранить данные диагностики локально в эмуляторе хранилища. Прежде чем сохранять данные диагностики, необходимо перейти на [классический портал Azure](http://manage.windowsazure.com/) и создать учетную запись хранения. Рекомендуется располагать учетную запись хранения в том же географическом расположении, что и приложение Azure, во избежание оплаты дополнительных расходов за пропускную способность, а также для уменьшения задержки.

### Добавление счетчиков производительности в файл диагностики

Вы можете использовать самые разные счетчики. В следующем примере приведено несколько счетчиков производительности, рекомендованных для мониторинга веб-ролей и рабочих ролей.

Откройте файл диагностики (diagnostics.wadcfg в пакете SDK 2.4 и ниже или diagnostics.wadcfgx в пакете SDK 2.5 и выше) и добавьте к элементу DiagnosticMonitorConfiguration следующий код:

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

Атрибут bufferQuotaInMB, который указывает максимальный объем хранилища файловой системы, доступной для типа собираемых данных (журналы Azure, журналы IIS и т. д.). Значение по умолчанию — 0. При достижении квоты самые старые данные удаляются по мере добавления новых. Сумма всех свойств bufferQuotaInMB должна быть больше значения атрибута OverallQuotaInMB. Более подробные сведения о способах определения требуемого объема хранилища для сбора данных диагностики см. в разделе «Настройки WAD» статьи [Рекомендации по устранению неполадок при разработке приложений Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

Атрибут scheduledTransferPeriod, который указывает интервал между запланированными передачами данных, округлен до ближайшей минуты. В следующих примерах задается равным PT30M (30 минутам). Определение для периода передачи небольшого значения, например 1 минуты, отрицательно повлияет на производительность приложения в рабочей среде, но может оказаться полезным для быстрой оценки диагностических данных во время тестирования. Период запланированного перемещения должен быть достаточно небольшим, чтобы данные диагностики не переопределялись на экземпляре, однако достаточно большим, чтобы не влиять на производительность приложения.

Атрибут counterSpecifier добавляет счетчик производительности, данные которого будут собираться. Атрибут sampleRate задает частоту выборки счетчика производительности. В данном случае это 30 секунд.

После добавления счетчиков производительности, данные которых будут собираться, сохраните изменения в файле диагностики. Затем необходимо указать учетную запись хранения, в которую будут сохраняться данные диагностики.

### Укажите учетную запись хранения

Чтобы сохранить данные диагностики в учетной записи хранения Azure, необходимо указать строку подключения в файле конфигурации службы (ServiceConfiguration.cscfg).

В пакете Azure SDK 2.5 учетную запись хранения можно указать в файле diagnostics.wadcfgx.

>[AZURE.NOTE]Эти инструкции применяются только к пакету Azure SDK 2.4 и ниже. В пакете Azure SDK 2.5 учетную запись хранения можно указать в файле diagnostics.wadcfgx.

Чтобы настроить строки подключения, выполните следующие действия:

1. Откройте файл ServiceConfiguration.Cloud.cscfg в любом текстовом редакторе и настройте строку подключения для хранилища. Значения *AccountName* и *AccountKey* можно найти на панели мониторинга учетной записи хранения в разделе "Управление ключами" классического портала Azure.

    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Сохраните файл ServiceConfiguration.Cloud.cscfg.

3. Откройте файл ServiceConfiguration.Local.cscfg и убедитесь, что для параметра UseDevelopmentStorage задано значение True.

    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
Теперь, когда заданы строки подключения, приложение будет сохранять данные диагностики в учетной записи хранения при развертывании приложения.
4. Сохраните и соберите свой проект, затем разверните приложение.

## Шаг 2. (Необязательно) Создание пользовательских счетчиков производительности

Помимо предварительно заданных счетчиков производительности можно добавить собственные счетчики производительности для мониторинга веб-ролей и рабочих ролей. Пользовательские счетчики производительности могут использоваться для отслеживания и мониторинга поведения, связанного с приложением, и могут создаваться или удаляться в задаче запуска, веб-роли или рабочей роли с повышенными разрешениями.

Агент диагностики Azure обновляет конфигурацию счетчиков производительности из файла .wadcfg через минуту после запуска. Если вы создаете пользовательские счетчики производительности в методе OnStart, а на выполнение задач запуска требуется более одной минуты, счетчики не будут созданы при попытке агента диагностики Azure загрузить их. В этом случае система диагностики Azure будет надлежащим образом собирать все данные диагностики — за исключением данных пользовательских счетчиков производительности. Чтобы устранить эту проблему, создайте счетчики производительности в задаче при запуске или переместите некоторые задачи запуска в метод OnStart после создания счетчиков производительности.

Выполните следующие действия для создания простого настраиваемого счетчика производительности с именем "\\MyCustomCounterCategory\\MyButton1Counter":

1. Откройте файл определения службы (CSDEF) для вашего приложения.
2. Добавьте элемент Runtime в элемент WebRole или WorkerRole, чтобы разрешить выполнение с повышенными правами:

    ```
    <RuntimeexecutionContext="elevated"/>
    ```
3. Сохраните файл .
4. Откройте файл диагностики (diagnostics.wadcfg в пакете SDK 2.4 и ниже или diagnostics.wadcfgx в пакете SDK 2.5 и выше) и добавьте в элемент DiagnosticMonitorConfiguration следующий код: 

    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Сохраните файл .
6. Создайте категорию настраиваемого счетчика производительности в методе OnStart роли, прежде чем вызвать base.OnStart. В следующем примере C# создается пользовательская категория, если она еще не существует:

    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);

       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);

       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }

    return base.OnStart();
    }
    ```
7. Обновите счетчики в приложении. В следующем примере выполняется обновление пользовательского счетчика производительности для событий Button1\_Click:

    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. Сохраните файл .  

Теперь данные пользовательских счетчиков производительности собирает монитор диагностики Azure.

## Шаг 3. Запрос данных счетчика производительности

После развертывания приложения и запуска диагностики монитор начнет собирать счетчики производительности и сохранять эти данные в хранилище Azure. Для просмотра данных счетчиков производительности в таблице WADPerformanceCountersTable используйте такие средства: обозреватель сервера в Visual Studio, [обозреватель хранилища Azure](http://azurestorageexplorer.codeplex.com/) или [диспетчер диагностики Azure](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) от Cerebrata. Можно также программно опросить службу таблиц с помощью запросов на [C#](../storage/storage-dotnet-how-to-use-tables.d), [Java](../storage/storage-java-how-to-use-table-storage.md), [Node.js](../storage/storage-nodejs-how-to-use-table-storage.md), [Python](../storage/storage-python-how-to-use-table-storage.md), [Ruby](../storage/storage-ruby-how-to-use-table-storage.md) или [PHP](../storage/storage-php-how-to-use-table-storage.md).

В примере на C# показан простой запрос таблицы WADPerformanceCountersTable с сохранением данных диагностики в CSV-файл. После сохранения счетчиков производительности в CSV-файл можно использовать возможности построения графиков в Microsoft Excel или любой другой инструмент для визуализации данных. Не забудьте добавить ссылку на библиотеку Microsoft.WindowsAzure.Storage.dll, которая включена в пакет Azure SDK для .NET в версии с октября 2012 г. Эта сборка устанавливается в каталог %Program Files%\\Microsoft SDKs\\Microsoft Azure.NET SDK\\version-num\\ref\\.

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

Сущности сопоставляются с объектами C# с помощью настраиваемого класса, производного от **TableEntity**. Приведенный ниже код определяет класс сущностей, который является счетчиком производительности в таблице **WADPerformanceCountersTable**.


    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## Дальнейшие действия

Вы изучили основные сведения о сборе данных счетчиков производительности. Дополнительные сведения о реализации более сложных сценариев устранения неполадок см. по следующим ссылкам.

[Рекомендации по устранению неполадок при разработке приложений Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx)

[Мониторинг облачных служб](./how-to-monitor-a-cloud-service.md)

<!----HONumber=AcomDC_1203_2015-->