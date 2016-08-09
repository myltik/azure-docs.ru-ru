<properties pageTitle="Телеметрия служб мультимедиа Azure с использованием .NET | Microsoft Azure" 
	description="В этой статье показано, как использовать телеметрию службы мультимедиа Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/23/2016"   
	ms.author="juliako"/>

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

## Использование данных телеметрии

Данные телеметрии записываются в таблицу службы хранилища Azure в учетной записи хранения, указанной при настройке данных телеметрии для учетной записи служб мультимедиа. Система телеметрии будет создавать отдельную таблицу для данных по каждому новому дню в 00:00 UTC. К примеру, с именем TelemetryMetrics20160321, где 20160321 — это дата создания таблицы. Для каждого дня будет создана отдельная таблица.

Можно запрашивать в таблицах следующие данные метрик.

### Журнал StreamingEndpoint

Вы можете запрашивать следующие метрики StreamingEndPoint.

Свойство|Описание|Образец значения
---|---|---
**PartitionKey**|Получает ключ секции записи.|60b71b0f6a0e4d869eb0645c16d708e1\_6efed125eef44fb5b61916edc80e6e23
**RowKey**|Получает ключ строки записи.|00959\_00000
**AccountId**|Получает идентификатор учетной записи служб мультимедиа.|6efed125-eef4-4fb5-b619-16edc80e6e23
**StreamingEndpointId**|Получает идентификатор конечной точки потоковой передачи служб мультимедиа.|d17ec9e4-a5d4-033d-0c36-def70229f06f
**ObservedTime**|Получает время обнаружения метрики.|20/01/16 23:44:01
**HostName**|Получает имя узла конечной точки потоковой передачи.|builddemoserver.origin.mediaservices.windows.net
**StatusCode**|Получает код состояния.|200
**ResultCode**|Получает код результата.|S\_OK
**RequestCount**|Получает число запросов.|3
**Передано байт**|Получает число отправленных байт.|2987358
**ServerLatency**|Получает значение задержки сервера (включая хранилище).|129
**EndToEndLatency**|Получает полное время выполнения запроса.|250


### Состояние динамического канала

Вы можете запрашивать следующие метрики динамического канала.

Свойство|Описание|Образец значения
---|---|---
**PartitionKey**|Получает ключ секции записи.|60b71b0f6a0e4d869eb0645c16d708e1\_0625cc45918e4f98acfc9a33e8066628
**RowKey**|Получает ключ строки записи.|13872\_00005
**AccountId**|Получает идентификатор учетной записи служб мультимедиа.|6efed125-eef4-4fb5-b619-16edc80e6e23
**ChannelId**|Получает идентификатор канала служб мультимедиа.|
**ObservedTime**|Получает время обнаружения метрики.|21/01/2016 20:08:49
**CustomAttributes**|Получает настраиваемые атрибуты.|
**TrackType**|Получает тип отслеживания.|video
**TrackName**|Получает имя отслеживания.|video
**Bitrate**|Получает значение скорости.|785000
**IncomingBitrate**|Получает значение входящей скорости.|784548
**OverlapCount**|Получает число перекрытий.|0
**DiscontinuityCount**|Получает число нарушений.|0
**LastTimestamp**|Получает последнюю метку времени.|1800488800
 
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
	

	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
		            INotificationEndPoint notificationEndPoint = 
		                          _context.NotificationEndPoints.Create("monitoring", 
								  NotificationEndPointType.AzureTable, GetTableEndPoint());

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

<!---HONumber=AcomDC_0727_2016-->