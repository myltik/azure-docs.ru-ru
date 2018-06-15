---
title: Диагностика ошибок и исключений в веб-приложениях с помощью Application Insights | Документация Майкрософт
description: Регистрируйте исключения приложений ASP.NET и телеметрию запросов.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: a3dcf4211df5d40c4b174fd9a818d3268ffaa3a0
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295716"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Диагностика исключений в веб-приложениях с помощью Application Insights
Об исключениях активного веб-приложения сообщает [Application Insights](app-insights-overview.md). Вы можете сопоставлять неудачно завершенные запросы с исключениями и другими событиями на клиенте и сервере, чтобы быстро выявлять причины неполадок.

## <a name="set-up-exception-reporting"></a>Настройка создания отчетов об исключениях
* Вот как можно настроить создание отчетов об исключениях серверного приложения.
  * Запрограммируйте установку [пакета SDK для Application Insights](app-insights-asp-net.md) в приложении или:
  * веб-серверы IIS: запустите [агент Application Insights](app-insights-monitor-performance-live-website-now.md) либо
  * веб-приложения Azure: добавьте [расширение Application Insights](app-insights-azure-web-apps.md).
  * Веб-приложения Java: установите [агент Java](app-insights-java-agent.md)
* Добавьте [фрагмент кода JavaScript](app-insights-javascript.md) в веб-страницы для перехвата исключений браузера.
* Для некоторых платформ приложений или заданных параметров необходимо выполнить дополнительные шаги, чтобы перехватывать дополнительные исключения:
  * [веб-формы](#web-forms);
  * [MVC](#mvc);
  * [веб-API 1.*](#web-api-1x);
  * [веб-API 2.*](#web-api-2x);
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Диагностика исключений с помощью Visual Studio
Откройте приложение в Visual Studio для отладки.

Запустите приложение на сервере или на компьютере разработки, нажав клавишу F5.

Откройте окно поиска Application Insights в Visual Studio и настройте его для отображения событий из приложения. Во время отладки это можно сделать, просто нажав кнопку Application Insights.

![Щелкните правой кнопкой мыши проект и последовательно выберите пункты "Application Insights" и "Открыть".](./media/app-insights-asp-net-exceptions/34.png)

Обратите внимание: отчет можно отфильтровать так, чтобы отображались только исключения.

*Исключения не отображаются? См. раздел [Запись исключений](#exceptions).*

Щелкните отчет об исключении, чтобы отобразить для него трассировку стека.
Щелкните ссылку на строку в трассировке стека, чтобы открыть соответствующий файл кода.  

В коде обратите внимание, что CodeLens показывает данные об исключениях.

![Уведомление об исключениях CodeLens](./media/app-insights-asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Диагностика сбоев с помощью портала Azure
В Application Insights входят проверенные возможности APM, которые помогут вам диагностировать сбои в отслеживаемых приложениях. Чтобы начать, в меню ресурсов Application Insights щелкните параметр "Сбои" в разделе "Сведения". Отобразится полноэкранное представление с тенденциями частоты сбоев для запросов, количеством невыполненных запросов и числом затронутых пользователей. Справа вы увидите некоторые из наиболее полезных распределений, относящиеся к выбранным операциям сбоя, включая первые 3 кода ответов, первые 3 типа исключений и 3 основных типа неудачных зависимостей. 

![Рассмотрение ошибок на вкладке "Операции"](./media/app-insights-asp-net-exceptions/FailuresTriageView.png)

Одним щелчком можно проанализировать типичные примеры для каждого из этих подмножеств операций. Например, для диагностики исключений можно щелкнуть счетчик числа нужных исключений и откроется колонка со сведениями об исключениях, например такая:

![Колонка сведений об исключениях](./media/app-insights-asp-net-exceptions/ExceptionDetailsBlade.png)

**Кроме того,** вместо просмотра исключений определенной операции со сбоем вы можете начать с общего представления исключений, перейдя на вкладку Exceptions (Исключения):

![Представление отсортированных ошибок на вкладке Exceptions (Исключения)](./media/app-insights-asp-net-exceptions/FailuresTriageView_Exceptions.png)

Здесь вы увидите все исключения, собранные для отслеживаемого приложения.

*Исключения не отображаются? См. раздел [Запись исключений](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Пользовательская трассировка и данные журналов
Для получения диагностических данных своего приложения вставьте код для отправки собственных данных телеметрии. Они отображаются в результатах диагностического поиска вместе с запросом, просмотром страницы и другими данными, которые собираются автоматически.

Доступно несколько параметров.

* [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) обычно используется для мониторинга шаблонов использования, но отправляемые с его помощью данные также отображаются в разделе пользовательских событий диагностического поиска. У событий есть имена и они могут содержать строковые свойства и числовые метрики, по которым можно [фильтровать результаты поиска диагностических данных](app-insights-diagnostic-search.md).
* [TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) позволяет отправлять более длинные данные, например данные POST.
* [TrackException()](#exceptions) отправляет трассировки стеков. [Дополнительные сведения об исключениях](#exceptions).
* Если вы уже используете какую-либо платформу ведения журналов, например Log4Net или NLog, [эти журналы можно записывать](app-insights-asp-net-trace-logs.md) и включать в результаты поиска диагностических данных вместе с данными запросов и исключений.

Чтобы просмотреть эти события, откройте область [Поиск](app-insights-diagnostic-search.md), щелкните "Фильтр", а затем выберите пользовательское событие, трассировку и исключение.

![Детализация](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> Если приложение генерирует много телеметрических данных, модуль адаптивной выборки автоматически сокращает объем отправляемых на портал данных, пересылая только репрезентативную часть событий. События, составляющие часть той же операции, отбираются как группа, что позволяет перемещаться между связанными событиями. [Дополнительная информация о выборке.](app-insights-sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Просмотр данных POST запроса
Сведения о запросе не содержат данные, отправляемые в приложение в вызове метода POST. Для внесения этих данных в отчет:

* [Установите пакет SDK](app-insights-asp-net.md) в проект своего приложения.
* Вставьте в свое приложение код для вызова [Microsoft.ApplicationInsights.TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace). Передайте данные POST в параметр сообщения. На допустимый размер есть ограничение, поэтому следует пытаться передавать только самые необходимые данные.
* При исследовании неудачно завершенных запросов найдите связанные трассировки.  

![Детализация](./media/app-insights-asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a> Запись исключений и связанных диагностических данных
Сначала вы не увидите на портале все исключения, которые приводят к сбоям в приложении. Вы увидите все исключения браузера (если на веб-страницах используется [пакет SDK для JavaScript](app-insights-javascript.md)). Но большинство серверных исключений перехватываются IIS, и вам нужно написать небольшой код, чтобы увидеть их.

Вы можете:

* **Явно регистрировать исключения** путем вставки кода в обработчики исключений для регистрации исключений.
* **Автоматически записывать исключения** путем настройки своей платформы ASP.NET. Для разных типов платформы необходимы различные дополнения.

## <a name="reporting-exceptions-explicitly"></a>Явная регистрация исключений
Самый простой способ — добавление в обработчик исключений вызова TrackException().

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
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
```

```VB
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
```

Параметры свойств и значений не являются обязательными, но они удобны для [фильтрации и добавления](app-insights-diagnostic-search.md) дополнительной информации. Например, если у вас есть приложение, которое запускает несколько игр, вы можете просматривать все отчеты об исключениях для каждой конкретной игры. Вы можете добавить в каждый словарь столько элементов, сколько вам нужно.

## <a name="browser-exceptions"></a>Исключения браузера
Большинство исключений браузера регистрируются.

Если веб-страница содержит файлы сценариев из сети доставки содержимого или других доменов, убедитесь, что тег сценария содержит атрибут ```crossorigin="anonymous"```, а сервер отправляет [заголовки CORS](http://enable-cors.org/). Это позволит вам извлекать трассировки стека и регистрировать необработанные исключения JavaScript из этих ресурсов.

## <a name="web-forms"></a>Веб-формы
Для веб-форм HTTP-модуль будет собирать исключения, если в CustomErrors не настроено перенаправление.

Однако при наличии активных перенаправлений добавьте следующие строки в функцию Application_Error в сценарий Global.asax.cs. (Добавьте файл Global.asax, если он еще не создан).

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```

## <a name="mvc"></a>MVC
Начиная с версии веб-пакета SDK 2.6 (бета-версии 3 и более поздних версий) для Application Insights в службе выполняется сбор необработанных исключений, автоматически созданных в методах контроллеров MVC 5+. Если ранее для отслеживания таких исключений вы добавляли настраиваемый обработчик (как указано в приведенных ниже примерах), можете удалить его, чтобы избежать двойного отслеживания исключений.

Существует несколько ситуаций, когда обработка фильтров исключений невозможна. Например: 

* Исключения выброшены из конструкторов контроллеров.
* Исключения выброшены из обработчиков сообщений.
* Исключения выброшены при маршрутизации.
* Исключения выброшены при сериализации содержимого ответа.
* исключение, возникшее при запуске приложения;
* исключение, возникшее в фоновых задачах.

Все исключения, которые *обрабатывает* приложение, по-прежнему должны отслеживаться вручную. Необработанные исключения, исходящие от контроллеров, приводят к ответу 500 — "Внутренняя ошибка сервера". Если такой ответ создан вручную как результат обработки исключения (или при отсутствии исключений), он будет отслеживаться в соответствующей телеметрии запросов с заданным для `ResultCode` значением 500, хотя пакет SDK для Application Insights не позволяет отслеживать соответствующее исключение.

### <a name="prior-versions-support"></a>Поддержка предыдущих версий
Примеры отслеживания исключений для MVC 4 (и более ранних версий) веб-пакета SDK 2.5 (и более ранних версий) для Application Insights приведены ниже.

Если для конфигурации [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) установлено значение `Off`, исключения будут доступны для сбора в [HTTP-модуле](https://msdn.microsoft.com/library/ms178468.aspx). Тем не менее, если это значение `RemoteOnly` (по умолчанию) или `On`, то исключение будет удалено и недоступно для автоматического сбора в Application Insights. Эту ситуацию можно исправить, переопределив [класс System.Web.Mvc.HandleErrorAttribute](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx) и применив переопределенный класс, как показано для разных версий MVC ([источник на GitHub](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

```csharp
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
```

#### <a name="mvc-2"></a>MVC 2
Замените атрибут HandleError новым атрибутом в контроллерах.

```csharp
    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...
```

[Пример](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Зарегистрируйте `AiHandleErrorAttribute` в качестве глобального фильтра в Global.asax.cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Пример](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Зарегистрируйте AiHandleErrorAttribute в качестве глобального фильтра в FilterConfig.cs:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Пример](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Веб-интерфейс API
Начиная с версии веб-пакета SDK 2.6 (бета-версии 3 и более поздних версий) для службы Application Insights в службе выполняется сбор необработанных исключений, автоматически созданных в методах контроллеров для WebAPI 2+. Если ранее для отслеживания таких исключений вы добавляли настраиваемый обработчик (как указано в приведенных ниже примерах), можете удалить его, чтобы избежать двойного отслеживания исключений.

Существует несколько ситуаций, когда обработка фильтров исключений невозможна. Например: 

* Исключения выброшены из конструкторов контроллеров.
* Исключения выброшены из обработчиков сообщений.
* Исключения выброшены при маршрутизации.
* Исключения выброшены при сериализации содержимого ответа.
* исключение, возникшее при запуске приложения;
* исключение, возникшее в фоновых задачах.

Все исключения, которые *обрабатывает* приложение, по-прежнему должны отслеживаться вручную. Необработанные исключения, исходящие от контроллеров, приводят к ответу 500 — "Внутренняя ошибка сервера". Если такой ответ создан вручную как результат обработки исключения (или при отсутствии исключений), он будет отслеживаться в соответствующей телеметрии запросов с заданным для `ResultCode` значением 500, хотя пакет SDK для Application Insights не позволяет отслеживать соответствующее исключение.

### <a name="prior-versions-support"></a>Поддержка предыдущих версий
Примеры отслеживания исключений для WebAPI 1 (и более ранних версий) веб-пакета SDK 2.5 (и более ранних версий) для Application Insights приведены ниже.

#### <a name="web-api-1x"></a>Web API 1.x
Переопределите System.Web.Http.Filters.ExceptionFilterAttribute:

```csharp
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
```

Можно добавить этот переопределенный атрибут в нужные контроллеры или в конфигурации глобальных фильтров в классе WebApiConfig:

```csharp
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
```

[Пример](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web API 2.x
Добавьте реализацию IExceptionLogger:

```csharp
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
```

Добавьте это в службы в WebApiConfig:

```csharp
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
```

[Пример](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

В качестве альтернативы можно выполнить следующее.

1. Замените только ExceptionHandler пользовательской реализацией IExceptionHandler. Он вызывается, только когда платформа по-прежнему может выбирать ответное сообщение для отправки (но не после прерывания соединения для экземпляра)
2. Фильтры исключений (как описано выше в разделе для контроллеров Web API 1.x) — не вызываются во всех случаях.

## <a name="wcf"></a>WCF
Добавьте класс, который расширяет Attribute и реализует IErrorHandler и IServiceBehavior.

```csharp
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

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Пример](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Счетчики производительности исключений
Если на сервере [установлен агент Application Insights](app-insights-monitor-performance-live-website-now.md), то можно получить диаграмму частоты исключений, вычисленной платформой .NET. Она включает как обработанные, так и необработанные исключения .NET.

Откройте колонку обозревателя метрик, добавьте новую диаграмму и выберите счетчик **Частота исключений**в списке "Счетчики производительности".

Для вычисление частоты платформа .NET подсчитывает число исключений в интервале и делит его на длину интервала.

Она будет отличаться от значения счетчика Exceptions, вычисленного порталом Application Insights по количеству отчетов TrackException. Интервалы выборки различаются, а пакет SDK не отправляет отчеты TrackException для всех обработанных и необработанных исключений.

## <a name="video"></a>Видео

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="next-steps"></a>Дополнительная информация
* [Настройка Application Insights: отслеживание зависимостей](app-insights-asp-net-dependencies.md)
* [Мониторинг времени загрузки страниц, исключений браузера и вызовов AJAX](app-insights-javascript.md)
* [Мониторинг счетчиков производительности](app-insights-performance-counters.md)
