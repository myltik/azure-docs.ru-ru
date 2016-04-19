<properties 
   pageTitle="Потоковая передача данных системы диагностики Azure в горячем пути с помощью концентраторов событий"
   description="Эта статья содержит полное описание настройки системы диагностики Azure с помощью концентраторов событий. Она также содержит указания для распространенных сценариев."
   services="event-hubs"
   documentationCenter="na"
   authors="tomarcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/30/2016"
   ms.author="tarcher" />

# Потоковая передача данных системы диагностики Azure в горячем пути с помощью концентраторов событий

## Обзор
Система диагностики Azure позволяет удобно собирать данные метрик и журналов от вычислительных виртуальных машин и передавать их в службу хранилища Azure. Начиная с марта 2016 года (пакет SDK версии 2.9) систему диагностики Azure можно подключать к полностью пользовательским источникам данных и передавать данные по горячему пути за несколько секунд с помощью концентраторов событий Azure.

Поддерживаемые типы данных включают:

- События ETW (трассировка событий Windows)
- Счетчики производительности
- Журналы событий Windows 
- Журналы приложений
- Журналы инфраструктуры системы диагностики Azure
 
В этой статье полностью описан процесс настройки системы диагностики Azure с использованием концентраторов событий. Также приведены инструкции для распространенных сценариев, включающих: выбор журналов и метрик, данные которых будут отправляться в концентраторы событий; изменение конфигурации в каждой среде; один из множества примеров просмотра потоковых данных для концентраторов событий; устранение неполадок подключения.

## Предварительные требования
Прием данных с помощью концентраторов событий в системе диагностики Azure осуществляется для всех типов вычислительных ресурсов (облачных служб, виртуальных машин, масштабируемых наборов виртуальных машин и Service Fabric), поддерживающих WAD, начиная с пакета SDK для Azure версии 2.9 и соответствующих инструментов Azure для Visual Studio.
  
