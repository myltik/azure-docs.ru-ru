<properties 
	pageTitle="Создание бизнес-приложения Azure с проверкой подлинности Azure Active Directory | Microsoft Azure" 
	description="Узнайте, как создать бизнес-приложение ASP.NET MVC в службе приложений Azure, осуществляющее проверку подлинности с помощью Azure Active Directory" 
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
	ms.date="08/31/2016" 
	ms.author="cephalin"/>

# Создание бизнес-приложения Azure с проверкой подлинности Azure Active Directory #

В этой статье показано, как создать бизнес-приложение .NET на основе [веб-приложения службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714) с использованием функции [Аутентификация или авторизация](../app-service/app-service-authentication-overview.md). Здесь также описано, как использовать [API Graph Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog), чтобы запросить данные каталога в приложении.

Используемый клиент Azure Active Directory может действовать исключительно как каталог Azure или [синхронизироваться с локальной службой Active Directory](../active-directory/active-directory-aadconnect.md) для создания возможности единого входа для локальных или удаленных сотрудников. В рамках этой статьи используется каталог учетной записи Azure по умолчанию.

<a name="bkmk_build"></a>
## Что будет строиться ##

Вы создадите простое бизнес-приложение по шаблону «создать-прочитать-обновить-удалить» (CRUD) в веб-приложениях службы приложений, которое отслеживает рабочие элементы с помощью следующих компонентов:

- аутентификация пользователей в службе Azure Active Directory;
- запросы пользователей и групп каталога с помощью [API Graph Azure Active Directory](http://msdn.microsoft.com/library/azure/hh974476.aspx).

Дополнительные сведения о создании бизнес-приложения Azure с поддержкой управления доступом на основе ролей (RBAC) см. в разделе [Дальнейшее действие](#next).

<a name="bkmk_need"></a>
## Необходимые элементы ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Чтобы выполнить инструкции этого учебника, необходимо следующее:

- Клиент Azure Active Directory с пользователями в различных группах
- Разрешения на создание приложений в клиенте Azure Active Directory
- Visual Studio 2013 с обновлением 4 или более поздней версии.
- [Пакет Azure SDK версии 2.8.1 или более поздней](https://azure.microsoft.com/downloads/).

<a name="bkmk_deploy"></a>
## Создание и развертывание веб-приложения в Azure ##

1. В Visual Studio щелкните **Файл** > **Создать** > **Проект**.

2. Выберите **Веб-приложение ASP.NET**, введите имя проекта и нажмите кнопку **ОК**.

3. Выберите шаблон **MVC** и задайте для проверки подлинности значение **Нет проверки подлинности**. Установите флажок **Разместить в облаке** и нажмите кнопку **ОК**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)

4. В диалоговом окне **Создать службу приложений** щелкните **Добавить учетную запись** (а затем в раскрывающемся списке выберите **Добавить учетную запись**), чтобы войти в учетную запись Azure.

5. После этого перейдите на страницу "Настройка" веб-приложения. Нажмите кнопку **Создать**, чтобы создать группу ресурсов и план службы приложений. Чтобы продолжить, щелкните **Explore additional services** (Анализ дополнительных служб).

	![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)

6. Чтобы добавить для приложения базу данных SQL, на вкладке **Службы** щелкните **+**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)

7. Чтобы создать экземпляр SQL Server, на вкладке **Настроить базу данных SQL** щелкните **Создать**.

8. Настройте экземпляр SQL Server на вкладке **Настроить SQL Server**. Затем щелкните **ОК**, **ОК** и **Создать**, чтобы запустить создание приложения в Azure.

9. Сведения о завершении создания приложения отображаются на вкладке **Действие службы приложений Azure**. Щелкните **Опубликовать &lt;*имя\_приложения*> в этом веб-приложении**, а затем — **Опубликовать**.

	После завершения Visual Studio откроет опубликованное приложение в окне браузера.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>
## Настройка проверки подлинности и доступа к каталогу

1. Войдите на [портал Azure](https://portal.azure.com).

2. В меню слева выберите **Службы приложений** > **&lt;*имя\_приложения*>** > **Аутентификация или авторизация**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)

3. Чтобы включить проверку подлинности Azure Active Directory, щелкните **Включено** > **Azure Active Directory** > **Экспресс** > **OК**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)

