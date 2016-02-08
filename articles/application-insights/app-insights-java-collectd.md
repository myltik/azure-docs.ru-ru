<properties 
	pageTitle="collectd: статистика производительности для Java на Unix в Application Insights" 
	description="Расширенный мониторинг веб-сайта Java с подключаемым модулем CollectD для Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="awills"/>
 
# collectd: метрики производительности Unix в Application Insights

*Доступна только предварительная версия компонента Application Insights.*

Для работы с метриками производительности системы Unix в [Application Insights](app-insights-overview.md) установите [collectd](http://collectd.org/) вместе с его подключаемым модулем Application Insights. Это решение с открытым исходным кодом собирает разнообразные данные системной и сетевой статистики.

Обычно collectd используется, если [веб-служба Java уже инструментирована с помощью Application Insights][java], а значит, имеются дополнительные данные для повышения производительности приложения или диагностики неисправностей.

![Примеры диаграмм](./media/app-insights-java-collectd/sample.png)

## Получение ключа инструментирования

На [портале Microsoft Azure](https://portal.azure.com) откройте ресурс [Application Insights](start) там, где требуется отображение данных. (Либо [создайте новый ресурс](app-insights-create-new-resource.md).)

Скопируйте ключ инструментирования, идентифицирующий этот ресурс.

![Просмотрите все, откройте свой ресурс, а затем найдите и скопируйте ключ инструментирования в раскрывающемся списке Esssentials.](./media/app-insights-java-collectd/02-props.png)



## Установка collectd и подключаемого модуля

На компьютере с сервером Unix выполните указанные ниже действия.

1. Установите [collectd](http://collectd.org/) 5.4.0 или более поздней версии.
2. Загрузите [подключаемый модуль записи Application Insights collectd](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.html). Запишите номер версии.
3. Скопируйте подключаемый модуль JAR в `/usr/share/collectd/java`.
3. Отредактируйте файл `/etc/collectd/collectd.conf`:
 * Убедитесь, что [подключаемый модуль Java](https://collectd.org/wiki/index.php/Plugin:Java) включен.
 * Обновите JVMArg для java.class.path, включив в него указанный ниже файл JAR. Номер версии должен совпадать с номером версии, которую вы загрузили:
  * `/usr/share/collectd/java/applicationinsights-collectd-0.9.4.jar`
 * Добавьте следующий фрагмент кода, используя ключ инструментирования из ресурса:

```

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Пример файла конфигурации (фрагмент):

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-0.9.4.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
                
      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
. ...

Настройте другие [подключаемые модули collectd](https://collectd.org/wiki/index.php/Table_of_Plugins), способные собирать различные данные из разных источников.

Перезапустите collectd в соответствии с его [документацией](https://collectd.org/wiki/index.php/First_steps).

## Просмотр данных в Application Insights

В ресурсе Application Insights откройте [обозреватель метрик и добавьте диаграммы][metrics], выбрав нужные метрики в пользовательской категории.

![](./media/app-insights-java-collectd/result.png)

По умолчанию метрики со всех хост-компьютеров, которые их поставляют, объединяются. Чтобы просмотреть метрики по хостам, в колонке сведений о диаграмме включите группировку и выберите группировку по параметру CollectD-Host.


## Исключение загрузки определенных статистических данных

По умолчанию подключаемый модуль Application Insights отправляет все данные, собранные всеми подключаемыми модулями read collectd.

Чтобы исключить данные из определенных подключаемых модулей или источников данных, выполните указанные ниже действия.

* Измените файл конфигурации. 
* В `<Plugin ApplicationInsightsWriter>` добавьте строки директив следующего вида:

Директива | Результат
---|---
`Exclude disk` | Исключаются все данные, собранные подключаемым модулем `disk`
`Exclude disk:read,write` | Исключаются источники `read` и `write` из подключаемого модуля `disk`

Каждая директива должна начинаться с новой строки.


## Проблемы?

*Данные не отображаются в портале*

* Откройте [Поиск][diagnostic] и проверьте поступление необработанных событий. Иногда они появляются в обозревателе метрик не сразу.
* Включите трассировку в подключаемом модуле Application Insights. Добавьте в `<Plugin ApplicationInsightsWriter>` следующую строку:
 *  `SDKLogger true`
* Откройте терминал и запустите collectd в режиме подробного протоколирования, чтобы проверить, не сообщает ли он о каких-либо неполадках:
 * `sudo collectd -f`




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=AcomDC_0128_2016-->