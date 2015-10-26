<properties 
	pageTitle="Диагностика ошибок и исключений в приложениях ASP.NET с помощью Application Insights" 
	description="Настройте свое приложение для получения диагностических данных путем регистрации исключений и телеметрии запросов." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/07/2015" 
	ms.author="awills"/>
 
# Диагностика ошибок и исключений в приложениях ASP.NET с помощью Application Insights  

Отслеживая приложения с помощью [Visual Studio Application Insights][start], можно сопоставлять неудачные запросы с исключениями и другими событиями на клиенте и сервере, благодаря чему можно быстро выявлять причины неполадок.

Для мониторинга приложения ASP.NET необходимо [добавить пакет SDK Application Insights][greenbrown] в свое приложение или [установить монитор состояния на сервере IIS][redfield], или, если приложение является веб-приложением Azure, добавить [расширение Application Insights][azure].

## Диагностика сбоев 

В колонке обзора на плитке "Сбои" показаны диаграммы исключений, ошибок HTTP-запросов и список URL-адресов запросов, наиболее часто вызывающих сбои.

![Выбор сбоев](./media/app-insights-asp-net-exceptions/012-start.png)

Щелкните один из типов неудачных запросов в списке, чтобы увидеть отдельные экземпляры сбоев. После этого просмотрите исключения или связанные с ними данные трассировки:

![Выберите экземпляр неудачно завершенного запроса и в разделе сведений об исключении перейдите к нужному экземпляру исключения.](./media/app-insights-asp-net-exceptions/030-req-drill.png)


**Кроме того**, можно воспользоваться списком исключений, который можно найти под колонкой "Сбои". Щелкните несколько раз, чтобы найти нужное исключение.


![Детализация](./media/app-insights-asp-net-exceptions/040-exception-drill.png)

*Исключения не отображаются? См. раздел [Запись исключений](#exceptions).*

Оттуда можно просмотреть трассировку стека и подробные свойства каждого исключения и найти связанные журналы трассировки или других событий.


![Детализация](./media/app-insights-asp-net-exceptions/050-exception-properties.png)

[Подробнее о поиске данных диагностики][diagnostic]

## Ошибки зависимости

*Зависимость* является службой, которую вызывает приложение, — обычно через подключение к API REST или базы данных. [Монитор состояния приложений Insights][redfield] автоматически отслеживает различные типы вызова зависимостей, измеряя продолжительность вызова и регистрируя его успешное выполнение или сбой.

Для получения данных о зависимости необходимо [установить монитор состояния][redfield] на сервере IIS или, если приложение является веб-приложением Azure, используйте [расширение Application Insights][azure].

Неудачные вызовы зависимостей перечислены в колонке "Сбои", и вы можете найти их в группе "Связанные элементы" в подробных сведениях о запросе и об исключении.

*Нет ошибок зависимости? Это хорошо. Но, чтобы убедиться, что данные зависимостей получены, откройте колонку "Производительность" и посмотрите на диаграмму "Продолжительность зависимости".*

 

## Пользовательская трассировка и данные журналов

Для получения диагностических данных своего приложения вставьте код для отправки собственных данных телеметрии. Они отображаются в результатах диагностического поиска вместе с запросом, просмотром страницы и другими данными, которые собираются автоматически.

Доступно несколько параметров.

* [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event) обычно используется для контроля шаблонов использования, но отправляемые с его помощью данные также отображаются в разделе пользовательских событий диагностического поиска. События имеют имена и могут содержать свойства строк и числовые показатели, по которым можно [фильтровать результаты диагностического поиска][diagnostic].
* [TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) позволяет отправлять более длинные данные, например данные POST.
* [TrackException()](#exceptions) отправляет трассировки стеков. [Дополнительная информация об исключениях](#exceptions).
* Если вы уже используете какую-либо платформу ведения журналов, например Log4Net или NLog, [эти журналы можно перехватить][netlogs] и включить в результаты диагностического поиска вместе с данными запросов и исключений.

Чтобы увидеть эти события, откройте [Поиск][diagnostic], выберите "Фильтр", а затем пользовательское событие, трассировку и исключение.


![Детализация](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)

### Просмотр данных POST запроса

Сведения о запросе не содержат данные, отправляемые в приложение в вызове метода POST. Для внесения этих данных в отчет:

* [Установите пакет SDK][greenbrown] в проект своего приложения.
* Вставьте в свое приложение код для вызова [Microsoft.ApplicationInsights.TrackTrace()][api]. Передайте данные POST в параметр сообщения. На допустимый размер есть ограничение, поэтому следует пытаться передавать только самые необходимые данные.
* При исследовании неудачно завершенных запросов найдите связанные трассировки.  

![Детализация](./media/app-insights-asp-net-exceptions/060-req-related.png)


## <a name="exceptions"></a> Запись исключений и связанных данных диагностики

Сначала вы не увидите на портале все исключения, которые приводят к сбоям в приложении. Вы увидите все исключения браузера (если на веб-страницах используется [SDK JavaScript][client]). Но большинство серверных исключений перехватываются IIS, и вам нужно написать небольшой код, чтобы увидеть их.

Вы можете:

* **Явно регистрировать исключения** путем вставки кода в обработчики исключений для регистрации исключений.
* **Автоматически записывать исключения** путем настройки своей платформы ASP.NET. Для разных типов платформы необходимы различные дополнения.

## Явная регистрация исключений

Самый простой способ — добавление в обработчик исключений вызова TrackException().

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Свойства и значения параметров не являются обязательными, но они полезны для [фильтрации и добавления][diagnostic] дополнительной информации. Например, если у вас есть приложение, которое запускает несколько игр, вы можете просматривать все отчеты об исключениях для каждой конкретной игры. Вы можете добавить в каждый словарь столько элементов, сколько вам нужно.

## Исключения браузера

Большинство исключений браузера регистрируются.

Если веб-страница содержит файлы сценариев из сети доставки содержимого или других доменов, убедитесь, что сценарий содержит атрибут ```crossorigin="anonymous"```, и что сервер отправляет [заголовки CORS](http://enable-cors.org/). Это позволит вам извлекать трассировки стека и регистрировать необработанные исключения JavaScript из этих ресурсов.

## Веб-формы

Для веб-форм HTTP-модуль будет собирать исключения, если в CustomErrors не настроено перенаправление.

Однако при наличии активных перенаправлений добавьте следующие строки в функцию Application\_Error в сценарий Global.asax.cs. (Добавьте файл Global.asax, если он еще не создан).

*C#*

    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }


## MVC

Если для конфигурации [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) установлено значение `Off`, исключения будут доступны для сбора в [HTTP-модуле](https://msdn.microsoft.com/library/ms178468.aspx). Тем не менее, если это значение `RemoteOnly` (по умолчанию) или `On`, то исключение будет удалено и недоступно для автоматического сбора в Application Insights. Эту ситуацию можно исправить путем переопределения [класса System.Web.Mvc.HandleErrorAttribute](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx) и применения переопределенного класса, как показано для разных версий MVC ([источник на github](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)] 
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above  
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                } 
            }
            base.OnException(filterContext);
        }
      }
    }

