---
title: "Отправка журналов системы диагностики Azure в Application Insights| Документация Майкрософт"
description: "Настройте сведения журналов диагностики облачных служб Azure для отправки на портал Application Insights."
services: application-insights
documentationcenter: .net
author: sbtron
manager: douge
ms.assetid: a67dd20a-fc5d-4391-ba63-bfe164fb62f7
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2843ff25c118afe5d5421b322b529e9a9f2d5036


---
# <a name="configure-azure-diagnostic-logging-to-application-insights"></a>Настройка системы диагностики Azure для входа в Application Insights
После настройки проекта облачной службы или виртуальной машины в Microsoft Azure [Azure может создавать журнал диагностики](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Его можно отправить в Application Insights для последующего анализа вместе с телеметрией данных диагностики и использования, которые отправляются из приложения пакетом SDK Application Insights. В журнале Azure содержатся события управления приложением: запуск, остановка, сбои, а также счетчики производительности. Журнал также включает в себя вызовы System.Diagnostics.Trace в приложении.

В этой статье подробно описана настройка сбора сведений диагностики.

В Visual Studio должен быть установлен пакет SDK для Azure 2.8 или более поздней версии.

## <a name="get-an-application-insights-resource"></a>Получение ресурса Application Insights
Для получения наилучших результатов [добавьте пакет SDK для Application Insights к каждой роли приложения облачных служб](app-insights-cloudservices.md) или [к любому приложению, которое будет работать на вашей виртуальной машине](app-insights-overview.md). Затем диагностические данные можно будет отправить для анализа и отображения того же ресурса Application Insights.

Кроме того, если вы не хотите использовать пакет SDK (например, если приложение уже запущено), можно просто [создать новый ресурс Application Insights](app-insights-create-new-resource.md) на портале Azure. Выберите **Система диагностики Azure** в качестве типа приложения.

## <a name="send-azure-diagnostics-to-application-insights"></a>Отправка данных диагностики Azure в Application Insights
Если у вас есть возможность обновлять проект приложения, в Visual Studio выберите каждую роль, откройте ее «Свойства» и на вкладке «Настройка» выберите **Отправлять данные диагностики в Application Insights**.

Кроме того, при использовании команды "Опубликовать" для передачи приложения на странице "Диагностика" можно выбрать параметр "Application Insights".

Если ваше приложение уже работает, его свойства можно просмотреть с помощью обозревателя серверов Visual Studio или обозревателя облачных служб. Установите флажок **Отправлять данные диагностики в Application Insights**.

В любом случае потребуется ввести более подробную информацию о созданном ресурсе Application Insights.

Мы рекомендуем отправлять данные отдельных ролей в разные ресурсы. Вы можете отобразить их диаграммы метрик рядом на портале, создав [панель мониторинга](app-insights-dashboards.md).

[Дополнительные сведения о настройке Application Insights для приложения облачных служб](app-insights-cloudservices.md).

## <a name="configuring-the-azure-diagnostics-adapter"></a>Настройка адаптера системы диагностики Azure
Продолжайте читать дальше, если вам необходимо выбрать отдельные разделы журнала для отправки в Application Insights. По умолчанию отправляются все данные, включая следующие сведения: события Microsoft Azure, счетчики производительности и трассировка вызовов System.Diagnostics.Trace из приложения.

При изменении параметров диагностики в редакторе свойств роли или мастере публикации на самом деле вы изменяете содержимое двух наборов файлов.

* Это [файлы конфигурации диагностики](https://msdn.microsoft.com/library/azure/dn782207.aspx) роли. Их можно найти в обозревателе решений в разделе `<Your Service>/Roles/*/diagnostics.wadcfgx`.
* И файлы конфигурации службы, `ServiceConfiguration.*.cscfg`.

Измените эти файлы напрямую, чтобы оперировать параметрами точнее, чем позволяют мастера. Чтобы получить дополнительные сведения, читайте дальше. 

## <a name="separate-development-and-production-resources"></a>Отдельные ресурсы для разработки и эксплуатации
Можно отправлять данные телеметрии приложения для меток разработки и эксплуатации в разные ресурсы Application Insights. Это позволит избежать поглощения данных телеметрии среды разработки данными телеметрии рабочей среды. 

1. [Создайте ресурсы Application Insights](app-insights-create-new-resource.md) для каждой метки. Получите ключ инструментирования на вкладке "Основное" каждого ресурса.
2. Измените два CSCFG-файла и вставьте в них разные ключи инструментирования.

## <a name="choose-the-priority-levels-to-send"></a>Выбор уровней приоритета для отправки данных
В файле конфигурации диагностики `diagnostics.wadcfgx` для каждой роли можно фильтровать сообщения журнала по уровню.

### <a name="define-a-sink"></a>Определение приемника
`<SinksConfig>` определяет дополнительный приемник, в который можно отправлять данные диагностики Azure.  Пример `SinksConfig` выглядит так:

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights/>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig>

```

`Channels` присваивает имя потоку данных, который будет отправляться в приемник. Канал действует как фильтр. Атрибут `loglevel` позволяет указать уровень журнала для отправки каналом. Возможные значения: `{Verbose, Information, Warning, Error, Critical}`.

### <a name="send-data-to-the-sink"></a>Отправка данных в приемник
Отправьте данные в приемник Application Insights, добавив атрибут sinks в узел DiagnosticMonitorConfiguration или любой узел, расположенный в нем. Добавляя элемент sinks в каждый узел, вы указываете, что данные, собранные с этого узла и его дочерних узлов, будут отправляться в указанный приемник.

Например, приемник по умолчанию, созданный с помощью пакета SDK Azure, должен отправлять все данные диагностики Azure:

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

Но если вы хотите отправлять только журналы ошибок, укажите имя приемника в имени канала:

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

Обратите внимание, что мы используем имя приемника, который определили вместе с именем канала, определенным выше.

Если вы хотите отправлять в приемник Application Insights только подробные журналы приложений, необходимо добавить атрибут sinks в узел `Logs` .

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

В конфигурацию можно также включить несколько приемников на разных уровнях иерархии. В этом случае приемник, указанный на верхнем уровне иерархии, действует как глобальный параметр, а приемник, указанный на уровне отдельного элемента, действует как переопределение для этого глобального параметра.

Ниже приведен полный пример файла открытой конфигурации, который отправляет все ошибки в Application Insights (указано в узле `DiagnosticMonitorConfiguration`), а также в подробные журналы для журналов приложений (указаны в узле `Logs`).

```xml

    <WadCfg>
     <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData">
       <!-- All info below sent to this channel -->
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
            sinks="ApplicationInsights.MyLogData"/>
       <!-- This specific info sent to this channel -->
     </DiagnosticMonitorConfiguration>

     <SinksConfig>
      <Sink name="ApplicationInsights">
        <ApplicationInsights/>
        <Channels>
          <Channel logLevel="Error" name="MyTopDiagData"  />
          <Channel logLevel="Verbose" name="MyLogData"  />
        </Channels>
      </Sink>
     </SinksConfig>
    </WadCfg>
```


Существуют некоторые ограничения этой функции, о которых следует знать.

* Каналы предназначены только для работы с журналами, но не счетчиками производительности. При указании канала с элементом счетчика производительности он игнорируется.
* Уровень журнала для канала не может превышать уровень журнала, данные которого собираются с помощью функции диагностики Azure. Например, нельзя собрать данные об ошибках в журнале приложений в элементе Logs и пытаться отправить подробные журналы для синхронизации с Application Insight. Количество журналов, собираемых атрибутом scheduledTransferLogLevelFilter, должно быть равным количеству журналов, которые вы пытаетесь отправить в приемник, или должно превышать его.
* В Application Insights нельзя отправлять собранные расширением диагностики Azure данные больших двоичных объектов. Например, данные, указанные в узле Directories. Что касается аварийных дампов, фактические аварийные дампы будут по-прежнему отправляться в хранилище BLOB-объектов, а в Application Insights будут отправляться только уведомления о том, что аварийный дамп был создан.

## <a name="next-steps"></a>Дальнейшие действия
* [Мониторинг облачных служб Azure с помощью Application Insights](app-insights-cloudservices.md)
* [Отправка данных диагностики Azure в Application Insights с помощью PowerShell](app-insights-powershell-azure-diagnostics.md)
* [Файл конфигурации системы диагностики Azure](https://msdn.microsoft.com/library/azure/dn782207.aspx)




<!--HONumber=Nov16_HO3-->