- Расширение системы диагностики Azure версии 1.6 (в [пакете SDK для Azure для .NET 2.9 или более поздней версии](https://azure.microsoft.com/downloads/) оно используется по умолчанию)
- [Visual Studio 2013 или более поздней версии](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- Предварительная настройка системы диагностики Azure для приложения в файле *.wadcfgx* с помощью одного из следующих методов:
	- Visual Studio: [настройка системы диагностики для облачных служб и виртуальных машин Azure](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
	- Windows PowerShell: [включение диагностики в облачных службах Azure с помощью PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
- Пространство имен концентраторов событий, подготовленное в соответствии с инструкциями в статье [Приступая к работе с концентраторами событий](./event-hubs-csharp-ephcs-getstarted.md)

## Подключение системы диагностики Azure к концентраторам событий
Система диагностики Azure по умолчанию всегда принимает журналы и метрики в учетную запись службы хранилища Azure. Приложение также может передавать данные в концентраторы событий, для этого необходимо добавить новый раздел **Sinks** в элемент **WadCfg** раздела **PublicConfig** в файле *.wadcfgx*. В Visual Studio файл *.wadcfgx* расположен по следующему пути: *Проект облачной службы > Роли > (Имя роли) > diagnostics.wadcfgx*.
  
	  <SinksConfig>
	    <Sink name="HotPath">
	      <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
	    </Sink>
	  </SinksConfig>

В этом примере в качестве URL-адреса концентратора событий устанавливается полное имя пространства имен для концентратора событий (пространство имен служебной шины + "/" + имя концентратора событий).

URL-адрес концентратора событий отображается на [классическом портале Azure](https://manage.windowsazure.com) на панели мониторинга концентраторов событий.

Значение строки **Приемника** может быть любым, достаточно использовать одну и ту же строку во всем файле конфигурации.

**Примечание.** В этом разделе могут быть настроены дополнительные приемники, например "applicationInsights". В системе диагностики Azure можно определить один или несколько приемников, при условии, что каждый приемник также объявляется в разделе **PrivateConfig**.

Приемники концентраторов событий также необходимо объявить и определить в разделе **PrivateConfig** конфигурационного файла *.wadcfgx*.

	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
	  </PrivateConfig>

Параметр **SharedAccessKeyName** должен соответствовать ключу SAS и политике, которая была определена в пространстве имен **ServiceBus/EventHub**. Это можно сделать, открыв панель мониторинга концентраторов событий на [классическом портале Azure](https://manage.windowsazure.com), щелкнув на вкладке **Настройка** и настроив именованную политику (например "SendRule") с разрешениями *Send*. Параметр **StorageAccount** также объявляется в **PrivateConfig**. Указанные значения изменять не нужно, особенно если они работают. В этом примере мы оставляем значения пустыми, это означает, что они будут установлены в нижележащем ресурсе; например, соответствующие имена и ключи среды будут установлены в файле *ServiceConfiguration.Cloud.cscfg*.

>[AZURE.WARNING] Учтите, что ключ SAS концентратора событий хранится в виде обычного текста в файле *.wadcfgx*. Этот файл часто попадает в систему управления версиями или доступен как ресурс на сервере сборки, поэтому следует защитить его надлежащим образом. Рекомендуется использовать ключ SAS с разрешениями *Send only*, чтобы злоумышленник мог в лучшем случае записывать данные в концентратор событий, но не мог прослушивать концентратор событий или управлять им.

## Настройка журналов и метрик системы диагностики Azure для приема данных из концентраторов событий
Как обсуждалось ранее, все пользовательские данные диагностики и данные диагностики по умолчанию (т. е. метрики и журналы) автоматически передаются в службу хранилища Azure с заданными интервалами. С концентраторами событий (и любыми дополнительными приемниками) можно указать любой корневой или листовой узел в иерархии, который будет передавать данные в концентратор событий. К этим данным относятся события трассировки Windows, счетчики производительности, журналы событий Windows и журналы приложений.

Важно учитывать фактическое количество точек данных, которые должны быть переданы в концентраторы событий. Обычно разработчики используют такую схему для данных горячего пути с низкой задержкой, которые должны использоваться и интерпретироваться быстро (например, в системах мониторинга оповещений или правилах автоматического масштабирования). Она также может использоваться для настройки альтернативного хранилища анализа или поиска — например Stream Analytics, ElasticSearch, пользовательской системы мониторинга или сторонней системы мониторинга, которую вы предпочитаете.

Ниже приведены некоторые примеры конфигурации.

        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>

В следующем примере приемник применяется к родительскому узлу **PerformanceCounters** в иерархии, что означает, что все дочерние счетчики производительности **PerformanceCounters** будут отправляться в концентраторы событий.

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
        </PerformanceCounters>

В примере выше приемник применяется только к трем счетчикам: "Запросы в очереди", "Отклоненные запросы" и "Загруженность процессора, %".

В следующем примере показано, как разработчик может ограничить объем данных, отправляемых в качестве важных метрик, используемых для оценки работоспособности службы.

        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />

В этом примере приемник применяется к журналам и принимает только события трассировки уровня "Ошибка".
 
## Развертывание и обновление приложения облачной службы и файла конфигурации системы диагностики

Простейший способ развертывания приложения с конфигурацией приемника для концентраторов событий — Visual Studio. Чтобы внести нужные изменения, откройте файл *.wadcfgx* в Visual Studio (он расположен по следующему пути: *Проект облачной службы > Роли > (Имя роли) > diagnostics.wadcfgx*) и сохраните файл после внесения изменений.

На этом этапе при каждом развертывании или обновлении развертывания в Visual Studio, Visual Studio Team System или командах или сценариях на основе MSBUILD (с целью /t:publish) в процесс упаковки будет включаться файл *.wadcfgx*. Этот файл также будет разворачиваться в Azure с помощью расширения для соответствующего агента WAD в ваших виртуальных машинах.
  
После успешного развертывания приложения и конфигурации системы диагностики Azure вы увидите действие на панели мониторинга концентратора событий. Это означает, что вы можете просмотреть данные горячего пути в прослушивателе или используемом средстве анализа.
 
На следующем рисунке показана панель мониторинга концентратора событий с успешной отправкой данных диагностики концентратору событий начиная примерно с 23:00, когда приложение было развернуто с обновленным файлом *.wadcfgx* и приемник был настроен правильно.

![][0]
  
>[AZURE.NOTE] При обновлении конфигурационного файла системы диагностики Azure (.wadcfgx) рекомендуется распространять обновления на все приложение и конфигурационный файл с помощью публикации в Visual Studio или сценария Windows PowerShell.

## Просмотр данных горячего пути

Как уже говорилось, существует множество сценариев для прослушивания и обработки данных концентраторов событий.
  
Один из простых подходов заключается в создании небольшого тестового консольного приложения, которое будет прослушивать концентратор событий и выводить данные в поток вывода. Код консольного приложения может быть следующим (дополнительные объяснения приведены в разделе [Приступая к работе с концентраторами событий](./event-hubs-csharp-ephcs-getstarted.md)).

Обратите внимание, что консольное приложение должно включать [пакет Nuget EventProcessor](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

Не забудьте заменить значения в угловых скобках в функции **Main** ниже соответствующими значениями для ваших ресурсов.

	//Console application code for EventHub test client
	using System;
	using System.Collections.Generic;
	using System.Diagnostics;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.ServiceBus.Messaging;
	
	namespace EventHubListener
	{
	    class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;
	
	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }
	
	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }
	
	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());
	
	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                    context.Lease.PartitionId, data));
	
	                foreach (var x in eventData.Properties)
	                {
	                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
	                }
	            }
	
	            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
	            {
	                await context.CheckpointAsync();
	                this.checkpointStopWatch.Restart();
	            }
	        }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            string eventHubConnectionString = "Endpoint= <Your Connection String>”
	            string eventHubName = "<EventHub Name>";
	            string storageAccountName = "<Storage Account Name>";
	            string storageAccountKey = "<Storage Account Key>”;
	            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
	
	            string eventProcessorHostName = Guid.NewGuid().ToString();
	            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
	            Console.WriteLine("Registering EventProcessor...");
	            var options = new EventProcessorOptions();
	            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
	            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
	
	            Console.WriteLine("Receiving. Press enter key to stop worker.");
	            Console.ReadLine();
	            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
	        }
	    }
	}