#### MVC 2

Замените атрибут HandleError новым атрибутом в контроллерах.

    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...

[Пример](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### MVC 3

Зарегистрируйте `AiHandleErrorAttribute` в качестве глобального фильтра в Global.asax.cs:

    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...

[Пример](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)



#### MVC 4, MVC5

Зарегистрируйте AiHandleErrorAttribute в качестве глобального фильтра в FilterConfig.cs:

    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }

[Пример](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## Web API 1.x


Переопределите System.Web.Http.Filters.ExceptionFilterAttribute:

    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);    
            }
            base.OnException(actionExecutedContext);
        }
      }
    }

Можно добавить этот переопределенный атрибут в нужные контроллеры или в конфигурации глобальных фильтров в классе WebApiConfig:

    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }

[Пример](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

Существует несколько ситуаций, когда обработка фильтров исключений невозможна. Например:

* Исключения выброшены из конструкторов контроллеров. 
* Исключения выброшены из обработчиков сообщений. 
* Исключения выброшены при маршрутизации. 
* Исключения выброшены при сериализации содержимого ответа. 

## Web API 2.x

Добавьте реализацию IExceptionLogger:

    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }

Добавьте это в службы в WebApiConfig:

    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger()); 
        }
      }
  }

[Пример](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

В качестве альтернативы можно выполнить следующее.

2. Замените только ExceptionHandler пользовательской реализацией IExceptionHandler. Он вызывается, только когда платформа по-прежнему может выбирать ответное сообщение для отправки (но не после прерывания соединения для экземпляра) 
3. Фильтры исключений (как описано выше в разделе для контроллеров Web API 1.x) — не вызываются во всех случаях.


## WCF

Добавьте класс, который расширяет Attribute и реализует IErrorHandler и IServiceBehavior.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above 
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error, 
            System.ServiceModel.Channels.MessageVersion version, 
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Добавьте атрибут в реализации службы:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1 
        { 
         ...

[Пример](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## Счетчики производительности исключений

Если на вашем сервере [установлен монитор состояний][redfield], вы можете получить диаграмму частоты исключений, вычисленной платформой .NET. Она включает как обработанные, так и необработанные исключения .NET.

Откройте колонку обозревателя метрик, добавьте новую диаграмму и выберите счетчик **Частота исключений** в списке "Счетчики производительности".

Для вычисление частоты платформа .NET подсчитывает число исключений в интервале и делит его на длину интервала.

Обратите внимание, что она будет отличаться от значения счетчика Exceptions, вычисленного порталом Application Insights по количеству отчетов TrackException. Интервалы выборки различаются, а пакет SDK не отправляет отчеты TrackException для всех обработанных и необработанных исключений.

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[netlogs]: app-insights-asp-net-trace-logs.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

 

<!---HONumber=Oct15_HO3-->