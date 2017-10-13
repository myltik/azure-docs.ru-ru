---
title: "Отделение телеметрии стадий разработки, тестирования и выпуска в Azure Application Insights | Документация Майкрософт"
description: "Отправка телеметрии к различным ресурсам для меток разработки, тестирования и эксплуатации."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: bwren
ms.openlocfilehash: f51fa4639aaa60686cc349683713c6e5f9732bb9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Отделение телеметрии стадий разработки, тестирования и эксплуатации

Разрабатывая следующую версию веб-приложения, вы не хотели бы путать данные телеметрии [Application Insights](app-insights-overview.md), полученные для новой версии, с данными уже выпущенной. Чтобы избежать путаницы, отправляйте данные телеметрии разных стадий разработки в отдельные ресурсы Application Insights, используя отдельные ключи инструментирования. Чтобы облегчить изменение ключа инструментирования в зависимости от стадии разработки, вы можете задать ключи инструментирования в коде, а не в файле конфигурации. 

(Если вы используете облачную службу Azure, есть [другой метод настройки отдельных ключей инструментирования ikey](app-insights-cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Ресурсы и ключи инструментирования

При настройке мониторинга Application Insights для веб-приложения вы создаете *ресурс* Application Insights в Microsoft Azure. Вы открываете этот ресурс на портале Azure для просмотра и анализа телеметрии, собранной из приложения. Каждый ресурс идентифицируется с помощью *ключа инструментирования* (ikey). При установке пакета Application Insights для мониторинга приложения вы можете настроить для него ключ инструментирования. Так ему будет известно, куда отправлять телеметрию.

Необходимость использования разных ресурсов или одного общего ресурса зависит от сценария:

* Различные, независимые приложения: используйте отдельный ресурс и ключ инструментирования для каждого приложения.
* Несколько компонентов или ролей одного бизнес-приложения: используйте [один общий ресурс](app-insights-monitor-multi-role-apps.md) для всех компонентов приложения. Данные телеметрии можно фильтровать и сегментировать с помощью свойства cloud_RoleName.
* Разработка, тестирование и выпуск: используйте отдельный ресурс и ключ инструментирования для версий системы отдельных стадий (меток).
* Тестирование A или B. Используйте один ресурс. Создайте TelemetryInitializer для добавления к телеметрии свойства, определяющего варианты.


## <a name="dynamic-ikey"></a> Динамический ключ инструментирования

Чтобы упростить изменение ключа инструментирования при перемещении кода между этапами производства, задавайте его в коде, а не в файле конфигурации.

Задайте ключ в методе инициализации, таком как global.aspx.cs, в службе ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

В этом примере ключи инструментирования (ikey) для различных ресурсов приводятся в различных версиях файла веб-конфигурации. При смене файла веб-конфигурации, что можно сделать в рамках сценария выпуска, сменится и целевой ресурс.

### <a name="web-pages"></a>Веб-страницы
Ключ инструментирования iKey также используется в веб-страницах приложения, в [сценарии, который вы получили в колонке быстрого запуска](app-insights-javascript.md). Вместо того чтобы вставлять его в код сценария напрямую, генерируйте его из состояния сервера. Например, в приложении ASP.NET:

*JavaScript в Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Создание дополнительных ресурсов Application Insights
Чтобы разделить данные телеметрии для разных компонентов приложения или для разных меток (разработки, тестирования или эксплуатации) одного и того же компонента, вам понадобится создать новый ресурс Application Insights.

Перейдите по адресу [portal.azure.com](https://portal.azure.com)и добавьте новый ресурс Application Insights.

![Нажмите "Создать" и "Application Insights"](./media/app-insights-separate-resources/01-new.png)

* **Тип приложения** определяет содержимое колонки «Обзор» и свойства, доступные в [обозревателе метрик](app-insights-metrics-explorer.md)Microsoft Azure. Если тип вашего приложения не отображается, выберите тип веб-ресурса для веб-страниц.
* **Группа ресурсов** — удобный способ для управления свойствами наподобие [контроля доступа](app-insights-resources-roles-access-control.md). Для разработки, тестирования и эксплуатации можно использовать отдельные группы ресурсов.
* **Подписка** представляет собой учетную запись для оплаты в Azure.
* **Расположение** – это место, в котором мы храним ваши данные. На данном этапе его нельзя изменить. 
* **Добавить на панель мониторинга** позволяет поместить плитку для быстрого доступа к ресурсу на главную страницу Azure. 

Создание ресурса занимает всего несколько секунд. Как только ресурс будет создан, появится предупреждение.

(Для автоматического создания ресурсов можно написать [сценарий PowerShell](app-insights-powershell-script-create-resource.md) .)

### <a name="getting-the-instrumentation-key"></a>Получение ключа инструментирования
Ключ инструментирования идентифицирует созданный вами ресурс. 

![Щелкните Essentials, выделите ключ инструментирования и нажмите клавиши CTRL + C.](./media/app-insights-separate-resources/02-props.png)

Вам потребуются ключи инструментирования всех ресурсов, в которые приложение будет отправлять данные.

## <a name="filter-on-build-number"></a>Фильтрация по номеру сборки
При публикации новой версии приложения имеет смысл отделить телеметрию от других сборок.

Для этого можно настроить свойство "Версия приложения" для фильтрации результатов [поиска](app-insights-diagnostic-search.md) и [обозревателя метрик](app-insights-metrics-explorer.md).

![Фильтрация по свойству](./media/app-insights-separate-resources/050-filter.png)

Свойство «Версия приложения» можно настроить различными способами.

* Напрямую:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Вставьте эту строку в [инициализатор телеметрии](app-insights-api-custom-events-metrics.md#defaults) , чтобы обеспечить согласованность всех экземпляров TelemetryClient.
* [ASP.NET] Задайте версию в `BuildInfo.config`. Веб-модуль берет номер версии из узла BuildLabel. Включите этот файл в проект и не забудьте установить свойство «Всегда копировать» в обозревателе решений.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Настройте автоматическое создание файла BuildInfo.config в MSBuild. Для этого добавьте в файл `.csproj` несколько строк:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Вы получите файл *Имя_проекта*.BuildInfo.config. В процессе публикации он переименовывается в BuildInfo.config.

    При создании сборки с помощью Visual Studio в подпись включается заполнитель (AutoGen_...). Если используется MSBuild, в подписи указывается правильный номер версии.

    Чтобы разрешить MSBuild генерировать номера версий, задайте версию вида `1.0.*` в файле AssemblyReference.cs.

## <a name="version-and-release-tracking"></a>Отслеживание версии и выпуска
Для отслеживания версии приложения убедитесь, что во время выполнения процесса Microsoft Build Engine создается `buildinfo.config`. Добавьте в CSPROJ-файл:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

При наличии данных сборки веб-модуль Application Insights автоматически добавляет **версию приложения** как свойство для каждого элемента телеметрии. Это позволяет применить фильтр по версии при [диагностическом поиске](app-insights-diagnostic-search.md) или [изучении метрик](app-insights-metrics-explorer.md).

Обратите внимание, что номер версии сборки создается только Microsoft Build Engine, а не в процессе сборки в Visual Studio.

### <a name="release-annotations"></a>Примечания к выпуску
Если используется Visual Studio Team Services, можно настроить [добавление маркера заметки](app-insights-annotations.md) к диаграммам при выпуске новой версии. На следующем рисунке показано, как появляется этот маркер.

![Снимок экрана, где показана диаграмма с примером заметки о новом выпуске](./media/app-insights-asp-net/release-annotation.png)
## <a name="next-steps"></a>Дальнейшие действия

* [Monitor multi-component applications with Application Insights (preview)](app-insights-monitor-multi-role-apps.md) (Мониторинг приложений с несколькими компонентами с помощью Application Insights (предварительная версия))
* [Добавление свойств: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties)
