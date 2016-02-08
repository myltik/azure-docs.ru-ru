<properties
   pageTitle="Application Insights для облачных служб Azure"
   description="Эффективное отслеживание веб-ролей и рабочих ролей с помощью Application Insights"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="douge"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="11/15/2015"
   ms.author="sdash"/>

# Application Insights для облачных служб Azure


*Application Insights находится в состоянии предварительной версии*

[Приложения облачных служб Microsoft Azure](https://azure.microsoft.com/services/cloud-services/) можно отслеживать с помощью [Visual Studio Application Insights][start], чтобы оценивать доступность, производительность, характер сбоев и частоту использования. Благодаря получаемым данным о производительности и эффективности работы приложения на практике вы можете принимать осознанные решения о направлении разработки в каждом жизненном цикле.

![Пример](./media/app-insights-cloudservices/sample.png)

Вам потребуется подписка [Microsoft Azure](http://azure.com). Войдите, используя учетную запись Майкрософт, которая уже может быть у вас для Windows, XBox Live или других облачных служб Майкрософт.


#### Пример приложения, инструментированного с помощью Application Insights

Взгляните на этот [пример приложения](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), в котором служба Application Insights добавлена в облачную службу с двумя рабочими ролями, размещенными в Azure.

Ниже описано, как адаптировать собственный проект облачной службы таким же образом.

## Создание ресурса Application Insights для каждой роли

Ресурс Application Insights — это место, в котором выполняется отображение и анализ данных телеметрии.

1.  На [портале Azure][portal] создайте новый ресурс Application Insights. Для параметра типа приложения выберите приложение ASP.NET. 

    ![Нажмите «Создать» и «Application Insights»](./media/app-insights-cloudservices/01-new.png)

2.  Сделайте копию ключа инструментирования Он потребуется позже для настройки пакета SDK.

    ![Нажмите "Свойства", выберите ключ и нажмите сочетание клавиш CTRL + C](./media/app-insights-cloudservices/02-props.png)


Обычно лучше создавать отдельный ресурс для данных из каждой рабочей роли и веб-роли.

В качестве альтернативы можно отправлять данные из всех ролей только в один ресурс, но задать значение [свойства по умолчанию][apidefaults], чтобы можно было фильтровать или группировать результаты из каждой роли.

## <a name="sdk"></a>Установка пакета SDK в каждый проект


1. В Visual Studio измените пакеты NuGet вашего проекта облачных приложений.

    ![Щелкните проект правой кнопкой мыши и выберите пункт "Управление пакетами Nuget"](./media/app-insights-cloudservices/03-nuget.png)


2. Добавьте пакет NuGet [Application Insights for Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web). Эта версия пакета SDK включает модули, которые добавляют контекст сервера, например информацию о роли. Для рабочих ролей используйте Application Insights для служб Windows.

    ![Поиск Application Insights](./media/app-insights-cloudservices/04-ai-nuget.png)


3. Настройте пакет SDK для отправки данных в ресурсы Application Insights.

    Задайте ключ инструментирования в качестве параметра конфигурации в файле `ServiceConfiguration.Cloud.cscfg`. ([Пример кода](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)).
 
    ```XML
     <Role name="WorkerRoleA"> 
      <Setting name="APPINSIGHTS_INSTRUMENTATIONKEY" value="YOUR IKEY" /> 
     </Role>
    ```
 
    В соответствующей функции запуска задайте ключ инструментирования в параметре конфигурации.

    ```C#
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```

    Обратите внимание, что это же имя `APPINSIGHTS_INSTRUMENTATIONKEY` параметра конфигурации будет использоваться отчетами системы диагностики Azure.


    Сделайте это для каждой роли в вашем приложении. См. указанные ниже примеры.
 
 * [Веб-роль](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
 * [Рабочая роль](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
 * [При работе с веб-страницами](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13)   

4. Задайте для файла ApplicationInsights.config незамедлительное копирование в выходной каталог. 

    (в CONFIG-файле имеются сообщения, указывающие, куда именно поместить ключ инструментирования. Тем не менее для облачных приложений лучше задать его в CSCFG-файле. Это обеспечит правильную идентификацию роли на портале.)


#### Запуск и публикация приложения

Запустите приложение и войдите в Azure. Откройте созданные ресурсы Application Insights, и вы увидите отдельные точки данных в области [Поиск](app-insights-diagnostic-search.md) и объединенные данные в [обозревателе метрик](app-insights-metrics-explorer.md).

Добавьте дополнительные данные телеметрии (см. разделы ниже), а затем опубликуйте приложение для оперативного получения отзывов о диагностике и использовании.


#### Данные отсутствуют?

* Откройте плитку [Поиск][diagnostic], чтобы просмотреть отдельные события.
* Используйте приложение, открывая различные страницы, чтобы создать некоторый объем данных телеметрии.
* Подождите несколько секунд и нажмите "Обновить".
* См. раздел [Устранение неполадок][qna].



## Дополнительные данные телеметрии

В следующих подразделах показано, как получить дополнительные данные телеметрии из различных аспектов приложения.


## Отслеживание запросов из рабочих ролей

В веб-ролях модуль запросов собирает данные об HTTP-запросах автоматически. Примеры переопределения поведения коллекции по умолчанию см. на странице [Пример роли MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole).

Производительность вызовов для рабочих ролей можно регистрировать, отслеживая их таким же образом, как запросы HTTP. В Application Insights тип телеметрии запроса определяет единицу работы для указанного сервера, которую можно учесть и которая может быть выполнена или не выполнена. Во время автоматической регистрации HTTP-запросов пакетом SDK можно добавить собственный код для отслеживания запросов к рабочим ролям.

Ознакомьтесь с двумя примерами рабочих ролей, которые инструментированы для того, чтобы отправлять отчеты по запросам: [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) и [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## Диагностика Azure

Данные [системы диагностики Azure](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) включают в себя события управления ролями, счетчики производительности и журналы приложений. Их можно отправить в Application Insights для последующего отображения вместе с остальными данными телеметрии, что упростит выполнение диагностики проблем.

Система диагностики Azure особенно полезна в случае неожиданного сбоя роли или ошибки ее запуска.

1. Щелкните правой кнопкой мыши роль (не проект!), чтобы открыть окно ее свойств, и выберите **Включить диагностику**, **Отправлять данные диагностики в Application Insights**.

    ![Поиск Application Insights](./media/app-insights-cloudservices/21-wad.png)

    **Или если приложение уже опубликовано и выполняется**, откройте обозреватель серверов или обозреватель облака, щелкните правой кнопкой мыши приложение и выберите тот же параметр.

3.  Выберите тот же ресурс Application Insights, что и для других данных телеметрии.

    Если требуется, в других конфигурациях службы (облачная, локальная) можно задать другой ресурс. В этом случае данные разработки будут находиться отдельно от динамических данных.

3. При необходимости [исключите ряд функций диагностики Azure](app-insights-azure-diagnostics.md), которые следует перенаправить в Application Insights. Значение по умолчанию — "Все".

### Просмотр событий диагностики Azure

Где найти средства диагностики:

* Счетчики производительности отображаются в виде настраиваемых метрик. 
* Журналы событий Windows отображаются в виде трассировок и настраиваемых событий.
* Журналы приложений, журналы ETW и все журналы инфраструктуры диагностики отображаются в виде трассировок.

Чтобы просмотреть счетчики производительности и счетчики событий, откройте [обозреватель метрик](app-insights-metrics-explorer.md) и добавьте новую диаграмму:


![](./media/app-insights-cloudservices/23-wad.png)

Используйте функцию [Поиск](app-insights-diagnostic-search.md) для поиска в различных журналах трассировки, отправляемых системой диагностики Azure. Например, если в роли есть необработанное исключение, которое вызвало сбой и переработку этой роли, соответствующие данные появятся в канале "Приложение" журнала событий Windows. Функцию поиска можно использовать для поиска ошибки в журнале событий Windows и получения полной трассировки стека для исключения, позволяющей установить причину возникшей проблемы.


![](./media/app-insights-cloudservices/25-wad.png)

## Диагностика приложений

В систему диагностики Azure автоматически включены записи журналов, создаваемые приложением с помощью System.Diagnostics.Trace.

Но если вы уже используете платформы Log4N или NLog, можно также [собирать трассировки журналов][netlogs].

Получить дополнительные сведения о производительности и использовании приложения поможет [отслеживание настраиваемых событий и метрик][api] на стороне клиента либо сервера или и там, и там.

## Зависимости

Пакет SDK Application Insights может сообщить о вызовах, которые ваше приложение отправляет к внешним зависимостям, например к API-интерфейсам REST и серверам SQL Server. Это позволяет увидеть, является ли определенная зависимость причиной медленных ответов или сбоев.

Для отслеживания зависимостей нужно настроить веб-роль и рабочую роль с помощью [агента Application Insights](app-insights-monitor-performance-live-website-now.md), который называется также «Монитор состояния».

Вот что нужно сделать, чтобы использовать агент Application Insights с помощью веб-ролей или рабочих ролей.

* Добавьте папку [AppInsightsAgent](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent) и два находящихся в ней файла в свои проекты веб- и рабочих ролей. Обязательно настройте их средства построения, чтобы эти проекты всегда копировались в выходной каталог. Эти файлы устанавливают агент.
* Добавьте задачу запуска в CSDEF-файл, как показано [ниже](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L18).
* Примечание. Для *рабочих ролей* требуются три переменные среды, как показано [здесь](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L44). Для веб-ролей этого не требуется.

Ниже приведен пример того, что отображается на портале Application Insights.

* Широкие возможности диагностики, включая автоматически коррелируемые запросы и зависимости.

    ![](./media/app-insights-cloudservices/SMxacy4.png)

* Сведения о производительности веб-роли и о зависимостях.

    ![](./media/app-insights-cloudservices/6yOBtKu.png)

* Ниже приведен снимок экрана, на котором показаны сведения о запросах и зависимостях для рабочей роли.

    ![](./media/app-insights-cloudservices/a5R0PBk.png)

## Исключения

Способы сбора необработанных исключений, выдаваемых веб-приложениями разных типов, см. в статье [Мониторинг исключений в Application Insights](app-insights-asp-net-exceptions.md).

Образец веб-роли имеет контроллеры MVC5 и веб-API 2. Необработанные исключения из 2 регистрируются такими объектами:

* объектом [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs), заданным [здесь](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) для контроллеров MVC5;
* объектом [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs), заданным [здесь](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) для контроллеров веб-API 2.

Для рабочих ролей есть два способа отслеживания исключений.

* TrackException(ex);
* Если вы добавили пакет NuGet прослушивателя трассировки Application Insights, вы можете вносить исключения в журнал с помощью System.Diagnostics.Trace. [Пример кода](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## Счетчики производительности

По умолчанию собираются приведенные ниже счетчики.

    * \Process(??APP_WIN32_PROC??)\% Processor Time
	* \Memory\Available Bytes
	* \.NET CLR Exceptions(??APP_CLR_PROC??)# of Exceps Thrown / sec
	* \Process(??APP_WIN32_PROC??)\Private Bytes
	* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
	* \Processor(_Total)\% Processor Time

А здесь приведены счетчики, которые собираются для веб-ролей.

	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec	
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Вы можете указать дополнительные пользовательские счетчики производительности или счетчики производительности Windows, как показано [здесь](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![](./media/app-insights-cloudservices/OLfMo2f.png)

## Коррелированная телеметрия для рабочих ролей

Если вы видите причины неудавшихся запросов или запросов с высокой задержкой, это значит, что вы обладаете широким спектром возможностей диагностики. При работе с веб-ролями пакет SDK автоматически настраивает корреляцию между связанными данными телеметрии. Для рабочих ролей вы можете использовать пользовательский инициализатор телеметрии, чтобы задать атрибут общего контекста Operation.Id, и тогда эта возможность будет доступна для всех сведений телеметрии. Вы сможете узнать причину сбоя или задержки — зависимость или ваш код — с одного взгляда.

Этот процесс описывается далее.

* Задайте идентификатор корреляции в объекте CallContext, как показано [здесь](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). В этом случае мы используем идентификатор запроса как идентификатор корреляции.
* Добавьте пользовательскую реализацию TelemetryInitializer, которая задает Operation.Id в наборе correlationId. Здесь см. [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Добавьте пользовательский инициализатор телеметрии. Это можно сделать в файле ApplicationInsights.config или в коде, как показано [здесь](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

Вот и все! Взаимодействие с порталом уже настроено, и вы можете просматривать все связанные сведения телеметрии одновременно.

![](./media/app-insights-cloudservices/bHxuUhd.png)



## Данные телеметрии клиента

[Добавьте пакет SDK JavaScript на веб-страницы][client], которые позволяют получать браузерные данные телеметрии, такие как число просмотров страниц, время загрузки страницы, исключения скриптов, и записывать настраиваемую телеметрию в скрипты страниц.

## Тесты доступности

[Настройте веб-тесты][availability], которые помогут быть уверенными в том, что приложение остается работоспособным и правильно отвечает на запросы.



## Пример

В [примере](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) отслеживается служба, которая имеет веб-роль и две рабочие роли.

## Связанные разделы

* [Настройка системы диагностики Azure для отправки данных в Application Insights](app-insights-azure-diagnostics.md)
* [Отправка данных системы диагностики Azure в Application Insights с помощью PowerShell])(app-insights-powershell-azure-diagnostics.md)



[api]: app-insights-api-custom-events-metrics.md
[apidefaults]: app-insights-api-custom-events-metrics.md#default-properties
[apidynamicikey]: app-insights-api-custom-events-metrics.md#dynamic-ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

<!---HONumber=AcomDC_0128_2016-->