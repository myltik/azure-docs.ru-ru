---
title: Начало работы с примером
description: Power BI Embedded, использование пакета SDK для добавления интерактивных отчетов Power BI в приложение бизнес-аналитики
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''

ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton

---
# <a name="get-started-with-power-bi-embedded-sample"></a>Начало работы с примером Microsoft Power BI Embedded
С помощью **Microsoft Power BI Embedded**можно интегрировать отчеты Power BI прямо в веб- или мобильные приложения. В этой статье приведены общие сведения о примере для начала работы с **Power BI Embedded** .

Прежде чем мы продолжим, вам следует сохранить приведенные ниже ресурсы. Они помогут вам при интеграции отчетов Power BI в пример приложения, а также в собственные приложения.

* [Пример панели мониторинга веб-приложения](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Справочник по API Power BI Embedded](https://msdn.microsoft.com/library/mt711493.aspx)
* [Пакет SDK Power BI Embedded для .NET ](http://go.microsoft.com/fwlink/?LinkId=746472) (доступен в NuGet)

> [!NOTE]
> Перед настройкой и запуском примера Power BI Embedded необходимо создать по крайней мере одну **коллекцию рабочих областей** в подписке Azure. Сведения о создании **коллекции рабочей области ** на портале Azure см. в статье [Начало работы с Microsoft Power BI Embedded](power-bi-embedded-get-started.md).
> 
> 

## <a name="configure-the-sample-app"></a>Настройка примера приложения
Давайте подробнее рассмотрим действия по настройке среды разработки Visual Studio для доступа к компонентам, необходимым для запуска примера приложения.

1. Загрузите и распакуйте пример [Power BI Embedded — интеграция отчета в веб-приложение](http://go.microsoft.com/fwlink/?LinkId=761493) в GitHub.
2. Откройте файл **PowerBI-embedded.sln** в Visual Studio. Может потребоваться выполнить команду **Update-Package** в консоли диспетчера пакетов NuGET, чтобы обновить пакеты, используемые в этом решении.
3. Выполните сборку решения.
4. Запустите консольное приложение **ProvisionSample** . В примере консольного приложения необходимо подготовить рабочую область и импортировать файл PBIX.
5. Чтобы подготовить новую **рабочую область**, выберите вариант 5, **Provision a new workspace in an existing workspace collection** (Подготовить новую рабочую область в имеющейся коллекции рабочих областей).
   
    ![](media\\powerbi-embedded-get-started-sample\\console-option-5.png)
6. Введите имя **коллекции рабочих областей** и **ключ доступа**. Их можно получить на **портале Azure**. Дополнительные сведения о том, как получить **ключ доступа**, см. в разделе [Просмотр ключей доступа для вызова API Power BI](power-bi-embedded-get-started-sample.md#view-access-keys) статьи "Начало работы с Microsoft Power BI Embedded".
   
    ![](media\\powerbi-embedded-get-started-sample\\azure-portal.png)
7. Скопируйте и сохраните созданный **идентификатор рабочей области** для дальнейшего использования в этой статье. После создания **идентификатора рабочей области** его можно найти на **портале Azure**.
   
    ![](media\\powerbi-embedded-get-started-sample\\workspace-id.png)
8. Чтобы импортировать PBIX-файл в свою **рабочую область**, выберите вариант **6. Импорт файла PBIX рабочего стола в существующую рабочую область**. Если у вас под рукой нет PBIX-файла, то вы можете скачать [PBIX-файл примера анализа розничной торговли](http://go.microsoft.com/fwlink/?LinkID=780547).
9. При появлении запроса укажите понятное имя для своего **набора данных**.

Вы должны получить примерно следующий ответ:

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> If your PBIX file contains any direct query connections, run option 7 to update the connection strings.
> 
> 

At this point, you have a Power BI PBIX report imported into your **Workspace**. Now, let's look at how to run the **Power BI Embedded** get started sample web app.

## Run the sample web app
The web app sample is a sample dashboard that renders reports imported into your **Workspace**. Here's how to configure the web app sample.

1. In the **PowerBI-embedded** Visual Studio solution, right click the **EmbedSample** web application, and choose **Set as StartUp project**.
2. In **web.config**, in the **EmbedSample** web application, edit the **appSettings**: **AccessKey**, **WorkspaceCollection** name, and **WorkspaceId**.
   
    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Run the **EmbedSample** web application.

Once you run the **EmbedSample** web application, the left navigation panel should contain a **Reports** menu. To view the report you imported, expand **Reports**, and click a report. If you imported the [Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), the sample web app would look like this:

![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-sample-left-nav.png)

After you click a report, the **EmbedSample** web application should look something this:

![](media\\powerbi-embedded-get-started-sample\\sample-web-app.png)

## Explore the sample code
The **Microsoft Power BI Embedded** sample is an example dashboard web app that shows you how to integrate **Power BI** reports into your app. It uses a Model-View-Controller (MVC) design pattern to demonstrate best practices. This section highlights parts of the sample code that you can explore within the **PowerBI-embedded** web app solution. The Model-View-Controller (MVC) pattern separates the modeling of the domain, the presentation, and the actions based on user input into three separate classes: Model, View, and Control. To learn more about MVC, see [Learn About ASP.NET](http://www.asp.net/mvc).

The **Microsoft Power BI Embedded** sample code is separated as follows. Each section includes the file name in the PowerBI-embedded.sln solution so that you can easily find the code in the sample.

> [!NOTE]
> This section is a summary of the sample code that shows how the code was written. To view the complete sample, please load the PowerBI-embedded.sln solution in Visual Studio.
> 
> 

### Model
The sample has a **ReportsViewModel** and **ReportViewModel**.

**ReportsViewModel.cs**: Represents Power BI Reports.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: Represents a Power BI Report.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### Connection string
The connection string must be in the following format:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Using common server and database attributes will fail. For example: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### View
The **View** manages the display of Power BI **Reports** and a Power BI **Report**.

**Reports.cshtml**: Iterate over **Model.Reports** to create an **ActionLink**. The **ActionLink** is composed as follows:

| Part | Description |
| --- | --- |
| Title |Name of the Report. |
| QueryString |A link to the Report ID. |

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

Report.cshtml: Set the **Model.AccessToken**, and the Lambda expression for **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller
**DashboardController.cs**: Creates a PowerBIClient passing an **app token**. A JSON Web Token (JWT) is generated from the **Signing Key** to get the **Credentials**. The **Credentials** are used to create an instance of **PowerBIClient**. Once you have an instance of **PowerBIClient**, you can call GetReports() and GetReportsAsync().

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


Task<ActionResult> Report(string reportId)

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

### Integrate a report into your app
Once you have a **Report**, you use an **IFrame** to embed the Power BI **Report**. Here is a code snippet from  powerbi.js in the **Microsoft Power BI Embedded** sample.

![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-iframe-code.png)

## Filter reports embedded in your application
You can filter an embedded report using a URL syntax. To do this, you add a **$filter** query string parameter with an **eq** operator to your iFrame src url with the filter specified. Here is the filter query syntax:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} cannot include spaces or special characters. The {fieldValue} accepts a single categorical value.  
> 
> 

## See also
* [Common Microsoft Power BI Embedded scenarios](power-bi-embedded-scenarios.md)
* [Authenticating and authorizing in Power BI Embedded](power-bi-embedded-app-token-flow.md)

<!--HONumber=Oct16_HO2-->


