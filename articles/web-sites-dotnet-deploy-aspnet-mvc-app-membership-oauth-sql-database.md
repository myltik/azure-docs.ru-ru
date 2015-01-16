<properties urlDisplayName="Website with SQL Database" pageTitle="Развертывание безопасного приложения ASP.NET MVC с членством, OAuth и базой данных SQL на веб-сайте Azure" metaKeywords ="учебник по созданию приложения hello world для Azure, учебник по началу работы с Azure, учебник по базам данных SQL, учебник по созданию приложения hello world для Azure с использованием .NET, учебник по созданию приложения hello world для Azure на C#, учебник по программированию SQL Azure на C#" description="Узнайте, как разработать веб-сайт ASP.NET MVC 5 с развертыванием серверной части Базы данных SQL в Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website" authors="riande"  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="riande" /> 



#  Развертывание безопасного приложения ASP.NET MVC 5 с членством, OAuth и базой данных SQL на веб-сайте Azure.

***Обновлено 12 октября 2014 г.***

В этом учебнике показано, как создать безопасное веб-приложение ASP.NET MVC 5, которое позволяет пользователям входить в систему с учетными данными из Google или Facebook. Вы также узнаете, как развернуть приложение в Azure.

Можно бесплатно создать учетную запись Azure, а если у вас еще нет Visual Studio 2013, то пакет SDK автоматически установит Visual Studio 2013 для Web Express. Вы можете приступить к разработке приложений для Azure совершенно бесплатно.

В данном учебнике предполагается, что у читателя нет опыта использования платформы Azure. По завершении изучения этого учебника вы создадите безопасное приложение на основе данных, запускаемое в облаке и использующее облачную базу данных.

Вы узнаете следующее:

