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
	ms.date="07/07/2015" 
	ms.author="cephalin"/>

# Создание веб-приложения .NET MVC в службе приложений Azure с аутентификацией Azure Active Directory #

В данной статье вы ознакомитесь с методом создания бизнес-приложения ASP.NET MVC на основе [веб-приложений службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714) с использованием [Azure Active Directory](/services/active-directory/) в качестве поставщика удостоверений. Вы также узнаете, как использовать [клиентскую библиотеку Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx), чтобы запросить данные каталога в приложении.

Используемый клиент Azure Active Directory может включать в себя только каталог Azure или синхронизироваться с локальной службой Active Directory (AD) для создания возможности единого входа локальных или удаленных сотрудников.

>[AZURE.NOTE]Функция Easy Auth в веб-приложениях службы приложений Azure дает возможность настраивать простую аутентификацию с помощью клиента Azure Active Directory всего несколькими нажатиями кнопки. Дополнительную информацию см. в разделе [Использование Active Directory для проверки подлинности в службе приложений Azure](web-sites-authentication-authorization.md).

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

>[AZURE.NOTE]Если вы хотите приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

Чтобы выполнить инструкции этого учебника, необходимо следующее:

- Клиент Azure Active Directory с пользователями в различных группах
- Разрешения на создание приложений в клиенте Azure Active Directory
- Visual Studio 2013
- [Пакет SDK для Azure 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) или более поздней версии.

<a name="bkmk_sample"></a>
## Использование примера приложения в качестве шаблона бизнес-приложения ##

Пример приложения в этом учебнике [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) создан рабочей группой Azure Active Directory и может использоваться в качестве шаблона для простого создания бизнес-приложений. Оно имеет следующие встроенные функции:

- использует [OpenID Connect](http://openid.net/connect/) для аутентификации с помощью Azure Active Directory;
- контроллер `Roles`, который содержит фильтр поиска Azure Active Directory и позволяет легко сопоставлять пользователей или группы Azure Active Directory с ролями приложения;
- пример контроллера `TaskTracker` для демонстрации авторизации различных ролей для определенных действий в приложении, в том числе стандартного использования `[Authorize]`. 

![](./media/web-sites-dotnet-lob-application-azure-ad/role-management.png)

<a name="bkmk_run" />
## Запуск учебного приложения ##

1.	Создайте клон или скачайте пример решения с сайта [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) в локальный каталог.

2.	Следуйте указаниям в файле [README.md](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md), чтобы настроить приложение Azure Active Directory и проект.

	> [AZURE.NOTE]Разрешения, настроенные в приложении Azure Active Directory, требуют наличия только роли <strong>Пользователь</strong>, а не **Глобальный администратор**.
	
3.	Завершив настройку приложения, введите `F5`, чтобы запустит его.

4.	После загрузки приложения щелкните **Вход**.

5.	Если приложение Azure Active Directory настроено правильно и заданы соответствующие параметры в файле Web.config, вы будете перенаправлены на страницу входа. Войдите с помощью учетной записи, используемой для создания приложения Azure Active Directory на портале Azure, так как эта учетная запись является владельцем приложения Azure Active Directory по умолчанию.
	
	> [AZURE.NOTE]В файле Startup.Auth.cs примера проекта обратите внимание на то, что в приложении есть метод с именем <code>AddOwnerAdminClaim</code>, который используется для добавления владельца приложения в роль администратора. Это дает возможность немедленно начать управление ролями приложения в контроллере <code>Roles</code>.
	
4.	После входа щелкните **Роли**, чтобы начать управление ролями приложения.

5.	В поле **Поиск пользователей или групп** начните вводить нужное имя пользователя или группы. Вы увидите в раскрывающемся списке отфильтрованный список пользователей и/или групп из клиента Azure Active Directory.

	![](./media/web-sites-dotnet-lob-application-azure-ad/select-user-group.png)

	> [AZURE.NOTE]В файле Views\Roles\Index.cshtml вы увидите, что представление использует объект JavaScript с именем <code>AadPicker</code> (определенный в Scripts\AadPickerLibrary.js) для доступа к действию <code>Search</code> в контроллере <code>Roles</code>. <pre class="prettyprint">var searchUrl = window.location.protocol + "//" + window.location.host + "<mark>/Roles/Search</mark>"; ... var picker = new <mark>AadPicker(searchUrl, maxResultsPerPage, input, token, tenant)</mark>;</pre> В файле Controllers\RolesController.cs вы увидите действие <code>Search</code>, которое отправляет непосредственный запрос к API Graph Azure Active Directory и возвращает ответ обратно на страницу. Позднее вы будете использовать этот же метод для создания простой функциональности в приложении.

6.	Выберите пользователя или группу в раскрывающемся списке, выберите роль и щелкните **Назначить роль**.

<a name="bkmk_deploy"></a>
## Развертывание примера приложения в веб-приложениях службы приложений

Теперь мы опубликуем приложение в веб-приложение службы приложений Azure. В файле [README.md](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/GroupClaims/README.md) уже есть указания по развертыванию веб-приложений службы приложений, но выполнение этих действий также приводит к уничтожению конфигурации локальной среды отладки. Я покажу вам, как выполнить развертывание с сохранением конфигурации отладки.

1. Щелкните правой кнопкой мыши свой проект и выберите **Опубликовать**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. Выберите **Веб-приложения Microsoft Azure**.

3. Если вы еще не вошли в службу Azure, нажмите кнопку **Вход** и укажите данные учетной записи Майкрософт для своей подписки Azure.

4. После входа щелкните **Создать**, чтобы создать новое веб-приложение в Azure.

5. Заполните все обязательные поля. Для этого приложения необходимо подключение к базе данных, чтобы хранить сопоставления ролей, кэшированные токены и любые данные приложения.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

6. Щелкните **Создать**. После создания веб-приложения открывается диалоговое окно **Публикация в Интернете**.

7. В поле **Конечный URL-адрес** замените **http** на **https**. Скопируйте весь URL-адрес в текстовый редактор. Он вам понадобится позднее. Нажмите кнопку **Далее**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. Снимите флажок **Включить аутентификацию в организации**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-disable-organizational-authentication.png)

