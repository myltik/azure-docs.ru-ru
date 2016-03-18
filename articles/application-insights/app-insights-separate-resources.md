<properties 
	pageTitle="Отдельные ресурсы Application Insights для разработки, тестирования и эксплуатации" 
	description="Отслеживание производительности и использования приложения на разных этапах разработки" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/25/2015" 
	ms.author="awills"/>

# Отдельные ресурсы Application Insights для разработки, тестирования и эксплуатации


Чтобы данные телеметрии, передаваемые отладочными, тестовыми и рабочими версиями вашего приложения, не смешивались друг с другом, создайте отдельный ресурс [Application Insights][start] для получения данных из каждой версии.

Полученные из приложения данные хранятся и обрабатываются службой Application Insights в *ресурсе* Microsoft Azure. Каждый ресурс идентифицируется с помощью *ключа инструментирования*. В приложении ключ предоставляется пакету SDK службы Application Insights, чтобы тот мог отправлять собранные данные в соответствующий ресурс. Ключ можно указать в коде или в файле ApplicationInsights.config. Изменив ключ в пакете SDK, можно направить данные в другие ресурсы.


## Создание ресурса Application Insights
  

Перейдите по адресу [portal.azure.com](https://portal.azure.com) и добавьте новый ресурс Application Insights.

![Нажмите «Создать» и «Application Insights»](./media/app-insights-separate-resources/01-new.png)


* Значение в поле **Тип приложения** определяет содержимое колонки «Обзор» и свойства, доступные в [обозревателе метрик][metrics]. Если вы не видите тип своего приложения, выберите тип веб-ресурса для веб-страниц и тип телефона для других устройств.
* **Группа ресурсов** — удобный способ для управления свойствами наподобие [контроля доступа](app-insights-resources-roles-access-control.md). Для разработки, тестирования и эксплуатации можно использовать отдельные группы ресурсов.
* **Подписка** представляет собой учетную запись для оплаты в Azure.
* **Расположение** – это место, в котором мы храним ваши данные. На данном этапе его нельзя изменить.
* **Добавить на начальную панель** позволяет поместить плитку для быстрого доступа к ресурсу на главную страницу Azure 

Создание ресурса занимает всего несколько секунд. Как только ресурс будет создан, появится предупреждение.

(Для автоматического создания ресурсов можно написать [сценарий PowerShell](app-insights-powershell-script-create-resource.md).)


## Копирование ключа инструментирования

Ключ инструментирования идентифицирует созданный вами ресурс.

![Щелкните Essentials, выделите ключ инструментирования и нажмите клавиши CTRL + C.](./media/app-insights-separate-resources/02-props.png)

Вам потребуются ключи инструментирования всех ресурсов, в которые приложение будет отправлять данные.


## <a name="dynamic-ikey"></a> Динамический ключ инструментирования

Обычно пакет SDK получает iKey из файла ApplicationInsights.config. Процесс можно упростить, указав ключи в коде.

Задайте ключ в методе инициализации, таком как global.aspx.cs, в службе ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

В этом примере ключи инструментирования (ikey) для различных ресурсов приводятся в различных версиях файла веб-конфигурации. При смене файла веб-конфигурации сменится и целевой ресурс.

#### Веб-страницы

Ключ инструментирования iKey также используется в веб-страницах приложения, в [сценарии, который вы получили в колонке быстрого запуска](app-insights-javascript.md). Вместо того чтобы вставлять его в код сценария напрямую, генерируйте его из состояния сервера. Например, в приложении ASP.NET:

*JavaScript в Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...





<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_1203_2015-->