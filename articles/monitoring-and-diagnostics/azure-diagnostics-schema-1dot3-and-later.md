---
title: "Схема конфигурации системы диагностики Azure версии 1.3, 1.4, 1.5, 1.6, 1.7 | Документация Майкрософт"
description: "Схема версии 1.3 и более поздние версии для системы диагностики Azure поставляются в составе пакета SDK для Microsoft Azure."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/09/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 72b343df7df38a43e5d8fe72b453e56e29d0aba4
ms.openlocfilehash: b5d800db47284ca9fa82fc07ed67617ab32cd143
ms.lasthandoff: 02/22/2017


---
# <a name="azure-diagnostics-13-14-15-configuration-schema"></a>Схема конфигурации системы диагностики Azure версий 1.3, 1.4 и 1.5
> [!NOTE]
> Система диагностики Azure — это компонент, который используется для сбора данных счетчиков производительности и других статистических данных из виртуальных машин Azure, масштабируемых наборов виртуальных машин, Service Fabric и облачных служб.  Данная страница применяется только в том случае, если вы используете одну из этих служб.
>

Система диагностики Azure используется в сочетании с другими продуктами диагностики корпорации Майкрософт, такими как Azure Monitor, Application Insights и Log Analytics.

Файл конфигурации системы диагностики Azure используется для задания параметров конфигурации диагностики при запуске монитора диагностики.  

