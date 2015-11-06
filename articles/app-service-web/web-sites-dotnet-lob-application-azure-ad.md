<properties 
	pageTitle="Создание веб-приложения .NET MVC в службе приложений Azure с аутентификацией Azure Active Directory" 
	description="Ознакомьтесь с методом создания бизнес-приложения ASP.NET MVC в службе приложений Azure, осуществляющего аутентификацию при помощи Azure Active Directory" 
	services="app-service\web, active-directory" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="10/14/2015" 
	ms.author="cephalin"/>

# Создание веб-приложения .NET MVC в службе приложений Azure с аутентификацией Azure Active Directory #

В данной статье вы ознакомитесь с методом создания бизнес-приложения ASP.NET MVC на основе [веб-приложений службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714) с использованием [Azure Active Directory](/services/active-directory/) в качестве поставщика удостоверений. Вы также узнаете, как использовать [клиентскую библиотеку Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx), чтобы запросить данные каталога в приложении.

Используемый клиент Azure Active Directory может представлять собой только каталог Azure или синхронизироваться с локальной службой Active Directory (AD) для создания возможности единого входа локальных или удаленных сотрудников.

>[AZURE.NOTE] Для веб-приложений службы приложений Azure вы можете настроить проверку подлинности с помощью клиента Azure Active Directory всего несколькими нажатиями кнопки. Дополнительную информацию см. в разделе [Использование Active Directory для проверки подлинности в службе приложений Azure](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Что будет строиться ##

Вы создадите простое бизнес-приложение по шаблону «создать-прочитать-обновить-удалить» (CRUD) в веб-приложениях службы приложений, которое отслеживает рабочие элементы с помощью следующих компонентов:

- аутентификация пользователей в службе Azure Active Directory;
- реализация функций входа и выхода;
- использование `[Authorize]` для авторизации пользователей для различных действий CRUD;
- запросы данных Azure Active Directory с помощью [Azure Active Directory API Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx);
- использование [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (вместо Windows Identity Foundation, то есть WIF). Этот интерфейс определяет будущее ASP.NET. Он гораздо проще в настройке для аутентификации и авторизации, чем WIF.

<a name="bkmk_need"></a>
## Необходимые условия ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

>[AZURE.NOTE] Если вы хотите приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

Чтобы выполнить инструкции этого учебника, необходимо следующее:

- Клиент Azure Active Directory с пользователями в различных группах
- Разрешения на создание приложений в клиенте Azure Active Directory
- Visual Studio 2013
- [Пакет SDK для Azure 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) или более поздней версии.

<a name="bkmk_sample"></a>
## Использование примера приложения в качестве шаблона бизнес-приложения ##

Пример приложения в этом учебнике [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) создан рабочей группой Azure Active Directory и может использоваться в качестве шаблона для простого создания бизнес-приложений. Оно имеет следующие встроенные функции:

- использует [OpenID Connect](http://openid.net/connect/) для аутентификации с помощью Azure Active Directory;
- содержит пример контроллера `TaskTracker` для демонстрации авторизации различных ролей для определенных действий в приложении, в том числе стандартного использования `[Authorize]`. 
- В многопользовательском приложении с предварительно определенными ролями можно сразу же назначить пользователей и группы. 

<a name="bkmk_run" />
## Запуск учебного приложения ##

1.	Создайте клон или скачайте пример решения с сайта [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) в локальный каталог.

2.	Воспользуйтесь указаниями в разделе [Запуск примера в однопользовательском приложении](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#how-to-run-the-sample-as-a-single-tenant-app), чтобы настроить приложение Azure Active Directory и проект.
Обязательно выполните инструкции по преобразованию многопользовательского приложения в однопользовательское.

3.	В представлении только что созданного приложения Azure Active Directory на [портале Azure](https://manage.windowsazure.com) щелкните вкладку **ПОЛЬЗОВАТЕЛИ**. Затем назначьте выбранным пользователям выбранные роли.

	>[AZURE.NOTE] Для назначения ролей группам необходимо обновить ваш клиент Azure Active Directory до версии [Azure Active Directory Premium](/pricing/details/active-directory/). Если вы видите вкладку **ПОЛЬЗОВАТЕЛИ** вместо вкладки **ПОЛЬЗОВАТЕЛИ И ГРУППЫ** в интерфейсе портала приложения, вы можете попробовать Azure Active Directory Premium, перейдя на вкладку **ЛИЦЕНЗИИ** вашего клиента Azure Active Directory.

3.	Завершив настройку приложения, введите `F5` в Visual Studio, чтобы запустить приложение ASP.NET.

4.	После загрузки приложения щелкните **Вход** и выполните вход в портал Azure с пользователем, у которого есть роль администратора.

5.	Если приложение Azure Active Directory настроено правильно и заданы соответствующие параметры в файле Web.config, вы будете перенаправлены на страницу входа. Войдите с помощью учетной записи, используемой для создания приложения Azure Active Directory на портале Azure, так как эта учетная запись является владельцем приложения Azure Active Directory по умолчанию.
	
<a name="bkmk_deploy"></a>
## Развертывание примера приложения в веб-приложениях службы приложений

Теперь мы опубликуем приложение в веб-приложение службы приложений Azure. В файле [README.md](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims/blob/master/README.md) уже есть указания по развертыванию веб-приложений службы приложений, но выполнение этих действий также приводит к уничтожению конфигурации локальной среды отладки. Я покажу вам, как выполнить развертывание с сохранением конфигурации отладки.

1. Щелкните правой кнопкой мыши свой проект и выберите **Опубликовать**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. Выберите **Веб-приложения Microsoft Azure**.

3. Если вы еще не вошли в службу Azure, щелкните **Добавить учетную запись** и укажите данные учетной записи Майкрософт для своей подписки Azure.

4. После входа щелкните **Создать**, чтобы создать новое веб-приложение в Azure.

5. Заполните все обязательные поля. Для этого приложения необходимо подключение к базе данных, чтобы хранить сопоставления ролей, кэшированные токены и любые данные приложения.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

6. Щелкните **Создать**. После создания веб-приложения открывается диалоговое окно **Публикация в Интернете**.

7. В поле **Конечный URL-адрес** замените **http** на **https**. Скопируйте весь URL-адрес в текстовый редактор. Он вам понадобится позднее. Нажмите кнопку **Далее**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. Снимите флажок **Включить аутентификацию в организации**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-enable-code-first-migrations.png)

8. Разверните **RoleClaimContext** и выберите **Выполнить Code First Migrations (при запуске приложения)**. [Code First Migrations](https://msdn.microsoft.com/data/jj591621.aspx) помогает обновить схему базы данных приложения в Azure при последующем определении дополнительных моделей данных Code First.

9. Вместо того, чтобы нажать кнопку **Опубликовать** для выполнения операции веб-публикации, щелкните **Закрыть**. Щелкните **Да** для сохранения изменений в профиле публикации.

2. На [портале управления Azure](https://manage.windowsazure.com) перейдите к клиенту Azure Active Directory и щелкните вкладку **Приложения**.

2. В нижней части страницы нажмите кнопку **Добавить**.

2. Выберите команду **Добавить приложение, разрабатываемое моей организацией**.

3. Выберите **Веб-приложение и/или веб-API**.

4. Присвойте приложению имя и щелкните **Далее**.

5. В окне свойств приложения укажите в поле **URL-адрес входа** ранее сохраненный URL-адрес веб-приложения (например, `https://<site-name>.azurewebsites.net/`), а в поле **URI ИДЕНТИФИКАТОРА ПРИЛОЖЕНИЯ** укажите `https://<aad-tenanet-name>/<app-name>`. Затем нажмите кнопку **Завершить**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

2.	После создания приложения обновите манифест приложения так же, как делали ранее в соответствии с разделом [Определение ролей приложения](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#step-2-define-your-application-roles).

3.	В представлении только что созданного приложения Azure Active Directory на [портале Azure](https://manage.windowsazure.com) щелкните вкладку **ПОЛЬЗОВАТЕЛИ**. Затем назначьте выбранным пользователям выбранные роли.

6. Выберите вкладку **НАСТРОЙКА**.

7. В разделе **Ключи** создайте новый ключ, выбрав **1 год** в раскрывающемся списке.

8. В разделе **Разрешения для других приложений** для записи **Azure Active Directory** выберите **Войти и прочесть профиль пользователя** и **Прочесть данные каталога** в раскрывающемся списке **Делегированные разрешения**.

	> [AZURE.NOTE] Необходимые разрешения зависят от желаемой функциональности приложения. Чтобы задать некоторые разрешения, требуется роль **глобального администратора**, но разрешения, необходимые для этого учебника, требуют использования только роли **Пользователь**.

9.  Щелкните **Сохранить**.  

10.  Прежде чем покинуть страницу сохраненной конфигурации, скопируйте следующую информацию в текстовый редактор.

	-	Идентификатор клиента
	-	Ключ (если покинуть страницу, ключ нельзя будет увидеть снова)

11. В Visual Studio откройте в своем проекте файл **Web.Release.config**. Вставьте следующий XML-код в тег `<configuration>` и замените значение каждого ключа на информацию, сохраненную для нового приложения Azure Active Directory.
	<pre class="prettyprint">
&lt;appSettings>
   &lt;add key="ida:ClientId" value="<mark>[e.g. 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
   &lt;add key="ida:AppKey" value="<mark>[e.g. rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo=]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
   &lt;add key="ida:PostLogoutRedirectUri" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
&lt;/appSettings></pre>

	Убедитесь, что значение ida:PostLogoutRedirectUri заканчивается косой чертой "/".

1. Щелкните правой кнопкой мыши свой проект и выберите **Опубликовать**.

2. Щелкните **Опубликовать** для публикации в веб-приложениях службы приложений Azure.

После завершения операции вы получите два приложения Azure Active Directory, настроенные на портале управления Azure. Одно из них используется для среды отладки в Visual Studio, а другое — для опубликованного веб-приложения в Azure. Во время отладки параметры приложения в файле Web.config используются, чтобы конфигурация **Отладка** работала со службой Azure Active Directory. После его публикации (по умолчанию публикуется конфигурация **Выпуск**) отправляется измененный файл Web.config, который содержит изменения параметров приложения в файле Web.Release.config.

Если требуется подключить опубликованное веб-приложение к отладчику (т. е. когда нужно отправить отладочные символы кода в опубликованное веб-приложение), можно создать клон конфигурации отладки для отладки в Azure. У этого клона должен быть собственный настраиваемый файл преобразования Web.config (например, Web.AzureDebug.config), в котором используются параметры приложения из файла Web.Release.config. Это дает возможность поддерживать статическую конфигурацию в различных средах.

<a name="bkmk_crud"></a>
## Добавление функциональности бизнес-приложения к примеру приложения

В этой части учебника вы научитесь создавать необходимую функциональность бизнес-приложения на основе примера приложения. Вы займетесь созданием простого приложения CRUD для отслеживания рабочих элементов, аналогичного контроллеру TaskTracker, но с использованием формирования стандартного шаблона CRUD и шаблона разработки. Кроме этого, будут использоваться приведенные скрипты Scripts\\AadPickerLibrary.js для обогащения приложения данными из API Azure Active Directory Graph.

5.	В папке Models создайте новую модель [Code First](http://www.asp.net/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) с именем WorkItem.cs и замените ее код следующим кодом:

		using System.ComponentModel.DataAnnotations;
		
		namespace WebApp_RoleClaims_DotNet.Models
		{
		    public class WorkItem
		    {
		        [Key]
		        public int ItemID { get; set; }
		        public string AssignedToID { get; set; }
		        public string AssignedToName { get; set; }
		        public string Description { get; set; }
		        public WorkItemStatus Status { get; set; }
		    }
		
		    public enum WorkItemStatus
		    {
		        Open, 
		        Investigating, 
		        Resolved, 
		        Closed
		    }
		}

6.	Откройте файл DAL\\RoleClaimContext.cs и добавьте выделенный код:  
	<pre class="prettyprint">
    public class RoleClaimContext : DbContext
    {
        public RoleClaimContext() : base("RoleClaimContext") { }

        public DbSet&lt;Task&gt; Tasks { get; set; }
        <mark>public DbSet&lt;WorkItem&gt; WorkItems { get; set; }</mark>
        public DbSet&lt;TokenCacheEntry&gt; TokenCacheEntries { get; set; }
    }</pre>

7.	Постройте проект, чтобы сделать новую модель доступной для логики формирования шаблона в Visual Studio.

8.	Добавьте новый элемент `WorkItemsController`, сформированный на основе шаблона, в папку «Контроллеры». Чтобы это сделать, щелкните правой кнопкой мыши **Контроллеры**, выберите **Добавить**, а затем выберите **Новый элемент, сформированный на основе шаблона**.

9.	Выберите **Контроллер MVC 5 с представлениями, использующий Entity Framework**, и щелкните **Добавить**.

10.	Выберите только что созданную модель и щелкните **Добавить**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	Откройте Controllers\\WorkItemsController.cs

11. Добавьте выделенные оформления [Authorize] к соответствующим действиям ниже.
	<pre class="prettyprint">
	...

    <mark>[Authorize(Roles = "Admin, Observer, Writer, Approver")]</mark>
    public class WorkItemsController : Controller
    {
		...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public ActionResult Create()
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Edit(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult&gt; Delete(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult&gt; DeleteConfirmed(int id)
        ...
	}</pre>

	Поскольку нас интересует сопоставление ролей в интерфейсе портала Azure, достаточно просто убедиться, что каждое действие авторизует соответствующую роль.

	> [AZURE.NOTE] В некоторых действиях вы можете заметить оформление <code>[ValidateAntiForgeryToken]</code>. Из-за поведения, описанного [Алленом Броком (Brock Allen)](https://twitter.com/BrockLAllen) в статье [MVC 4, AntiForgeryToken и утверждений](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/), команда HTTP POST может завершиться ошибкой при проверке маркеров защиты от подделки по следующей причине:
	> + Azure Active Directory не отправляет http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, который по умолчанию требуется для маркера защиты от подделки.
	> + Если каталог Azure Active Directory синхронизирован с AD FS, в связи с установленным доверием AD FS также по умолчанию не отправляется утверждение http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider. Однако вы можете вручную настроить AD FS для отправки этого утверждения.
	> Мы займемся этим на следующем шаге.

12.  В файле App\_Start\\Startup.Auth.cs добавьте следующую строку кода в метод `ConfigureAuth`. Щелкните правой кнопкой мыши по каждой ошибке разрешения имен, чтобы ее исправить.

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` определяет утверждение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, которое служба Azure Active Directory не предоставляет. Теперь после того, как решен вопрос с авторизацией (честно говоря, на это ушло немного времени), можно посвятить свое время фактической функциональности действий.

13.	В методы Create() и Edit() добавьте следующий код, чтобы сделать некоторые переменные доступными для JavaScript позже: Щелкните правой кнопкой мыши по каждой ошибке разрешения имен, чтобы ее исправить.

        ViewData["token"] = AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value);
        ViewData["tenant"] = ConfigHelper.Tenant;

13.	Метод `AcquireToken()` еще не определен. Определите его в классе `WorkItemsController`. Щелкните правой кнопкой мыши по каждой ошибке разрешения имен, чтобы ее исправить.

        static string AcquireToken(string userObjectId)
        {
            ClientCredential cred = new ClientCredential(ConfigHelper.ClientId, ConfigHelper.AppKey);
            Claim tenantIdClaim = ClaimsPrincipal.Current.FindFirst(Globals.TenantIdClaimType);
            AuthenticationContext authContext = new AuthenticationContext(String.Format(CultureInfo.InvariantCulture, ConfigHelper.AadInstance, tenantIdClaim.Value), new TokenDbCache(userObjectId));
            AuthenticationResult result = authContext.AcquireTokenSilent(ConfigHelper.GraphResourceId, cred, new UserIdentifier(userObjectId, UserIdentifierType.UniqueId));
            return result.AccessToken;
        }
		
14.	В файле Views\\WorkItems\\Create.cshtml (автоматически сформированный на основе шаблона элемент) найдите вспомогательный метод `Html.BeginForm` и измените его следующим:  
	<pre class="prettyprint">@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>))
	{
	    @Html.AntiForgeryToken()
	    
	    &lt;div class="form-horizontal"&gt;
	        &lt;h4&gt;WorkItem&lt;/h4&gt;
	        &lt;hr /&gt;
	        @Html.ValidationSummary(true, "", new { @class = "text-danger" })
	
	        &lt;div class="form-group"&gt;
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type=&quot;hidden&quot;</mark> } })
	                @Html.ValidationMessageFor(model =&gt; model.AssignedToID, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model =&gt; model.Description, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = "form-control" })
	                @Html.ValidationMessageFor(model =&gt; model.Status, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            &lt;div class="col-md-offset-2 col-md-10"&gt;
	                &lt;input type="submit" value="Create" class="btn btn-default" <mark>id="submit-button"</mark> /&gt;
	            &lt;/div&gt;
	        &lt;/div&gt;
	    &lt;/div&gt;
	
	    <mark>&lt;script&gt;
	            // People/Group Picker Code
	            var maxResultsPerPage = 14;
	            var input = document.getElementById("AssignedToName");
	            var token = "@ViewData["token"]";
	            var tenant = "@ViewData["tenant"]";
	
	            var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
	
	            // Submit the selected user/group to be asssigned.
	            $("#submit-button").click({ picker: picker }, function () {
	                if (!picker.Selected())
	                    return;
	                $("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
	            });
	    &lt;/script&gt;</mark>
	
	}</pre>

	В сценарии объект AadPicker вызывает [API Graph Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) для поиска пользователей и групп, которые соответствуют введенным данным.

15. Откройте [Консоль диспетчера пакетов](http://docs.nuget.org/Consume/Package-Manager-Console) и запустите **Enable-Migrations – EnableAutomaticMigrations**. По аналогии с параметром, который вы выбираете при публикации приложения в Azure, эта команда позволяет обновить схему базы данных приложения в [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) при отладке в Visual Studio.

15. Теперь можно либо запустить приложение в отладчике Visual Studio, либо снова опубликовать его в веб-приложениях службы приложений. Войдите в систему как владелец приложения и перейдите на страницу `https://<webappname>.azurewebsites.net/WorkItems/Create`. Теперь вы сможете выбрать пользователя или группу Azure Active Directory из раскрывающегося списка или ввести какие-либо данные для фильтрации списка.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. Заполните оставшуюся часть формы и щелкните **Создать**. На странице ~/WorkItems/Index теперь отображается вновь созданный элемент. На приведенном ниже снимке экрана вы увидите, что я удалил столбец `AssignedToID` в файле Views\\WorkItems\\Index.cshtml.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	Сейчас необходимо сделать аналогичные изменения в представлении **Изменение**. В файле Views\\WorkItems\\Edit.cshtml внесите такие же изменения во вспомогательном методе `Html.BeginForm`, которые были сделаны на предыдущем шаге в Views\\WorkItems\\Create.cshtml(замените Create на Edit в выделенном коде выше).

Вот и все!

Теперь, после настройки авторизаций и функциональности бизнес-приложения для различных действия в контроллере WorkItems, можно попробовать входить в систему как пользователи с различными ролями приложения, чтобы увидеть отклик приложения.

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## Дополнительные ресурсы

- [Защита приложения с помощью SSL и атрибута авторизации](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Использование Active Directory для аутентификации в службе приложений Azure](web-sites-authentication-authorization.md)
- [Создание веб-приложения .NET MVC в службе приложений Azure с аутентификацией AD FS](web-sites-dotnet-lob-application-adfs.md)
- [Примеры и документация Microsoft Azure Active Directory](https://github.com/AzureADSamples)
- [Блог Витторио Берточчи](http://blogs.msdn.com/b/vbertocci/)
- [Миграция веб-проекта VS2013 из WIF в Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Новые гибридные подключения Azure, а не родительский элемент #hybridCloud](/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/)
- [Сходства Active Directory и Azure AD](http://technet.microsoft.com/library/dn518177.aspx)
- [DirSync с единым входом](http://technet.microsoft.com/library/dn441213.aspx)
- [Поддерживаемые типы маркеров и утверждений](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-----HONumber=Oct15_HO4-->