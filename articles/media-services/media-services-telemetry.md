<properties pageTitle="Телеметрия служб мультимедиа Azure с использованием .NET | Microsoft Azure" 
	description="В этой статье показано, как использовать телеметрию службы мультимедиа Azure." 
	services="" 
	documentationCenter=""
	authors="juliako" />

# Телеметрия служб мультимедиа Azure с использованием .NET

## Обзор

Благодаря функциям телеметрии и мониторинга служб мультимедиа клиенты могут получать доступ к данным метрик для своих служб. Текущая версия поддерживает данные телеметрии для канала и конечной точки потоковой передачи StreamingEndpoint. Телеметрию можно настроить на уровне детализации компонента. Существует два уровня детализации: "Обычный" и "Подробный". Текущая версия поддерживает только "Обычный".

Данные телеметрии записываются в учетную запись хранения Azure, предоставленную клиентом (эту учетную запись нужно подключить к учетной записи служб мультимедиа). Данные телеметрии записываются в таблицу службы хранилища Azure в указанной учетной записи хранения. Система телеметрии будет создавать отдельную таблицу для данных по каждому новому дню в 00:00 UTC. К примеру, с именем TelemetryMetrics20160321, где 20160321 — это дата создания таблицы. Для каждого дня будет создана отдельная таблица.

Система телеметрии не предусматривает период хранения данных и автоматическое удаление старых записей. Поэтому необходимо контролировать и удалять их самостоятельно. Это несложно, так как для каждого дня создается отдельная таблица. Можно также просто удалить старые таблицы.

В этом разделе показано, как включить данные телеметрии для указанных служб AMS и как выполнить запрос метрик с помощью .NET.

## Настройка данных телеметрии для учетной записи служб мультимедиа

Чтобы включить телеметрию, необходимо выполнить следующие шаги:

- Получите учетные данные учетной записи хранения, подключенной к учетной записи служб мультимедиа.
- Создайте конечную точку уведомления, задав для параметра **EndPointType** значение **AzureTable** и указав в качестве адреса endPontAddress таблицу хранилища.

	    INotificationEndPoint notificationEndPoint = 
	                  _context.NotificationEndPoints.Create("monitoring", 
	                  NotificationEndPointType.AzureTable,
	                  "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");

- Создайте параметры конфигурации мониторинга служб, которые требуется отслеживать. Разрешено не более одного параметра конфигурации мониторинга.
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });


## Журнал StreamingEndpoint

###Доступные метрики

Вы можете запрашивать следующие метрики StreamingEndPoint.

- **PartitionKey** получает ключ раздела записи.
- **RowKey** получает ключ строки записи.
- **AccountId** получает идентификатор учетной записи служб мультимедиа.
- **AccountId** получает идентификатор конечной точки потоковой передачи служб мультимедиа.
- **ObservedTime** получает время обнаружения метрики.
- **HostName** получает имя узла конечной точки потоковой передачи.
- **StatusCode** получает код состояния.
- **ResultCode** получает код результата.
- **RequestCount** получает число запросов.
- **BytesSent** получает число отправленных байт.
- **ServerLatency** получает значение задержки сервера.
- **EndToEndLatency** получает полное время выполнения запроса.

###Пример результата запроса конечной точки потоковой передачи

![Запрос конечной точки потоковой передачи](media/media-services-telemetry/media-services-telemetry01.png)


## Состояние динамического канала

###Доступные метрики

Вы можете запрашивать следующие метрики динамического канала.

- **PartitionKey** получает ключ раздела записи.
- **RowKey** получает ключ строки записи.
- **AccountId** получает идентификатор учетной записи служб мультимедиа.
- **ChannelId** получает идентификатор канала служб мультимедиа.
- **ObservedTime** получает время обнаружения метрики.
- **CustomAttributes** получает настраиваемые атрибуты.
- **TrackType** получает тип отслеживания.
- **TrackType** получает имя отслеживания.
- **Bitrate** получает значение скорости.
- **IncomingBitrate** получает значение входящей скорости.
- **OverlapCount** получает число перекрытий.
- **DiscontinuityCount** возвращает число нарушений.
- **LastTimestamp** получает последнюю метку времени.
 