Эта страница предназначена для версий 1.3, 1.4 и 1.5 (пакет Azure SDK 2.4 и более поздней версии). Новые разделы конфигурации снабжены комментариями, указывающими, в какой версии они были добавлены.  

 Скачайте общедоступное определение схемы файла конфигурации, выполнив следующую команду PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Дополнительные сведения об использовании системы диагностики Azure см. в статье [Включение системы диагностики Azure в облачных службах Azure](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Пример файла конфигурации диагностики  
 В следующем примере показан типичный файл конфигурации диагностики.  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

    </WadCfg>  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" />
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" />
    </SecondaryStorageAccounts>
    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  

Ниже приведен эквивалент предыдущего XML-файла конфигурации в формате JSON.  
```json
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount"
}


{
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

## <a name="reading-this-page"></a>Чтение этой страницы  
 Приведенные ниже теги указаны примерно в том же порядке, что и в предыдущем примере.  Если вы не видите полное описание там, где оно предполагается, найдите соответствующий элемент или атрибут на странице.  

## <a name="common-attribute-types"></a>Общие типы атрибутов  
 Атрибут **scheduledTransferPeriod** присутствует в нескольких элементах. Это интервал между запланированными передачами в службу хранилища, округленный с точностью до ближайшей минуты. Значение относится к [типу данных XML "Duration"](http://www.w3schools.com/schema/schema_dtypes_date.asp).

## <a name="diagnostics-configuration-namespace"></a>Пространство имен конфигурации диагностики  
 Пространство имен XML для файла конфигурации диагностики:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="diagnosticsconfiguration-element"></a>Элемент DiagnosticsConfiguration  
 Добавлен в версии 1.3.  

 Элемент верхнего уровня в файле конфигурации диагностики.  

|Дочерние элементы|Описание|  
|--------------------|-----------------|  
|**PublicConfig**|обязательный параметр. Ознакомьтесь с описанием в другом разделе на этой странице.|  
|**PrivateConfig**|необязательный параметр. Ознакомьтесь с описанием в другом разделе на этой странице.|  
|**IsEnabled**|Логическое значение. Ознакомьтесь с описанием в другом разделе на этой странице.|  

## <a name="publicconfig-element"></a>Элемент PublicConfig  
 Описывает общедоступную конфигурацию диагностики.  

|Дочерние элементы|Описание|  
|--------------------|-----------------|  
|**WadCfg**|обязательный параметр. Ознакомьтесь с описанием в другом разделе на этой странице.|  
|**StorageAccount**|Имя учетной записи хранения Azure для хранения данных. Может также быть указан как параметр при выполнении командлета Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Каталог на виртуальной машине, в котором Monitoring Agent хранит данные событий. Если этот параметр не задан, используется каталог по умолчанию:<br /><br /> для рабочей роли или веб-роли: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> для виртуальной машины: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Ниже перечислены обязательные атрибуты.<br /><br /> - **path**: каталог в системе для использования системой диагностики Azure.<br /><br /> - **expandEnvironment**: позволяет раскрыть переменные среды в пути.|  

## <a name="wadcfg-element"></a>Элемент WadCFG  
 Позволяет определить и настроить сбор данных телеметрии.  

|Дочерние элементы|Описание|  
|--------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Обязательный элемент.<br /><br /> Необязательные атрибуты:<br /><br /> - **overallQuotaInMB**: максимальный объем пространства на локальном жестком диске, доступный для диагностических данных различного типа, собранных системой диагностики Azure. Значение по умолчанию составляет 5120 МБ.<br /><br /> - **useProxyServer**: позволяет настроить систему диагностики Azure для использования параметров прокси-сервера, указанных в настройках IE.|  
|**CrashDumps**|Ознакомьтесь с описанием в другом разделе на этой странице.|  
|**DiagnosticInfrastructureLogs**|Включает сбор журналов, создаваемых системой диагностикой Azure. Журналы инфраструктуры диагностики удобны для устранения неполадок в самой системе диагностики. Необязательные атрибуты:<br /><br /> - **scheduledTransferLogLevelFilter**: задает минимальный уровень серьезности собираемых журналов.<br /><br /> - **scheduledTransferPeriod**: интервал между запланированными передачами в службу хранилища, округленный с точностью до ближайшей минуты. Значение относится к [типу данных XML "Duration"](http://www.w3schools.com/schema/schema_dtypes_date.asp). |  
|**Directories**|Ознакомьтесь с описанием в другом разделе на этой странице.|  
|**EtwProviders**|Ознакомьтесь с описанием в другом разделе на этой странице.|  
|**Метрики**|Ознакомьтесь с описанием в другом разделе на этой странице.|  
|**PerformanceCounters**|Ознакомьтесь с описанием в другом разделе на этой странице.|  
|**WindowsEventLog**|Ознакомьтесь с описанием в другом разделе на этой странице.|  

## <a name="crashdumps-element"></a>Элемент CrashDumps  
 Включает сбор аварийных дампов.  

|Атрибуты|Описание|  
|----------------|-----------------|  
|**containerName**|необязательный параметр. Имя контейнера больших двоичных объектов в вашей учетной записи хранения Azure, используемого для хранения аварийных дампов.|  
|**crashDumpType**|необязательный параметр.  Позволяет настроить систему диагностики Azure для сбора мини-дампов или полных дампов.|  
|**directoryQuotaPercentage**|необязательный параметр.  Позволяет настроить процент от значения **overallQuotaInMB**, резервируемый для аварийных дампов на виртуальной машине.|  

|Дочерние элементы|Описание|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|обязательный параметр. Определяет значения конфигурации для каждого процесса.<br /><br /> Следующий атрибут также является обязательным:<br /><br /> **processName**: имя процесса, для которого системе диагностики Azure нужно собирать аварийные дампы.|  

## <a name="directories-element"></a>Элемент Directories  
 Включает сбор содержимого каталога, журналов невыполненных запросов на вход IIS и (или) журналов IIS.  

 Необязательный атрибут **scheduledTransferPeriod**. Ознакомьтесь с описанием выше.  

|Дочерние элементы|Описание|  
|--------------------|-----------------|  
|**DataSources**|Задает список отслеживаемых каталогов.|  
|**FailedRequestLogs**|Если добавить этот элемент в конфигурацию, то будет включен сбор журналов о невыполненных запросах к сайту или приложению IIS. Вам также необходимо включить параметры трассировки в разделе **system.WebServer** файла **Web.config**.|  
|**IISLogs**|Если добавить этот элемент в конфигурацию, то будет включен сбор журналов IIS.<br /><br /> **containerName**: имя контейнера больших двоичных объектов в вашей учетной записи хранения Azure, используемого для хранения журналов IIS.|  

## <a name="datasources-element"></a>Элемент DataSources  
 Задает список отслеживаемых каталогов.  

|Дочерние элементы|Описание|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|обязательный параметр. Обязательный атрибут:<br /><br /> **containerName**: имя контейнера больших двоичных объектов в вашей учетной записи хранения Azure, используемого для хранения файлов журнала.|  

## <a name="directoryconfiguration-element"></a>Элемент DirectoryConfiguration  
 Может содержать только один из элементов **Absolute** и **LocalResource**, но не оба.  

|Дочерние элементы|Описание|  
|--------------------|-----------------|  
|**Absolute**|Абсолютный путь к отслеживаемому каталогу. Ниже приведены обязательные атрибуты.<br /><br /> - **Path**: абсолютный путь к отслеживаемому каталогу.<br /><br /> - **expandEnvironment**: позволяет раскрыть переменные среды, указанные в пути.|  
|**LocalResource**|Путь относительно отслеживаемого локального ресурса. Ниже перечислены обязательные атрибуты.<br /><br /> - **Name**: локальный ресурс, который содержит каталог для отслеживания.<br /><br /> - **relativePath**: путь относительно значения Name, содержащего отслеживаемый каталог.|  

## <a name="etwproviders-element"></a>Элемент EtwProviders  
 Настраивает сбор событий ETW (трассировка событий Windows) из поставщиков на основе EventSource и (или) манифеста ETW.  

|Дочерние элементы|Описание|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Позволяет настроить сбор событий, создаваемых из [класса EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Обязательный атрибут:<br /><br /> **provider**: имя класса события EventSource.<br /><br /> Необязательные атрибуты:<br /><br /> - **scheduledTransferLogLevelFilter**: минимальный уровень серьезности события для переноса в вашу учетную запись хранения.<br /><br /> - **scheduledTransferPeriod**: интервал между запланированными передачами в службу хранилища, округленный с точностью до ближайшей минуты. Значение относится к [типу данных XML "Duration"](http://www.w3schools.com/schema/schema_dtypes_date.asp). |  
|**EtwManifestProviderConfiguration**|Обязательный атрибут:<br /><br /> **provider**: GUID поставщика событий.<br /><br /> Необязательные атрибуты:<br /><br /> - **scheduledTransferLogLevelFilter**: минимальный уровень серьезности события для переноса в вашу учетную запись хранения.<br /><br /> - **scheduledTransferPeriod**: интервал между запланированными передачами в службу хранилища, округленный с точностью до ближайшей минуты. Значение относится к [типу данных XML "Duration"](http://www.w3schools.com/schema/schema_dtypes_date.asp). |  

## <a name="etweventsourceproviderconfiguration-element"></a>Элемент EtwEventSourceProviderConfiguration  
 Позволяет настроить сбор событий, создаваемых из [класса EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Дочерние элементы|Описание|  
|--------------------|-----------------|  
|**DefaultEvents**|Необязательный атрибут:<br/><br/> **eventDestination**: имя таблицы для хранения событий.|  
|**Event**|Обязательный атрибут:<br /><br /> **id**: идентификатор события.<br /><br /> Необязательный атрибут:<br /><br /> **eventDestination**: имя таблицы для хранения событий.|  

## <a name="etwmanifestproviderconfiguration-element"></a>Элемент EtwManifestProviderConfiguration  

|Дочерние элементы|Описание|  
|--------------------|-----------------|  
|**DefaultEvents**|Необязательный атрибут:<br /><br /> **eventDestination**: имя таблицы для хранения событий.|  
|**Event**|Обязательный атрибут:<br /><br /> **id**: идентификатор события.<br /><br /> Необязательный атрибут:<br /><br /> **eventDestination**: имя таблицы для хранения событий.|  

## <a name="metrics-element"></a>Элемент Metrics  
 Позволяет создать таблицу счетчиков производительности, которая оптимизирована для быстрых запросов. Каждый счетчик производительности, который определен в элементе **PerformanceCounters**, помимо таблицы счетчиков производительности хранится в таблице метрик.  

 Атрибут **ResourceId** является обязательным.  Это идентификатор ресурса виртуальной машины, на которую развертывается система диагностики Azure. Значение **resourceID** можно получить на [портале Azure](https://portal.azure.com). Выберите **Обзор** -> **Группы ресурсов** -> **<Имя\>**. Щелкните элемент **Свойства** и скопируйте значение поля **Идентификатор**.  

|Дочерние элементы|Описание|  
|--------------------|-----------------|  
|**MetricAggregation**|Обязательный атрибут:<br /><br /> **scheduledTransferPeriod**: интервал между запланированными передачами в службу хранилища, округленный с точностью до ближайшей минуты. Значение относится к [типу данных XML "Duration"](http://www.w3schools.com/schema/schema_dtypes_date.asp). |  

## <a name="performancecounters-element"></a>Элемент PerformanceCounters  
 Включает сбор данных счетчиков производительности.  

 Необязательный атрибут:  

 Необязательный атрибут **scheduledTransferPeriod**. Ознакомьтесь с описанием выше.

|Дочерний элемент|Описание|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Ниже приведены обязательные атрибуты.<br /><br /> - **counterSpecifier** — имя счетчика производительности. Например, `\Processor(_Total)\% Processor Time`. Чтобы получить список счетчиков производительности на узле, выполните команду `typeperf`.<br /><br /> - **sampleRate**: частота выборки для счетчика.<br /><br /> Необязательный атрибут:<br /><br /> **unit**: единица измерения счетчика.|  

## <a name="windowseventlog-element"></a>Элемент WindowsEventLog  
 Включает сбор журналов событий Windows.  

 Необязательный атрибут **scheduledTransferPeriod**. Ознакомьтесь с описанием выше.  

|Дочерний элемент|Описание|  
|-------------------|-----------------|  
|**DataSource**|Собираемые журналы событий Windows. Обязательный атрибут:<br /><br /> **name** — запрос XPath, описывающий собираемые события Windows. Например:<br /><br /> `Application!*[Application[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[Security[(Level <= 3)]`<br /><br /> Для сбора всех событий укажите "*".|  

## <a name="logs-element"></a>Элемент Logs  
 Используется в версиях 1.0 и 1.1. Отсутствует в версии 1.2. Снова добавлен в версии 1.3.  

 Определяет конфигурацию буфера для базовых журналов Azure.  

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|необязательный параметр. Указывает максимальный объем хранилища файловой системы, который доступен для указанных данных.<br /><br /> Значение по умолчанию — 0.|  
|**scheduledTransferLogLevelFilterr**|**string**|необязательный параметр. Указывает минимальный уровень серьезности для передаваемых записей журнала. Значение по умолчанию — **Undefined**, при котором передаются все журналы. Другие возможные значения (в порядке убывания информативности): **Verbose**, **Information**, **Warning**, **Error** и **Critical**.|  
|**scheduledTransferPeriod**|**duration**|необязательный параметр. Указывает интервал между запланированными передачами данных, округленный с точностью до ближайшей минуты.<br /><br /> По умолчанию используется значение PT0S.|  
|**sinks** (добавлен в версии 1.5)|**string**|необязательный параметр. Указывает расположение приемника для отправки диагностических данных. Например, Application Insights.|  

## <a name="sinksconfig-element"></a>Элемент SinksConfig  
 Содержит список расположений для отправки диагностических данных и конфигурацию, связанную с этими расположениями.  

|Имя элемента|Описание|  
|------------------|-----------------|  
|**Приемник**|Ознакомьтесь с описанием в другом разделе на этой странице.|  

## <a name="sink-element"></a>Элемент Sink  
 Добавлен в версии 1.5.  

 Определяет расположение для отправки диагностических данных. Например, это может быть служба Application Insights.  

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**name**|строка|Строка, определяющая имя приемника.|  

|Элемент|Тип|Описание|  
|-------------|----------|-----------------|  
|**Application Insights**|строка|Используется только при отправке данных в Application Insights. Содержит ключ инструментирования для активной учетной записи Application Insights, к которой у вас есть доступ.|  
|**Channels**|строка|Указывается для каждой дополнительной фильтрации, используемой при потоковой передаче.|  

## <a name="channels-element"></a>Элемент Channels  
 Добавлен в версии 1.5.  

 Определяет фильтры для потоков данных журнала, проходящих через приемник.  

|Элемент|Тип|Описание|  
|-------------|----------|-----------------|  
|**Channel**|строка|Ознакомьтесь с описанием в другом разделе на этой странице.|  

## <a name="channel-element"></a>Элемент Channel  
 Добавлен в версии 1.5.  

 Определяет расположение для отправки диагностических данных. Например, это может быть служба Application Insights.  

|Атрибуты|Тип|Описание|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Указывает минимальный уровень серьезности для передаваемых записей журнала. Значение по умолчанию — **Undefined**, при котором передаются все журналы. Другие возможные значения (в порядке убывания информативности): **Verbose**, **Information**, **Warning**, **Error** и **Critical**.|  
|**name**|**string**|Уникальное имя для использования ссылки на канал.|  

## <a name="privateconfig-element"></a>Элемент PrivateConfig  
 Добавлен в версии 1.3.  

 Необязательно  

 Хранит частные сведения об учетной записи хранения (имя, ключ и конечную точку). Эта информация отправляется на виртуальную машину, но извлечь ее из виртуальной машины невозможно.  

|Дочерние элементы|Описание|  
|--------------------|-----------------|  
|**StorageAccount**|Используемая учетная запись хранения. Ниже приведены обязательные атрибуты.<br /><br /> - **name**: имя учетной записи хранения.<br /><br /> - **key**: ключ учетной записи хранения.<br /><br /> - **endpoint**: конечная точка для доступа к учетной записи хранения.|  

## <a name="isenabled-element"></a>Элемент IsEnabled  
 Логическое значение. Используйте `true`, чтобы включить диагностику, или `false`, чтобы отключить ее.

