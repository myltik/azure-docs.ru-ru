---
title: "Настройка диагностики Azure для отправки данных в Application Insights | Документация Майкрософт"
description: "Обновление открытой конфигурации диагностики Azure для отправки данных в Application Insights."
services: multiple
documentationcenter: .net
author: sbtron
manager: douge
editor: 
ms.assetid: f9e12c3e-c307-435e-a149-ef0fef20513a
ms.service: application-insights
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2015
ms.author: saurabh
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fff688c7088a3c677763495b1a46ce54606d6816


---
# <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Настройка диагностики Azure для отправки данных в Application Insights
Данные диагностики Azure хранятся в таблицах хранилища Azure.  Однако передавать все данные или набор данных в Application Insights также можно, настроив "приемники" и "каналы" в конфигурации при использовании расширения диагностики Azure версии 1.5 или более поздней.

В этой статье описывается создание открытой конфигурации для расширения диагностики Azure для отправки данных в Application Insights.

## <a name="configuring-application-insights-as-a-sink"></a>Настройка Application Insights в качестве приемника
В расширении диагностики Azure 1.5 элемент **<SinksConfig>** включается в открытую конфигурацию. Он определяет дополнительный *приемник* , в который можно отправлять данные диагностики Azure. Сведения о ресурсе Application Insights, в которой предполагается отправлять данные диагностики Azure, можно указать в рамках этого элемента **<SinksConfig>**.
Ниже приведен пример **SinksConfig** :  

    <SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>

Для элемента **Sink** атрибут *name* задает строковое значение, которое будет использоваться для уникальной ссылки на приемник.
Элемент **ApplicationInsights** указывает ключ инструментирования ресурса Application Insights, в который будут отправляться данные диагностики Azure. Если ресурс Application Insights еще не существует, см. статью [Создание нового ресурса Application Insights](application-insights/app-insights-create-new-resource.md), где содержатся дополнительные сведения о создании ресурса и получении ключа инструментирования.

При разработке проекта облачной службы с помощью пакета SDK для Azure 2.8 этот ключ инструментирования заполняется автоматически в открытой конфигурации на основе параметра конфигурации службы **APPINSIGHTS_INSTRUMENTATIONKEY**, когда создается пакет проекта облачной службы. См. статью [Устранение неполадок облачных служб с помощью Application Insights](cloud-services/cloud-services-dotnet-diagnostics-applicationinsights.md).

Элемент **Channels** позволяет определить один или несколько элементов **Channel** для данных, которые будут отправлены в приемник. Канал действует как фильтр и позволяет выбирать конкретные уровни журнала, которые требуется отправить в приемник. Например, можно собирать подробные журналы и отправлять их в хранилище или определить канал с уровнем журнала Error (Ошибка), при отправке журналов по этому каналу в приемник будут отправляться только ошибки.
Для элемента **Channel** атрибут *name* используется для однозначной ссылки на этот канал.
Атрибут *Loglevel* позволяет указать уровень журнала для канала. Доступны следующие уровни ведения журнала (от наиболее к наименее информативным):

* Подробная информация
* Информация
* Предупреждение
* Ошибка
* критические ошибки.

## <a name="send-data-to-the-application-insights-sink"></a>Отправка данных в приемник Application Insights
После определения приемника Application Insights можно отправлять данные в этот приемник, добавив атрибут *sink* в элементы узла **DiagnosticMonitorConfiguration** . Добавляя элемент *sinks* в каждый узел, вы указываете, что данные, собранные с этого узла и его дочерних узлов, будут отправляться в указанный приемник.

Например, если вы хотите отправлять все данные, собираемые функцией диагностики Azure, можно добавить атрибут *sink* непосредственно в узел **DiagnosticMonitorConfiguration** . Задайте для элемента *sinks* имя приемника, которое было указано в **SinkConfig**.

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">

Если вы хотите отправлять в приемник Application Insights только журналы ошибок, можно задать для элемента *sinks* имя приемника, за которым следует имя канала, отделенное точкой ("."). Например, чтобы отправлять в приемник Application Insights только журналы ошибок, используйте канал MyTopDiagdata, который был определен в SinksConfig выше.  

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">

Если вы хотите отправлять в приемник Application Insights только подробные журналы приложений, необходимо добавить атрибут *sinks* в узел **Logs** .

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>

В конфигурацию можно также включить несколько приемников на разных уровнях иерархии. В этом случае приемник, указанный на верхнем уровне иерархии, действует как глобальный параметр, а приемник, указанный на уровне отдельного элемента, действует как переопределение для этого глобального параметра.    

Ниже приведен полный пример файла открытой конфигурации, который отправляет все ошибки в Application Insights (указано в узле **DiagnosticMonitorConfiguration**) и, кроме того, в журналы уровня Verbose для журналов приложений (указаны в узле **Logs**).

    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
           sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
        <DiagnosticInfrastructureLogs />
        <PerformanceCounters>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
                sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
      </DiagnosticMonitorConfiguration>

    <SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>

![Открытая конфигурация диагностики](./media/azure-diagnostics-configure-applicationinsights/diagnostics-publicconfig.png)

Существуют некоторые ограничения этой функции, о которых следует знать.

* Каналы предназначены только для работы с журналами, но не счетчиками производительности. При указании канала с элементом счетчика производительности он игнорируется.
* Уровень журнала для канала не может превышать уровень журнала, данные которого собираются с помощью функции диагностики Azure. Например, нельзя собрать данные об ошибках в журнале приложений в элементе Logs и пытаться отправить подробные журналы в службу синхронизации Application Insight. Атрибут *ScheduledTransferLogLevelFilter* должен всегда собирать равное или большее число журналов, чем число журналов, которые вы пытаетесь отправить в приемник.
* В Application Insights нельзя отправлять собранные расширением диагностики Azure данные больших двоичных объектов. Например, данные, указанные в узле *Directories* . Что касается аварийных дампов, фактические аварийные дампы будут по-прежнему отправляться в хранилище BLOB-объектов, а в Application Insights будут отправляться только уведомления о том, что аварийный дамп был создан.

## <a name="next-steps"></a>Дальнейшие действия
* Используйте [PowerShell](cloud-services/cloud-services-diagnostics-powershell.md), чтобы включить расширение диагностики Azure для вашего приложения.
* Используйте [Visual Studio](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) , чтобы включить расширение диагностики Azure для вашего приложения.



<!--HONumber=Feb17_HO3-->


