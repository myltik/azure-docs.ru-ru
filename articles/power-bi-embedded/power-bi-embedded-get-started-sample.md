<properties
   pageTitle="Приступая к работе с примером"
   description=""
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Приступая к работе с примером Microsoft Power BI Embedded

**Предварительная версия Microsoft Power BI Embedded** позволяет интегрировать отчеты Power BI в веб-приложения или мобильные приложения, позволяя обойтись без создания собственных решений визуализации данных для пользователей. Чтобы приступить к интеграции отчетов Power BI в свое приложение, воспользуйтесь следующими ресурсами.

 -	[Пример панели мониторинга веб-приложения](http://go.microsoft.com/fwlink/?LinkId=761493)
 -	[Справочник по API Power BI Embedded](https://msdn.microsoft.com/library/mt712303.aspx)
 -	[Пакет SDK Power BI Embedded для .NET \(доступен в NuGet\)](http://go.microsoft.com/fwlink/?LinkId=746472)

В этой статье приведены общие сведения о примере для начала работы с **Power BI Embedded**. Давайте приступим к настройке примера приложения, чтобы вы могли запустить пример веб-приложения.

> [AZURE.NOTE] Перед настройкой и запуском примера Power BI Embedded необходимо создать по крайней мере одну **коллекцию рабочей области** в подписке Azure. Чтобы узнать, как создать **коллекцию рабочей области** на портале Azure, обратитесь к разделу [Приступая к работе с предварительной версией Power BI Embedded](power-bi-embedded-get-started.md).

## Настройка примера приложения

Далее описаны действия по настройке среды разработки Visual Studio для доступа к компонентам предварительной версии, необходимым для запуска примера приложения.

1. Загрузите и распакуйте пример [Power BI Embedded — интеграция отчета в веб-приложение](http://go.microsoft.com/fwlink/?LinkId=761493) в GitHub.

2. Откройте файл **PowerBI-embedded.sln** в Visual Studio.

3. Выполните сборку решения.

4. Запустите консольное приложение **ProvisionSample**. В примере консольного приложения необходимо подготовить рабочую область и импортировать файл PBIX.

5. Чтобы подготовить новую **рабочую область**, выберите параметр **5. Подготовьте новую рабочую область в существующую коллекцию рабочих областей**.

    ![](media\powerbi-embedded-get-started-sample\console-option-5.png)

6. Введите имя **Коллекции рабочих областей** и **Ключ доступа**. Их можно получить на **портале Azure**. Дополнительные сведения о том, как получить **Ключ доступа**, см. в разделе [Просмотр ключей доступа для API Power BI](power-bi-embedded-get-started-sample.md#view-access-keys) статьи "Приступая к работе с предварительной версией Microsoft Power BI Embedded".

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

7. Скопируйте и сохраните созданный **идентификатор рабочей области** для дальнейшего использования в этой статье. После создания **идентификатора рабочей области** его можно найти на **портале Azure**.

    ![](media\powerbi-embedded-get-started-sample\workspace-id.png)

8. Чтобы импортировать файл PBIX в вашу **рабочую область**, выберите параметр **6. Импорт файла PBIX рабочего стола в существующую рабочую область**. Если у вас под рукой нет PBIX-файла, можете скачать [PBIX-файл примера анализа розничной торговли](http://go.microsoft.com/fwlink/?LinkID=780547).

9. При появлении запроса укажите понятное имя для вашего **Набора данных**.

Вы должны получить примерно следующий ответ:

\````
Checking import state... Publishing
Checking import state... Succeeded
```

> [AZURE.NOTE] Если файл PBIX содержит подключения для прямых запросов, выберите вариант 7 для обновления строк подключения.

На этом этапе у вас есть отчет PBIX Power BI, импортированный в **рабочую область**. В следующем разделе показано, как запустить пример веб-приложения **Power BI Embedded**. В следующем разделе вы узнаете, как запустить пример веб-приложения.

## Запуск примера веб-приложения

Пример веб-приложения представляет собой пример панели мониторинга, на которой отображаются отчеты, импортированные в **рабочую область**.

Вот как настроить пример веб-приложения.

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

После запуска веб-приложения **EmbedSample** в левой навигационной панели должно появиться меню **Отчеты**. Чтобы просмотреть отчет, который был импортирован, разверните **Отчеты** и щелкните по отчету. Если вы импортировали [PBIX-файл примера анализа розничной торговли](http://go.microsoft.com/fwlink/?LinkID=780547), пример веб-приложения будет выглядеть следующим образом.

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-sample-left-nav.png)

После щелчка по отчету веб-приложение **EmbedSample** должно выглядеть примерно так:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

В следующем разделе рассматривается пример кода **Power BI Embedded**.

## Изучение примера кода
Пример для предварительной версии **Microsoft Power BI Embedded** представляет собой панель мониторинга веб-приложения и показывает, как интегрировать отчеты **Power BI** в приложение. Для демонстрации рекомендаций в нем используется шаблон проектирования "модель-представление-контроллер" \(MVC\). В этом разделе описаны части примера кода, которые можно изучить в решении веб-приложения **PowerBI-embedded**. Шаблон "модель-представление-контроллер" \(MVC\) разбивает модель домена, представление и действия на основе ввода пользователя на три различных класса: модель, представление и контроллер. Дополнительные сведения о MVC см. в разделе [Сведения об ASP.NET](http://www.asp.net/mvc).

Пример кода для предварительной версии **Microsoft Power BI Embedded** разбивается следующим образом. Каждая часть включает имя файла для решения PowerBI-embedded.sln, поэтому вы легко найдете соответствующий код в примере.

> [AZURE.NOTE] В этом разделе приведены общие сведения о примере кода, которые показывают, как был написан этот код. По мере приближения к выпуску общедоступной версии мы будем дополнять описание примера. Чтобы просмотреть полный пример, загрузите решение PowerBI-embedded.sln в Visual Studio.

### Модель
Образец содержит следующие модели: **ReportsViewModel** и **ReportViewModel**.

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

### Просмотр
**Представление** управляет отображением **Отчетов** и **Отчета** Power BI.

**Reports.cshtml**: перебирает отчеты **Model.Reports** для создания **ActionLink**. **ActionLink** формируется следующим образом:

|Часть|Описание
|---|---
|Название| Имя отчета.
|QueryString| Ссылка на идентификатор отчета.

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

### Controller

**DashboardController.cs**: создает PowerBIClient, передающий **маркер приложения**. Для получения **Учетных данных** создается веб-маркер JSON \(JWT\) на основе **Ключа подписи**. **Учетные данные** используются для создания экземпляра **PowerBIClient**. Дополнительные сведения о **маркерах приложения** см. в статье [Как устроен поток маркеров приложений?](#key-flow). После создания экземпляра **PowerBIClient** можно вызвать методы GetReports\(\) и GetReportsAsync\(\).

CreatePowerBIClient\(\)

    private IPowerBIClient CreatePowerBIClient(PowerBIToken token)
    {
        var jwt = token.Generate(accessKey);
        var credentials = new TokenCredentials(jwt, "AppToken");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports\(\)

    public ActionResult Reports()
    {
        var devToken = PowerBIToken.CreateDevToken(this.workspaceCollection, this.workspaceId);
        using (var client = this.CreatePowerBIClient(devToken))
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Задача<ActionResult> Report\(string reportId\)

    public async Task<ActionResult> Report(string reportId)
    {
        var devToken = PowerBIToken.CreateDevToken(this.workspaceCollection, this.workspaceId);
        using (var client = this.CreatePowerBIClient(devToken))
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, Guid.Parse(report.Id));

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### Интеграция отчета в приложение

После получения **Отчета** можно использовать **IFrame** для внедрения **Отчета** Power BI. Ниже приведен фрагмент кода из файла powerbi.js в примере для предварительной версии **Microsoft Power BI Embedded**.

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-iframe-code.png)


### Фильтрация отчетов, внедренных в приложение

Можно отфильтровать внедренный отчет, используя синтаксис URL-адреса. Для этого добавьте параметр строки запроса в исходный URL-адрес iFrame с указанием фильтра. Ниже приведен синтаксис запроса фильтра.

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-4c70-9673-ee9655d54a4a&
$filter={tableName/fieldName} eq '{fieldValue}'
```

> [AZURE.NOTE] {tableName/fieldName} не может содержать пробелы или специальные знаки. {FieldValue} принимает одно дискретное значение.


## См. также

- [Что такое Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Типичные сценарии использования Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)
- [Приступая к работе с предварительной версией Microsoft Power BI Embedded](power-bi-embedded-get-started.md)
- [Сведения о потоке маркеров приложений в Power BI Embedded](power-bi-embedded-app-token-flow.md)

<!---HONumber=AcomDC_0413_2016-->