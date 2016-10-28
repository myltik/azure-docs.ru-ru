<properties
    pageTitle="Отправка журналов системы диагностики Azure в Application Insights"
    description="Настройте сведения журналов диагностики облачных служб Azure для отправки на портал Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
	ms.date="11/17/2015"
    ms.author="awills"/>

# Настройка системы диагностики Azure для входа в Application Insights

После настройки проекта облачной службы или виртуальной машины в Microsoft Azure [Azure может создавать журнал диагностики](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Его можно отправить в Application Insights для последующего анализа вместе с телеметрией данных диагностики и использования, которые отправляются из приложения пакетом SDK Application Insights. В журнале Azure содержатся события управления приложением: запуск, остановка, сбои, а также счетчики производительности. Журнал также включает в себя вызовы System.Diagnostics.Trace в приложении.

В этой статье подробно описана настройка сбора сведений диагностики.

Вам потребуется пакет Azure SDK 2.8, установленный в Visual Studio.

## Получение ресурса Application Insights

Для получения наилучших результатов [добавьте пакет SDK Application Insights к каждой роли приложения облачных служб](app-insights-cloudservices.md) или [к любому приложению, которое работает на вашей виртуальной машине](app-insights-overview.md). Затем данные диагностики можно отправить для анализа и отображения того же ресурса Application Insights.

Кроме того, если вы не хотите использовать пакет SDK (например, если приложение уже запущено), можно просто [создать новый ресурс Application Insights](app-insights-create-new-resource.md) на портале Azure. Выберите **Система диагностики Azure** в качестве типа приложения.


## Отправка данных диагностики Azure в Application Insights

Если у вас есть возможность обновлять проект приложения, в Visual Studio выберите каждую роль, откройте ее «Свойства» и на вкладке «Настройка» выберите **Отправлять данные диагностики в Application Insights**.

Если ваше приложение уже работает, его свойства можно просмотреть с помощью обозревателя серверов Visual Studio или обозревателя облачных служб. Установите флажок **Отправлять данные диагностики в Application Insights**.

В любом случае потребуется ввести более подробную информацию о созданном ресурсе Application Insights.

[Дополнительные сведения о настройке Application Insights для приложения облачных служб](app-insights-cloudservices.md).

## Настройка адаптера системы диагностики Azure

Продолжайте читать дальше, если вам необходимо выбрать отдельные разделы журнала для отправки в Application Insights. По умолчанию отправляются все данные, включая следующие сведения: события Microsoft Azure, счетчики производительности и трассировка вызовов System.Diagnostics.Trace из приложения.

Данные диагностики Azure хранятся в таблицах хранилища Azure. Если вы используете расширение системы диагностики Azure 1.5 или более поздней версии, вы можете передавать все данные или набор данных в Application Insights, настроив «приемники» и «каналы».

### Настройка Application Insights в качестве приемника

Если в свойствах роли Azure SDK (2.8 или более поздней версии) выбрать параметр «Отправлять данные в Application Insights», к общедоступному [файлу конфигурации системы диагностики Azure](https://msdn.microsoft.com/library/azure/dn782207.aspx) роли будет добавлен элемент `<SinksConfig>`.

`<SinksConfig>` определяет дополнительный приемник, в который можно отправлять данные диагностики Azure. Пример `SinksConfig` выглядит так:

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig>

```

Элемент `ApplicationInsights` указывает ключ инструментирования, определяющий ресурс Application Insights, в который будут отправляться данные диагностики Azure. После выбора ресурс автоматически заполняется с учетом конфигурации службы `APPINSIGHTS_INSTRUMENTATIONKEY`. (Чтобы установить его вручную, необходимо получить ключ из раскрывающегося списка Essentials ресурса).

`Channels` определяют данные, которые будут отправляться в приемник. Канал действует как фильтр. Атрибут `loglevel` позволяет указать уровень журнала для отправки каналом. Возможные значения: `{Verbose, Information, Warning, Error, Critical}`.

### Отправка данных в приемник

Отправьте данные в приемник Application Insights, добавив атрибут sinks в узел DiagnosticMonitorConfiguration. Добавляя элемент sinks в каждый узел, вы указываете, что данные, собранные с этого узла и его дочерних узлов, будут отправляться в указанный приемник.

Например, приемник по умолчанию, созданный с помощью пакета SDK Azure, должен отправлять все данные диагностики Azure:

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

Но если вы хотите отправлять только журналы ошибок, укажите имя приемника в имени канала:

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

Обратите внимание, что мы используем имя приемника, который определили вместе с именем канала, определенным выше.

Если вы хотите отправлять в приемник Application Insights только подробные журналы приложений, необходимо добавить атрибут sinks в узел `Logs`.

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

В конфигурацию можно также включить несколько приемников на разных уровнях иерархии. В этом случае приемник, указанный на верхнем уровне иерархии, действует как глобальный параметр, а приемник, указанный на уровне отдельного элемента, действует как переопределение для этого глобального параметра.

Ниже приведен полный пример файла открытой конфигурации, который отправляет все ошибки в Application Insights (указано в узле `DiagnosticMonitorConfiguration`), а также в подробные журналы для журналов приложений (указаны в узле `Logs`).

```xml

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
            sinks="ApplicationInsights.MyLogData"/>
       <!-- This specific info sent to this channel -->
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
```

![](./media/app-insights-azure-diagnostics/diagnostics-publicconfig.png)

Существуют некоторые ограничения этой функции, о которых следует знать.

* Каналы предназначены только для работы с журналами, но не счетчиками производительности. При указании канала с элементом счетчика производительности он игнорируется.
* Уровень журнала для канала не может превышать уровень журнала, данные которого собираются с помощью функции диагностики Azure. Например, нельзя собрать данные об ошибках в журнале приложений в элементе Logs и пытаться отправить подробные журналы для синхронизации с Application Insight. Количество журналов, собираемых атрибутом scheduledTransferLogLevelFilter, должно быть равным количеству журналов, которые вы пытаетесь отправить в приемник, или должно превышать его.
* В Application Insights нельзя отправлять собранные расширением диагностики Azure данные больших двоичных объектов. Например, данные, указанные в узле Directories. Что касается аварийных дампов, фактические аварийные дампы будут по-прежнему отправляться в хранилище BLOB-объектов, а в Application Insights будут отправляться только уведомления о том, что аварийный дамп был создан.

## Связанные разделы

* [Мониторинг облачных служб Azure с помощью Application Insights](app-insights-cloudservices.md)
* [Отправка данных диагностики Azure в Application Insights с помощью PowerShell](app-insights-powershell-azure-diagnostics.md)
* [Файл конфигурации системы диагностики Azure](https://msdn.microsoft.com/library/azure/dn782207.aspx)

<!---HONumber=AcomDC_0907_2016-->