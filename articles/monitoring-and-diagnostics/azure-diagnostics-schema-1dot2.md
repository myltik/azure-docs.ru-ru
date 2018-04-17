---
title: Схема конфигурации системы диагностики Azure версии 1.2 | Документация Майкрософт
description: Применимо ТОЛЬКО при использовании пакета Azure SDK 2.5 с виртуальными машинами Azure, масштабируемыми наборами виртуальных машин, Service Fabric или облачными службами.
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: robb
ms.openlocfilehash: 1e9cc6d0950945df8c4fba74d8e1f6196be224f0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Схема конфигурации системы диагностики Azure 1.2
> [!NOTE]
> Система диагностики Azure — это компонент, который используется для сбора данных счетчиков производительности и других статистических данных из виртуальных машин Azure, масштабируемых наборов виртуальных машин, Service Fabric и облачных служб.  Данная страница применяется только в том случае, если вы используете одну из этих служб.
>

Система диагностики Azure используется с другими продуктами диагностики корпорации Майкрософт, такими как Azure Monitor, Application Insights и Log Analytics.

Эта схема определяет возможные значения, которые можно использовать для инициализации параметров конфигурации диагностики при запуске монитора диагностики.  


 Скачайте общедоступное определение схемы файла конфигурации, выполнив следующую команду PowerShell:  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Дополнительные сведения об использовании системы диагностики Azure см. в статье [Включение системы диагностики Azure в облачных службах Azure](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Пример файла конфигурации диагностики  
 В следующем примере показан типичный файл конфигурации диагностики.  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
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
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
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
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>Пространство имен конфигурации диагностики  
 Пространство имен XML для файла конфигурации диагностики:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>Элемент PublicConfig  
 Элемент верхнего уровня файла конфигурации диагностики. В следующей таблице описаны элементы файла конфигурации.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**WadCfg**|Обязательный элемент. Параметры конфигурации для собираемых данных телеметрии.|  
|**StorageAccount**|Имя учетной записи хранения Azure для хранения данных. Может также быть указан как параметр при выполнении командлета Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Каталог на виртуальной машине, в котором Monitoring Agent будет хранить данные событий. Если этот параметр не задан, используется каталог по умолчанию:<br /><br /> для рабочей роли или веб-роли: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> для виртуальной машины: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Ниже перечислены обязательные атрибуты.<br /><br /> -                      **path**: каталог в системе для использования системой диагностики Azure.<br /><br /> -                      **expandEnvironment**: позволяет раскрыть переменные среды в пути.|  

## <a name="wadcfg-element"></a>Элемент WadCFG  
Определяет параметры конфигурации для собираемых данных телеметрии. В следующей таблице описаны дочерние элементы.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Обязательный элемент. Необязательные атрибуты:<br /><br /> -                     **overallQuotaInMB**: максимальный объем пространства на локальном жестком диске, доступный для диагностических данных различного типа, собранных системой диагностики Azure. Значение по умолчанию составляет 5120 МБ.<br /><br /> -                     **useProxyServer**: позволяет настроить систему диагностики Azure для использования параметров прокси-сервера, указанных в настройках IE.|  
|**CrashDumps**|Включает сбор аварийных дампов. Необязательные атрибуты:<br /><br /> -                     **containerName**: имя контейнера больших двоичных объектов в вашей учетной записи хранения Azure, используемого для хранения аварийных дампов.<br /><br /> -                     **crashDumpType**: позволяет настроить систему диагностики Azure для сбора мини-дампов или полных дампов.<br /><br /> -                     **directoryQuotaPercentage**: позволяет настроить процент от **overallQuotaInMB**, резервируемый для аварийных дампов на виртуальной машине.|  
|**DiagnosticInfrastructureLogs**|Включает сбор журналов, создаваемых системой диагностикой Azure. Журналы инфраструктуры диагностики удобны для устранения неполадок в самой системе диагностики. Необязательные атрибуты:<br /><br /> -                     **scheduledTransferLogLevelFilter**: задает минимальный уровень серьезности собираемых журналов.<br /><br /> -                     **scheduledTransferPeriod**: интервал между запланированными передачами в службу хранилища, округленный с точностью до ближайшей минуты. Значение относится к [типу данных XML "Duration"](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**Directories**|Включает сбор содержимого каталога, журналов невыполненных запросов на вход IIS и (или) журналов IIS. Необязательный атрибут:<br /><br /> **scheduledTransferPeriod**: интервал между запланированными передачами в службу хранилища, округленный с точностью до ближайшей минуты. Значение относится к [типу данных XML "Duration"](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**EtwProviders**|Настраивает сбор событий ETW (трассировка событий Windows) из поставщиков на основе EventSource и (или) манифеста ETW.|  
|**Метрики**|Этот элемент позволяет создать таблицу счетчиков производительности, которая оптимизирована для быстрых запросов. Каждый счетчик производительности, который определен в элементе **PerformanceCounters**, помимо таблицы счетчиков производительности хранится в таблице метрик. Обязательный атрибут:<br /><br /> **resourceId**: идентификатор ресурса виртуальной машины, на которую развертывается система диагностики Azure. Значение **resourceID** можно получить на [портале Azure](https://portal.azure.com). Выберите **Обзор** -> **Группы ресурсов** -> **<Имя\>**. Щелкните элемент **Свойства** и скопируйте значение поля **Идентификатор**.|  
|**PerformanceCounters**|Включает сбор данных счетчиков производительности. Необязательный атрибут:<br /><br /> **scheduledTransferPeriod**: интервал между запланированными передачами в службу хранилища, округленный с точностью до ближайшей минуты. Значение относится к [типу данных XML "Duration"](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**WindowsEventLog**|Включает сбор журналов событий Windows. Необязательный атрибут:<br /><br /> **scheduledTransferPeriod**: интервал между запланированными передачами в службу хранилища, округленный с точностью до ближайшей минуты. Значение относится к [типу данных XML "Duration"](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="crashdumps-element"></a>Элемент CrashDumps  
 Включает сбор аварийных дампов. В следующей таблице описаны дочерние элементы.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Обязательный элемент. Обязательный атрибут:<br /><br /> **processName**: имя процесса, для которого системе диагностики Azure нужно собирать аварийные дампы.|  
|**crashDumpType**|Позволяет настроить систему диагностики Azure для сбора мини-дампов или полных дампов.|  
|**directoryQuotaPercentage**|Позволяет настроить процент от значения **overallQuotaInMB**, резервируемый для аварийных дампов на виртуальной машине.|  

## <a name="directories-element"></a>Элемент Directories  
 Включает сбор содержимого каталога, журналов невыполненных запросов на вход IIS и (или) журналов IIS. В следующей таблице описаны дочерние элементы.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**DataSources**|Задает список отслеживаемых каталогов.|  
|**FailedRequestLogs**|Если добавить этот элемент в конфигурацию, то будет включен сбор журналов о невыполненных запросах к сайту или приложению IIS. Вам также необходимо включить параметры трассировки в разделе **system.WebServer** файла **Web.config**.|  
|**IISLogs**|Если добавить этот элемент в конфигурацию, то будет включен сбор журналов IIS.<br /><br /> **containerName**: имя контейнера больших двоичных объектов в вашей учетной записи хранения Azure, используемого для хранения журналов IIS.|  

## <a name="datasources-element"></a>Элемент DataSources  
 Задает список отслеживаемых каталогов. В следующей таблице описаны дочерние элементы.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Обязательный элемент. Обязательный атрибут:<br /><br /> **containerName**: имя контейнера больших двоичных объектов в вашей учетной записи хранения Azure, используемого для хранения файлов журнала.|  

## <a name="directoryconfiguration-element"></a>Элемент DirectoryConfiguration  
 **DirectoryConfiguration** может содержать только один из элементов **Absolute** и **LocalResource**, но не оба. В следующей таблице описаны дочерние элементы.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**Absolute**|Абсолютный путь к отслеживаемому каталогу. Ниже приведены обязательные атрибуты.<br /><br /> -                     **Path**: абсолютный путь к отслеживаемому каталогу.<br /><br /> -                      **expandEnvironment**: позволяет раскрыть переменные среды, указанные в пути.|  
|**LocalResource**|Путь относительно отслеживаемого локального ресурса. Ниже перечислены обязательные атрибуты.<br /><br /> -                     **Name**: локальный ресурс, который содержит каталог для отслеживания.<br /><br /> -                     **relativePath**: путь относительно значения Name, содержащего отслеживаемый каталог.|  

## <a name="etwproviders-element"></a>Элемент EtwProviders  
 Настраивает сбор событий ETW (трассировка событий Windows) из поставщиков на основе EventSource и (или) манифеста ETW. В следующей таблице описаны дочерние элементы.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Позволяет настроить сбор событий, создаваемых из [класса EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Обязательный атрибут:<br /><br /> **provider**: имя класса события EventSource.<br /><br /> Необязательные атрибуты:<br /><br /> -                     **scheduledTransferLogLevelFilter**: минимальный уровень серьезности события для переноса в вашу учетную запись хранения.<br /><br /> -                     **scheduledTransferPeriod**: интервал между запланированными передачами в службу хранилища, округленный с точностью до ближайшей минуты. Значение относится к [типу данных XML "Duration"](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Обязательный атрибут:<br /><br /> **provider**: GUID поставщика событий.<br /><br /> Необязательные атрибуты:<br /><br /> - **scheduledTransferLogLevelFilter**: минимальный уровень серьезности события для переноса в вашу учетную запись хранения.<br /><br /> -                     **scheduledTransferPeriod**: интервал между запланированными передачами в службу хранилища, округленный с точностью до ближайшей минуты. Значение относится к [типу данных XML "Duration"](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>Элемент EtwEventSourceProviderConfiguration  
 Позволяет настроить сбор событий, создаваемых из [класса EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). В следующей таблице описаны дочерние элементы.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**DefaultEvents**|Необязательный атрибут:<br /><br /> **eventDestination**: имя таблицы для хранения событий.|  
|**Event**|Обязательный атрибут:<br /><br /> **id**: идентификатор события.<br /><br /> Необязательный атрибут:<br /><br /> **eventDestination**: имя таблицы для хранения событий.|  

## <a name="etwmanifestproviderconfiguration-element"></a>Элемент EtwManifestProviderConfiguration  
 В следующей таблице описаны дочерние элементы.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**DefaultEvents**|Необязательный атрибут:<br /><br /> **eventDestination**: имя таблицы для хранения событий.|  
|**Event**|Обязательный атрибут:<br /><br /> **id**: идентификатор события.<br /><br /> Необязательный атрибут:<br /><br /> **eventDestination**: имя таблицы для хранения событий.|  

## <a name="metrics-element"></a>Элемент Metrics  
 Позволяет создать таблицу счетчиков производительности, которая оптимизирована для быстрых запросов. В следующей таблице описаны дочерние элементы.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**MetricAggregation**|Обязательный атрибут:<br /><br /> **scheduledTransferPeriod**: интервал между запланированными передачами в службу хранилища, округленный с точностью до ближайшей минуты. Значение относится к [типу данных XML "Duration"](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>Элемент PerformanceCounters  
 Включает сбор данных счетчиков производительности. В следующей таблице описаны дочерние элементы.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Ниже приведены обязательные атрибуты.<br /><br /> -                     **counterSpecifier** — имя счетчика производительности. Например, `\Processor(_Total)\% Processor Time`. Чтобы получить список счетчиков производительности на узле, выполните команду `typeperf`.<br /><br /> -                     **sampleRate**: частота выборки для счетчика.<br /><br /> Необязательный атрибут:<br /><br /> **unit**: единица измерения счетчика.|  

## <a name="performancecounterconfiguration-element"></a>Элемент PerformanceCounterConfiguration  
 В следующей таблице описаны дочерние элементы.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**annotation**|Обязательный атрибут:<br /><br /> **displayName**: отображаемое имя счетчика.<br /><br /> Необязательный атрибут:<br /><br /> **locale**: языковый стандарт, используемый при отображении имени счетчика.|  

## <a name="windowseventlog-element"></a>Элемент WindowsEventLog  
 В следующей таблице описаны дочерние элементы.  

|Имя элемента|ОПИСАНИЕ|  
|------------------|-----------------|  
|**DataSource**|Собираемые журналы событий Windows. Обязательный атрибут:<br /><br /> **name** — запрос XPath, описывающий собираемые события Windows. Например: <br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Для сбора всех событий укажите "*".|