## Устранение неполадок приемников для концентраторов событий

- В концентраторе событий не отображаются входящие или исходящие события, которые должны отображаться.

	Убедитесь, что концентратор событий успешно подготовлен. Все сведения о подключении в разделе **PrivateConfig** файла *.wadcfgx* должны соответствовать сведениям, указанным на портале для вашего ресурса. Убедитесь, что на портале задана политика SAS (в данном примере "SendRule") и добавлено разрешение Send.

- После обновления в концентраторе событий не отображаются входящие или исходящие события.

	Во-первых, проверьте концентратор событий и сведения о подключении, как описано выше. Некоторые проблемы вызваны тем, что раздел **PrivateConfig** сбрасывается при обновлении развертывания. Для решения проблемы рекомендуется внести все изменения в файл *.wadcfgx* в проекте, а затем полностью обновить приложение. Если это невозможно, убедитесь, что при обновлении диагностики в файл конфигурации добавляется полный раздел **PrivateConfig** с ключом SAS.

- Я выполнил действия, описанные выше, и концентратор событий все равно не работает.

	Попробуйте найти в службе хранилища Azure таблицу, которая содержит журналы и ошибки для самой системы диагностики Azure: **WADDiagnosticInfrastructureLogsTable**. Для этого можно использовать, например, [Обозреватель хранилищ Azure](http://www.storageexplorer.com). Подключитесь к соответствующей учетной записи хранения, просмотрите эту таблицу и выполните запрос с использованием временной отметки "за последние 24 часа". С помощью этого средства можно экспортировать результаты в формат CSV и открыть их, например в Microsoft Excel. С помощью поиска по ключевым словам, например "EventHubs", в Excel легко увидеть, где возникла ошибка.

## Дальнейшие действия
• [Дополнительные сведения о концентраторах событий](https://azure.microsoft.com/services/event-hubs/)

## Приложение. Полный пример конфигурационного файла системы диагностики Azure (.wadcfgx)
	<?xml version="1.0" encoding="utf-8"?>
	<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
	        <Directories scheduledTransferPeriod="PT1M">
	          <IISLogs containerName="wad-iis-logfiles" />
	          <FailedRequestLogs containerName="wad-failedrequestlogs" />
	        </Directories>
	        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
	          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
	        </PerformanceCounters>
	        <WindowsEventLog scheduledTransferPeriod="PT1M">
	          <DataSource name="Application!*" />
	        </WindowsEventLog>
	        <CrashDumps>
	          <CrashDumpConfiguration processName="WaIISHost.exe" />
	          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
	          <CrashDumpConfiguration processName="w3wp.exe" />
	        </CrashDumps>
	        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
	      </DiagnosticMonitorConfiguration>
	      <SinksConfig>
	        <Sink name="HotPath">
	          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
	        </Sink>
	        <Sink name="applicationInsights">
	          <ApplicationInsights />
	          <Channels>
	            <Channel logLevel="Error" name="errors" />
	          </Channels>
	        </Sink>
	      </SinksConfig>
	    </WadCfg>
	    <StorageAccount />
	  </PublicConfig>
	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
	  </PrivateConfig>
	  <IsEnabled>true</IsEnabled>
	</DiagnosticsConfiguration>

Дополнительный файл *ServiceConfiguration.Cloud.cscfg* для этого примера выглядит следующим образом.

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
	  <Role name="MyFixIt.WorkerRole">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
	    </ConfigurationSettings>
	  </Role>
	</ServiceConfiguration>

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png

<!---HONumber=AcomDC_0406_2016-->