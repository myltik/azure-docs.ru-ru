<properties 
	pageTitle="Приступая к работе с Application Insights в веб-проекте Java" 
	description="Мониторинг производительности и использования веб-сайта Java с помощью Application Insights" 
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
	ms.date="06/30/2015" 
	ms.author="awills"/>
 
# Приступая к работе с Application Insights в веб-проекте Java

*Доступна только предварительная версия компонента Application Insights.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Application Insights — это расширяемая служба аналитики, позволяющая оценивать производительность и использование динамических приложений. С ее помощью можно выявлять и диагностировать проблемы с производительностью и исключения, а также [писать код][api] для отслеживания действия, которые выполняют с приложением пользователи.

![пример данных](./media/app-insights-java-get-started/5-results.png)

[Веб-тесты Application Insights][availability] контролируют доступность приложения.

Вам потребуется следующее:

* Oracle JRE 1.6 или более поздней версии либо Zulu JRE 1.6 или более поздней версии;
* подписка на [Microsoft Azure](http://azure.microsoft.com/). (Можно начать с [бесплатной пробной версии](http://azure.microsoft.com/pricing/free-trial/).)


## 1\. Получение ключа инструментирования Application Insights

1. Войдите в [портал Microsoft Azure](https://portal.azure.com)
2. Создание нового ресурса Application Insights

    ![Нажмите кнопку "+" и выберите пункт "Application Insights"](./media/app-insights-java-get-started/01-create.png)
3. Задайте тип приложения: веб-приложение Java.

    ![Введите имя, выберите веб-приложение Java и нажмите кнопку "Создать"](./media/app-insights-java-get-started/02-create.png)
4. Найдите ключ инструментирования нового ресурса. Далее будет необходимо вставить его в проект кода.

    ![В обзоре нового ресурса щелкните "Свойства" и скопируйте ключ инструментирования](./media/app-insights-java-get-started/03-key.png)

## 2\. Добавление в проект пакета SDK Application Insights для Java

*Выберите подходящий метод для проекта.*

#### Если вы создаете динамический веб-проект в Eclipse...

Используйте [пакет SDK Application Insights для подключаемого модуля Java][eclipse].

#### Если вы используете Maven...

Если проект уже настроен для сборки с использованием Maven, добавьте следующий фрагмент кода в файл pom.xml.

Затем обновите зависимости проекта, чтобы загрузить двоичные файлы.

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[1.0,)</version>
      </dependency>
    </dependencies>


* *Ошибки проверки сборки или контрольной суммы? Попробуйте указать конкретную версию, например * `<version>1.0.n</version>`. Информацию о последней версии см. в статье [Заметки о выпуске пакета SDK](app-insights-release-notes-java.md) или в [артефактах репозитория Maven](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Обновление пакета SDK до новой версии*
 * Обновите зависимости проекта.

#### Если вы используете Gradle...

Если проект уже настроен для сборки с использованием Gradle, добавьте следующий фрагмент кода в файл build.gradle.

Затем обновите зависимости проекта, чтобы загрузить двоичные файлы.

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '1.+'
      // or applicationinsights-core for bare API
    }

* *Ошибки проверки сборки или контрольной суммы? Попробуйте указать конкретную версию, например * `version:'1.0.n'`. *Информацию о последней версии см. в статье [Заметки о выпуске пакета SDK](app-insights-release-notes-java.md).* 
* *Обновление пакета SDK до новой версии*
 * Обновите зависимости проекта.

#### В противном случае...

Вручную добавьте пакет SDK:

1. Загрузите [пакет SDK Application Insights для Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html).
2. Извлеките из ZIP-файла и добавьте в проект следующие двоичные файлы:

Вопросы

* *Каковы отношения между компонентами `-core` и `-web` в ZIP-архиве?*

 * Компонент `applicationinsights-core` предоставляет чистый API. Требуется всегда.
 * Компонент `applicationinsights-web` предоставляет метрики для отслеживания количества запросов HTTP и значений времени ответа. Его можно опустить, если автоматический сбор данных телеметрии не требуется, например, если вы хотите написать собственный код сбора данных.

* *Обновление пакета SDK*
 * Загрузите последнюю версию [пакета SDK Application Insights для Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html) и установите ее вместо более старых версий.
 * Изменения описаны в статье [Заметки о выпуске пакета SDK](app-insights-release-notes-java.md).



## 3\. Добавление XML-файла Application Insights

Добавьте ApplicationInsights.xml в папку ресурсов проекта или проверьте, добавлен ли этот файл в путь класса развертывания проекта. Скопируйте в него следующий код XML.

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
* Компонент HTTP-запросов является необязательным. Он автоматически передает в портал данные телеметрии о запросах и значения времени ответа.
* Корреляционные данные для событий являются дополнением к компоненту HTTP-запросов. Это дополнение назначает идентификатор для каждого запроса, полученного сервером, и добавляет его в качестве свойства каждого элемента телеметрии в форме "Операция.ИД". Благодаря этому можно выделить данные телеметрии, связанные с каждым из запросов, путем установки фильтра [Поиск по журналу диагностики][diagnostic].

## 4\. Добавление фильтра HTTP

Последний шаг настройки позволяет компоненту HTTP-запросов выполнить протоколирование каждого веб-запроса. (Не обязательно, если используется только упрощенный интерфейс API.)

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

#### Если вы используете MVC 3.1 или более поздней версии

Для включения пакета Application Insights измените следующие элементы:

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

#### Если вы используете Struts 2

Добавьте в файл конфигурации Struts следующий элемент (обычно называется struts.xml или struts-default.xml):

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

(При наличии перехватчиков, определенных в стандартном стеке, перехватчик можно просто добавить в стек.)

## 5\. Установка на сервере

На серверах Windows необходимо установить следующее:

* [распространяемые компоненты Microsoft Visual C++](http://www.microsoft.com/download/details.aspx?id=40784).

(Сюда входят счетчики производительности.)

## 6\. Запуск приложения

Запустите приложение в режиме отладки на компьютере разработки или опубликуйте его на своем сервере.

## 7\. Просмотр данных телеметрии в Application Insights

Вернитесь к ресурсу Application Insights на [портале Microsoft Azure](https://portal.azure.com).

В колонке обзора появятся данные HTTP-запросов. (Если данные отсутствуют, подождите несколько секунд и нажмите кнопку обновления).

![пример данных](./media/app-insights-java-get-started/5-results.png)
 

Щелкните любую диаграмму, чтобы увидеть более подробные метрики.

![](./media/app-insights-java-get-started/6-barchart.png)

 

При просмотре свойств запроса можно увидеть события телеметрии, связанные с ним, такие как запросы и исключения.
 
![](./media/app-insights-java-get-started/7-instance.png)



[Дополнительные сведения о метриках.][metrics]

#### Вычисление имени смарт-адреса

Служба Application Insights предполагает, что HTTP-запросы для приложений MVC имеют следующий формат: `VERB controller/action` (КОМАНДА контроллер/действие).


Например `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` и `GET Home/Product/sdf96vws` будут сгруппированы в `GET Home/Product`.

Это позволяет осмысленно группировать запросы, получая, например, число запросов и среднее время выполнения запросов.

## Исключения и ошибки запросов

Необработанные исключения фиксируются:

![](./media/app-insights-java-get-started/21-exceptions.png)

Для сбора данных по другим исключениям доступны два варианта.

* [Вставить в код вызовы функции TrackException][apiexceptions].
* [Установить на сервере агент для Java](app-insights-java-agent.md). Необходимо указать методы, которые требуется отслеживать.


## Мониторинг вызовов методов и внешних зависимостей.

[Установите агент для Java](app-insights-java-agent.md), чтобы регистрировать заданные внутренние методы и вызовы, произведенные через JDBC, с указанием времени выполнения.


## Счетчики производительности

Щелкните плитку "Серверы", и вы увидите ряд счетчиков производительности.


![](./media/app-insights-java-get-started/11-perf-counters.png)

### Настройка сбора данных счетчиками производительности

Чтобы отключить сбор данных стандартным набором счетчиков производительности, добавьте следующий фрагмент кода в корневой узел файла ApplicationInsights.xml:

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### Сбор данных дополнительными счетчиками производительности

Можно задать необходимость сбора данных дополнительными счетчиками производительности.

#### Счетчики JMX (предоставляются виртуальной машиной Java)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*	`displayName` — имя, отображаемое в портале Application Insights.
*	`objectName` — имя объекта JMX.
*	`attribute` — атрибут имени объекта JMX для выборки
*	`type` (необязательно) — тип атрибута объекта JMX:
 *	по умолчанию: простой тип, такой как int или long;
 *	`composite` — данные счетчика производительности имеют формат "Атрибут.Данные";
 *	`tabular` — данные счетчика производительности имеют формат строки таблицы.



#### Счетчики производительности Windows 

Каждый [счетчик производительности Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) входит в состав категории (аналогично поле является членом класса). Категория может быть глобальной либо иметь пронумерованные или именованные экземпляры.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*	displayName — имя, отображаемое в портале Application Insights.
*	categoryName — категория счетчика производительности (объект производительности), с которой связан этот счетчик производительности.
*	counterName — имя счетчика производительности.
*	instanceName — имя экземпляра категории счетчика производительности или пустая строка (""), если категория содержит единственный экземпляр. Если categoryName имеет значение "Process", и источником данных для счетчиков производительности является текущий процесс виртуальной машины Java, на которой выполняется ваше приложение, укажите `"__SELF__"`.

Счетчики производительности отображаются как пользовательские метрики в [обозревателе метрик][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)


### Счетчики производительности Unix

* [Установите collectd с подключаемым модулем Application Insights](app-insights-java-collectd.md) для получения разнообразных системных и сетевых данных.

## Получение данных о пользователях и сеансах

Итак, вы отправляете данные телеметрии с веб-сервера. Теперь для получения полного представления о приложении можно настроить дополнительные функции мониторинга:

* [Добавьте телеметрии на веб-страницы][usage] для мониторинга просмотров страниц и метрик пользователя.
* [Настройте веб-тесты][availability], чтобы убедиться, что приложение работает и отвечает на запросы.

## Журнал трассировки

Службу Application Insights можно использовать для протоколирования продольных и поперечных срезов данных из Log4J, Logback или других платформ ведения журнала. Записи журналов можно соотносить с HTTP-запросами и другими данными телеметрии. [Подробнее][javalogs].

## Отправка собственных данных телеметрии

После установки пакета SDK можно использовать интерфейс API для отправки собственных данных телеметрии.

* [Отслеживайте пользовательские события и метрики][api], чтобы знать, какие операции выполняют пользователи в приложении.
* [Поиск событий и журналов][diagnostic] для диагностики неполадок.






## Вопросы? Проблемы?

[Устранение неполадок Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO3-->