9. Вместо того, чтобы нажать кнопку **Опубликовать** для выполнения операции веб-публикации, щелкните **Закрыть**. Щелкните **Да** для сохранения изменений в профиле публикации.

2. На [портале управления Azure](https://manage.windowsazure.com) перейдите к клиенту Azure Active Directory и щелкните вкладку **Приложения**.

2. В нижней части страницы нажмите кнопку **Добавить**.

3. Выберите **Веб-приложение и/или веб-API**.

4. Присвойте приложению имя и щелкните **Далее**.

5. В окне свойств приложения укажите в поле **URL-адрес входа** ранее сохраненный URL-адрес веб-приложения (например, `https://<site-name>.azurewebsites.net`), а в поле **URI ИДЕНТИФИКАТОРА ПРИЛОЖЕНИЯ** укажите `https://<aad-tenanet-name>/<app-name>`. Затем нажмите кнопку **Завершить**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

6. После создания приложения щелкните **Настройка**.

7. В разделе **Ключи** создайте новый ключ, выбрав **1 год** в раскрывающемся списке.

8. В разделе **Разрешения для других приложений** для записи **Azure Active Directory** выберите **Доступ к каталогу вашей организации** в раскрывающемся списке **Делегированные разрешения**.

	> [AZURE.NOTE]Необходимые разрешения зависят от желаемой функциональности приложения. Чтобы задать некоторые разрешения, требуется роль **глобального администратора**, но разрешения, необходимые для этого учебника, требуют использования только роли **Пользователь**.

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
&lt;/appSettings></pre>Убедитесь, что значение ida:PostLogoutRedirectUri заканчивается косой чертой "/".

1. Щелкните правой кнопкой мыши свой проект и выберите **Опубликовать**.

2. Щелкните **Опубликовать** для публикации в веб-приложениях службы приложений Azure.

После завершения операции вы получите два приложения Azure Active Directory, настроенные на портале управления Azure. Одно из них используется для среды отладки в Visual Studio, а другое — для опубликованного веб-приложения в Azure. Во время отладки параметры приложения в файле Web.config используются, чтобы конфигурация **Отладка** работала со службой Azure Active Directory. После его публикации (по умолчанию публикуется конфигурация **Выпуск**) отправляется измененный файл Web.config, который содержит изменения параметров приложения в файле Web.Release.config.

Если требуется подключить опубликованное веб-приложение к отладчику (т. е. когда нужно отправить отладочные символы кода в опубликованное веб-приложение), можно создать клон конфигурации отладки для отладки в Azure. У этого клона должен быть собственный настраиваемый файл преобразования Web.config (например, Web.AzureDebug.config), в котором используются параметры приложения из файла Web.Release.config. Это дает возможность поддерживать статическую конфигурацию в различных средах.

<a name="bkmk_crud"></a>
## Добавление функциональности бизнес-приложения к примеру приложения

В этой части учебника вы научитесь создавать необходимую функциональность бизнес-приложения на основе примера приложения. Вы займетесь созданием простого приложения CRUD для отслеживания рабочих элементов, аналогичного контроллеру TaskTracker, но с использованием формирования стандартного шаблона CRUD и шаблона разработки. Кроме этого, будут использоваться приведенные скрипты Scripts\AadPickerLibrary.js для обогащения приложения данными из API Azure Active Directory Graph.

5.	В папке Models создайте новую модель с именем WorkItem.cs и замените код следующим кодом:

		using System.ComponentModel.DataAnnotations;
		
		namespace WebAppGroupClaimsDotNet.Models
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

6.	Откройте DAL\GroupClaimContext.cs и добавьте выделенный код:
	<pre class="prettyprint">
public class GroupClaimContext : DbContext
{
    public GroupClaimContext() : base("GroupClaimContext") { }

    public DbSet&lt;RoleMapping> RoleMappings { get; set; }
    public DbSet&lt;Task> Tasks { get; set; }
    <mark>public DbSet&lt;WorkItem> WorkItems { get; set; }</mark>
    public DbSet&lt;TokenCacheEntry> TokenCacheEntries { get; set; }
}</pre>

7.	Постройте проект, чтобы сделать новую модель доступной для логики формирования шаблона в Visual Studio.

8.	Добавьте новый элемент `WorkItemsController`, сформированный на основе шаблона, в папку «Контроллеры». Чтобы это сделать, щелкните правой кнопкой мыши **Контроллеры**, выберите **Добавить**, а затем выберите **Новый элемент, сформированный на основе шаблона**.

9.	Выберите **Контроллер MVC 5 с представлениями, использующий Entity Framework**, и щелкните **Добавить**.

10.	Выберите только что созданную модель и щелкните **Добавить**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	Откройте Controllers\WorkItemsController.cs

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
    public async Task&lt;ActionResult> Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
    ...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public async Task&lt;ActionResult> Edit(int? id)
    ...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public async Task&lt;ActionResult> Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
    ...

    <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
    public async Task&lt;ActionResult> Delete(int? id)
    ...

    <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
    public async Task&lt;ActionResult> DeleteConfirmed(int id)
    ...
}</pre>Поскольку нас интересует сопоставление ролей к контроллере ролей, достаточно просто убедиться, что каждое действие авторизует соответствующую роль.

	> [AZURE.NOTE]В некоторых действиях вы можете заметить оформление <code>[ValidateAntiForgeryToken]</code>. Из-за поведения, описанного [Алленом Броком (Brock Allen)](https://twitter.com/BrockLAllen) в статье [MVC 4, AntiForgeryToken и утверждений](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/), команда HTTP POST может завершиться ошибкой при проверке маркеров защиты от подделки по следующей причине: + Azure Active Directory не отправляет http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, который по умолчанию требуется для маркера защиты от подделки. + Если каталог Azure Active Directory синхронизирован с AD FS, в связи с установленным доверием AD FS также по умолчанию не отправляется утверждение http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider. Однако вы можете вручную настроить AD FS для отправки этого утверждения. Мы займемся этим на следующем шаге.

12.  В файле App_Start\Startup.Auth.cs добавьте следующую строку кода в метод `ConfigureAuth`:

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` определяет утверждение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, которое служба Azure Active Directory не предоставляет. Теперь после того, как решен вопрос с авторизацией (честно говоря, на это ушло немного времени), можно посвятить свое время фактической функциональности действий.

13.	В Create() и Edit() добавьте следующий код для предоставления JavaScript доступа к некоторым переменным в дальнейшем: ViewData["token"] = GraphHelper.AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value); ViewData["tenant"] = ConfigHelper.Tenant;

14.	В файле Views\WorkItems\Create.cshtml (автоматически сформированный на основе шаблона элемент) найдите вспомогательный метод `Html.BeginForm` и измените его следующим:
	<pre class="prettyprint">@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>))
{
    @Html.AntiForgeryToken()

    &lt;div class="form-horizontal">
        &lt;h4>WorkItem&lt;/h4>
        &lt;hr />
        @Html.ValidationSummary(true, "", new { @class = "text-danger" })

        &lt;div class="form-group">
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type="hidden"</mark> } })
                @Html.ValidationMessageFor(model => model.AssignedToID, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            @Html.LabelFor(model => model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.AssignedToName, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            @Html.LabelFor(model => model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
            &lt;div class="col-md-10">
                @Html.EnumDropDownListFor(model => model.Status, htmlAttributes: new { @class = "form-control" })
                @Html.ValidationMessageFor(model => model.Status, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            &lt;div class="col-md-offset-2 col-md-10">
                &lt;input type="submit" value="Create" class="btn btn-default" <mark>id="submit-button"</mark> />
            &lt;/div>
        &lt;/div>
    &lt;/div>

    <mark>&lt;script>
            // People/Group Picker Code
            var maxResultsPerPage = 14;
            var searchUrl = window.location.protocol + "//" + window.location.host + "/Roles/Search";
            var input = document.getElementById("AssignedToName");
            var token = "@ViewData["token"]";
            var tenant = "@ViewData["tenant"]";

            var picker = new AadPicker(searchUrl, maxResultsPerPage, input, token, tenant);

            // Отправьте выбранного пользователя или группу для назначения.
            $("#submit-button").click({ picker: picker }, function () {
                if (!picker.Selected())
                    return;
                $("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
            });
    &lt;/script></mark>

}</pre>В сценарии объект AadPicker ищет в действии `~/Roles/Search` пользователей и группы Azure Active Directory, которые соответствуют введенным данным. Затем при нажатии кнопки «Отправить» объект AadPicker сохраняет идентификатор пользователя в скрытом поле `AssignedToID`.

15. Теперь можно либо запустить приложение в отладчике Visual Studio, либо опубликовать его в веб-приложениях службы приложений Azure. Войдите в систему как владелец приложения и перейдите на страницу `~/WorkItems/Create`. Для своего опубликованного бизнес-приложения я перехожу на страницу `https://mylobapp.azurewebsites.net/WorkItems/Create`. Теперь вы увидите, что можно использовать тот же фильтр поиска AadPicker для выбора пользователя Azure Active Directory.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. Заполните оставшуюся часть формы и щелкните **Создать**. На странице ~/WorkItems/Index теперь отображается вновь созданный элемент. На приведенном ниже снимке экрана вы увидите, что я удалил столбец `AssignedToID` в файле Views\WorkItems\Index.cshtml.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	Сейчас необходимо сделать аналогичные изменения в представлении **Изменение**. В файле Views\WorkItems\Edit.cshtml внесите такие же изменения во вспомогательном методе `Html.BeginForm`, которые были сделаны на предыдущем шаге в Views\WorkItems\Create.cshtml(замените Create на Edit в выделенном коде выше).

Вот и все!

Теперь после настройки авторизаций и функциональности бизнес-приложения для различных действия в контроллере WorkItems можно попробовать входить в систему как пользователи с различными ролями приложения.

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
 

<!---HONumber=July15_HO4-->