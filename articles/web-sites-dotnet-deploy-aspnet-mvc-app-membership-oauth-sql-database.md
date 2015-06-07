<properties 
	pageTitle="Создание приложения ASP.NET MVC с проверкой подлинности и базой данных SQL и развертывание службы приложений Azure" 
	description="Узнайте, как разработать приложение ASP.NET MVC 5 с внутренней базой данных SQL, добавить проверку подлинности и авторизацию и развернуть его в службе Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="riande"/>



# Создание приложения ASP.NET MVC с проверкой подлинности и базой данных SQL и развертывание службы приложений Azure

В этом учебнике показано, как создать безопасное веб-приложение ASP.NET MVC 5, которое позволяет пользователям входить в систему с учетными данными из Google или Facebook. Вы также узнаете, как развернуть приложение в [службе приложений](http://go.microsoft.com/fwlink/?LinkId=529714).

Можно бесплатно создать учетную запись Azure, а если у вас еще нет Visual Studio 2013, то пакет SDK автоматически установит Visual Studio 2013 для Web Express. Вы можете приступить к разработке приложений для Azure совершенно бесплатно.

В данном учебнике предполагается, что у читателя нет опыта использования платформы Azure. По завершении изучения этого учебника вы создадите безопасное приложение на основе данных, выполняемое в облаке и использующее облачную базу данных.

Вы узнаете следующее:

* Создание безопасного проекта ASP.NET MVC 5 и его публикация в [веб-приложениях службы приложений](http://go.microsoft.com/fwlink/?LinkId=529714) в службе приложений Azure.
* Использование [OAuth](http://oauth.net/ "http://oauth.net/") и базы данных членства ASP.NET для защиты приложения.
* Как использовать базу данных SQL для хранения данных в Azure.

Вы создадите простое веб-приложение для списка контактов, основанное на платформе ASP.NET MVC 5 и использующее ADO.NET Entity Framework для доступа к базе данных. На следующем рисунке показана страница входа для готового приложения:

![Страница входа][rxb]

>[AZURE.NOTE]Для работы с этим учебником необходимо использовать учетную запись Microsoft Azure. Если у вас нет учетной записи, можно <a href="/ru-ru/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">активировать преимущества для подписчиков MSDN</a> или <a href="/ru-ru/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">подписаться на бесплатную пробную версию</a>.

>Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

Чтобы настроить среду разработки, необходимо установить [Visual Studio 2013 с обновлением 4](http://go.microsoft.com/fwlink/?LinkId=390521) или более позднюю версию и последнюю версию [Azure SDK для Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Эта статья была написана для версий Visual Studio с обновлением 4 и пакета SDK 2.5.1.

## Создание приложения ASP.NET MVC 5

### Создание проекта

1. В меню **Файл** выберите **Новый проект**.

	!["Новый проект" в меню "Файл"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

1. В диалоговом окне **Новый проект** разверните **C#** и выберите **Интернет** в **Установленные шаблоны**, а затем выберите **Веб-приложение ASP.NET**.

1. Присвойте приложению имя **ContactManager** и нажмите кнопку **ОК**.

	![Диалоговое окно "Новый проект"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
	**Примечание.** Необходимо ввести именно ContactManager. В блоках кода, которые вы будете копировать позже, предполагается, что проект имеет имя ContactManager.

1. В диалоговом окне **Новый проект ASP.NET** выберите шаблон **MVC**. Убедитесь, что параметр **Проверка подлинности** имеет значение **Индивидуальные учетные записи** пользователей, установлен флаг **Размещать в облаке** и выбран параметр **Веб-приложения**.

	![Диалоговое окно "Новый проект ASP.NET"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/newproject.png)

1. Мастер настройки предложит уникальное имя на основе *ContactManager*. Необходимо решить, следует ли создавать новую группу ресурсов и план приложения службы. Рекомендации по поводу того, следует ли создать новый план или группу ресурсов, см. в разделе [Исчерпывающий обзор планов службы приложения Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md). В этом учебнике, возможно, необходимо будет создать хотя бы новую группу ресурсов. Выберите ближайшую область. Поиск центра обработки данных с наименьшим временем задержки можно выполнить с помощью веб-сайта [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com"). На следующем шаге необходимо будет настроить базу данных, поэтому пока не нажимайте **ОК**.

   ![Новый план и группа ресурсов](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/newplanandgroup.png)
 
2. Если вы еще не создали сервер базы данных, нажмите **Создать новый сервер**, а затем укажите имя, имя пользователя и пароль для базы данных.

   ![Использование новой базы данных](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/newdb.png)

3. Если у вас есть сервер баз данных, используйте его для создания новой базы данных. Серверы баз данных являются ценным ресурсом, и обычно удобнее создать сразу несколько баз данных на одном сервере для тестирования и разработки, чем создавать по одному серверу на каждую базу данных. Убедитесь, что веб-приложение и база данных находятся в одном регионе.

    ![Использование имеющейся базы данных](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/useexistingdb.png)

### Задание верхнего и нижнего колонтитула страницы


1. В **обозревателе решений** откройте файл *Layout.cshtml* в папке *Views\\Shared*.

	![_Layout.cshtml в обозревателе решений][newapp004]

1. Замените разметку файла *Layout.cshtml* на код, приведенный ниже. Изменения выделены ниже.

<pre>
			&lt;!DOCTYPE html>
			&lt;html>
			&lt;head>
			    &lt;meta charset="utf-8" />
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0">
			    &lt;title>@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title>
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head>
			&lt;body>
			    &lt;div class="navbar navbar-inverse navbar-fixed-top">
			        &lt;div class="container">
			            &lt;div class="navbar-header">
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
			                    &lt;span class="icon-bar">&lt;/span>
			                    &lt;span class="icon-bar">&lt;/span>
			                    &lt;span class="icon-bar">&lt;/span>
			                &lt;/button>
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div>
			            &lt;div class="navbar-collapse collapse">
			                &lt;ul class="nav navbar-nav">
			                    &lt;li>@Html.ActionLink("Home", "Index", "Home")&lt;/li>
			                    &lt;li>@Html.ActionLink("About", "About", "Home")&lt;/li>
			                    &lt;li>@Html.ActionLink("Contact", "Contact", "Home")&lt;/li>
			                &lt;/ul>
			                @Html.Partial("_LoginPartial")
			            &lt;/div>
			        &lt;/div>
			    &lt;/div>
			    &lt;div class="container body-content">
			        @RenderBody()
			        &lt;hr />
			        &lt;footer>
			            &lt;p>&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p>
			        &lt;/footer>
			    &lt;/div>
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required: false)
			&lt;/body>
			&lt;/html>
</pre>

### Локальный запуск приложения

1. Для запуска приложения нажмите сочетание клавиш CTRL+F5.

	Главная страница приложения откроется в браузере, используемом по умолчанию.

	![Веб-приложение, выполняющееся локально](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Это все, что требуется для создания приложения, которое будет развернуто в Azure.

## Включение SSL для проекта ##

1. Включение SSL. В обозревателе решений щелкните проект **ContactManager**, а затем нажмите F4, чтобы открыть диалоговое окно свойств. Для параметра **SSL включен** измените значение на "true". Скопируйте **URL-адрес SSL**. Если вы ранее не создали веб-сайты SSL, URL-адрес SSL будет https://localhost:44300/.

	![Включение SSL][rxSSL]
 
1. В обозревателе решений щелкните правой кнопкой мыши проект **Contact Manager**, затем щелкните **Свойства**.
1. На левой вкладке щелкните **Интернет**.
1. Измените **URL-адрес проекта** на **URL-адрес SSL** и сохраните страницу (Ctrl + S).

	![Включение SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. Убедитесь, что обозреватель Visual Studio открывает браузер Internet Explorer, как показано на рисунке ниже:

	![браузер по умолчанию](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	Средство выбора браузера позволяет указать используемый Visual Studio браузер.

 	![средство выбора браузера](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

	Вы можете выбрать несколько браузеров и обновлять их при внесении изменений. Дополнительные сведения см. в разделе [Использование ссылки на браузер в Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).


1. Для запуска приложения нажмите сочетание клавиш CTRL+F5. Следуйте инструкциям, чтобы подтвердить доверие к самозаверяющему сертификату, созданному IIS Express.

	 ![инструкции для подтверждения доверия к самозаверяющему сертификату, созданному IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. Прочитайте текст в диалоговом окне **Предупреждение системы безопасности** и нажмите **Да**, если необходимо установить сертификат, представляющий **localhost**.

 	![Предупреждение о сертификате localhost в IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. В IE откроется *Главная* страница без предупреждений SSL.

	 ![IE с SSL-сертификатом localhost и без предупреждений](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	Google Chrome также принимает сертификат и отображает HTTPS-содержимое без предупреждения. Firefox использует собственное хранилище сертификатов, поэтому отображается предупреждение.

	 ![Предупреждение сертификата FireFox](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

## Развертывание приложения в Azure

1. В Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать** в контекстном меню.

	!["Опубликовать" в контекстном меню проекта](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   Откроется мастер **Публикация веб-сайта**.

1. В диалоговом окне **Публикации веб-сайта** щелкните **Опубликовать**.

	![Опубликовать](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	Созданное вами приложение теперь выполняется в облаке. При следующем развертывании приложения будут развернуты только измененные (или новые) файлы.

	![Запуск в облаке](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

## Добавление базы данных в приложение

Далее вам предстоит обновить приложение, чтобы добавить возможности отображения и обновления контактов и сохранения данных в базе данных. Приложение будет использовать компонент Entity Framework (EF) для создания базы данных, а также чтения и обновления данных в базе данных.

### Добавление классов модели данных для контактов

Начните с создания простой модели данных в коде.

1. В **обозревателе решений** щелкните правой кнопкой мыши папку "Модели" и выберите **Добавить**, а затем щелкните **Класс**.

	![Контекстное меню добавления класса в папке "Модели"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. В диалоговом окне **Добавление нового элемента** присвойте новому файлу класса имя *Contact.cs*, затем щелкните **Добавить**.

	![Диалоговое окно "Добавление нового элемента"][adddb002]

3. Замените содержимое файла Contacts.cs на код, приведенный ниже.

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }
Класс **Contacts** определяет данные, которые будут храниться для каждого контакта, а также первичный ключ, *ContactID*, необходимый для базы данных.

### Создание веб-страниц, позволяющих пользователям приложений работать с контактами

Компонент формирования шаблонов в ASP.NET MVC может автоматически создавать код, выполняющий операции создания, чтения, обновления и удаления.

## Добавление контроллера и представления для данных

1. Создайте проект **(Ctrl + Shift + B)** (проект необходимо создать перед использованием механизма формирования шаблонов). 
1. В **обозревателе решений** щелкните правой кнопкой мыши папку "Контроллеры" и выберите **Добавить**, а затем щелкните **Контроллер**.

	![Контекстное меню добавления контроллера в папке "Контроллеры"][addcode001]

5. В диалоговом окне **Добавление Scaffold** выберите **Контроллер MVC 5 с представлениями, использующий EF** и щелкните **Добавить**.
	
	![Диалоговое окно "Добавление Scaffold"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. В раскрывающемся списке **Класс модели** выберите** Контакт (ContactManager.Models)**. (См. изображение ниже.)
1. В поле **Класс контекста модели** выберите **ApplicationDbContext (ContactManager.Models)**. **ApplicationDbContext** будет использоваться и для базы данных членства, и для наших контактных данных.
1. В текстовом поле **Имя контроллера** введите имя контроллера «CmController». 

	![Диалоговое окно "Новый контекст данных"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. Щелкните **Добавить**.

   Visual Studio создает методы и представления контроллера для операций создания, чтения, обновления и удаления в базе данных для объектов **Контакт**.

## Включение Migrations, создание базы данных, добавление примера данных и инициализатора данных ##

Следующая задача заключается в активации компонента [Code First Migrations](http://msdn.microsoft.com/library/hh770484.aspx) для создания базы данных на основе созданной модели данных.

1. В меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем **Консоль диспетчера пакетов**. !["Консоль диспетчера пакетов" в меню "Сервис"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. В окне **Консоль диспетчера пакетов** введите следующую команду:

		enable-migrations
	Команда **enable-migrations** создает папку *Migrations *и помещает в нее файл *Configuration.cs*, который можно изменять и использовать для заполнения базы данных и настройки миграций.

2. В окне **Консоль диспетчера пакетов** введите следующую команду:

		add-migration Initial


	Команда **add-migration Initial** создает файл с именем **&lt;date_stamp&gt;Initial** в папке *Migrations*, использованной при создании базы данных. Первый параметр (**Initial**) является произвольным и используется для создания имени файла. Новые файлы классов можно просмотреть в **обозревателе решений**. В классе **Initial** метод **Up** создает таблицу Contacts, а метод **Down** (используется при возвращении к предыдущему состоянию) удаляет эту таблицу.
3. Откройте файл *Migrations\\Configuration.cs*. 
4. Добавьте следующее пространство имен. 

    	 using ContactManager.Models;



5. Замените метод *Seed* следующим кодом:

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

	Этот код инициализирует (заполняет) базу данных с контактной информацией. Дополнительные сведения об инициализации базы данных см. в разделе [Инициализация и отладка баз данных Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. В окне **Консоль диспетчера пакетов** введите команду:

		update-database

	![Команды консоли диспетчера пакетов][addcode009]

	Команда **update-database** выполняет первую миграцию, при которой создается база данных. По умолчанию база данных создается как локальная база данных SQL Server Express LocalDB.

7. Нажмите CTRL + F5 для запуска приложения, а затем щелкните ссылку **CM Demo** или перейдите по адресу https://localhost:(port#)/Cm.

	Приложение показывает начальные данные и предоставляет ссылки для изменения, удаления и просмотра подробных сведений. Можно создавать, изменять, удалять и просматривать данные.

	![Представление MVC для данных][rx2]



## Добавление поставщика OAuth2

[OAuth](http://oauth.net/ "http://oauth.net/") — это открытый протокол, обеспечивающий безопасную авторизацию простым и стандартным методом из веб-приложений, мобильных и настольных приложений. В интернет-шаблоне ASP.NET MVC используется OAuth для предоставления Facebook, Twitter, Google и Microsoft в качестве поставщиков проверки подлинности. В этом учебнике в качестве поставщика проверки подлинности используется только Google, но вы легко можете изменить код, чтобы использовать любой другой поставщик. Шаги по реализации других поставщиков очень похожи на шаги, представленные в этом учебнике. Для использования Facebook в качестве поставщика проверки подлинности см. учебник [Приложение MVC 5 со входом через Facebook, Twitter, LinkedIn и Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

Помимо проверки подлинности в учебнике будут использоваться роли для реализации авторизации. Только пользователи, добавленные к роли *canEdit*, смогут изменять данные (то есть создавать, править и удалять контакты).

Выполните инструкции из учебника [Приложение MVC 5 со входом через Facebook, Twitter, LinkedIn и Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog), раздел **Создание приложения Google для OAuth 2, чтобы настроить приложение Google для OAuth2**. Запустите и протестируйте приложение, чтобы убедиться, что вы можете войти в систему с использованием проверки подлинности Google.

## Использование API членства
В этом разделе в базу данных членства будут добавлены локальный пользователь и роль *canEdit*. Только пользователи, входящие в роль *canEdit*, смогут изменять данные. Рекомендуется именовать роли по выполняемым ими действиям, например имя *canEdit* предпочтительнее имени *admin*. По мере развития приложения можно добавлять новые роли, например роль *canDeleteMembers*, имя которой предпочтительнее, чем менее описательное имя *superAdmin*.

1. Откройте файл *migrations\\configuration.cs* и добавьте следующие инструкции `using`:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Добавьте следующий метод **AddUserAndRole** в класс:

    
         bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
         {
            IdentityResult ir;
            var rm = new RoleManager<IdentityRole>
                (new RoleStore<IdentityRole>(context));
            ir = rm.Create(new IdentityRole("canEdit"));
            var um = new UserManager<ApplicationUser>(
                new UserStore<ApplicationUser>(context));
            var user = new ApplicationUser()
            {
               UserName = "user1@contoso.com",
            };
            ir = um.Create(user, "P_assw0rd1");
            if (ir.Succeeded == false)
               return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
         }

1. Вызовите новый метод из метода **Seed**: <pre> protected override void Seed(ContactManager.Models.ApplicationDbContext context) { <mark>AddUserAndRole(context);</mark> context.Contacts.AddOrUpdate(p => p.Name, // Код удален для краткости } </pre> <span></span> На следующих изображениях показаны изменения в методе *Seed*:

	![изображение кода](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   Этот код создает новую роль с именем *canEdit*, создает нового локального пользователя *user1@contoso.com* и добавляет *user1@contoso.com* к роли *canEdit*. Дополнительную информацию см. в [учебниках по удостоверениям ASP.NET](http://www.asp.net/identity/overview/features-api).

## Использование временного кода для добавления новых пользователей социальных сетей к роли canEdit  ##
В этом разделе будет временно изменен метод **ExternalLoginConfirmation** в контроллере Account для добавления новых пользователей, регистрирующихся с помощью поставщика OAuth, к роли *canEdit*. Мы временно изменим метод **ExternalLoginConfirmation** таким образом, чтобы новые пользователи автоматически добавлялись к роли администратора. Пока нет средств для добавления ролей и управления ими, мы будем использовать приведенный ниже код временной автоматической регистрации. Надеемся в будущем предоставить средство, аналогичное [WSAT](http://msdn.microsoft.com/library/ms228053.aspx), которое позволит вам создавать и изменять учетные записи пользователей и роли.

1. Откройте файл **Controllers\\AccountController.cs** и перейдите к методу **ExternalLoginConfirmation**.
1. Добавьте следующий вызов в **AddToRoleAsync** прямо перед вызовом **SignInAsync**.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   Приведенный выше код добавляет вновь зарегистрированного пользователя в роль canEdit, что дает ему доступ к методам действий, которые изменяют (редактируют) данные. 
	<pre> 
		// POST: /Account/ExternalLoginConfirmation
	      [HttpPost]
	      [AllowAnonymous]
	      [ValidateAntiForgeryToken]
	      public async Task<ActionResult> ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl)
	      {
	         if (User.Identity.IsAuthenticated)
	         {
	            return RedirectToAction("Index", "Manage");
	         }
	         if (ModelState.IsValid)
	         {
	            // Get the information about the user from the external login provider
	            var info = await AuthenticationManager.GetExternalLoginInfoAsync();
	            if (info == null)
	            {
	               return View("ExternalLoginFailure");
	            }
	            var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
	            var result = await UserManager.CreateAsync(user);
	            if (result.Succeeded)
	            {
	               result = await UserManager.AddLoginAsync(user.Id, info.Login);
	               if (result.Succeeded)
	               {
	                  <mark>await UserManager.AddToRoleAsync(user.Id, "canEdit");</mark>
	                  await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false);
	                  return RedirectToLocal(returnUrl);
	               }
	            }
	            AddErrors(result);
	         }
	         ViewBag.ReturnUrl = returnUrl;
	         return View(model);
	      }
	</pre>

Далее в этом учебнике будет развернуто приложение в Azure, благодаря которому можно будет войти с помощью Google или другого стороннего поставщика проверки подлинности. Это добавит вновь зарегистрированную учетную запись к роли *canEdit*. Каждый, кому известен URL-адрес вашего веб-приложения, сможет использовать свой идентификатор Google для регистрации и обновления вашей базы данных. Чтобы предотвратить подобные действия со стороны других лиц, можно остановить сайт. Изучив базу данных, вы сможете проверить, кто входит в роль *canEdit*.

На **консоли диспетчера пакетов** нажмите клавишу со стрелкой вверх, чтобы открыть следующую команду:

		Update-Database

Выполните команду **Update-Database**, которая вызывает метод **Seed** и только что добавленный метод **AddUserAndRole**. Метод **AddUserAndRole** создаст пользователя *user1@contoso.com* и добавит его к роли *canEdit*.

## Защита приложения с помощью SSL и атрибута Authorize ##

В этом разделе будет применен атрибут [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) для ограничения доступа к методам действий. Анонимные пользователи смогут просматривать только метод действия **Индекс** главного контроллера. Зарегистрированные пользователи смогут видеть контактные данные (страницы **Индекс** и **Сведения** контроллера Cm), сведения о программе и страницы связи. Только пользователи с назначенной ролью *canEdit* получат доступ к методам действий, изменяющих данные.

1. Добавьте в приложение фильтры [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) и [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx). В качестве альтернативы можно добавить атрибуты [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) и [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) для каждого контроллера, но с точки зрения безопасности рекомендуется применять их ко всему приложению. В случае глобального добавления атрибутов каждый новый контроллер и метод действия будет защищен автоматически, вам не потребуется помнить о необходимости их применения. Дополнительные сведения см. в разделе [Защита приложений ASP.NET MVC и новый атрибут AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) Откройте файл *App_Start\\FilterConfig.cs* и замените метод *RegisterGlobalFilters* следующим кодом (который добавляет два фильтра): <pre> public static void RegisterGlobalFilters(GlobalFilterCollection filters) { filters.Add(new HandleErrorAttribute()); <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute()); filters.Add(new RequireHttpsAttribute());</mark> } </pre>




	Фильтр [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx), применяемый приведенным выше кодом, не позволит анонимным пользователям получить доступ к методам в приложении. С помощью атрибута [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) вы сможете отказаться от обязательной авторизации в соответствующих методах, чтобы анонимные пользователи могли входить в систему и просматривать главную страницу. Фильтр [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) будет требовать использования HTTPS для доступа к веб-приложению.

1. Добавьте атрибут [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) в метод **Index** контроллера Home. Атрибут [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) позволяет помещать в белый список методы, которые требуется вывести из-под авторизации. <pre> public class HomeController : Controller { <mark>[AllowAnonymous]</mark> public ActionResult Index() { return View(); } </pre>

2. Выполнив глобальный поиск для атрибута *AllowAnonymous*, вы увидите, что он используется в методах входа и регистрации контроллера Account.
1. В файле *CmController.cs* добавьте `[Authorize(Roles = "canEdit")]` к методам HttpGet и HttpPost, которые изменяют данные (Create, Edit, Delete, все методы действия, кроме Index и Details), в контроллере *Cm*. Ниже показана часть готового кода: <pre> // GET: Cm/Create <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Create() { return View(new Contact { Address = "123 N 456 W", City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT", Zip = "59405"}); } // POST: Cm/Create // Для защиты от атак оверпостингом включите определенные свойства, к которым требуется установить привязку, // дополнительные сведения см. в http://go.microsoft.com/fwlink/?LinkId=317598. [HttpPost][ValidateAntiForgeryToken] <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Create([Bind(Include = "ContactId,Name,Address,City,State,Zip,Email")] Contact contact) { if (ModelState.IsValid) { db.Contacts.Add(contact); db.SaveChanges(); return RedirectToAction("Index"); } return View(contact); } // GET: Cm/Edit/5 <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Edit(int? id) { if (id == null) { return new HttpStatusCodeResult(HttpStatusCode.BadRequest); } Contact contact = db.Contacts.Find(id); if (contact == null) { return HttpNotFound(); } return View(contact); } </pre>

1. Если вы все еще не вышли из предыдущего сеанса, нажмите ссылку **Выход**.
1. Щелкните ссылку **О программе** или **Связь**. Вы перейдете на страницу входа, поскольку анонимные пользователи не могут просматривать эти страницы. 
1. Щелкните ссылку **Зарегистрироваться в качестве нового пользователя** и добавьте локального пользователя с адресом электронной почты *joe@contoso.com*. Проверьте, может ли *Joe* просматривать главную страницу, страницу сведений о программе и страницу связи. 
	![вход](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Щелкните ссылку *CM Demo* и убедитесь, что видите данные.
1. Щелкнув ссылку "Изменить" на странице, вы перейдете на страницу входа (поскольку новый локальный пользователь не добавляется к роли *canEdit*).
1. Войдите как *user1@contoso.com* с паролем P_assw0rd1 (0 в word — это нуль). Вы перейдете на ранее выбранную страницу изменения. <br/> Если не получается войти в систему с этой учетной записью и этим паролем, попробуйте скопировать пароль из исходного кода и вставить его. Если войти по-прежнему не удается, проверьте в столбце **UserName** таблицы **AspNetUsers**, добавлен ли пользователь *user1@contoso.com*. 
1. Убедитесь, что можете изменять данные.

## Развертывание приложения в Azure

1. В Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать** в контекстном меню.

	!["Опубликовать" в контекстном меню проекта][firsdeploy003]

	Откроется мастер **Публикация веб-сайта**.

1. Откройте вкладку **Параметры** в левой части диалогового окна **Публикация веб-сайта**. Щелкните значок **v**, чтобы выбрать параметр **Строка удаленного подключения** для **ApplicationDbContext**, а затем выберите **ContactManagerNN_db**.

   
	![Параметры](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. В разделе **ContactManagerContext** выберите **Выполнить Code First Migrations**.

	![Параметры](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. Щелкните **Опубликовать**.
1. Войдите в систему как *user1@contoso.com* с паролем P_assw0rd1 и убедитесь, что вы можете изменять данные. 1. Выйдите из системы.
1. Перейдите в [консоль разработчиков Google](https://console.developers.google.com/) и на вкладке **Учетные данные** обновите URI перенаправления и JavaScript Orgins для использования URL-адреса Azure.
1. Войдите с помощью Google или Facebook. При этом учетная запись Google или Facebook добавится к роли **canEdit**. Если появится ошибка HTTP 400 с сообщением *URI перенаправления в запросе https://contactmanager{my version}.azurewebsites.net/signin-google не совпадает с зарегистрированным URI перенаправления*, вам придется подождать, пока распространяются изменения. Если эта ошибка будет появляться несколько минут, проверьте правильность универсальных кодов ресурсов.

### Остановка веб-приложения для предотвращения регистрации других пользователей  

1. В **обозревателе сервера** перейдите к элементу **Веб-приложения**.
4. Щелкните правой мышкой веб-приложение и выберите **Остановить**. 

	![остановка веб-приложения](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/s1.png)

	Можно также выбрать веб-сайт на [портале Azure](http://go.microsoft.com/fwlink/?LinkId=529715), а затем щелкнуть значок **остановки** в нижней части страницы.

	![остановка портала веб-приложений](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/stopweb.png)

### Удаление AddToRoleAsync, публикация и тестирование

1. Закомментируйте или удалите следующий код из метода **ExternalLoginConfirmation** в контроллере Account: `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Создайте проект (который сохраняет изменения файла и проверяет наличие ошибок компиляции).
5. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать**.

	   !["Опубликовать" в контекстном меню проекта](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. Нажмите кнопку **Начало предварительного просмотра**. Развертываются только файлы, которые необходимо обновить.
5. Запустите веб-приложение в Visual Studio или на портале. **Вы не сможете опубликовать веб-приложение, если оно остановлено**.

	![запуск веб-приложения](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. Вернитесь в Visual Studio и щелкните **Опубликовать**.
3. Приложение Azure открывается в браузере по умолчанию. Если вы вошли в систему, выйдите, чтобы просмотреть главную страницу как анонимный пользователь.  
4. Щелкните ссылку **О программе**. Вы перейдете на страницу входа.
5. Щелкните ссылку **Регистрация** на странице входа и создайте локальную учетную запись. С помощью этой локальной учетной записи мы убедимся, что вы имеете доступ к страницам, предназначенным только для чтения, но не имеете доступа к страницам, где изменяются данные (которые защищены ролью *canEdit*). Далее в этом учебнике мы удалим доступ локальной учетной записи. 

	![Зарегистрировать](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. Проверьте, можете ли вы перейти на страницы *О программе* и *Связь*.

	![Выход из системы](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. Щелкните ссылку **CM Demo** для перехода к контроллеру **Cm**. Можно также добавить *Cm* в URL-адрес.

	![Страница CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. Щелкните ссылку "Изменить". Вы перейдете на страницу входа. В разделе **Использовать другую службу для входа** щелкните Google или Facebook и войдите с использованием ранее зарегистрированной учетной записи. (Если вы работаете быстро и файл cookie сеанса еще действует, вы автоматически войдете с ранее использованной учетной записью Google или Facebook.)
2. Убедитесь, что можете изменять данные при входе с этой учетной записью. **Примечание.** С помощью этого приложения вы не можете выйти из Google и войти в другую учетную запись Google в том же браузере. Если используется один браузер, необходимо перейти в Google и выйти из системы. Используя другой браузер, вы можете войти с другой учетной записью из того же стороннего приложения проверки подлинности (например, Google).

Если вы не указали имя и фамилию в информации учетной записи Google, возникнет исключение NullReferenceException.


## Проверка базы данных SQL Azure ##

1. В **Обозревателе сервера** перейдите к элементу **ContactDB**.
2. Щелкните правой кнопкой мыши **ContactDB** и выберите **Открыть в обозревателе объектов SQL Server**.
 
	![Открытие в SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
3. Если вы не подключились к этой базе данных ранее, возможно, система попросит добавить правило брандмауэра, чтобы разрешить доступ для текущего IP-адреса. IP-адрес будет указан системой. Просто щелкните **Добавить правило брандмауэра** для разрешения доступа.

  ![добавление правила брандмауэра](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/addfirewallrule.png)
        
  Затем войдите в базу данных с именем пользователя и паролем, указанными при создании базы данных.
 
1. Щелкните правой кнопкой мыши таблицу **AspNetUsers** и выберите **Просмотр данных**.

	![Страница CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. Запишите или запомните идентификатор учетной записи Google, с которой вы зарегистрировались, чтобы попасть в роль **canEdit**, а также идентификатор пользователя *user1@contoso.com*. Только эти пользователи должны входить в роль **canEdit**. (Вы проверите это на следующем шаге.)

	![Страница CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/s2.png)
 
2. В **обозревателе объектов SQL Server** щелкните правой кнопкой мыши элемент **AspNetUserRoles** и выберите **Просмотр данных**.

	![Страница CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
Убедитесь, что идентификаторы **UserId** принадлежат пользователю *user1@contoso.com* и используемой для регистрации учетной записи Google. 


## Дальнейшие действия

Выполните задания из учебников на основе данного образца:

1.	[Создание безопасного веб-приложения ASP.NET MVC 5 со входом, подтверждением электронной почты и сбросом пароля](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[Приложение ASP.NET MVC 5 с двухфакторной проверкой подлинности с использованием SMS и электронной почты](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Рекомендации по развертыванию паролей и других конфиденциальных данных в ASP.NET и Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
4.	Инструкции по добавлению данных профиля в базу данных регистрации пользователей и подробные инструкции по использованию Facebook в качестве поставщика проверки подлинности приведены в учебнике [Создание приложения ASP.NET MVC 5 с использованием Facebook и Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).
5.	[Приступая к работе с ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Чтобы включить кнопки входа с помощью социальных сетей, расположенные в верхней части этого учебника, изучите раздел [Удобные кнопки входа социальных сетей для ASP.NET MVC 5](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/).

В отличном учебнике Тома Дейкстры [Начало работы с EF и MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) показаны более сложные методы программирования MVC и EF.

Этот учебник и пример приложения были написаны [Риком Андерсоном (Rick Anderson)](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) в соавторстве с Томом Дейкстрой (Tom Dykstra) и Бэрри Доррансом (Barry Dorrans)(Twitter [@blowdart](https://twitter.com/blowdart)).

***Пожалуйста, оставьте отзыв*** о том, что вам понравилось или что вы хотели бы улучшить, не только о самом учебнике, но и о продуктах, которые он демонстрирует. Ваши отзывы помогут нам определить, какие улучшения наиболее приоритетные. Вы также можете запросить и проголосовать за новые темы на странице [Покажите, как это сделать в коде](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Руководство по смене старого портала на новый портал см. в разделе [Справочник по навигации на предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure web apps]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png

<!--HONumber=54-->