* Создание безопасного проекта ASP.NET MVC 5 и его публикация на веб-сайте Azure.
* Использование [OAuth](http://oauth.net/ "http://oauth.net/") и базы данных членства ASP.NET для защиты приложения.
* Как использовать базу данных SQL для хранения данных в Azure.

Вы научитесь создавать простые веб-приложения для списка контактов, основанные на платформе ASP.NET MVC 5 и использующие ADO.NET Entity Framework для доступа к базе данных. На следующем рисунке показана страница входа в готовое приложение:

![login page][rxb]

>[WACOM.NOTE] Для работы с этим учебником необходимо использовать учетную запись Microsoft Azure. Если у вас нет учетной записи, можно <a href="/ru-ru/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">активировать преимущества для подписчиков MSDN</a> или <a href="/ru-ru/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">подписаться на бесплатную пробную версию</a>


В этом учебнике рассматриваются следующие темы:

- [Настройка среды разработки](#setupdevenv)
- [Создание приложения ASP.NET MVC 5][createapplication]
- [Развертывание приложения в Azure][deployapp1]
- - [Добавление базы данных в приложение][adddb]
- [Добавление поставщика OAuth][]
- [Развертывание приложения в Azure][deployapp11]
- [Дальнейшие действия][]


[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Чтобы использовать новый сертификат SSL для localhost, необходимо установить [Visual Studio 2013 с обновлением 3](http://go.microsoft.com/fwlink/?LinkId=390521) или более позднюю версию.

<h2><a name="bkmk_createmvc4app"></a>Создание приложения ASP.NET MVC 5</h2>

### Создание проекта

1. В меню **Файл** выберите **Новый проект**.

	![New Project in File menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

1. В диалоговом окне **Новый проект** разверните узел **C#** и выберите **Веб** в разделе **Установленные шаблоны**, а затем выберите **Веб-приложение ASP.NET**.

1. Присвойте приложению имя **ContactManager** и нажмите кнопку **ОК**.

	![New Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
	**Примечание.** Необходимо ввести именно ContactManager. В блоках кода, которые вы будете копировать позже, предполагается, что проект имеет имя ContactManager. 

1. В диалоговом окне **Новый проект ASP.NET** выберите шаблон **MVC**. Убедитесь, что **Проверка подлинности** настроена на **Учетные записи индивидуальных пользователей*, при этом установлен флажок **Разместить в облаке** и выбран **Веб-сайт**.

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

1. Мастер настройки предложит уникальное имя на основе *ContactManager* (см. рисунок ниже). Выберите ближайшую область. Поиск центра обработки данных с наименьшим временем задержки можно выполнить с помощью веб-сайта [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com"). 
2. Если вы еще не создали сервер базы данных, нажмите **Создать новый сервер**, а затем укажите имя пользователя и пароль для базы данных.

	![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configAz.PNG)

Если у вас есть сервер базы данных, используйте его для создания новой базы данных. Серверы баз данных являются ценным ресурсом, и обычно удобнее создать сразу несколько баз данных на одном сервере для тестирования и разработки, чем создавать по одному серверу на каждую базу данных. Убедитесь, что для веб-сайта и базы данных указан один и тот же регион.

![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configWithDB.PNG)

### Задание верхнего и нижнего колонтитула страницы


1. В **обозревателе решений** откройте файл *Layout.cshtml* в папке *Views\Shared*.

	![_Layout.cshtml in Solution Explorer][newapp004]

1. Замените разметку файла *Layout.cshtml* на код, приведенный ниже. Изменения выделены ниже.

<pre>
			&lt;!DOCTYPE html&gt;
			&lt;html&gt;
			&lt;head&gt;
			    &lt;meta charset="utf-8" /&gt;
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
			    &lt;title&gt;@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title&gt;
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head&gt;
			&lt;body&gt;
			    &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt;
			        &lt;div class="container"&gt;
			            &lt;div class="navbar-header"&gt;
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                &lt;/button&gt;
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div&gt;
			            &lt;div class="navbar-collapse collapse"&gt;
			                &lt;ul class="nav navbar-nav"&gt;
			                    &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt;
			                &lt;/ul&gt;
			                @Html.Partial("_LoginPartial")
			            &lt;/div&gt;
			        &lt;/div&gt;
			    &lt;/div&gt;
			    &lt;div class="container body-content"&gt;
			        @RenderBody()
			        &lt;hr /&gt;
			        &lt;footer&gt;
			            &lt;p&gt;&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p&gt;
			        &lt;/footer&gt;
			    &lt;/div&gt;
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required: false)
			&lt;/body&gt;
			&lt;/html&gt;
</pre>


![code changes](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### Локальный запуск приложения

1. Для запуска приложения нажмите сочетание клавиш CTRL+F5.

	Главная страница приложения откроется в браузере, используемом по умолчанию.

	![Web site running locally](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Это все, что требуется для создания приложения, которое будет развернуто в Azure. 

## Включение SSL для проекта ##

1. Включите SSL. В обозревателе решений щелкните проект **ContactManager**, а затем нажмите F4, чтобы открыть диалоговое окно свойств. Для параметра **SSL включен** измените значение на "true". Скопируйте **URL-адрес SSL**. Если вы ранее не создавали веб-сайты SSL, URL-адресом SSL будет https://localhost:44300/.

	![enable SSL][rxSSL]
 
1. В обозревателе решений щелкните правой кнопкой мыши проект **Contact Manager** и выберите **Свойства**.
1. На левой вкладке щелкните **Интернет**.
1. Измените **URL-адрес проекта** на **URL-адрес SSL** и сохраните страницу (CTRL+S).

	![enable SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. Убедитесь, что обозреватель Visual Studio открывает браузер Internet Explorer, как показано на рисунке ниже:

	![default browser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	Средство выбора браузера позволяет указать используемый Visual Studio браузер.

 	![browser selector](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

	Вы можете выбрать несколько браузеров и обновлять их при внесении изменений. Дополнительные сведения см. в разделе [Использование ссылки на браузер в Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).


1. Нажмите сочетание клавиш CTRL+F5 для запуска приложения. Следуйте инструкциям, чтобы подтвердить доверие к самозаверяющему сертификату, созданному IIS Express.

	 ![instructions to trust the self-signed certificate that IIS Express has generated](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. Прочитайте текст в диалоговом окне **Предупреждение системы безопасности** и нажмите кнопку **Да**, чтобы установить сертификат, представляющий **localhost**.

 	![localhost IIS Express certificate warning ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. В IE откроется *Главная* страница без предупреждений SSL.

	 ![IE with localhost SSL and no warnings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	Google Chrome также принимает сертификат и отображает HTTPS-содержимое без предупреждения. Firefox использует собственное хранилище сертификатов, поэтому отображается предупреждение.

	 ![FireFox Cert Warning](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

<h2><a name="bkmk_deploytowindowsazure1"></a>Развертывание приложения в Azure</h2>

1. В Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и из контекстного меню выберите **Опубликовать**.

	![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   Откроется мастер **Публикация веб-сайта**.

1. В диалоговом окне **Публикация веб-сайта** щелкните **Опубликовать**.

	![Publish](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	Созданное вами приложение теперь выполняется в облаке. При следующем развертывании приложения будут развернуты только измененные (или новые) файлы.

	![Running in Cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>Добавление базы данных в приложение</h2>

Теперь вы научитесь обновлять приложения MVC, чтобы добавлять возможности для отображения и обновления контактов и сохранения данных в базе данных. Приложение будет использовать Entity Framework (EF) для создания базы данных, а также для считывания и обновления данных.

### Добавление классов модели данных для контактов

Начните с создания простой модели данных в коде.

1. В **обозревателе решений** щелкните правой кнопкой мыши папку Models, выберите **Добавить**, а затем щелкните **Класс**.

	![Add Class in Models folder context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. В диалоговом окне **Добавление нового элемента** присвойте новому файлу класса имя *Contact.cs*, а затем нажмите кнопку **Добавить**.

	![Add New Item dialog box][adddb002]

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
Класс **Contacts** определяет данные, которые будут храниться для каждого контакта, а также первичный ключ *ContactID*, необходимый для базы данных.

### Создание веб-страниц, позволяющих пользователям приложений работать с контактами

Компонент формирования шаблонов в ASP.NET MVC может автоматически создавать код, выполняющий операции создания, чтения, обновления и удаления.

<h2><a name="bkmk_addcontroller"></a>Добавление контроллера и представления для данных</h2>

1. Постройте проект **(Ctrl+Shift+B)**. (проект необходимо создать перед использованием механизма формирования шаблонов). 
1. В **обозревателе решений** щелкните правой кнопкой мыши папку Controllers и выберите **Добавить**, а затем щелкните **Контроллер**.

	![Add Controller in Controllers folder context menu][addcode001]

5. В диалоговом окне **Добавление Scaffold** выберите **Контроллер MVC 5 с представлениями, использующий EF**, и щелкните **Добавить**.
	
	![Add Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. В раскрывающемся списке **Класс модели** выберите **Contact (ContactManager.Models)**. (См. изображение ниже.)
1. В поле **Класс контекста данных** выберите **ApplicationDbContext (ContactManager.Models)**. **ApplicationDbContext** будет использоваться и для базы данных членства, и для наших контактных данных.
1. В текстовом поле **Имя контроллера** введите "CmController" в качестве имени контроллера. 

	![New data ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. Щелкните **Добавить**.

   Visual Studio создает методы и представления контроллера для операций создания, чтения, обновления и удаления в базе данных для объектов **Contact**.

## Включите миграцию, создайте базу данных, добавьте данные и инициализатор данных ##

Следующая задача заключается в активации компонента [Code First Migrations](http://msdn.microsoft.com/library/hh770484.aspx) для создания базы данных на основе созданной модели данных.

1. В меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем **Консоль диспетчера пакетов**.
	![Package Manager Console in Tools menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. В окне **Консоль диспетчера пакетов** введите следующую команду:

		enable-migrations 
 Команда **enable-migrations** создает папку *Migrations* и размещает в ней файл *Configuration.cs*, который можно использовать для заполнения базы данных и настройки миграций. 

2. В окне **Консоль диспетчера пакетов** введите следующую команду:

		add-migration Initial


Команда **add-migration Initial** создает файл с именем **&lt;метка_времени&gt;Initial** в папке *Migrations*, который в свою очередь создает базу данных. Первый параметр (**Initial**) является произвольным и используется для создания имени файла. Новые файлы классов можно просмотреть в **Обозревателе решений**.
	В классе **Initial** метод **Up** создает таблицу Contacts, а метод **Down** (используется при возвращении к предыдущему состоянию) удаляет эту таблицу.
3. Откройте файл *Migrations\Configuration.cs*. 
4. Добавьте следующее пространство имен. 

    	 использование ContactManager.Models;



5. Замените метод *Seed* следующим кодом.

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

Этот код инициализирует базу данных с контактной информацией. Дополнительные сведения об инициализации базы данных см. в разделе [Заполнение и отладка баз данных Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. В окне **Консоль диспетчера пакетов** введите команду:

		update-database

	![Package Manager Console commands][addcode009]

Команда **update-database** выполняет первую миграцию, при которой создается база данных. По умолчанию база данных создается как локальная база данных SQL Server Express LocalDB. 

7. Нажмите CTRL+F5 для запуска приложения, а затем щелкните ссылку **CM Demo** или перейдите по адресу http://localhost:(port#)/Cm.

Приложение показывает начальные данные и предоставляет ссылки для изменения, удаления и просмотра подробных сведений. С его помощью можно создавать, изменять, удалять и просматривать данные.

	![MVC view of data][rx2]



<h2><a name="addOauth"></a>Добавление поставщика OAuth2</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") - это открытый протокол, обеспечивающий безопасную авторизацию простым и стандартным методом из веб-приложений, мобильных и настольных приложений. В интернет-шаблоне ASP.NET MVC используется OAuth для предоставления Facebook, Twitter, Google и Microsoft в качестве поставщиков проверки подлинности. В этом учебнике в качестве поставщика проверки подлинности используется только Google, но вы легко можете изменить код, чтобы использовать любой другой поставщик. Шаги по реализации других поставщиков очень похожи на шаги, представленные в этом учебнике. Для использования Facebook в качестве поставщика проверки подлинности см. учебник [Приложение MVC 5 со входом через Facebook, Twitter, LinkedIn и Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

Кроме проверки подлинности в учебнике для авторизации будут использоваться роли. Только пользователи, добавленные к роли *canEdit*, смогут изменять данные (то есть создавать, редактировать и удалять контакты).

Выполните инструкции из учебника [Приложение MVC 5 со входом через Facebook, Twitter, LinkedIn и Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog), раздел **Создание приложения Google для OAuth 2, чтобы настроить приложение Google для OAuth2**. Запустите и протестируйте приложение, чтобы проверить, можете ли вы выполнять вход с использованием проверки подлинности Google.

<h2><a name="mbrDB"></a>Использование API членства</h2>
В этом разделе в базу данных членства будут добавлены локальный пользователь и роль *canEdit*. Только пользователи, входящие в роль *canEdit*, смогут изменять данные. Рекомендуется именовать роли по выполняемым ими действиям, например имя *canEdit* предпочтительнее имени *admin*. По мере развития приложения можно добавлять новые роли, например роль *canDeleteMembers*, имя которой предпочтительнее, чем менее описательное имя *superAdmin*.

1. Откройте файл *migrations\configuration.cs* и добавьте следующие инструкции using:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Добавьте в класс следующий метод **AddUserAndRole**:

    
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

1. Вызовите новый метод из метода **Seed**:
	<pre>
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
            context.Contacts.AddOrUpdate(p => p.Name,
                // Код удален для краткости
        }
	</pre>  
<span></span>
	The following images shows the changes to *Seed* method:

	![code image](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   This code creates a new role called *canEdit*, creates a new local user *user1@contoso.com*, and adds *user1@contoso.com* to the *canEdit* role. For more information, see the [ASP.NET Identity resource page](http://curah.microsoft.com/55636/aspnet-identity).

## Use Temporary Code to Add New Social Login Users to the canEdit Role  ##
In this section you will temporarily modify the **ExternalLoginConfirmation** method in the Account controller to add new users registering with an OAuth provider to the *canEdit* role. We will temporarily modify the **ExternalLoginConfirmation** method to automatically add new users to an administrative role. Until we provide a tool to add and manage roles, we'll use the temporary automatic registration code below. We hope to provide a tool similar to [WSAT](http://msdn.microsoft.com/ru-ru/library/ms228053.aspx) in the future which allow you to create and edit user accounts and roles. Later in the tutorial I'll show how you can use **Server Explorer** to add users to roles.  

1. Open the **Controllers\AccountController.cs** file and navigate to the **ExternalLoginConfirmation** method.
1. Add the following call to **AddToRoleAsync** just before the **SignInAsync** call.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   The code above adds the newly registered user to the "canEdit" role, which gives them access to action methods that change (edit) data. An image of the code change is shown below:

   ![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Later in the tutorial you will deploy the application to Azure, where you will log-on with Google or another third party authentication provider. This will add your newly registered account to the *canEdit* role. Anyone who finds your site's URL and has a Google ID can then register and update your database. To prevent other people from doing that, you can stop the site. You'll be able to verify who is in the *canEdit* role by examining the database.

In the **Package Manager Console** hit the up arrow key to bring up the following command:

		Update-Database

Run the  **Update-Database** command which will run the **Seed** method, and that will run the **AddUserAndRole** you just added. The **AddUserAndRole** will create the user *user1@contoso.com* and add her to the *canEdit* role.

## Protect the Application with SSL and the Authorize Attribute ##

In this section you will apply the [Authorize](http://msdn.microsoft.com/ru-ru/library/system.web.mvc.authorizeattribute.aspx) attribute to restrict access to the action methods. Anonymous users will be able to view the **Index** action method of the home controller only. Registered users will be able to see contact data (The **Index** and **Details** pages of the Cm controller), the About, and the Contact pages. Only users in the *canEdit* role will be able to access action methods that change data.

1. Add the [Authorize](http://msdn.microsoft.com/ru-ru/library/system.web.mvc.authorizeattribute.aspx) filter and the [RequireHttps](http://msdn.microsoft.com/ru-ru/library/system.web.mvc.requirehttpsattribute.aspx) filter to the application. An alternative approach is to add the [Authorize](http://msdn.microsoft.com/ru-ru/library/system.web.mvc.authorizeattribute.aspx) attribute and the [RequireHttps](http://msdn.microsoft.com/ru-ru/library/system.web.mvc.requirehttpsattribute.aspx) attribute to each controller, but it's considered a security best practice to apply them to the entire application. By adding them globally, every new controller and action method you add will automatically be protected, you won't need to remember to apply them. For more information see [Securing your ASP.NET MVC  App and the new AllowAnonymous Attribute](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Open the *App_Start\FilterConfig.cs* file and replace the *RegisterGlobalFilters* method with the following (which adds the two filters):
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>


	The following image shows the changed code:


	The [Authorize](http://msdn.microsoft.com/ru-ru/library/system.web.mvc.authorizeattribute.aspx) filter applied in the code above will prevent anonymous users from accessing any methods in the application. You will use the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to opt out of the authorization requirement in a couple methods, so anonymous users can log in and can view the home page. The  [RequireHttps](http://msdn.microsoft.com/ru-ru/library/system.web.mvc.requirehttpsattribute.aspx) will require all access to the web app be through HTTPS.

1. Add the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to the **Index** method of the Home controller. The [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute enables you to white-list the methods you want to opt out of authorization. An image of a portion of the HomeController is shown below:	

  	![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. Do a global search for *AllowAnonymous*, you can see it is used in the log in and registration methods of the Account controller.
1. In *CmController.cs*, add `[Authorize(Roles = "canEdit")]` to the HttpGet and HttpPost methods that change data (Create, Edit, Delete, every action method except Index and Details) in the *Cm* controller. A portion of the completed code is shown below: 

   	![img of code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)



1. If you are still logged in from a previous session, hit the **Log out** link.
1. Click on the **About** or **Contact** links. You will be redirected to the log in page because anonymous users cannot view those pages. 
1. Click the **Register as a new user** link and add a local user with email *joe@contoso.com*. Verify *Joe* can view the Home, About and Contact pages. 

	![login](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Click the *CM Demo* link and verify you see the data. 
1. Click an edit link on the page, you will be redirected to the log in page (because a new local user is not added to the *canEdit* role).
1. Log in as *user1@contoso.com* with password of "P_assw0rd1" (the "0" in "word" is a zero). You will be redirected to the edit page you previously selected. <br/>
   If you can't log in with that account and password, try copying the password from the source code and pasting it. If you still can't log in, check the **UserName** column of the **AspNetUsers** table to verify *user1@contoso.com* was added. 

1. Verify you can make data changes.

<h2><a name="bkmk_deploytowindowsazure11"></a>Развертывание приложения в Azure</h2>

1. В Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и из контекстного меню выберите **Опубликовать**.

	![Publish in project context menu][firsdeploy003]

	Откроется мастер **Публикация веб-сайта**.

1. Откройте вкладку **Параметры** в левой части диалогового окна **Публикация веб-сайта**. Щелкните значок **v**, чтобы выбрать параметр **Строка удаленного подключения** для **ApplicationDbContext**, затем выберите **ContactManagerNN_db**.

   
	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. В разделе **ContactManagerContext** выберите **Выполнить Code First Migrations**.

	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. Щелкните **Опубликовать**.
1. Войдите в систему под именем * user1@contoso.com * (пароль: P_assw0rd1) и проверьте, можете ли вы изменять данные.
1. Выйдите из системы.
1. Перейдите в [консоль разработчиков Google](https://console.developers.google.com/) и на вкладке **Учетные данные** обновите URI перенаправления и JavaScript Orgins для использования URL-адреса Azure.
1. Войдите с помощью Google или Facebook. При этом учетная запись Google или Facebook добавится к роли **canEdit**. Если появляется сообщение об ошибке HTTP 400 *URI перенаправления в запросе: https://contactmanager{my version}.azurewebsites.net/signin-google did not match a registered redirect URI.*, you'll have to wait until the changes you made are propagated. If you get this error after more than a minute, verify the URIs are correct.

### Остановка веб-сайта для предотвращения регистрации других пользователей  

1. В **обозревателе серверов** перейдите к элементу **Веб-сайты**.
4. Щелкните правой кнопкой мыши каждый экземпляр веб-сайта и выберите **Остановить веб-сайт**. 

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

	Вы можете также выбрать веб-сайт на портале управления Azure, а затем щелкнуть значок **остановить** в нижней части страницы.

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### Удаление AddToRoleAsync, публикация и тестирование

1. Закомментируйте или удалите следующий код из метода **ExternalLoginConfirmation** контроллера Account: 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Создайте проект (который сохраняет изменения файла и проверяет, нет ли ошибок компиляции).
5. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите команду **Опубликовать**.

	   ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. Нажмите кнопку **Начало предварительного просмотра**. Развертываются только те файлы, которые необходимо обновить.
5. Запустите веб-сайт в Visual Studio или на портале. **Когда веб-сайт остановлен, публикация невозможна**.

	![start web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. Вернитесь в Visual Studio и нажмите кнопку **Опубликовать**.
3. Приложение Azure открывается в браузере по умолчанию. Если вы вошли в систему, выйдите, чтобы просмотреть главную страницу как анонимный пользователь.  
4. Щелкните ссылку **О программе**. Вы перейдете на страницу входа.
5. Щелкните ссылку **Регистрация** на странице входа и создайте локальную учетную запись. С помощью этой локальной учетной записи мы убедимся, что вы имеете доступ к страницам, предназначенным только для чтения, но не имеете доступа к страницам, где изменяются данные (которые защищены ролью *canEdit*). Далее в этом учебнике мы удалим доступ локальной учетной записи. 

	![Register](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. Убедитесь, что вы можете перейти на страницы *Сведения* и *Контакт*.

	![Log off](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. Щелкните ссылку **CM Demo** для перехода к контроллеру **Cm**. Можно также добавить запись *Cm* в URL-адрес. 

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. Щелкните ссылку "Изменить". Вы перейдете на страницу входа. В разделе **Использовать другую службу для входа** щелкните Google или Facebook и войдите с использованием ранее зарегистрированной учетной записи. (Если вы работаете быстро и файл cookie сеанса еще действует, вы автоматически войдете с ранее использованной учетной записью Google или Facebook.)
2. Убедитесь, что можете изменять данные при входе с этой учетной записью.
 	**Примечание.** С помощью этого приложения вы не можете выйти из Google и войти в другую учетную запись Google в том же браузере. Если используется один браузер, необходимо перейти в Google и выйти из системы. Используя другой браузер, вы можете войти с другой учетной записью из того же стороннего приложения проверки подлинности (например, Google).

Если вы не указали имя и фамилию в информации учетной записи Google, возникнет исключение NullReferenceException.


## Проверка базы данных SQL Azure ##

1. В **обозревателе сервера** перейдите к элементу **ContactDB**.
2. Щелкните правой кнопкой мыши **ContactDB** и выберите **Открыть в обозревателе объектов SQL Server**.
 
	![open in SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**Примечание.** Если не удается развернуть **Базы данных SQL** и *не удается* просмотреть **ContactDB** в Visual Studio, необходимо выполнить приведенные ниже инструкции, чтобы открыть порт или диапазон портов. Следуйте инструкциям в разделе **Настройка правил брандмауэра Azure**. Возможно, после добавления правила брандмауэра потребуется подождать несколько минут, чтобы получить доступ к базе данных.
 
1. Щелкните правой кнопкой мыши таблицу **AspNetUsers** и выберите **Просмотр данных**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. Запишите или запомните идентификатор учетной записи Google, с которой вы зарегистрировались, чтобы попасть в роль **canEdit**, а также идентификатор пользователя *user1@contoso.com*. Только эти пользователи должны входить в роль **canEdit**. (Вы проверите это на следующем шаге.)

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. В **обозревателе объектов SQL Server** щелкните правой кнопкой мыши элемент **AspNetUserRoles** и выберите **Просмотр данных**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
Убедитесь, что идентификаторы **UserId** взяты из *user1@contoso.com* и зарегистрированной вами учетной записи Google. 


## Настройка правил брандмауэра Azure ##

Если вам не удается подключиться к SQL Azure из Visual Studio или открывается диалоговое окно с ошибкой "Не удается открыть сервер", выполните действия, описанные в этом разделе.

![firewall error](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

Потребуется добавить ваш IP-адрес к разрешенным IP-адресам.

1. На портале Azure выберите **Базы данных SQL** на левой вкладке.

	![Select SQL][rx6]

1. Щелкните **ContactDB**.

1. Щелкните ссылку **Настроить правила брандмауэра Azure для этого IP-адреса**.

	![firewall rules][rx7]

1. При появлении запроса "Текущий IP-адрес xxx.xxx.xxx.xxx не включен в существующие правила брандмауэра. Обновить правила?" щелкните **Да**. Добавления этого адреса за некоторыми корпоративными брандмауэрами часто бывает недостаточно, поэтому вам потребуется добавить диапазон IP-адресов.

Следующим шагом является добавление диапазона разрешенных IP-адресов.

1. На портале Azure щелкните **Базы данных SQL**.
1. Перейдите на вкладку **Серверы** и выберите сервер, который требуется настроить.

	![Servers tab in Azure ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

1. Выберите вкладку **Настройка**.

1. Добавьте имя правила, начальный и конечный IP-адреса.

	![ip range][rx9]

1. В нижней части страницы щелкните **Сохранить**.
1. Пожалуйста, оставьте отзыв и сообщите мне, если вам требуется добавить диапазон IP-адресов для подключения.

Наконец, можно подключиться к экземпляру базы данных SQL из SSOX.

1. В меню "Вид" выберите **Обозреватель объектов SQL Server**.
1. Щелкните правой кнопкой мыши **SQL Server** и выберите **Добавить SQL Server**.
1. В диалоговом окне **Подключиться к серверу** задайте для параметра **Проверка подлинности** значение **Проверка подлинности SQL Server**. Значения **Имя сервера** и **Вход** вы получите на портале Azure.
1. В браузере перейдите на портал и выберите **Базы данных SQL**.
1. Выберите **ContactDB**, затем щелкните **Просмотреть строки подключения базы данных SQL**.
1. На странице **Строки подключения** скопируйте значения **Сервер** и **Идентификатор пользователя**.
 
	![con string](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)
1. Вставьте значения **Сервер** и **Идентификатор пользователя** в диалоговое окно **Подключение к серверу** в Visual Studio. Значение **ИД пользователя** переходит в поле **Вход**. Введите пароль, который использовался для создания базы данных SQL.

	![Connect to Server DLG](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

Теперь можно перейти к базе данных Contact DB, следуя приведенным ранее инструкциям.


## Добавление пользователя к роли canEdit путем изменения таблиц базы данных

Ранее в этом учебнике вы использовали код для добавления пользователей к роли canEdit. Альтернативой является непосредственное изменение данных в таблицах членства. В следующих шагах показано, как использовать этот альтернативный метод для добавления пользователя к роли.

2. В **обозревателе объектов SQL Server** щелкните правой кнопкой мыши элемент **AspNetUserRoles** и выберите **Просмотр данных**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. Скопируйте идентификатор *RoleId* и вставьте его в пустую (новую) строку.
	
	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. В таблице **AspNetUsers** найдите пользователя, которого нужно добавить к роли, затем скопируйте *Id* пользователя и вставьте его в столбец **UserId** таблицы **AspNetUserRoles**.

Мы работаем над средством, которое позволит значительно упростить управление пользователями и ролями.


<h2><a name="nextsteps"></a>Дальнейшие действия</h2>

Выполните задания из учебников на основе данного образца:

1.	[Создание безопасного веб-приложения ASP.NET MVC 5 со входом, подтверждением электронной почты и сбросом пароля](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[Приложение ASP.NET MVC 5 с двухфакторной проверкой подлинности с использованием SMS и электронной почты](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Рекомендации по развертыванию паролей и других конфиденциальных данных в ASP.NET и Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
Инструкции по добавлению данных профиля в базу данных регистрации пользователей и подробные инструкции по использованию Facebook в качестве поставщика проверки подлинности приведены в учебнике 4.	[Создание приложения ASP.NET MVC 5 с использованием Facebook и Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on ).
5.	[Приступая к работе с ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Чтобы включить кнопки входа с помощью социальных сетей, показанные в верхней части этого учебника, см. раздел [Удобные кнопки входа социальных сетей для ASP.NET MVC 5](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/).

В отличном учебнике Тома Дейкстры (Tom Dykstra) [Начало работы с EF и MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) показаны более сложные методы программирования MVC и EF.

Этот учебник и пример приложения были написаны [Риком Андерсоном (Rick Anderson)](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) в соавторстве с Томом Дейкстрой (Tom Dykstra) и Бэрри Доррансом (Barry Dorrans) (Twitter [@blowdart](https://twitter.com/blowdart)). 

***Пожалуйста, оставьте отзыв*** о том, что вам понравилось или что вы хотели бы улучшить не только в самом учебнике, но и в продуктах, которые он демонстрирует. Ваши отзывы помогут нам определить, какие улучшения наиболее приоритетные. Вы также можете запросить и проголосовать за новые темы на странице [Покажите, как это сделать в коде](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

<!-- bookmarks -->
[Добавление поставщика OAuth]: #addOauth
[Использование API членства]:#mbrDB
[Создание скрипта развертывания данных]:#ppd
[Обновление базы данных членства]:#ppd2

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


[Важные сведения об ASP.NET на веб-сайтах Azure]: #aspnetwindowsazureinfo
[Дальнейшие действия]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
