4. Щелкните в командной строке **Сохранить**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)

    После сохранения параметров проверки подлинности перейдите к своему приложению в браузере. В соответствии с параметрами по умолчанию проверка подлинности применяется для всего приложения. Если вход в систему не выполнен, откроется экран входа. После входа откроется приложение, защищенное по протоколу HTTPS. Далее необходимо включить доступ к данным каталога.

5. Перейдите на [классический портал](https://manage.windowsazure.com).

6. В меню слева выберите **Active Directory** > **Каталог по умолчанию** > **Приложения** > **&lt;*имя\_приложения*>**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)

	Это приложение Azure Active Directory, созданное службой приложений для включения функции "Аутентификация или авторизация".

7. Чтобы проверить наличие пользователей и групп в каталоге, щелкните **Пользователи** и **Группы**. Если пользователи и группы отсутствуют, создайте несколько тестовых пользователей и групп.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)

7. Щелкните **Настройка**, чтобы настроить это приложение.

8. Прокрутите вниз до раздела **Ключи** и добавьте ключ, выбрав срок его действия. Затем щелкните раскрывающийся список **Делегированные разрешения** и выберите разрешение **Чтение данных каталога**. Щелкните **Сохранить**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)

8. После сохранения параметров прокрутите вверх до раздела **Ключи** и нажмите кнопку **копирования**, чтобы скопировать клиентский ключ.

	![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)

	>[AZURE.IMPORTANT] Если покинуть эту страницу сейчас, вы больше никогда не сможете получить доступ к этому клиентскому ключу.

9. Теперь необходимо настроить веб-приложение с этим ключом. Войдите в [обозреватель ресурсов Azure](https://resources.azure.com), используя учетную запись Azure.

10. В верхней части страницы щелкните **Чтение и запись**, чтобы внести изменения в обозревателе ресурсов Azure.

	![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)

11. Перейдите к параметрам проверки подлинности приложения, щелкнув "Подписки" > **&lt;*имя\_подписки*>** > **Группы ресурсов** > **&lt;*имя\_группы\_русурсов*>** > **Поставщики** > **Microsoft.Web** > **Сайты** > **&lt;*имя\_приложения*>** > **Настройки** > **Параметры проверки подлинности**.

12. Нажмите кнопку **Изменить**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)

13. В области редактирования задайте свойство `clientSecret` и `additionalLoginParams`, как показано ниже.

		...
		"clientSecret": "<client key from the Azure Active Directory application>",
		...
		"additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
		...

14. Чтобы отправить изменения, в верхней части страницы нажмите кнопку **Поместить**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)

14. Теперь, чтобы проверить наличие маркера проверки подлинности, используемого для доступа к API Graph Azure Active Directory, измените файл ~\\Controllers\\HomeController.cs, задав в нем следующий метод действия `Index()`.
	<pre class="prettyprint">
	public ActionResult Index()
	{
		return <mark>Content(Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);</mark>
	}
	</pre>

15. Чтобы опубликовать изменения, щелкните проект правой кнопкой и выберите **Опубликовать**. Снова щелкните **Опубликовать** в диалоговом окне.

	![](./media/web-sites-dotnet-lob-application-azure-ad/15-publish-token-code.png)

	Если маркер доступа отображается на домашней странице приложения, тогда приложение имеет доступ к API Graph Azure Active Directory. Изменения, внесенные в файл ~\\Controllers\\HomeController.cs, можно отменить.

В следующем разделе описываются действия, выполняемые с данными каталога.

<a name="bkmk_crud"></a>
## Добавление функциональности бизнес-приложения к приложению

Теперь необходимо создать простое приложения CRUD для отслеживания рабочих элементов.

5.	В папке ~\\Models создайте файл класса с именем WorkItem.cs и замените `public class WorkItem {...}` следующим кодом.

		using System.ComponentModel.DataAnnotations;

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

7.	Постройте проект, чтобы сделать новую модель доступной для логики формирования шаблона в Visual Studio.

8.	Добавьте новый шаблонный элемент `WorkItemsController` в папку ~\\Controllers (щелкните правой кнопкой мыши **Контроллеры**, выберите **Добавить**, а затем — **Создать шаблонный элемент**).

9.	Выберите **Контроллер MVC 5 с представлениями, использующий Entity Framework**, и щелкните **Добавить**.

10.	Выберите созданную модель, щелкните **+** и **Добавить**, чтобы добавить контекст данных, а затем нажмите кнопку **Добавить**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)

