---
title: "Схема конфигурации системы диагностики Azure версии 1.0 | Документация Майкрософт"
description: "Применимо ТОЛЬКО при использовании пакета Azure SDK 2.4 и ниже с виртуальными машинами Azure, масштабируемыми наборами виртуальных машин, Service Fabric или облачными службами."
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
ms.sourcegitcommit: 66f733d7602a8d26627fcc205f357e7a4a266d11
ms.openlocfilehash: c749a0929d292913e03fa7340bbbebad411fb44d
ms.lasthandoff: 02/22/2017


---
# <a name="azure-diagnostics-10-configuration-schema"></a>Схема конфигурации системы диагностики Azure версии 1.0
> [!NOTE]
> Система диагностики Azure — это компонент, который используется для сбора данных счетчиков производительности и других статистических данных из виртуальных машин Azure, масштабируемых наборов виртуальных машин, Service Fabric и облачных служб.  Данная страница применяется только в том случае, если вы используете одну из этих служб.
>

Система диагностики Azure используется с другими продуктами диагностики корпорации Майкрософт, такими как Azure Monitor, Application Insights и Log Analytics.

Файл конфигурации системы диагностики Azure определяет значения, которые используются для инициализации монитора диагностики. Этот файл используется для инициализации параметров конфигурации диагностики при запуске монитора диагностики.  

 По умолчанию файл схемы конфигурации системы диагностики Azure устанавливается в каталог `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas`. Замените `<version>` установленной версией [пакета SDK для Azure](http://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Файл конфигурации диагностики обычно используется в задачах запуска, переда запуском которых должны быть собраны нужные им диагностические данные. Дополнительные сведения об использовании системы диагностики Azure см. в статье [Включение системы диагностики Azure в облачных службах Azure](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Пример файла конфигурации диагностики  
 В следующем примере показан типичный файл конфигурации диагностики.  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>Пространство имен DiagnosticsConfiguration  
 Пространство имен XML для файла конфигурации диагностики:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Элементы схемы  
 Файл конфигурации диагностики содержит следующие элементы.


## <a name="diagnosticmonitorconfiguration-element"></a>Элемент DiagnosticMonitorConfiguration  
Элемент верхнего уровня в файле конфигурации диагностики.  

Атрибуты:

|Атрибут  |Тип   |Обязательно| значение по умолчанию | Описание|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|длительность|Необязательно | PT1M| Указывает интервал, с которым монитор диагностики опрашивает наличие изменений конфигурации диагностики.|  
|**overallQuotaInMB**|unsignedInt|Необязательно| 4000 МБ. Если указать значение, оно не должно превышать эту величину. |Общий объем хранилища файловой системы, выделенный для всех буферов ведения журнала.|  

## <a name="diagnosticinfrastructurelogs-element"></a>Элемент DiagnosticInfrastructureLogs  
Определяет конфигурацию буфера для журналов, которые создает базовая инфраструктура диагностики.

Родительский элемент: [DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).  

Атрибуты:

|Атрибут|Тип|Описание|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|необязательный параметр. Указывает максимальный объем хранилища файловой системы, который доступен для указанных данных.<br /><br /> Значение по умолчанию — 0.|  
|**scheduledTransferLogLevelFilter**|строка|необязательный параметр. Указывает минимальный уровень серьезности для передаваемых записей журнала. По умолчанию используется значение **Undefined**. Другие возможные значения: **Verbose**, **Information**, **Warning**, **Error** и **Critical**.|  
|**scheduledTransferPeriod**|длительность|необязательный параметр. Указывает интервал между запланированными передачами данных, округленный с точностью до ближайшей минуты.<br /><br /> По умолчанию используется значение PT0S.|  

## <a name="logs-element"></a>Элемент Logs  
 Определяет конфигурацию буфера для базовых журналов Azure.

 Родительский элемент: [DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).  

Атрибуты:  

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|необязательный параметр. Указывает максимальный объем хранилища файловой системы, который доступен для указанных данных.<br /><br /> Значение по умолчанию — 0.|  
|**scheduledTransferLogLevelFilter**|строка|необязательный параметр. Указывает минимальный уровень серьезности для передаваемых записей журнала. По умолчанию используется значение **Undefined**. Другие возможные значения: **Verbose**, **Information**, **Warning**, **Error** и **Critical**.|  
|**scheduledTransferPeriod**|длительность|необязательный параметр. Указывает интервал между запланированными передачами данных, округленный с точностью до ближайшей минуты.<br /><br /> По умолчанию используется значение PT0S.|  

## <a name="directories-element"></a>Элемент Directories  
Определяет конфигурацию буфера для журналов на основе файлов, которые можно определить.

Родительский элемент: [DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).  


Атрибуты:  

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|необязательный параметр. Указывает максимальный объем хранилища файловой системы, который доступен для указанных данных.<br /><br /> Значение по умолчанию — 0.|  
|**scheduledTransferPeriod**|длительность|необязательный параметр. Указывает интервал между запланированными передачами данных, округленный с точностью до ближайшей минуты.<br /><br /> По умолчанию используется значение PT0S.|  

## <a name="crashdumps-element"></a>Элемент CrashDumps  
 Определяет каталог аварийных дампов.

 Родительский элемент: [Directories](#Directories).  

Атрибуты:  

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**container**|строка|Имя контейнера, в который будет передаваться содержимое каталога.|  
|**directoryQuotaInMB**|unsignedInt|необязательный параметр. Определяет максимальный размер каталога в мегабайтах.<br /><br /> Значение по умолчанию — 0.|  

## <a name="failedrequestlogs-element"></a>Элемент FailedRequestLogs  
 Определяет каталог журнала невыполненных запросов.

 Родительский элемент: [Directories](#Directories).  

Атрибуты:  

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**container**|строка|Имя контейнера, в который будет передаваться содержимое каталога.|  
|**directoryQuotaInMB**|unsignedInt|необязательный параметр. Определяет максимальный размер каталога в мегабайтах.<br /><br /> Значение по умолчанию — 0.|  

##  <a name="iislogs-element"></a>Элемент IISLogs  
 Определяет каталог журнала IIS.

 Родительский элемент: [Directories](#Directories).  

Атрибуты:  

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**container**|строка|Имя контейнера, в который будет передаваться содержимое каталога.|  
|**directoryQuotaInMB**|unsignedInt|необязательный параметр. Определяет максимальный размер каталога в мегабайтах.<br /><br /> Значение по умолчанию — 0.|  

## <a name="datasources-element"></a>Элемент DataSources  
 Определяет ноль или более дополнительных каталогов журналов.

 Родительский элемент: [Directories](#Directories).

## <a name="directoryconfiguration-element"></a>Элемент DirectoryConfiguration  
 Определяет каталог файлов журнала для отслеживания.

 Родительский элемент: [DataSources](#DataSources).

Атрибуты:

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**container**|строка|Имя контейнера, в который будет передаваться содержимое каталога.|  
|**directoryQuotaInMB**|unsignedInt|необязательный параметр. Определяет максимальный размер каталога в мегабайтах.<br /><br /> Значение по умолчанию — 0.|  

## <a name="absolute-element"></a>Элемент Absolute  
 Определяет абсолютный путь к отслеживаемому каталогу с необязательным раскрытием переменных среды.

 Родительский элемент: [DirectoryConfiguration](#DirectoryConfiguration).  

Атрибуты:  

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**path**|строка|обязательный параметр. Абсолютный путь к отслеживаемому каталогу.|  
|**expandEnvironment**|Логическое|обязательный параметр. Если задано значение **true**, то переменные среды в пути раскрываются.|  

## <a name="localresource-element"></a>Элемент LocalResource  
 Определяет путь относительно локального ресурса, заданного в определении службы.

 Родительский элемент: [DirectoryConfiguration](#DirectoryConfiguration).  

Атрибуты:  

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**name**|строка|обязательный параметр. Имя локального ресурса, который содержит каталог для отслеживания.|  
|**relativePath**|строка|обязательный параметр. Путь относительно отслеживаемого локального ресурса.|  

## <a name="performancecounters-element"></a>Элемент PerformanceCounters  
 Определяет путь к счетчику производительности, данные которого будут собираться.

 Родительский элемент: [DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).


 Атрибуты:  

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|необязательный параметр. Указывает максимальный объем хранилища файловой системы, который доступен для указанных данных.<br /><br /> Значение по умолчанию — 0.|  
|**scheduledTransferPeriod**|длительность|необязательный параметр. Указывает интервал между запланированными передачами данных, округленный с точностью до ближайшей минуты.<br /><br /> По умолчанию используется значение PT0S.|  

## <a name="performancecounterconfiguration-element"></a>Элемент PerformanceCounterConfiguration  
 Определяет счетчик производительности, данные которого будут собираться.

 Родительский элемент: [PerformanceCounters](#PerformanceCounters).  

 Атрибуты:  

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**counterSpecifier**|строка|обязательный параметр. Путь к счетчику производительности, данные которого будут собираться.|  
|**sampleRate**|длительность|обязательный параметр. Частота сбора данных счетчика производительности.|  

## <a name="windowseventlog-element"></a>Элемент WindowsEventLog  
 Определяет журналы событий для отслеживания.

 Родительский элемент: [DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).

  Атрибуты:

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|необязательный параметр. Указывает максимальный объем хранилища файловой системы, который доступен для указанных данных.<br /><br /> Значение по умолчанию — 0.|  
|**scheduledTransferLogLevelFilter**|строка|необязательный параметр. Указывает минимальный уровень серьезности для передаваемых записей журнала. По умолчанию используется значение **Undefined**. Другие возможные значения: **Verbose**, **Information**, **Warning**, **Error** и **Critical**.|  
|**scheduledTransferPeriod**|длительность|необязательный параметр. Указывает интервал между запланированными передачами данных, округленный с точностью до ближайшей минуты.<br /><br /> По умолчанию используется значение PT0S.|  

## <a name="datasource-element"></a>Элемент DataSource  
 Определяет журнал событий для отслеживания.

 Родительский элемент: [WindowsEventLog](#windowsEventLog).  

 Атрибуты:

|Атрибут|Тип|Описание|  
|---------------|----------|-----------------|  
|**name**|строка|обязательный параметр. Выражение XPath, задающее журнал для сбора.|  

