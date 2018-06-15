---
title: Начало работы с примером
description: В этой статье приведены общие сведения о примере для начала работы с коллекциями рабочих областей Power BI.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: maghan
ms.openlocfilehash: 4daa675092bed1788025e7e3f7e7e11d02ef1671
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31414887"
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Пример для начала работы с коллекциями рабочих областей Power BI

С помощью **коллекций рабочих областей Microsoft Power BI** можно интегрировать отчеты Power BI прямо в веб-приложения или мобильные приложения. В этой статье приведены общие сведения о примере для начала работы с **коллекциями рабочих областей Power BI**.

> [!IMPORTANT]
> Использовать службу "Коллекции рабочих областей Power BI" не рекомендуется. Она будет доступна до июня 2018 года или до даты, указанной в договоре. Мы советуем организовать перенос приложения в Power BI Embedded, чтобы избежать прерываний в его работе. Сведения о том, как перенести данные из коллекций рабочих областей Power BI в Power BI Embedded, см. в [этой статье](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Прежде чем мы продолжим, необходимо сохранить приведенные ниже ресурсы. Они помогут при интеграции отчетов Power BI в пример приложения, а также в ваши приложения.

* [Пример веб-приложения рабочей области](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Справочник по API коллекций рабочих областей Power BI](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [Пакет SDK Power BI для .NET ](http://go.microsoft.com/fwlink/?LinkId=746472) (доступен в NuGet)
* [Пример внедрения отчета JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Перед настройкой и запуском примера для начала работы с коллекциями рабочих областей Power BI необходимо создать по крайней мере одну **коллекцию рабочих областей** в подписке Azure. Узнайте, как создать **коллекцию рабочей области** на портале Azure, ознакомившись с разделом [Начало работы с коллекциями рабочих областей Power BI](get-started.md).

## <a name="configure-the-sample-app"></a>Настройка примера приложения

Давайте подробнее рассмотрим действия по настройке среды разработки Visual Studio для доступа к компонентам, необходимым для запуска примера приложения.

1. Скачайте и распакуйте пример для [интеграции отчета в веб-приложение с помощью коллекций рабочих областей Power BI](http://go.microsoft.com/fwlink/?LinkId=761493) с сайта GitHub.
2. Откройте файл **PowerBI-embedded.sln** в Visual Studio. Может потребоваться выполнить команду **Update-Package** в консоли диспетчера пакетов NuGET, чтобы обновить пакеты, используемые в этом решении.
3. Выполните сборку решения.
4. Запустите консольное приложение **ProvisionSample** . В примере консольного приложения необходимо подготовить рабочую область и импортировать файл PBIX.
5. Чтобы подготовить новую **рабочую область**, выберите параметр 1 — **Collection management** (Управление коллекциями), а затем параметр 6 — **Provision a new Workspace** (Подготовка новой рабочей области).
6. Чтобы импортировать новый **отчет**, выберите параметр 2 — **Report management** (Управление отчетами), а затем параметр 3 — **Import PBIX Desktop file into a workspace** (Импорт PBIX-файла рабочего стола в рабочую область).

7. Введите имя **коллекции рабочих областей** и **ключ доступа**. Их можно получить на **портале Azure**. Дополнительные сведения о том, как получить **ключ доступа**, см. в разделе [Просмотр ключей доступа для вызова API Power BI](get-started.md#view-power-bi-api-access-keys) статьи "Начало работы с Microsoft Power BI Embedded".

    ![Ключи доступа на портале Azure](media/get-started-sample/azure-portal.png)
8. Скопируйте и сохраните созданный **идентификатор рабочей области** для дальнейшего использования в этой статье. После создания **идентификатора рабочей области** его можно найти на **портале Azure**.

    ![Идентификатор рабочей области на портале Azure](media/get-started-sample/workspace-id.png)
9. Чтобы импортировать PBIX-файл в свою **рабочую область**, выберите вариант **6. Импорт файла PBIX рабочего стола в существующую рабочую область**. Если у вас нет PBIX-файла, то можете скачать [PBIX-файл примера анализа розничной торговли](http://go.microsoft.com/fwlink/?LinkID=780547).
10. При появлении запроса укажите понятное имя для своего **набора данных**.

Вы должны получить примерно следующий ответ:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Если файл PBIX содержит подключения для прямых запросов, выберите вариант 7 для обновления строк подключения.

На этом этапе у вас есть PBIX-файл отчета Power BI, импортированный в **рабочую область**. Теперь рассмотрим, как запустить пример веб-приложения для начала работы с **коллекциями рабочих областей Power BI**.

## <a name="run-the-sample-web-app"></a>Запуск примера веб-приложения

Пример веб-приложения представляет собой приложение, в котором отображаются отчеты, импортированные в **рабочую область**. Вот как настроить пример веб-приложения.

1. В решении **PowerBI-embedded** Visual Studio щелкните веб-приложение **EmbedSample** правой кнопкой мыши и выберите **Назначить запускаемым проектом**.
2. В файле **web.config** в веб-приложении **EmbedSample** измените параметры приложения **appSettings**: ключ доступа **AccessKey**, имя коллекции рабочих областей **WorkspaceCollection** и идентификатор рабочей области **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Запустите веб-приложение **EmbedSample**.

После запуска веб-приложения **EmbedSample** в левой навигационной панели должно появиться меню **Отчеты**. Чтобы просмотреть отчет, который был импортирован, разверните **Отчеты** и щелкните отчет. Если вы импортировали [PBIX-файл примера анализа розничной торговли](http://go.microsoft.com/fwlink/?LinkID=780547), пример веб-приложения будет выглядеть следующим образом.

![Левая область навигации в примере приложения](media/get-started-sample/sample-left-nav.png)

После выбора отчета веб-приложение **EmbedSample** должно выглядеть примерно так:

![Отображение примера отчета в приложении](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Изучение примера кода

Пример для **коллекций рабочих областей Power BI** представляет собой веб-приложение и показывает, как интегрировать отчеты **Power BI** в приложение. Для демонстрации рекомендаций в нем используется шаблон проектирования "модель-представление-контроллер" (MVC). В этом разделе описаны части примера кода, которые можно изучить в решении веб-приложения **PowerBI-embedded**. Шаблон "модель-представление-контроллер" (MVC) разбивает модель домена, представление и действия на основе ввода пользователя на три различных класса: модель, представление и контроллер. Дополнительные сведения о MVC см. в документации [ASP.NET](http://www.asp.net/mvc).

Пример кода для **коллекций рабочих областей Power BI** разделяется следующим образом. Каждая часть включает имя файла для решения PowerBI-embedded.sln, поэтому вы легко найдете соответствующий код в примере.

> [!NOTE]
> В этом разделе приведены общие сведения о примере кода, которые показывают, как был написан этот код. Чтобы просмотреть полный пример, загрузите решение PowerBI-embedded.sln в Visual Studio.

### <a name="model"></a>Модель

Пример содержит следующие модели: **ReportsViewModel** и **ReportViewModel**.

**ReportsViewModel.cs**: представляет отчеты Power BI.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: представляет отчет Power BI.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### <a name="connection-string"></a>Строка подключения

Строка подключения должна быть в следующем формате:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Попытка использования общих атрибутов сервера и базы данных завершается ошибкой. Например: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Просмотр

**Представление** управляет отображением **отчетов** и **отчета** Power BI.

**Reports.cshtml**: выполняет итерацию по отчетам **Model.Reports**, чтобы создать **ActionLink**. **ActionLink** формируется следующим образом:

| Часть | ОПИСАНИЕ |
| --- | --- |
| Название |Имя отчета. |
| QueryString |Ссылка на идентификатор отчета. |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: устанавливает **Model.AccessToken** и лямбда-выражение для **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### <a name="controller"></a>Controller

**DashboardController.cs**: создает PowerBIClient, передающий **маркер приложения**. Для получения **учетных данных** создается JSON Web Token (JWT) на основе **ключа подписывания**. **Учетные данные** используются для создания экземпляра **PowerBIClient**. После создания экземпляра **PowerBIClient** можно вызвать методы GetReports() и GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Задача<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### <a name="integrate-a-report-into-your-app"></a>Интеграция отчета в приложение

После получения **отчета** можно использовать **IFrame** для внедрения **отчета** Power BI. Ниже приведен фрагмент кода из файла powerbi.js примера для **коллекций рабочих областей Power BI**.

```
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<igrame style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Фильтрация отчетов, внедренных в приложение

Можно отфильтровать внедренный отчет, используя синтаксис URL-адреса. Для этого добавьте параметр строки запроса **$filter** с оператором **eq** в исходный URL-адрес iFrame с указанием фильтра. Ниже приведен синтаксис запроса фильтра.

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} не может содержать пробелы или специальные знаки. {FieldValue} принимает одно дискретное значение.  

## <a name="see-also"></a>См. также

[Распространенные сценарии работы с коллекциями рабочих областей Microsoft Power BI](scenarios.md)  
[Аутентификация и авторизация в коллекциях рабочих областей Power BI](app-token-flow.md)  
[Внедрение отчета в Power BI Embedded](embed-report.md)  
[Создание отчета из набора данных](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Пример внедрения JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

У вас имеются и другие вопросы? [Попробуйте задать их в сообществе Power BI](http://community.powerbi.com/)
