---
title: "Настройка телеметрии служб мультимедиа Azure с использованием .NET | Документация Майкрософт"
description: "В этой статье показано, как использовать телеметрию служб мультимедиа Azure с помощью пакета SDK для .NET."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: f8f55e37-0714-49ea-bf4a-e6c1319bec44
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: d693bc0de2f8a03d67b346f3b2d4693284ae4d71
ms.openlocfilehash: da7d4f87fdcaca6517fa95152d42c138533772b9


---

# <a name="configuring-azure-media-services-telemetry-with-net"></a>Настройка телеметрии служб мультимедиа Azure с использованием .NET

В этом разделе описываются общие действия по настройке телеметрии служб мультимедиа Azure (AMS) с помощью пакета SDK для .NET. 

>[!NOTE]
>Подробнее о том, что такое телеметрия AMS и как ее использовать, описывается в этом[обзоре](media-services-telemetry-overview.md).

Данные телеметрии можно использовать одним из следующих способов.

- Можно считывать данные непосредственно из хранилища таблиц Azure (например, с помощью пакета SDK для хранилища). Описание таблиц хранилища телеметрии см. в подразделе **Использование данных телеметрии** [этого](https://msdn.microsoft.com/library/mt742089.aspx) раздела.

Или

- Для чтения данных из хранилища можно использовать поддержку, реализованную в пакете SDK служб мультимедиа для .NET. В этом разделе показано, как включить телеметрию для указанной учетной записи AMS и как запросить метрики с помощью пакета SDK служб мультимедиа Azure для .NET.  

## <a name="configuring-telemetry-for-a-media-services-account"></a>Настройка данных телеметрии для учетной записи служб мультимедиа

Чтобы включить телеметрию, необходимо выполнить следующие шаги:

- Получите учетные данные учетной записи хранения, подключенной к учетной записи служб мультимедиа. 
- Создайте конечную точку уведомления, задав для параметра **EndPointType** значение **AzureTable** и указав в качестве адреса endPointAddress таблицу хранилища.

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

## <a name="consuming-telemetry-information"></a>Использование данных телеметрии

Сведения об использовании данных телеметрии см. в [этом](media-services-telemetry-overview.md) разделе.
 
## <a name="example"></a>Пример  
    
В примере в этом разделе предполагается, что в файле App.config имеется следующий раздел.
    
    <appSettings>
      <add key="MediaServicesAccountName" value="ams_acct_name" />
      <add key="MediaServicesAccountKey" value="ams_acct_key" />
      <add key="MediaServicesAccountID" value="ams_acct_id" />
      <add key="StorageAccountName" value="storage_name" />
      <add key="StorageAccountKey" value="storage_key" />
    </appSettings>

В примере ниже показано, как включить телеметрию для указанной учетной записи AMS и запросить метрики с помощью пакета SDK служб мультимедиа Azure для .NET.  

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    
    namespace AMSMetrics
    {
        class Program
        {
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            private static readonly string _mediaServicesAccountID =
                ConfigurationManager.AppSettings["MediaServicesAccountID"];
            private static readonly string _mediaServicesStorageAccountName =
                ConfigurationManager.AppSettings["StorageAccountName"];
            private static readonly string _mediaServicesStorageAccountKey =
                ConfigurationManager.AppSettings["StorageAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            private static IStreamingEndpoint _streamingEndpoint = null;
            private static IChannel _channel = null;
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                _streamingEndpoint = _context.StreamingEndpoints.FirstOrDefault();
                _channel = _context.Channels.FirstOrDefault();
    
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
                            new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
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
                Console.WriteLine(string.Format("Telemetry for streaming endpoint '{0}'", _streamingEndpoint.Name));
    
                var end = DateTime.UtcNow;
                var start = DateTime.UtcNow.AddHours(-5);
    
                // Get some streaming endpoint metrics.
                var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
                        GetTableEndPoint(),
                        _mediaServicesStorageAccountKey,
                        _mediaServicesAccountID,
                        _streamingEndpoint.Id,
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
                if (_channel == null)
                {
                    Console.WriteLine("There are no channels in this AMS account");
                    return;
                }
    
                Console.WriteLine(string.Format("Telemetry for channel '{0}'", _channel.Name));
    
                var end = DateTime.UtcNow;
                var start = DateTime.UtcNow.AddHours(-5);
    
                // Get some channel metrics.
                var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
                    GetTableEndPoint(),
                    _mediaServicesStorageAccountKey,
                    _mediaServicesAccountID,
                   _channel.Id,
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


## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!--HONumber=Nov16_HO5-->


