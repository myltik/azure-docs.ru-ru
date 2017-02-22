---
title: "Начало работы с примером"
description: "Power BI Embedded, использование пакета SDK для добавления интерактивных отчетов Power BI в приложение бизнес-аналитики"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 02/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 71476ae63d2394e7edefa10b8c71d15c04190290
ms.openlocfilehash: 299e06bf6d4bd3af5d6dc7496ba9a947f42c19b7


---
# <a name="get-started-with-power-bi-embedded-sample"></a>Начало работы с примером Microsoft Power BI Embedded

С помощью **Microsoft Power BI Embedded**можно интегрировать отчеты Power BI прямо в веб- или мобильные приложения. В этой статье приведены общие сведения о примере для начала работы с **Power BI Embedded** .

Прежде чем мы продолжим, вам следует сохранить приведенные ниже ресурсы. Они помогут вам при интеграции отчетов Power BI в пример приложения, а также в собственные приложения.

* [Пример панели мониторинга веб-приложения](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Справочник по API Power BI Embedded](https://msdn.microsoft.com/en-US/library/azure/mt711507.aspx)
* [Пакет SDK Power BI Embedded для .NET ](http://go.microsoft.com/fwlink/?LinkId=746472) (доступен в NuGet)
* [Пример внедрения отчета JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE] 
> Перед настройкой и запуском примера Power BI Embedded необходимо создать по крайней мере одну **коллекцию рабочих областей** в подписке Azure. Сведения о создании **коллекции рабочей области ** на портале Azure см. в статье [Начало работы с Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

## <a name="configure-the-sample-app"></a>Настройка примера приложения

Давайте подробнее рассмотрим действия по настройке среды разработки Visual Studio для доступа к компонентам, необходимым для запуска примера приложения.

1. Загрузите и распакуйте пример [Power BI Embedded — интеграция отчета в веб-приложение](http://go.microsoft.com/fwlink/?LinkId=761493) в GitHub.
2. Откройте файл **PowerBI-embedded.sln** в Visual Studio. Может потребоваться выполнить команду **Update-Package** в консоли диспетчера пакетов NuGET, чтобы обновить пакеты, используемые в этом решении.
3. Выполните сборку решения.
4. Запустите консольное приложение **ProvisionSample** . В примере консольного приложения необходимо подготовить рабочую область и импортировать файл PBIX.
5. Чтобы подготовить новую **рабочую область**, выберите вариант 5, **Provision a new workspace in an existing workspace collection** (Подготовить новую рабочую область в имеющейся коллекции рабочих областей).

    ![](media/powerbi-embedded-get-started-sample/console-option-5.png)
6. Введите имя **коллекции рабочих областей** и **ключ доступа**. Их можно получить на **портале Azure**. Дополнительные сведения о том, как получить **ключ доступа**, см. в разделе [Просмотр ключей доступа для вызова API Power BI](power-bi-embedded-get-started.md#view-power-bi-api-access-keys) статьи "Начало работы с Microsoft Power BI Embedded".

    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
7. Скопируйте и сохраните созданный **идентификатор рабочей области** для дальнейшего использования в этой статье. После создания **идентификатора рабочей области** его можно найти на **портале Azure**.

    ![](media/powerbi-embedded-get-started-sample/workspace-id.png)
8. Чтобы импортировать PBIX-файл в свою **рабочую область**, выберите вариант **6. Импорт файла PBIX рабочего стола в существующую рабочую область**. Если у вас нет PBIX-файла, то можете скачать [PBIX-файл примера анализа розничной торговли](http://go.microsoft.com/fwlink/?LinkID=780547).
9. При появлении запроса укажите понятное имя для своего **набора данных**.

Вы должны получить примерно следующий ответ:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Если файл PBIX содержит подключения для прямых запросов, выберите вариант 7 для обновления строк подключения.

На этом этапе у вас есть PBIX-файл отчета Power BI, импортированный в **рабочую область**. Теперь рассмотрим, как запустить пример веб-приложения **Power BI Embedded**.

## <a name="run-the-sample-web-app"></a>Запуск примера веб-приложения
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

После запуска веб-приложения **EmbedSample** в левой навигационной панели должно появиться меню **Отчеты**. Чтобы просмотреть отчет, который был импортирован, разверните **Отчеты** и щелкните отчет. Если вы импортировали [PBIX-файл примера анализа розничной торговли](http://go.microsoft.com/fwlink/?LinkID=780547), пример веб-приложения будет выглядеть следующим образом.

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-sample-left-nav.png)

После выбора отчета веб-приложение **EmbedSample** должно выглядеть примерно так:

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Изучение примера кода

Пример **Microsoft Power BI Embedded** представляет собой панель мониторинга веб-приложения и показывает, как интегрировать отчеты **Power BI** в приложение. Для демонстрации рекомендаций в нем используется шаблон проектирования "модель-представление-контроллер" (MVC). В этом разделе описаны части примера кода, которые можно изучить в решении веб-приложения **PowerBI-embedded**. Шаблон "модель-представление-контроллер" (MVC) разбивает модель домена, представление и действия на основе ввода пользователя на три различных класса: модель, представление и контроллер. Дополнительные сведения о MVC см. в документации [ASP.NET](http://www.asp.net/mvc).

Пример кода **Microsoft Power BI Embedded** разбивается следующим образом. Каждая часть включает имя файла для решения PowerBI-embedded.sln, поэтому вы легко найдете соответствующий код в примере.

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

Попытка использования общих атрибутов сервера и базы данных завершится ошибкой. Например: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Просмотр

**Представление** управляет отображением **отчетов** и **отчета** Power BI.

**Reports.cshtml**: выполняет итерацию по отчетам **Model.Reports**, чтобы создать **ActionLink**. **ActionLink** формируется следующим образом:

| Часть | Описание |
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

После получения **отчета** можно использовать **IFrame** для внедрения **отчета** Power BI. Ниже приведен фрагмент кода из файла powerbi.js в примере **Microsoft Power BI Embedded**.

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-iframe-code.png)

## <a name="filter-reports-embedded-in-your-application"></a>Фильтрация отчетов, внедренных в приложение

Можно отфильтровать внедренный отчет, используя синтаксис URL-адреса. Для этого добавьте параметр строки запроса **$filter** с оператором **eq** в исходный URL-адрес iFrame с указанием фильтра. Ниже приведен синтаксис запроса фильтра.

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} не может содержать пробелы или специальные знаки. {FieldValue} принимает одно дискретное значение.  

## <a name="see-also"></a>Дополнительные материалы
* [Типичные сценарии использования Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)
* [Аутентификация и авторизация в Power BI Embedded](power-bi-embedded-app-token-flow.md)

У вас имеются и другие вопросы? [Попробуйте задать их в сообществе Power BI](http://community.powerbi.com/)



<!--HONumber=Feb17_HO2-->


