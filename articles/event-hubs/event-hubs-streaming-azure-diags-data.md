.<properties
	pageTitle="Потоковая передача данных системы диагностики Azure по критическому пути с помощью концентраторов событий | Microsoft Azure"
	description="Эта статья содержит полное описание настройки системы диагностики Azure для использования концентраторов событий. Она также содержит указания для распространенных сценариев."
	services="event-hubs"
	documentationCenter="na"
	authors="sethmanheim"
	manager="timlt"
	editor="" />
<tags
	ms.service="event-hubs"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="07/14/2016"
	ms.author="sethm" />

# Потоковая передача данных системы диагностики Azure по критическому пути с помощью концентраторов событий

Система диагностики Azure позволяет удобно собирать данные метрик и журналов от виртуальных машин облачных служб и передавать результаты в службу хранилища Azure. Начиная с марта 2016 года (пакет SDK версии 2.9) систему диагностики Azure можно использовать для приема информации из полностью пользовательских источников данных и передачи данных по критическому пути за несколько секунд с помощью [концентраторов событий Azure](https://azure.microsoft.com/services/event-hubs/).

Поддерживаемые типы данных включают:

- Трассировка событий Windows (ETW)
- Счетчики производительности
- Журналы событий Windows
- Журналы приложений
- Журналы инфраструктуры системы диагностики Azure

В этой статье полностью описан процесс настройки системы диагностики Azure с использованием концентраторов событий. В ней также описаны наиболее распространенные сценарии:

- Как настроить журналы и метрики, данные которых передаются в концентраторы событий.
- Как изменить конфигурации в каждой среде.
- Как просмотреть данные потока концентраторов событий.
- Как устранить неполадки подключения.

## Предварительные требования

Прием данных с помощью концентраторов событий в системе диагностики Azure поддерживается для облачных служб, виртуальных машин, наборов масштабирования виртуальных машин и Service Fabric, начиная с выпуска пакета SDK для Azure 2.9 и соответствующих инструментов Azure для Visual Studio.

- Расширение системы диагностики Azure версии 1.6 (в [пакете SDK для Azure для .NET 2.9 или более поздней версии](https://azure.microsoft.com/downloads/) оно используется по умолчанию).
- [Visual Studio 2013 или более поздней версии](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- Наличие конфигураций системы диагностики Azure в приложении, использующих *WADCFGX*-файл и один из следующих методов:
	- Visual Studio: [настройка системы диагностики для облачных служб и виртуальных машин Azure](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).
	- Windows PowerShell: [включение диагностики в облачных службах Azure с помощью PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md).
- Пространство имен концентраторов событий, подготовленное в соответствии с инструкциями в статье [Приступая к работе с концентраторами событий](./event-hubs-csharp-ephcs-getstarted.md).

## Подключение системы диагностики Azure к приемнику концентраторов событий

Система диагностики Azure по умолчанию всегда принимает журналы и метрики в учетную запись хранения Azure. Приложение может также передавать данные в концентраторы событий. Для этого необходимо добавить новый раздел **Sinks** в элемент **WadCfg** раздела **PublicConfig** в *WADCFGX*-файле. В Visual Studio *WADCFGX*-файл доступен по следующему пути: **Проект облачной службы** > **Роли** > **(имя роли)** > **diagnostics.wadcfgx**.

```
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```

В этом примере в качестве URL-адреса концентратора событий устанавливается полное имя пространства имен для концентратора событий (пространство имен концентраторов событий + "/" + имя концентратора событий).

URL-адрес концентратора событий отображается на [портале Azure](http://go.microsoft.com/fwlink/?LinkID=213885) на панели мониторинга концентраторов событий.

Значение строки **Приемник** может быть любым, достаточно использовать одну и ту же строку во всем файле конфигурации.

> [AZURE.NOTE]  В этом разделе могут быть настроены дополнительные приемники, например *applicationInsights*. В системе диагностики Azure можно определить один или несколько приемников, при условии, что каждый приемник также объявляется в разделе **PrivateConfig**.

Приемник концентраторов событий необходимо также объявить и определить в разделе **PrivateConfig** *WADCFGX*-файла конфигурации.

```
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="" key="" endpoint="" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
</PrivateConfig>
```

Значение `SharedAccessKeyName` должно соответствовать ключу подписанного URL-адреса (SAS) и политике, которая была определена в пространстве имен **концентраторов событий**. Перейдите к панели мониторинга концентраторов событий на [портале Azure](https://manage.windowsazure.com), выберите вкладку **Настройка** и настройте именованную политику (например, SendRule) с разрешениями *Отправка*. Параметр **StorageAccount** также объявляется в разделе **PrivateConfig**. Указанные значения изменять не нужно, если они работают. В этом примере мы оставили значения пустым. Это значит, что их задаст подчиненный ресурс-контейнер. Например, файл конфигурации среды *ServiceConfiguration.Cloud.cscfg* задает соответствующие имена и ключи среды.

> [AZURE.WARNING] Ключ SAS концентраторов событий хранится в виде обычного текста в *WADCFGX*-файле. Этот ключ часто возвращается в систему управления версиями или доступен как ресурс-контейнер на сервере сборки, поэтому следует защитить его надлежащим образом. Рекомендуется использовать ключ SAS с разрешениями *Только отправка*, чтобы злоумышленник мог только записывать данные в концентратор событий, но не мог прослушивать концентратор событий или управлять им.

## Настройка журналов и метрик системы диагностики Azure для приема данных с помощью концентраторов событий

Как обсуждалось ранее, все пользовательские данные диагностики и данные диагностики по умолчанию (т. е. метрики и журналы) автоматически передаются в службу хранилища Azure с заданными интервалами. Используя концентраторы событий (и любые дополнительные приемники), можно указать любой корневой или листовой узел в иерархии, в который будет передавать данные концентратор событий. К этим данным относятся события трассировки Windows, счетчики производительности, журналы событий Windows и журналы приложений.

Важно учитывать фактическое количество точек данных, которые должны быть переданы в концентраторы событий. Обычно разработчики передают по критическому пути с низкой задержкой данные, которые должны быть быстро использованы и интерпретированы. Примерами являются системы, отслеживающие предупреждения, или правила автомасштабирования. Разработчик также может настроить альтернативное хранилище данных анализа или поиска, например, Stream Analytics, ElasticSearch, пользовательской системы мониторинга или сторонней системы мониторинга, которую вы предпочитаете.

Ниже приведены некоторые примеры конфигурации.

```
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
```

В следующем примере приемник применяется к родительскому узлу **PerformanceCounters** в иерархии, что означает, что все дочерние счетчики производительности **PerformanceCounters** будут отправляться в концентраторы событий.

```
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
```

В предыдущем примере приемник применяется только к трем счетчикам: **Запросов в очереди**, **Отклонено запросов** и **% загруженности процессора**.

В следующем примере показано, как разработчик может ограничить объем данных, отправляемых в качестве важных метрик, которые используются для оценки работоспособности службы.

```
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```

В этом примере приемник применяется к журналам и принимает только события трассировки уровня "Ошибка".

## Развертывание и обновление приложения облачной службы и конфигурации системы диагностики

Простейший способ развертывания приложения и конфигурации приемника концентраторов событий — воспользоваться Visual Studio. Чтобы просмотреть и изменить *WADCFGX*-файл, откройте его в Visual Studio, после чего измените его и сохраните. Следует выбрать **Проект облачной службы** > **Роли** > **(имя роли)** > **diagnostics.wadcfgx**.

На этом этапе все действия развертывания и обновления в Visual Studio и Visual Studio Team System, а также все команды или сценарии, которые основаны на MSBuild и используют **/t:publish** в качестве цели, включают этот *WADCFGX*-файл в процессе упаковки. Кроме того, при операциях развертывания и обновления этот файл развертывается в Azure с помощью соответствующего расширения агента системы диагностики Azure на ваших виртуальных машинах.

После развертывания приложения и конфигурации системы диагностики Azure вы увидите активность на панели мониторинга концентратора событий. Это означает, что вы можете просмотреть данные, передаваемые по критическому пути, в выбранном клиенте прослушивателя или инструменте анализа.

На следующем рисунке панель мониторинга концентратора событий отображает функционирующую отправку данных диагностики в концентратор событий, которая начинается после 23:00. Это время, когда приложение было развернуто с обновленным *WADCFGX*-файлом и приемник был настроен должным образом.

![][0]

> [AZURE.NOTE] При обновлении файла конфигурации системы диагностики Azure (WADCFGX-файла) рекомендуется распространять обновления, а также конфигурацию, на все приложение с помощью функции публикации в Visual Studio или сценария Windows PowerShell.

## Просмотр данных, передаваемых по критическому пути

Как уже говорилось, существует множество сценариев для прослушивания и обработки данных концентраторов событий.

Один из простых подходов заключается в создании небольшого тестового консольного приложения, которое будет прослушивать концентратор событий и выводить данные в поток вывода. В консольное приложение можно добавить приведенный ниже код. Пояснения к нему приведены в разделе [Приступая к работе с концентраторами событий](./event-hubs-csharp-ephcs-getstarted.md).

Обратите внимание, что консольное приложение должно включать в себя [пакет Nuget узла обработчика событий](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

Не забудьте заменить значения в угловых скобках в функции **Main** соответствующими значениями для своих ресурсов.

```
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
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event Hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
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
```

## Устранение неполадок приемника концентраторов событий

- В концентраторе событий не отображаются входящие или исходящие события, которые должны отображаться.

	Убедитесь, что концентратор событий успешно подготовлен. Все сведения о подключении в разделе **PrivateConfig** *WADCFGX*файла должны соответствовать сведениям, указанным на портале для вашего ресурса. Убедитесь, что на портале задана политика SAS (в данном примере это SendRule) и предоставлено разрешение *Отправка*.

- После обновления в концентраторе событий не отображаются входящие или исходящие события.

	Сначала убедитесь в том, что параметры концентратора событий и конфигурации настроены правильно, как описано выше. Иногда **PrivateConfig** сбрасывается при обновлении развертывания. Для устранения проблемы рекомендуется внести все изменения в *WADCFGX*-файл в проекте, а затем полностью обновить приложение. Если это невозможно, убедитесь, что при обновлении системы диагностики распространяется полный раздел **PrivateConfig** с ключом SAS.

- Все предложенные действия выполнены, но концентратор событий все равно не работает.

	Попробуйте найти в службе хранилища Azure таблицу, которая содержит журналы и ошибки для самой системы диагностики Azure: **WADDiagnosticInfrastructureLogsTable**. Для этого можно использовать, например, [обозреватель хранилищ Azure](http://www.storageexplorer.com). Подключитесь к соответствующей учетной записи хранения, просмотрите эту таблицу и выполните запрос по метке времени за последние 24 часа. С помощью этого инструмента можно экспортировать информацию в CSV-файл и открыть его в таком приложении, как Microsoft Excel. Excel упрощает поиск строк карточек, таких как **EventHubs**, чтобы узнать, какая обнаружена ошибка.

## Дальнейшие действия

• [Дополнительные сведения о концентраторах событий](https://azure.microsoft.com/services/event-hubs/)

## Приложение. Полный пример файла конфигурации системы диагностики Azure (WADCFGX-файл)

```
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
```

Дополнительный файл *ServiceConfiguration.Cloud.cscfg* для этого примера выглядит следующим образом.

```
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

.<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png

<!---HONumber=AcomDC_0817_2016-->