<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Профилирование производительности" pageTitle="Использование счетчиков производительности в Azure (.NET)" metaKeywords="Счетчики производительности Azure, профилирование производительности Azure, счетчики производительности Azure C#, профилирование производительности Azure C#" description="Узнайте, как включить и собрать данные со счетчиков производительности в приложениях Azure. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Использование счетчиков производительности в Azure" authors="ryanwi" solutions="" manager="" editor="" />





# Использование счетчиков производительности в Azure

В приложении Azure счетчики производительности можно использовать
для сбора данных, которые позволят определить узкие места системы
и точно оптимизировать производительность системы и приложения. Счетчики производительности, доступные для Windows Server 2008, Windows Server 2012, IIS и ASP.NET, могут быть собраны и использованы для определения работоспособности приложения Azure. 

В этом разделе объясняется, как включить счетчики производительности в вашем приложении с помощью файла конфигурации diagnostics.wadcfg. Дополнительные сведения о мониторинге производительности вашего приложения на [портале управления Azure][] см. в разделе [Мониторинг облачных служб][]. Подробнее о создании стратегии ведения журналов и трассировки и об использовании средств диагностики и других методик для устранения неполадок и оптимизации приложений Azure см. в разделе [Советы и рекомендации по устранению неполадок при разработке приложений Azure][].

Эта задача включает следующие шаги:

-   [Предварительные требования][]
-   [Шаг 1. Сбор и хранение данных счетчиков производительности][]
-   [Шаг 2. (Необязательно) Создание пользовательских счетчиков производительности][]
-   [Шаг 3. Запрос данных счетчика производительности][]
-   [Дальнейшие действия][]
-   [Дополнительные ресурсы][]

## <a name="prereqs"> </a>Предварительные требования

При работе с этой статьей предполагается, что вы импортировали монитор диагностики в свое приложение и добавили файл конфигурации diagnostics.wadcfg в решение Visual Studio.  Дополнительные сведения см. в описании шагов 1 и 2 в разделе [Включение диагностики в Azure][].

## <a name="step1"> </a>Шаг 1. Сбор и хранение данных счетчиков производительности

После добавления файла diagnostics.wadcfg в решение Visual Studio можно настроить сбор и хранение данных счетчиков производительности в приложении Azure.  Это можно сделать, добавив счетчики производительности в файл diagnostics.wadcfg.  Данные диагностики, включая счетчики производительности, сначала собираются на уровне экземпляра.  Затем данные сохраняются в таблице **WADPerformanceCountersTable** службы Azure, поэтому необходимо также указать в своем приложении учетную запись хранения. При проверке приложения локально в эмуляторе вычислений можно также сохранить данные диагностики локально в эмуляторе хранилища. Прежде чем сохранить данные диагностики, необходимо сначала перейти на [портал управления Azure][] и создать учетную запись хранения. Рекомендуется располагать учетную запись хранения в том же географическом расположении, что и приложение Azure, во избежание оплаты дополнительных расходов за пропускную способность, а также для уменьшения задержки.

### Добавление счетчиков производительности в файл diagnostics.wadcfg

Существует множество счетчиков производительности, которые можно собирать. В следующем примере показано несколько счетчиков производительности, рекомендованных для мониторинга веб-ролей и рабочих ролей. 

Откройте файл diagnostics.wadcfg и добавьте следующий код в элемент **DiagnosticMonitorConfiguration**:

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
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
		</PerformanceCounters>    

Атрибут **bufferQuotaInMB**, который указывает максимальный объем хранилища файловой системы, доступной для типа собираемых данных (журналы Azure, журналы IIS и т. д.). По умолчанию используется значение 0.  При достижении квоты самые старые данные удаляются по мере добавления новых данных. Сумма всех свойств **bufferQuotaInMB** должна быть больше значения атрибута **OverallQuotaInMB**.  Более подробное обсуждение способов определения требуемого объема хранилища для сбора данных диагностики см. в разделе настройки WAD раздела [Рекомендации по устранению неполадок, связанных с развертыванием приложений Azure][].

