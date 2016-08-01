<properties
   pageTitle="Начало работы с примером"
   description="Power BI Embedded, использование пакета SDK для добавления интерактивных отчетов Power BI в приложение бизнес-аналитики"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/14/2016"
   ms.author="owend"/>

# Начало работы с примером Microsoft Power BI Embedded

С помощью **Microsoft Power BI Embedded** можно интегрировать отчеты Power BI прямо в веб- или мобильные приложения. В этой статье приведены общие сведения о примере для начала работы с **Power BI Embedded**.

Прежде чем мы продолжим, вам следует сохранить приведенные ниже ресурсы. Они помогут вам при интеграции отчетов Power BI в пример приложения, а также в собственные приложения.

 -	[Пример панели мониторинга веб-приложения](http://go.microsoft.com/fwlink/?LinkId=761493)
 -	[Справочник по API Power BI Embedded](https://msdn.microsoft.com/library/mt711493.aspx)
 -	[Пакет SDK Power BI Embedded для .NET](http://go.microsoft.com/fwlink/?LinkId=746472) (доступен в NuGet)



> [AZURE.NOTE] Перед настройкой и запуском примера Power BI Embedded необходимо создать по крайней мере одну **коллекцию рабочей области** в подписке Azure. Сведения о создании **коллекции рабочей области** на портале Azure см. в статье [Начало работы с Microsoft Power BI Embedded (предварительная версия)](power-bi-embedded-get-started.md).

## Настройка примера приложения

Давайте подробнее рассмотрим действия по настройке среды разработки Visual Studio для доступа к компонентам, необходимым для запуска примера приложения.

1. Загрузите и распакуйте пример [Power BI Embedded — интеграция отчета в веб-приложение](http://go.microsoft.com/fwlink/?LinkId=761493) в GitHub.

2. Откройте файл **PowerBI-embedded.sln** в Visual Studio.

3. Выполните сборку решения.

4. Запустите консольное приложение **ProvisionSample**. В примере консольного приложения необходимо подготовить рабочую область и импортировать файл PBIX.

5. Чтобы подготовить новую **рабочую область**, выберите вариант 5, **Provision a new workspace in an existing workspace collection** (Подготовить новую рабочую область в имеющуюся коллекцию рабочих областей).

    ![](media\powerbi-embedded-get-started-sample\console-option-5.png)

6. Введите имя **Коллекции рабочих областей** и **Ключ доступа**. Их можно получить на **портале Azure**. Дополнительные сведения о том, как получить **ключ доступа**, см. в разделе о [просмотре ключей доступа для API Power BI](power-bi-embedded-get-started-sample.md#view-access-keys) статьи "Приступая к работе с примером Microsoft Power BI Embedded".

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

7. Скопируйте и сохраните созданный **идентификатор рабочей области** для дальнейшего использования в этой статье. После создания **идентификатора рабочей области** его можно найти на **портале Azure**.

    ![](media\powerbi-embedded-get-started-sample\workspace-id.png)

8. Чтобы импортировать файл PBIX в вашу **рабочую область**, выберите параметр **6. Импорт файла PBIX рабочего стола в существующую рабочую область**. Если у вас под рукой нет PBIX-файла, можете скачать [PBIX-файл примера анализа розничной торговли](http://go.microsoft.com/fwlink/?LinkID=780547).

9. При появлении запроса укажите понятное имя для вашего **Набора данных**.

Вы должны получить примерно следующий ответ:

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [AZURE.NOTE] Если файл PBIX содержит подключения для прямых запросов, выберите вариант 7 для обновления строк подключения.

На этом этапе у вас есть отчет PBIX Power BI, импортированный в **рабочую область**. Теперь рассмотрим, как запустить пример веб-приложения **Power BI Embedded**.

## Запуск примера веб-приложения

Пример веб-приложения представляет собой пример панели мониторинга, на которой отображаются отчеты, импортированные в **рабочую область**. Вот как настроить пример веб-приложения.

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

После запуска веб-приложения **EmbedSample** в левой навигационной панели должно появиться меню **Отчеты**. Чтобы просмотреть импортированный отчет, разверните **Отчеты** и выберите отчет. Если вы импортировали [PBIX-файл с примером анализа данных о продажах](http://go.microsoft.com/fwlink/?LinkID=780547), пример веб-приложения будет выглядеть следующим образом:

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-sample-left-nav.png)

После выбора отчета веб-приложение **EmbedSample** должно выглядеть примерно так:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)


## Изучение примера кода
Пример **Microsoft Power BI Embedded** представляет собой панель мониторинга веб-приложения и показывает, как интегрировать отчеты **Power BI** в приложение. Для демонстрации рекомендаций в нем используется шаблон проектирования "модель-представление-контроллер" (MVC). В этом разделе описаны части примера кода, которые можно изучить в решении веб-приложения **PowerBI-embedded**. Шаблон "модель-представление-контроллер" (MVC) разбивает модель домена, представление и действия на основе ввода пользователя на три различных класса: модель, представление и контроллер. Дополнительные сведения о MVC см. в разделе об [ASP.NET](http://www.asp.net/mvc).

Пример кода **Microsoft Power BI Embedded** разбивается следующим образом. Каждая часть включает имя файла для решения PowerBI-embedded.sln, поэтому вы легко найдете соответствующий код в примере.

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
**Представление** управляет отображением **отчетов** и **отчета** Power BI.

**Reports.cshtml**: перебирает отчеты **Model.Reports**, чтобы создать **ActionLink**. **ActionLink** формируется следующим образом:

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

**DashboardController.cs**: создает PowerBIClient, передающий **маркер приложения**. Для получения **учетных данных** создается веб-маркер JSON (JWT) на основе **ключа подписи**. **Учетные данные** используются для создания экземпляра **PowerBIClient**. После создания экземпляра **PowerBIClient** можно вызвать методы GetReports() и GetReportsAsync().

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

### Интеграция отчета в приложение

После получения **отчета** можно использовать **IFrame** для внедрения **отчета** Power BI. Ниже приведен фрагмент кода из файла powerbi.js в примере **Microsoft Power BI Embedded**.

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-iframe-code.png)


## Фильтрация отчетов, внедренных в приложение

Можно отфильтровать внедренный отчет, используя синтаксис URL-адреса. Для этого добавьте параметр строки запроса **$filter** с оператором **eq** в исходный URL-адрес iFrame с указанием фильтра. Ниже приведен синтаксис запроса фильтра.

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [AZURE.NOTE] {tableName/fieldName} не может содержать пробелы или специальные знаки. {FieldValue} принимает одно дискретное значение.


## См. также

- [Типичные сценарии использования Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)
- [Аутентификация и авторизация в Power BI Embedded](power-bi-embedded-app-token-flow.md)

<!---HONumber=AcomDC_0720_2016-->