<properties 
	pageTitle="Application Insights для уже действующих веб-приложений Java" 
	description="Начните отслеживать веб-приложение, которое уже выполняется на вашем сервере." 
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
	ms.date="07/21/2015" 
	ms.author="awills"/>
 
# Application Insights для уже действующих веб-приложений Java

*Доступна только предварительная версия компонента Application Insights.*

Если у вас уже есть запущенное на сервере J2EE веб-приложение, вы можете начать отслеживать его с помощью [Appliction Insights](app-insights-overview.md). Для этого вам не нужно вносить изменения в код или повторно компилировать проект. Таким образом вы сможете получать информацию о HTTP-запросах, отправляемых на сервер, необработанных исключениях и счетчиках производительности.

Вам потребуется подписка [Microsoft Azure](http://azure.com).

> [AZURE.NOTE]В ходе описанной на этой странице процедуры в веб-приложение во время его выполнения добавляется пакет SDK. Это избавляет от необходимости изменять или повторно компилировать исходный код. Но если есть такая возможность, мы рекомендуем [добавлять пакет SDK непосредственно в исходный код](app-insights-java-get-started.md). Так вы получите дополнительные возможности, например сможете написать код для отслеживания действий пользователей.

## 1\. Получение ключа инструментирования Application Insights

1. Войдите в [портал Microsoft Azure](https://portal.azure.com)
2. Создание нового ресурса Application Insights

    ![Нажмите кнопку "+" и выберите пункт "Application Insights"](./media/app-insights-java-get-started/01-create.png)
3. Задайте тип приложения: веб-приложение Java.

    ![Введите имя, выберите веб-приложение Java и нажмите кнопку "Создать"](./media/app-insights-java-get-started/02-create.png)
4. Найдите ключ инструментирования нового ресурса. Далее будет необходимо вставить его в проект кода.

    ![В обзоре нового ресурса щелкните "Свойства" и скопируйте ключ инструментирования](./media/app-insights-java-get-started/03-key.png)

## 2\. Скачивание пакета SDK

1. Скачайте [пакет SDK Application Insights для Java](http://dl.windowsazure.com/applicationinsights/javabin/sdk.zip). 
2. На сервере извлеките содержимое пакета SDK в каталог, из которого загружаются двоичные файлы проекта. В случае с Tomcat обычно это каталог `webapps<your_app_name>\WEB-INF\lib`.


## 3\. Добавление XML-файла Application Insights

Создайте файл ApplicationInsights.xml в папке, в которую добавили пакет SDK. Вставьте в него следующий код XML.

Замените ключ инструментирования на полученный в портале Azure.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* Ключ инструментирования пересылается вместе с каждым элементом телеметрии; служба Application Insights отобразит его в ресурсе.
* Компонент HTTP-запросов является необязательным. Он автоматически передает на портал телеметрию о запросах и значения времени ответа.
* Корреляционные данные для событий являются дополнением к компоненту HTTP-запросов. Это дополнение назначает идентификатор для каждого запроса, полученного сервером, и добавляет его в качестве свойства каждого элемента телеметрии в форме "Операция.ИД". Благодаря этому можно выделить данные телеметрии, связанные с каждым из запросов, путем установки фильтра [Поиск по журналу диагностики](app-insights-diagnostic-search.md).


## 4\. Добавление фильтра HTTP

Найдите и откройте файл web.xml в проекте, добавьте следующий фрагмент кода в узел web-app, где настраиваются фильтры вашего приложения.

Для получения наиболее точных результатов этот фильтр должен применяться до всех остальных фильтров.

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

## 5\. Перезапуск веб-приложения

## 6\. Просмотр данных телеметрии в Application Insights

Вернитесь к ресурсу Application Insights на [портале Microsoft Azure](https://portal.azure.com).

В колонке обзора появятся данные HTTP-запросов. (Если данные отсутствуют, подождите несколько секунд и нажмите кнопку обновления).

![пример данных](./media/app-insights-java-get-started/5-results.png)
 

Щелкните любую диаграмму, чтобы увидеть более подробные метрики.

![](./media/app-insights-java-get-started/6-barchart.png)

 

При просмотре свойств запроса можно увидеть события телеметрии, связанные с ним, такие как запросы и исключения.
 
![](./media/app-insights-java-get-started/7-instance.png)


[Дополнительные сведения о метриках.](app-insights-metrics-explorer.md)



## Дальнейшие действия

* [Добавьте телеметрии на веб-страницы](app-insights-web-track-usage.md) для мониторинга просмотров страниц и метрик пользователя.
* [Настройте веб-тесты](app-insights-monitor-web-app-availability.md), которые помогут быть уверенными в том, что приложение остается работоспособным и правильно отвечает на запросы.
* [Журнал трассировки](app-insights-java-trace-logs.md)
* [Поиск событий и журналов](app-insights-diagnostic-search.md) для диагностики неполадок.


 

<!---HONumber=Oct15_HO3-->