###Пример результата запроса динамического канала

![Запрос конечной точки потоковой передачи](media/media-services-telemetry/media-services-telemetry01.png)

## Пример метрики StreamingEndpoint
		
	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace AMSMetrics
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	        private static readonly string _mediaServicesAccountID =
	            ConfigurationManager.AppSettings["MediaServicesAccountID"];
	        private static readonly string _streamingEndpointID =
	            ConfigurationManager.AppSettings["StreamingEndpointID"];
	        private static readonly string _mediaServicesStorageAccountName =
	            ConfigurationManager.AppSettings["StorageAccountName"];
	        private static readonly string _mediaServicesStorageAccountKey =
	            ConfigurationManager.AppSettings["StorageAccountKey"];
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            INotificationEndPoint notificationEndPoint = 
	                          _context.NotificationEndPoints.Create("monitoring", NotificationEndPointType.AzureTable, GetTableEndPoint());
	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
	                    new List<ComponentMonitoringSetting>()
	                    {
	                        new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
	                    });
	            }
	
	            //Print metrics for a Streaming Endpoint.
	            PrintStreamingEndpointMetrics();
	
	            Console.ReadLine();
	        }
	
	        private static string GetTableEndPoint()
	        {
	            return "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/";
	        }
	
	        private static void PrintStreamingEndpointMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some streaming endpoint metrics.
	            var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
	                    GetTableEndPoint(),
	                    _mediaServicesStorageAccountKey,
	                    _mediaServicesAccountID,
	                    _streamingEndpointID,
	                    start,
	                    end);
	
	
	            Console.Title = "Streaming endpoint metrics:";
	
	            foreach (var log in res)
	            {
	                Console.WriteLine("AccountId: {0}", log.AccountId);
	                Console.WriteLine("BytesSent: {0}", log.BytesSent);
	                Console.WriteLine("EndToEndLatency: {0}", log.EndToEndLatency);
	                Console.WriteLine("HostName: {0}", log.HostName);
	                Console.WriteLine("ObservedTime: {0}", log.ObservedTime);
	                Console.WriteLine("PartitionKey: {0}", log.PartitionKey);
	                Console.WriteLine("RequestCount: {0}", log.RequestCount);
	                Console.WriteLine("ResultCode: {0}", log.ResultCode);
	                Console.WriteLine("RowKey: {0}", log.RowKey);
	                Console.WriteLine("ServerLatency: {0}", log.ServerLatency);
	                Console.WriteLine("StatusCode: {0}", log.StatusCode);
	                Console.WriteLine("StreamingEndpointId: {0}", log.StreamingEndpointId);
	                Console.WriteLine();
	            }
	
	            Console.WriteLine();
	        }

	        private static void PrintChannelMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some channel metrics.
	            var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
	                GetTableEndPoint(),
	                _mediaServicesStorageAccountKey,
	                _mediaServicesAccountName,
	                _context.Channels.FirstOrDefault().Id,
	                start,
	                end);
	
	            // Print the channel metrics.
	            Console.WriteLine("Channel metrics:");
	
	            foreach (var channelHeartbeat in channelMetrics.OrderBy(x => x.ObservedTime))
	            {
	                Console.WriteLine(
	                    "    Observed time: {0}, Last timestamp: {1}, Incoming bitrate: {2}",
	                    channelHeartbeat.ObservedTime,
	                    channelHeartbeat.LastTimestamp,
	                    channelHeartbeat.IncomingBitrate);
	            }
	
	            Console.WriteLine();
	        }
	    }
	}


##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Дальнейшие действия
 
См. схемы обучения по службам мультимедиа Azure, которые позволят вам узнать о возможностях, предлагаемых AMS.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

<!---HONumber=AcomDC_0720_2016-->