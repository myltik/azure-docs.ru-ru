---
title: Application Insights для уже действующих веб-приложений Java
description: Начните отслеживать веб-приложение, которое уже выполняется на вашем сервере.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: mbullwin
ms.openlocfilehash: b8ddd43fb19b3800840180c458268301b5ec26f6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Application Insights для уже действующих веб-приложений Java


Если у вас уже есть запущенное на сервере J2EE веб-приложение, вы можете выполнять его мониторинг с помощью [Application Insights](app-insights-overview.md). Для этого вам не нужно вносить изменения в код или повторно компилировать проект. Таким образом вы сможете получать информацию об HTTP-запросах, отправляемых на сервер, необработанных исключениях и счетчиках производительности.

Вам понадобится подписка [Microsoft Azure](https://azure.com).

> [!NOTE]
> В ходе описанной на этой странице процедуры в веб-приложение во время его выполнения добавляется пакет SDK. Это инструментирование во время выполнения избавляет от необходимости изменять или повторно компилировать исходный код. Но если есть такая возможность, мы рекомендуем [добавлять пакет SDK непосредственно в исходный код](app-insights-java-get-started.md) . Так вы получите дополнительные возможности, например сможете написать код для отслеживания действий пользователей.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Получение ключа инструментирования Application Insights
1. Войдите на [портал Microsoft Azure](https://portal.azure.com)
2. Создайте ресурс Application Insights и задайте тип приложения "Веб-приложение Java".
   
    ![Введите имя, выберите веб-приложение Java и нажмите кнопку "Создать"](./media/app-insights-java-live/02-create.png)

    Через несколько секунд ресурс будет создан.

4. Откройте новый ресурс и получите его ключ инструментирования. Далее будет необходимо вставить его в проект кода.
   
    ![В обзоре нового ресурса щелкните "Свойства" и скопируйте ключ инструментирования](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Скачивание пакета SDK
1. Загрузите [пакет SDK Application Insights для Java](https://aka.ms/aijavasdk). 
2. На сервере извлеките содержимое пакета SDK в каталог, из которого загружаются двоичные файлы проекта. В случае с Tomcat обычно это каталог `webapps/<your_app_name>/WEB-INF/lib`.

Обратите внимание, что это необходимо сделать в каждом экземпляре сервера и для каждого приложения.

## <a name="3-add-an-application-insights-xml-file"></a>3. Добавление XML-файла Application Insights
Создайте файл ApplicationInsights.xml в папке, в которую добавили пакет SDK. Вставьте в него следующий код XML.

Замените ключ инструментирования на полученный в портале Azure.

```XML

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
```

* Ключ инструментирования пересылается вместе с каждым элементом телеметрии; служба Application Insights отобразит его в ресурсе.
* Компонент HTTP-запросов является необязательным. Он автоматически передает на портал телеметрию о запросах и значения времени ответа.
* Корреляционные данные для событий являются дополнением к компоненту HTTP-запросов. Это дополнение назначает идентификатор для каждого запроса, полученного сервером, и добавляет его в качестве свойства каждого элемента телеметрии в форме "Операция.ИД". Благодаря этому можно выделить данные телеметрии, связанные с каждым из запросов, путем установки фильтра [Поиск по журналу диагностики](app-insights-diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. Добавление фильтра HTTP
Найдите и откройте файл web.xml в проекте, добавьте следующий фрагмент кода в узел web-app, где настраиваются фильтры вашего приложения.

Для получения наиболее точных результатов этот фильтр должен применяться до всех остальных фильтров.

```XML

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
```

## <a name="5-check-firewall-exceptions"></a>5. Проверка исключений брандмауэра
Вам может понадобиться [задать исключения для отправки исходящих данных](app-insights-ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. Перезапуск веб-приложения
## <a name="7-view-your-telemetry-in-application-insights"></a>7. Просмотр данных телеметрии в Application Insights
Вернитесь к ресурсу Application Insights на [портале Microsoft Azure](https://portal.azure.com).

В колонке обзора появятся данные телеметрии HTTP-запросов. (Если данные отсутствуют, подождите несколько секунд и нажмите кнопку обновления).

![пример данных](./media/app-insights-java-live/5-results.png)

Щелкните любую диаграмму, чтобы увидеть более подробные метрики. 

![](./media/app-insights-java-live/6-barchart.png)

При просмотре свойств запроса можно увидеть события телеметрии, связанные с ним, такие как запросы и исключения.

![](./media/app-insights-java-live/7-instance.png)

[Дополнительные сведения о метриках.](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>Дополнительная информация
* [Добавьте телеметрии на веб-страницы](app-insights-javascript.md) для мониторинга просмотров страниц и метрик пользователя.
* [Настройте веб-тесты](app-insights-monitor-web-app-availability.md) , которые помогут быть уверенными в том, что приложение остается работоспособным и правильно отвечает на запросы.
* [Журнал трассировки](app-insights-java-trace-logs.md)
* [Поиск событий и журналов](app-insights-diagnostic-search.md) для диагностики неполадок.