9.	Откройте файл ~\\Controllers\\WorkItemsController.cs.

13.	В начале методов `Create()` и `Edit(int? id)` добавьте следующий код, чтобы позже некоторые переменные были доступны в JavaScript. Чтобы исправить все ошибки, связанные с разрешением имен, щелкните каждую ошибку и нажмите клавиши `Ctrl`+`.`.

		ViewData["token"] = Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"];
		ViewData["tenant"] =
			ClaimsPrincipal.Current.Claims
			.Where(c => c.Type == "http://schemas.microsoft.com/identity/claims/tenantid")
			.Select(c => c.Value).SingleOrDefault();

	> [AZURE.NOTE] В некоторых действиях вы можете заметить оформление <code>[ValidateAntiForgeryToken]</code>. Из-за поведения, описанного [Алленом Броком (Brock Allen)](https://twitter.com/BrockLAllen) в статье [MVC 4, AntiForgeryToken and Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) (MVC 4, маркер защиты от подделки и утверждения), команда HTTP POST может завершиться ошибкой при проверке маркеров защиты от подделки по следующей причине:

	> - Azure Active Directory не отправляет http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, который по умолчанию необходим для маркера защиты от подделки.
	> - Если Azure Active Directory является каталогом, синхронизированным с AD FS, то инструмент доверия AD FS по умолчанию также не отправляет утверждение http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, хотя AD FS можно настроить вручную для отправки такого утверждения.

	> Мы займемся этой проблемой на следующем шаге.

12.  В файле ~\\Global.asax добавьте следующую строку кода в метод `Application_Start()`. Чтобы исправить все ошибки, связанные с разрешением имен, щелкните каждую ошибку и нажмите клавиши `Ctrl`+`.`.

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` определяет утверждение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, которое служба Azure Active Directory не предоставляет.

14.	В файле ~\\Views\\WorkItems\\Create.cshtml (автоматически сформированный на основе шаблона элемент) найдите вспомогательный метод `Html.BeginForm` и добавьте следующие выделенные строки.
	<pre class="prettyprint">
	@model WebApplication1.Models.WorkItem
	
	@{
		ViewBag.Title = "Create";
	}
	
	&lt;h2>Create&lt;/h2>
	
	@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>)) 
	{
		@Html.AntiForgeryToken()
	
		&lt;div class="form-horizontal">
			&lt;h4>WorkItem&lt;/h4>
			&lt;hr />
			@Html.ValidationSummary(true, "", new { @class = "text-danger" })
			&lt;div class="form-group">
				@Html.LabelFor(model => model.AssignedToID, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type = "hidden"</mark> } })
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
					&lt;input type="submit" value="Create" class="btn btn-default"<mark> id="submit-button"</mark> />
				&lt;/div>
			&lt;/div>
		&lt;/div>
	}
	
	&lt;div>
		@Html.ActionLink("Back to List", "Index")
	&lt;/div>
	
	@section Scripts {
		@Scripts.Render("~/bundles/jqueryval")
		<mark>&lt;script>
			// People/Group Picker Code
			var maxResultsPerPage = 14;
			var input = document.getElementById("AssignedToName");
	
			var token = "@ViewData["token"]";
			var tenant = "@ViewData["tenant"]";
	
			var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
	
			// Отправьте выбранного пользователя или группу для назначения.
			$("#submit-button").click({ picker: picker }, function () {
				if (!picker.Selected())
					return;
				$("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
			});
		&lt;/script></mark>
	}
	</pre>
	
	Обратите внимание, что `token` и `tenant` используются объектом `AadPicker` для выполнения вызовов API Graph Azure Active Directory. Объект `AadPicker` добавим позже.

15. Внесите аналогичные изменения в файл ~\\Views\\WorkItems\\Edit.cshtml.

15. Объект `AadPicker` определяется в сценарии, который необходимо добавить в проект. Щелкните папку ~\\Scripts правой кнопкой мыши, выберите **Добавить**, а затем щелкните **Файл JavaScript**. Ведите для имени файла значение `AadPickerLibrary` и нажмите кнопку **ОК**.

16. Скопируйте содержимое [отсюда](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js) и вставьте его в файл ~\\Scripts\\AadPickerLibrary.js.

	В сценарии объект `AadPicker` вызывает [API Graph Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) для поиска пользователей и групп, которые соответствуют введенным данным.

17. В файле ~\\Scripts\\AadPickerLibrary.js также используется [мини-приложение автозаполнения пользовательского интерфейса jQuery](https://jqueryui.com/autocomplete/). Поэтому в проект необходимо добавить пользовательский интерфейс jQuery. Щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**.

18. В диспетчере пакетов NuGet щелкните "Обзор", введите в строке поиска **jquery-ui** и выберите **jQuery.UI.Combined**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)

19. В области справа щелкните **Установить** и нажмите кнопку **ОК**, чтобы продолжить.

19. Откройте файл ~\\App\_Start\\BundleConfig.cs и добавьте следующие выделенные строки.
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
		bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
					"~/Scripts/jquery-{version}.js"<mark>,
					"~/Scripts/jquery-ui-{version}.js",
					"~/Scripts/AadPickerLibrary.js"</mark>));
	
		bundles.Add(new ScriptBundle("~/bundles/jqueryval").Include(
					"~/Scripts/jquery.validate*"));
	
		// Используйте версию разработки Modernizr как основу для обучения и разработки. Когда вы будете готовы
		// к развертыванию в рабочей среде, используйте инструмент создания по адресу http://modernizr.com, что выбирать только необходимые тесты.
		bundles.Add(new ScriptBundle("~/bundles/modernizr").Include(
					"~/Scripts/modernizr-*"));
	
		bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
					"~/Scripts/bootstrap.js",
					"~/Scripts/respond.js"));
	
		bundles.Add(new StyleBundle("~/Content/css").Include(
					"~/Content/bootstrap.css",
					"~/Content/site.css"<mark>,
					"~/Content/themes/base/jquery-ui.css"</mark>));
	}
	</pre>

	Существуют и более производительные способы управления файлами CSS и JavaScript в приложении. Однако для простоты используйте пакеты, предоставленные с каждым представлением.

20. Теперь опубликуйте изменения. Щелкните свой проект правой кнопкой мыши и выберите **Опубликовать**.

21. Щелкните **Параметры**, убедитесь в наличии строки подключения к базе данных SQL, выберите **Обновить базу данных**, чтобы внести изменения в схему модели, и нажмите кнопку **Опубликовать**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)

22. В браузере перейдите на страницу https://&lt;*appname*>.azurewebsites.net/workitems и щелкните **Создать**.

23. Щелкните поле **AssignedToName**. В раскрывающемся списке должны отобразиться пользователи и группы из клиента Azure Active Directory. Задайте условия фильтрации или используйте клавиши `Up` и `Down`, чтобы выбрать пользователя или группу.

	![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)

24. Нажмите кнопку **Создать**, чтобы сохранить изменения. Затем в созданном рабочем элементе щелкните **Изменить**, и вы сможете наблюдать за таким же поведением.

Поздравляем, теперь бизнес-приложение с доступом к каталогу запущено в Azure! API Graph предлагает гораздо больше возможностей. См. [справочные материалы по API Graph Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog).

<a name="next"></a>
## Дальнейшее действие

Чтобы создать бизнес-приложение Azure с поддержкой управления доступом на основе ролей (RBAC), воспользуйтесь [образцом WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims), предоставленным рабочей группой Azure Active Directory. В нем показано, как включить роли для приложения Azure Active Directory, а затем авторизовать пользователей с оформлением `[Authorize]`.

Чтобы предоставить бизнес-приложению доступ к локальным данным, см. статью [Доступ к локальным ресурсам с помощью гибридных подключений в службе приложений Azure](web-sites-hybrid-connection-get-started.md).

<a name="bkmk_resources"></a>
## Дополнительные ресурсы

- [Проверка подлинности и авторизация в службе приложений Azure](../app-service/app-service-authentication-overview.md)
- [Проверка подлинности в приложении Azure с помощью локального каталога Active Directory](web-sites-authentication-authorization.md)
- [Создание веб-приложения .NET MVC в службе приложений Azure с аутентификацией AD FS](web-sites-dotnet-lob-application-adfs.md)
- [App Service Auth and the Azure AD Graph API](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/) (Проверка подлинности службы приложений и API Graph Azure AD)
- [Примеры и документация Microsoft Azure Active Directory](https://github.com/AzureADSamples)
- [Поддерживаемые типы маркеров и утверждений](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[Protect the Application with SSL and the Authorize Attribute]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute

<!---HONumber=AcomDC_0831_2016-->