Атрибут **scheduledTransferPeriod**, который указывает интервал между запланированными передачами данных, округлен до ближайшей минуты. В следующих примерах задается равным PT30M (30 минутам). Определение для периода передачи небольшого значения, например 1 минуты, отрицательно повлияет на производительность приложения в рабочей среде, но может оказаться полезным для быстрой оценки диагностических данных во время тестирования.  Период запланированного перемещения должен быть достаточно небольшим, чтобы данные диагностики не переопределялись на экземпляре, однако достаточно большим, чтобы не влиять на производительность приложения.

Атрибут **counterSpecifier** указывает счетчик производительности для сбора.

Атрибут **sampleRate** указывает частоту сэмплирования счетчика производительности. В данном случае – 30 секунд.

После добавления счетчиков производительности, которые следует собрать, сохраните изменения в файле diagnostics.wadcfg.  Затем необходимо указать учетную запись хранения, в которую будут сохраняться данные диагностики.

### Укажите учетную запись хранения

Чтобы сохранить данные диагностики в учетной записи хранения Azure, необходимо указать строку подключения в файле конфигурации службы (ServiceConfiguration.cscfg).  Средства Azure для Visual Studio версии 1.4 (август 2011 г.) и выше позволяют использовать разные файлы конфигурации (ServiceConfiguration.cscfg) для локальных и облачных ресурсов. Несколько конфигураций служб полезны для диагностики, так как можно бесплатно использовать эмулятор хранилища для локального тестирования, поддерживая при этом отдельный файл конфигурации для рабочей среды.

Чтобы настроить строки подключения, выполните следующие действия:

1.  Откройте файл ServiceConfiguration.Cloud.cscfg в текстовом редакторе по своему выбору и настройте строку подключения для учетной записи хранения.

        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
        </ConfigurationSettings>

    Значения **AccountName** и **AccountKey** можно найти на панели мониторинга учетной записи в разделе **Управление ключами** портала управления.

2.  Сохраните файл ServiceConfiguration.Cloud.cscfg.
3.  Откройте файл ServiceConfiguration.Local.cscfg и убедитесь, что для параметра **UseDevelopmentStorage** задано значение true (значение по умолчанию).

		<ConfigurationSettings>
      	   <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
		</ConfigurationSettings>

	Теперь, когда заданы строки подключения, приложение будет сохранять данные диагностики в учетной записи хранения при развертывании приложения.  
4. Сохраните и соберите свой проект, затем разверните приложение.

## <a name="step2"> </a>Шаг 2. (Необязательно) Создание пользовательских счетчиков производительности

Помимо предварительно заданных счетчиков производительности можно добавить собственные счетчики производительности для мониторинга веб-ролей и рабочих ролей.  Пользовательские счетчики производительности могут использоваться для отслеживания и мониторинга поведения, связанного с приложением, и могут создаваться или удаляться в задаче запуска, веб-роли или рабочей роли с повышенными разрешениями.

Выполните следующие действия для создания простого настраиваемого счетчика производительности с именем "\MyCustomCounterCategory\MyButton1Counter":

1.  Откройте файл определения службы (CSDEF) для вашего приложения.
2.  Добавьте элемент **Runtime** в элемент **WebRole** или **WorkerRole**,
    чтобы разрешить выполнение с повышенными правами:

        <Runtime executionContext="elevated" />

3.  Сохраните файл.
4.  Откройте файл diagnostics.wadcfg и добавьте следующий код в элемент **DiagnosticMonitorConfiguration**:

		<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
		<PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
		</PerformanceCounters>		

5. 	Сохраните файл.
6.  Создайте категорию настраиваемого счетчика производительности в методе **OnStart**
    роли, прежде чем вызвать **base.OnStart**. В следующем примере C# создается пользовательская категория, если она еще не существует:

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

7.  Обновите счетчики в приложении. В следующем примере 
    обновляется пользовательский счетчик производительности для событий на **Button1_Click**:

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

8.  Сохраните файл.

После завершения этих действий, данные пользовательского счетчика производительности
собираются монитором диагностики Azure.

## <a name="step3"> </a>Шаг 3. Запрос данных счетчика производительности

После развертывания приложения и запуска диагностики монитор начнет собирать счетчики производительности и сохранять эти данные в хранилище Azure. Используйте средства, такие как **обозреватель сервера в Visual Studio**, [обозреватель хранилища Azure][] или [диспетчер диагностики Azure][] от Cerebrata для просмотра данных счетчиков производительности в таблице **WADPerformanceCountersTable**.  Можно также программно опросить службу таблицы с помощью [C#][], [Java][], [Node.js][], [Python][] или [PHP][].  

В примере на C# показан простой запрос таблицы **WADPerformanceCountersTable** с сохранением данных диагностики в CSV-файл. После сохранения счетчиков производительности в CSV-файл можно использовать возможности построения графиков в Microsoft Excel или любой другой инструмент для визуализации данных.  Не забудьте добавить ссылку на библиотеку Microsoft.WindowsAzure.Storage.dll, которая включена в пакет Azure SDK для .NET в версии с октября 2012 г. Эта сборка устанавливается в каталог %Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\version-num\ref\.

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Table;

		...

		// Get the connection string. When using Azure Cloud Services, it is recommended 
		// you store your connection string using the Azure service configuration
		// system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type 
		// to retrieve your storage connection string.  If you're not using Cloud Services, it's
		// recommended that you store the connection string in your web.config or app.config file.
		// Use the ConfigurationManager type to retrieve your storage connection string.
		
		string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
		//string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;
            
		// Get a reference to the storage account using the connection string.  You can also use the development storage account (Storage Emulator)
		// for local debugging.              
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

Сущности сопоставляются с объектами C# с помощью настраиваемого класса, производного от **TableEntity**. Следующий код определяет класс сущностей, который представляет собой счетчик производительности в таблице **WADPerformanceCountersTable**.

		public class PerformanceCountersEntity : TableEntity
		{
			public long EventTickCount { get; set; }
			public string DeploymentId { get; set; }
			public string Role { get; set; }
			public string RoleInstance { get; set; }
			public string CounterName { get; set; }
			public double CounterValue { get; set; }                
		}

## <a name="nextsteps"> </a>Дальнейшие действия

Вы изучили основные сведения о сборе данных счетчиков производительности. Дополнительные сведения о реализации более сложных сценариев устранения неполадок см. по следующим ссылкам.

- [Советы и рекомендации по устранению неполадок при разработке приложений Azure][]
- [Мониторинг облачных служб][]
- [Практическое руководство по использованию блока приложения автомасштабирования][]
- [Создание гибких и устойчивых облачных приложений]

## <a name="additional"> </a>Дополнительные ресурсы

- [Включение диагностики в Azure][]
- [Сбор данных журнала с помощью системы диагностики Azure][]
- [Отладка приложения Azure][]


  [Общие сведения о создании и использовании счетчиков производительности в приложении Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh411520.aspx
  [Предварительные требования]: #prereqs
  [Шаг 1. Сбор и хранение данных счетчиков производительности]: #step1
  [Шаг 2. (Необязательно) Создание пользовательских счетчиков производительности]: #step2
  [Шаг 3. Запрос данных счетчика производительности]: #step3
  [Дальнейшие действия]: #nextsteps
  [Дополнительные ресурсы]: #additional
  
  [Сбор данных журнала с помощью системы диагностики Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433048.aspx
  [Отладка приложения Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee405479.aspx
  [Практическое руководство по использованию блока приложения автомасштабирования]: http://www.windowsazure.com/ru-ru/develop/net/how-to-guides/autoscaling/
  [Советы и рекомендации по устранению неполадок при разработке приложений Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh771389.aspx
  [Включение диагностики в Azure]: https://www.windowsazure.com/ru-ru/develop/net/common-tasks/diagnostics/
  [Использование службы хранилища таблиц]: http://www.windowsazure.com/ru-ru/develop/net/how-to-guides/table-services/
  [Обозреватель хранилища Azure]: http://azurestorageexplorer.codeplex.com/
  
  [Java]: http://www.windowsazure.com/ru-ru/develop/java/how-to-guides/table-service/
  [Python]: http://www.windowsazure.com/ru-ru/develop/python/how-to-guides/table-service/
  [PHP]: http://www.windowsazure.com/ru-ru/develop/php/how-to-guides/table-service/
  
  [Создание гибких и устойчивых облачных приложений]: http://msdn.microsoft.com/ru-ru/library/hh680949(PandP.50).aspx
  [Портал управления Azure]: http://manage.windowsazure.com
  [Диспетчер диагностики Azure]: http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx
  [Мониторинг облачных служб]: https://www.windowsazure.com/ru-ru/manage/services/cloud-services/how-to-monitor-a-cloud-service/

