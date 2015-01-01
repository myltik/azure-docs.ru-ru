<properties urlDisplayName="REST service using Web API" pageTitle="Служба .NET REST с использованием веб-API - учебник Azure" metaKeywords="Azure tutorial web site, ASP.NET API web site, Azure VS" description="A tutorial that teaches you how to deploy an app that uses the ASP.NET Web API to an Azure website by using Visual Studio." metaCanonical="" services="web-sites" documentationCenter=".NET" title="REST service using ASP.NET Web API and SQL Database" authors="riande" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/06/2014" ms.author="riande" />




# Служба REST с использованием веб-API ASP.NET и базы данных SQL 

**Авторы:[Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT) и Том Дайкстра (Tom Dykstra).**

В этом учебнике показано, как развернуть веб-приложение ASP.NET на веб-сайте Azure с помощью мастера публикации веб-сайта в Visual Studio 2013 или Visual Studio 2013 for Web Express. 

Можно бесплатно создать учетную запись Azure, а если у вас еще нет Visual Studio 2013, пакет SDK автоматически установит Visual Studio 2013 for Web Express. Благодаря этому вы можете приступить к разработке приложений для Azure совершенно бесплатно.

В данном учебнике предполагается, что у читателя нет опыта использования платформы Azure. По завершении изучения этого учебника вы получите простое веб-приложение, работающее в облаке.
 
Вы узнаете:

* Как подготовить компьютер к разработке для Azure путем установки пакета Azure SDK.
* Как создать проект Visual Studio ASP.NET MVC 5 и опубликовать его на веб-сайте Azure.
* Как использовать веб-API ASP.NET для включения вызовов API Restful.
* Как использовать базу данных SQL для хранения данных в Azure.
* Как публиковать обновления приложений в Azure.

Вы создадите простое веб-приложение для списка контактов, основанное на платформе ASP.NET MVC 5 и использующее ADO.NET Entity Framework для доступа к базе данных. На следующем рисунке показано завершенное приложение:

![screenshot of web site][intro001]
В этом учебнике рассматриваются следующие темы:

* [Настройка среды разработки][setupdbenv]
* [Настройка среды Azure][setupwindowsazureenv]
* [Создание приложения ASP.NET MVC 5][createapplication]
* [Развертывание приложения в Azure][deployapp1]
* [Добавление базы данных в приложение][adddb]
* [Добавление контроллера и представления для данных][addcontroller]
* [Добавление веб-интерфейса API Restful][addwebapi]
* [Добавление защиты XSRF][]
* [Публикация обновления приложения в Azure и базе данных SQL][deploy2]

<a name="bkmk_setupdevenv"></a>
<!-- the next line produces the "Set up the development environment" section as see at http://www.windowsazure.com/ru-ru/documentation/articles/web-sites-dotnet-get-started/ -->
[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2><a name="bkmk_setupwindowsazure"></a>Настройка среды Azure</h2>

Затем настройте среду Azure, создав веб-сайт Azure и базу данных SQL.

### Создание веб-сайта и базы данных SQL в Azure

Следующим шагом будет создание веб-сайта Azure и базы данных SQL, используемых вашим приложением.

Ваш веб-сайт Azure будет работать в общей среде размещения, а это означает, что он работает на виртуальных машинах, используемых совместно с другими клиентами Azure. Общая среда размещения - недорогой способ начать работу в облаке. Если позднее ваш веб-трафик увеличится, приложение можно масштабировать по необходимости, запуская его на выделенных виртуальных машинах. Если требуется более сложная архитектура, можно перейти в облачную службу Azure. Облачные службы работают на выделенных виртуальных машинах, которые можно настроить в соответствии с конкретными потребностями.

База данных SQL - это облачная служба реляционной базы данных, построенная на основе технологий SQL Server. Средства и приложения, работающие с SQL Server, работают и с базой данных SQL.

1. На [портале управления Azure](https://manage.windowsazure.com) на левой вкладке выберите **Веб-сайты**, затем щелкните **Создать**.

2. Щелкните **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.

	![Create with Database link in Management Portal](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr6.PNG)

	Откроется мастер **Новый веб-сайт - настраиваемое создание**. 

3. На шаге **Создать веб-сайт** в мастере введите в поле **URL-адрес** строку, которая будет использоваться как уникальный URL-адрес для приложения. Полный URL-адрес будет состоять из введенной здесь строки и суффикса, который отображается под текстовым полем. На рисунке показан "contactmgr11", однако этот URL-адрес может быть занят, так что вам понадобится выбрать другой.

1. В раскрывающемся списке **Регион** выберите ближайший к вам регион.

1. В раскрывающемся списке **База данных** выберите **Создать бесплатную базу данных SQL 20 МБ**.

	![Create a New Website step of New Website - Create with Database wizard](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrCWS.png)

6. Щелкните стрелку, указывающую вправо, в нижней части окна.

	Мастер перейдет к шагу **Параметры базы данных**.

7. В поле **Имя** введите ContactDB.

8. В поле **Сервер** выберите **Создать сервер базы данных SQL**. Если же вы ранее создали базу данных SQL Server, можно выбрать в раскрывающемся списке этот SQL Server.

9. Щелкните стрелку, указывающую вправо, в нижней части окна.

10. Введите **ИМЯ ДЛЯ ВХОДА В СИСТЕМУ** и **ПАРОЛЬ** администратора. Если выбрано действие **Создать сервер базы данных SQL**, не используйте здесь существующие имя и пароль, введите новые, которые в дальнейшем будут использоваться при обращении к базе данных. Если выбран созданный ранее SQL Server, появится запрос на ввод пароля для ранее созданной учетной записи SQL Server. В этом учебнике поле **Дополнительно** не отмечается. Поле **Дополнительно** позволяет задавать размер базы данных (по умолчанию 1 ГБ, однако вы можете увеличить его до 150 ГБ), а также сортировку.

11. Щелкните флажок снизу от поля, чтобы указать завершение работы.

	![Database Settings step of New Website - Create with Database wizard][setup007]

	 На следующем изображении показано использование существующего SQL Server и входа в систему.
	
	![Database Settings step of New Website - Create with Database wizard][rxPrevDB]

	Портал управления возвращается на страницу веб-сайтов, и в столбце **Состояние** указывается, что веб-сайт создается. Через какое-то время (обычно меньше минуты) в столбце **Состояние** указывается, что сайт успешно создан. В строке навигации слева число сайтов вашей учетной записи отображается рядом со значком **Веб-сайты**, а число баз данных - рядом со значком **Базы данных SQL**.

<!-- [Websites page of Management Portal, website created][setup009] -->

<h2><a name="bkmk_createmvc4app"></a>Создание приложения ASP.NET MVC 5</h2>

Веб-сайт Azure создан, но его контент еще отсутствует. Следующим шагом будет создание проекта веб-приложения Visual Studio, которое будет опубликовано в Azure.

### Создание проекта

1. Откройте Visual Studio 2013.
1. В меню **Файл** выберите **Новый проект**.
3. В диалоговом окне **Новый проект** разверните узел **Visual C#**, выберите **Интернет**, а затем **Веб-приложение ASP.NET MVC 5**. Оставьте значение по умолчанию **.NET Framework 4.5**. Присвойте приложению имя **ContactManager** и нажмите кнопку **ОК**.
	![New Project dialog box](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.PNG)]
1. В диалоговом окне **Новый проект ASP.NET** выберите шаблон **MVC**, установите флажок **Веб-API**, а затем щелкните **Изменить аутентификацию**.

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

1. В диалоговом окне **Изменить аутентификацию** щелкните **Без аутентификации** и нажмите кнопку **ОК**.

	![No Authentication](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	В создаваемом образце приложения не будет функций, требующих от пользователей входа в систему. Дополнительную информацию о способах реализации функций аутентификации и авторизации см. в разделе [Дальнейшие действия](#nextsteps) в конце данного учебника. 

1. В диалоговом окне **Новый проект ASP.NET** нажмите кнопку **ОК**.

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

### Задание верхнего и нижнего колонтитула страницы


1. В **обозревателе решений** разверните папку Views\Shared и откройте файл _Layout.cshtml.

	![_Layout.cshtml in Solution Explorer][newapp004]

1. Замените содержимое файла *_Layout.cshtml* на код, приведенный ниже:


		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="utf-8" />
		    <title>@ViewBag.Title - Contact Manager</title>
		    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
		    <meta name="viewport" content="width=device-width" />
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		</head>
		<body>
		    <header>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p class="site-title">@Html.ActionLink("Contact Manager", "Index", "Home")</p>
		            </div>
		        </div>
		    </header>
		    <div id="body">
		        @RenderSection("featured", required: false)
		        <section class="content-wrapper main-content clear-fix">
		            @RenderBody()
		        </section>
		    </div>
		    <footer>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		            </div>
		        </div>
		    </footer>
		    @Scripts.Render("~/bundles/jquery")
		    @RenderSection("scripts", required: false)
		</body>
		</html>
			
Приведенный выше код позволяет изменить имя приложения с "Мое приложение ASP.NET" на "Диспетчер контактов" и удаляет ссылки на **главную** страницу, а также страницы **сведений** и **контактов**.

### Локальный запуск приложения

1. Для запуска приложения нажмите сочетание клавиш CTRL+F5.
Главная страница приложения откроется в браузере, используемом по умолчанию.
	![To Do List home page](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.PNG)

Это все, что требуется для создания приложения, которое будет развернуто в Azure. Далее вы добавите функции работы с базой данных.

<h2><a name="bkmk_deploytowindowsazure1"></a>Развертывание приложения в Azure</h2>

1. В Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать** в контекстном меню.

	![Publish in project context menu][PublishVSSolution]

	Откроется мастер **публикации веб-сайта**.

2. На вкладке **Профиль** мастера **публикации веб-сайта** щелкните **Импорт**.

	![Import publish settings][ImportPublishSettings]

	Появится диалоговое окно **Импорт профиля публикации**.

 3.	Выберите "Импорт с веб-сайта Azure". Если вы еще не выполнили вход в систему, сделайте это. Щелкните **Войти**. Укажите связанного с подпиской пользователя и выполните процедуру входа.

	![sign in](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr7.png)

	Выберите веб-сайт в раскрывающемся списке и нажмите кнопку **ОК**.

	![Import Publish Profile](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr8.png)

8. На вкладке **Подключение** щелкните **Проверить подключение**, чтобы убедиться в правильности определения параметров.

	![Validate connection][ValidateConnection]

9. После проверки подключения рядом с кнопкой **Проверить подключение** отображается зеленая галочка.

	![connection successful icon and Next button in Connection tab][firsdeploy007]

1. Нажмите кнопку **Далее**.

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	Можно принять заданные по умолчанию параметры, представленные на этой вкладке.  Вы развертываете конфигурацию сборки для выпуска, и вам не требуется удалять файлы на целевом сервере, выполнять предварительную компиляцию приложения или исключать файлы из папки App_Data. Чтобы выполнить отладку активного сайта Azure, вам необходимо развернуть конфигурацию отладки (не выпуск). См. раздел [Дальнейшие действия](#nextsteps) в конце данного учебника.

12. На вкладке **Предварительный просмотр** нажмите кнопку **Начало предварительного просмотра**.

	На вкладке отображается список файлов, которые будут копироваться на сервер. Предварительный просмотр не является обязательным для публикации приложения, но это полезная функция, о которой нужно знать. В этом случае вам не нужно ничего делать со списком показанных файлов. При следующей публикации в списке предварительного просмотра будут содержаться только измененные файлы.

	![StartPreview button in the Preview tab][firsdeploy009]

12. Щелкните **Опубликовать**.

	Visual Studio начинает процесс копирования файлов на сервер Azure. В окне **Вывод** указывается, какие действия по развертыванию были выполнены, а также сообщается об успешном завершении развертывания.

14. Браузер по умолчанию автоматически открывает URL-адрес развернутого сайта.

	Созданное вами приложение теперь выполняется в облаке.
	
	![To Do List home page running in Azure][rxz2]

<h2><a name="bkmk_addadatabase"></a>Добавление базы данных в приложение</h2>

Теперь вы обновите приложение MVC, чтобы добавить возможности для отображения и обновления контактов и сохранения данных в базе данных. Приложение будет использовать компонент Entity Framework для создания базы данных, а также чтения и обновления данных в базе данных.

### Добавление классов модели данных для контактов

Начните с создания простой модели данных в коде.

1. В **обозревателе решений** щелкните правой кнопкой мыши папку "Модели" и выберите **Добавить**, а затем щелкните **Класс**.

	![Add Class in Models folder context menu][adddb001]

2. В диалоговом окне **Добавление нового элемента** присвойте новому файлу класса имя Contact.cs, затем щелкните **Добавить**.

	![Add New Item dialog box][adddb002]

3. Замените содержимое файла Contacts.cs на код, приведенный ниже.

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
				public string Email { get; set; }
				public string Twitter { get; set; }
				public string Self
        		{
            		get { return string.Format(CultureInfo.CurrentCulture,
				         "api/contacts/{0}", this.ContactId); }
            		set { }
        		}
    		}
		}

Класс **Contacts** определяет данные, которые будут храниться для каждого контакта, а также первичный ключ ContactID, необходимый для базы данных. Дополнительную информацию о моделях данных см. в разделе [Дальнейшие действия](#nextsteps) в конце данного учебника.

### Создание веб-страниц, позволяющих пользователям приложений работать с контактами

Компонент формирования шаблонов в ASP.NET MVC может автоматически создавать код, выполняющий операции создания, чтения, обновления и удаления.

<h2><a name="bkmk_addcontroller"></a>Добавление контроллера и представления для данных</h2>

1. В **обозревателе решений** разверните папку "Контроллеры".

3. Создайте проект **(Ctrl+Shift+B)**. Проект необходимо создать перед использованием механизма формирования шаблонов. 

4. Щелкните правой кнопкой мыши папку "Контроллеры" и выберите **Добавить**, а затем щелкните **Контроллер**.

	![Add Controller in Controllers folder context menu][addcode001]

1. В диалоговом окне **Добавление Scaffold** выберите **Контроллер MVC с представлениями, использующий Entity Framework**, и щелкните **Добавить**.

 ![Add controller](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.PNG)

6. Укажите имя контроллера **HomeController**. Выберите класс модели **Contact**. Нажмите кнопку **Новый контекст данных** и оставьте значение ContactManager.Models.ContactManagerContext, предлагаемое по умолчанию в разделе **Новый тип контекста данных**. Щелкните **Добавить**.

	![Add Controller dialog box](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr9.PNG)

	В диалоговом окне появится сообщение: "Файл с именем HomeController уже существует. Вы хотите заменить его?". Щелкните **Да**. Контроллер Home, созданный для нового проекта, будет перезаписан В списке контактов будет использоваться новый контроллер Home.

	Visual Studio создает методы и представления контроллера для операций создания, чтения, обновления и удаления в базе данных для объектов **Контакт**.

## Включение Migrations, создание базы данных, добавление примера данных и инициализатора данных

Следующее задание - включить функцию [Code First Migrations](http://curah.microsoft.com/55220) , чтобы создать базу данных на основе созданной вами модели данных.

1. В меню **Сервис** выберите **Диспетчер пакетов библиотеки**, а затем **Консоль диспетчера пакетов**.

	![Package Manager Console in Tools menu][addcode008]

2. В окне **Консоль диспетчера пакетов** введите следующую команду:

		enable-migrations 
  
	Команда **enable-migrations** создает папку Migrations и размещает в этой папке файл Configuration.cs, который можно использовать для настройки переносов данных. 

2. В окне **Консоль диспетчера пакетов** введите следующую команду:

		add-migration Initial

	Команда **add-migration Initial** создает класс с именем **&lt;date_stamp&gt;Initial**, который, в свою очередь, создает базу данных. Первый параметр (Initial) является произвольным и используется для создания имени файла. Новые файлы классов вы можете просмотреть в **обозревателе решений**.

	В классе **Initial** метод **Up** создает таблицу Contacts, а метод **Down** (используется при возвращении к предыдущему состоянию) удаляет эту таблицу.

3. Откройте файл Migrations\Configuration.cs. 

4. Добавьте следующее пространства имен. 

    	 using ContactManager.Models;

5. Замените метод Seed следующим кодом:
		
        protected override void Seed(ContactManager.Models.ContactManagerContext context)
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
                   Twitter = "debra_example"
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                    Twitter = "thorsten_example"
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                    Twitter = "yuhong_example"
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                    Twitter = "jon_example"
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                    Twitter = "diliana_example"
                }
                );
        }

	Приведенный выше код инициализирует базу данных с контактной информацией. Дополнительную информацию об инициализации базы данных см. в разделе [Инициализация баз данных Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


1. В **консоли диспетчера пакетов** введите команду:

		update-database

	![Package Manager Console commands][addcode009]

	Команда **update-database** выполняет первый перенос, при котором создается база данных. По умолчанию база данных создается как база данных LocalDB в SQL Server Express.

1. Для запуска приложения нажмите сочетание клавиш CTRL+F5. 

Приложение показывает начальные данные и предоставляет ссылки для изменения, удаления и просмотра подробных сведений.

![MVC view of data][rxz3]

<h2><a name="bkmk_addview"></a>Редактирование представления</h2>

1. Откройте файл Views\Home\Index.cshtml. На следующем шаге мы заменим созданную разметку кодом, в котором используются [jQuery](http://jquery.com/) и [Knockout.js](http://knockoutjs.com/). Новый код извлекает список контактов с помощью веб-интерфейса API и JSON, а затем привязывает данные контактов к пользовательскому интерфейсу с использованием knockout.js. Дополнительную информацию см. в разделе [Дальнейшие действия](#nextsteps) в конце данного учебника. 


2. Замените содержимое файла на код, приведенный ниже.

		@model IEnumerable<ContactManager.Models.Contact>
		@{
		    ViewBag.Title = "Home";
		}
		@section Scripts {
		    @Scripts.Render("~/bundles/knockout")
		    <script type="text/javascript">
		        function ContactsViewModel() {
		            var self = this;
		            self.contacts = ko.observableArray([]);
		            self.addContact = function () {
		                $.post("api/contacts",
		                    $("#addContact").serialize(),
		                    function (value) {
		                        self.contacts.push(value);
		                    },
		                    "json");
		            }
		            self.removeContact = function (contact) {
		                $.ajax({
		                    type: "DELETE",
		                    url: contact.Self,
		                    success: function () {
		                        self.contacts.remove(contact);
		                    }
		                });
		            }

		            $.getJSON("api/contacts", function (data) {
		                self.contacts(data);
		            });
		        }
		        ko.applyBindings(new ContactsViewModel());	
		</script>
		}
		<ul id="contacts" data-bind="foreach: contacts">
		    <li class="ui-widget-content ui-corner-all">
		        <h1 data-bind="text: Name" class="ui-widget-header"></h1>
		        <div><span data-bind="text: $data.Address || 'Address?'"></span></div>
		        <div>
		            <span data-bind="text: $data.City || 'City?'"></span>,
		            <span data-bind="text: $data.State || 'State?'"></span>
		            <span data-bind="text: $data.Zip || 'Zip?'"></span>
		        </div>
		        <div data-bind="if: $data.Email"><a data-bind="attr: { href: 'mailto:' + Email }, text: Email"></a></div>
		        <div data-bind="ifnot: $data.Email"><span>Email?</span></div>
		        <div data-bind="if: $data.Twitter"><a data-bind="attr: { href: 'http://twitter.com/' + Twitter }, text: '@@' + Twitter"></a></div>
		        <div data-bind="ifnot: $data.Twitter"><span>Twitter?</span></div>
		        <p><a data-bind="attr: { href: Self }, click: $root.removeContact" class="removeContact ui-state-default ui-corner-all">Remove</a></p>
		    </li>
		</ul>
		<form id="addContact" data-bind="submit: addContact">
		    <fieldset>
		        <legend>Add New Contact</legend>
		        <ol>
		            <li>
		                <label for="Name">Name</label>
		                <input type="text" name="Name" />
		            </li>
		            <li>
		                <label for="Address">Address</label>
		                <input type="text" name="Address" >
		            </li>
		            <li>
		                <label for="City">City</label>
		                <input type="text" name="City" />
		            </li>
		            <li>
		                <label for="State">State</label>
		                <input type="text" name="State" />
		            </li>
		            <li>
		                <label for="Zip">Zip</label>
		                <input type="text" name="Zip" />
		            </li>
		            <li>
		                <label for="Email">E-mail</label>
		                <input type="text" name="Email" />
		            </li>
		            <li>
		                <label for="Twitter">Twitter</label>
		                <input type="text" name="Twitter" />
		            </li>
		        </ol>
		        <input type="submit" value="Add" />
		    </fieldset>
		</form>

3. Щелкните правой кнопкой мыши папку Content и выберите **Добавить**, а затем щелкните **Новый элемент...**.

	![Add style sheet in Content folder context menu][addcode005]

4. В диалоговом окне **Добавить новый элемент** введите **Стиль** в строке поиска, расположенной в правом верхнем углу, затем выберите **Таблица стилей**.
	![Add New Item dialog box][rxStyle]

5. Присвойте файлу имя Contacts.css и выберите **Добавить**. Замените содержимое файла на код, приведенный ниже.
    
        .column {
            float: left;
            width: 50%;
            padding: 0;
            margin: 5px 0;
        }
        form ol {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }
        form li {
            padding: 1px;
            margin: 3px;
        }
        form input[type="text"] {
            width: 100%;
        }
        #addContact {
            width: 300px;
            float: left;
            width:30%;
        }
        #contacts {
            list-style-type: none;
            margin: 0;
            padding: 0;
            float:left;
            width: 70%;
        }
        #contacts li {
            margin: 3px 3px 3px 0;
            padding: 1px;
            float: left;
            width: 300px;
            text-align: center;
            background-image: none;
            background-color: #F5F5F5;
        }
        #contacts li h1
        {
            padding: 0;
            margin: 0;
            background-image: none;
            background-color: Orange;
            color: White;
            font-family: Trebuchet MS, Tahoma, Verdana, Arial, sans-serif;
        }
        .removeContact, .viewImage
        {
            padding: 3px;
            text-decoration: none;
        }

	Указанная таблица стилей будет определять макет, цвета и стили, используемые в приложении диспетчера контактов.

6. Откройте файл App_Start\BundleConfig.cs.


7. Добавьте следующий код для регистрации подключаемого модуля [Knockout](http://knockoutjs.com/index.html "KO") .

		bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
		            "~/Scripts/knockout-{version}.js"));
	В этом примере knockout используется для упрощения динамического кода JavaScript, который обрабатывает шаблоны экрана.

8. Измените запись contents/css для регистрации таблицы стилей contacts.css. Измените следующую строку:

                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));
To:

        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));

1. В консоли диспетчера пакетов выполните следующую команду для установки подключаемого модуля Knockout.

	Install-Package knockoutjs

<h2><a name="bkmk_addwebapi"></a>Добавление контроллера для веб-интерфейса API Restful</h2>

1. В **обозревателе решений** щелкните правой кнопкой мыши "Контроллеры" и выберите **Добавить**, а затем щелкните **Контроллер....** 

1. В диалоговом окне **Добавление Scaffold** введите **Web API 2 Controller with actions, using Entity Framework**, и щелкните **Добавить**.

	![Add API controller](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.PNG)

4. В диалоговом окне **Добавление контроллера** введите ContactsController в качестве имени контроллера. В раскрывающемся списке **Класс модели** выберите Contact (ContactManager.Models).  Оставьте значение по умолчанию для элемента **Класс контекста данных**. 

6. Щелкните **Добавить**.

### Локальный запуск приложения

1. Для запуска приложения нажмите сочетание клавиш CTRL+F5.

	![Index page][intro001]

2. Введите контакт и нажмите кнопку **Добавить**. Приложение возвращается на главную страницу и отображает введенный контакт.

	![Index page with to-do list items][addwebapi004]

3. В браузере добавьте к URL-адресу **/api/contacts**.

	Итоговый URL-адрес будет выглядеть аналогично http://localhost:1234/api/contacts. Добавленный вами веб-интерфейс API RESTful возвращает сохраненные контакты. FireFox и Chrome отображают данные в формате XML.

	![Index page with to-do list items][rxFFchrome]
	

	IE запрашивает открытие и сохранение контактов.

	![Web API save dialog][addwebapi006]
	
	
	Возвращенные контакты можно открыть в блокноте или браузере.
	
	Эти выходные данные могут использоваться другим приложением, например мобильной веб-страницей или приложением.

	![Web API save dialog][addwebapi007]

	**Предупреждение системы безопасности**: На этом этапе ваше приложение не защищено и уязвимо для атак с подделкой межсайтовых запросов (CSRF). Эта уязвимость будет удалена позднее в рамках этого учебника. Дополнительную информацию см. в разделе [Предотвращение атак с подделкой межсайтовых запросов (CSRF)][prevent-csrf-attacks].

<h2><a name="xsrf"></a>Добавление защиты XSRF</h2>

Подделка межсайтовых запросов (также называемая XSRF или CSRF) является атакой, направленной на размещенные веб-приложения, при которой вредоносный веб-сайт может влиять на взаимодействие между клиентским браузером и веб-сайтом, которому доверяет этот браузер. Эти атаки становятся возможными, потому что браузеры отправляют маркеры аутентификации автоматически вместе с каждым запросом веб-сайта. Типичный пример - файл cookie для проверки подлинности, например билет проверки форм ASP.NET. Однако веб-сайты, которые используют какой-либо постоянный механизм аутентификации (например, аутентификацию Windows или обычную аутентификацию), могут стать уязвимыми для этого вида атак.

Атака XSRF отличается от фишинга. При проведении фишинговой атаки требуется взаимодействие пользователя. В фишинговой атаке вредоносный веб-сайт практически идентичен целевому веб-сайту, поэтому жертва предоставляет атакующей стороне свои конфиденциальные данные. При атаке XSRF очень часто никаких действий со стороны жертвы не требуется. Злоумышленник больше полагается на то, что браузер автоматически отправляет все необходимые файлы cookie на целевой веб-сайт.

Дополнительную информацию см. в разделе [Открытый проект безопасности веб-приложений](https://www.owasp.org/index.php/Main_Page) (OWASP) [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).

1. В **обозревателе решений** щелкните правой кнопкой мыши проект**ContactManager**, выберите **Добавить**, а затем щелкните **Класс**.

2. Присвойте файлу имя ValidateHttpAntiForgeryTokenAttribute.cs и добавьте следующий код:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Helpers;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using System.Web.Mvc;
        namespace ContactManager.Filters
        {
            public class ValidateHttpAntiForgeryTokenAttribute : AuthorizationFilterAttribute
            {
                public override void OnAuthorization(HttpActionContext actionContext)
                {
                    HttpRequestMessage request = actionContext.ControllerContext.Request;
                    try
                    {
                        if (IsAjaxRequest(request))
                        {
                            ValidateRequestHeader(request);
                        }
                        else
                        {
                            AntiForgery.Validate();
                        }
                    }
                    catch (HttpAntiForgeryException e)
                    {
                        actionContext.Response = request.CreateErrorResponse(HttpStatusCode.Forbidden, e);
                    }
                }
                private bool IsAjaxRequest(HttpRequestMessage request)
                {
                    IEnumerable<string> xRequestedWithHeaders;
                    if (request.Headers.TryGetValues("X-Requested-With", out xRequestedWithHeaders))
                    {
                        string headerValue = xRequestedWithHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(headerValue))
                        {
                            return String.Equals(headerValue, "XMLHttpRequest", StringComparison.OrdinalIgnoreCase);
                        }
                    }
                    return false;
                }
                private void ValidateRequestHeader(HttpRequestMessage request)
                {
                    string cookieToken = String.Empty;
                    string formToken = String.Empty;
					IEnumerable<string> tokenHeaders;
                    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
                    {
                        string tokenValue = tokenHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(tokenValue))
                        {
                            string[] tokens = tokenValue.Split(':');
                            if (tokens.Length == 2)
                            {
                                cookieToken = tokens[0].Trim();
                                formToken = tokens[1].Trim();
                            }
                        }
                    }
                    AntiForgery.Validate(cookieToken, formToken);
                }
            }
        }

1. Добавьте в контроллер контактов следующую инструкцию using, которая обеспечивает доступ к атрибуту **[ValidateHttpAntiForgeryToken]**.

	using ContactManager.Filters;

1. Добавьте атрибут **[ValidateHttpAntiForgeryToken]** в методы Post объекта **ContactsController**, чтобы защитить его от подделки межсайтовых запросов. Этот атрибут необходимо добавить в методы действий PutContact, PostContact и**DeleteContact**.

	[ValidateHttpAntiForgeryToken]
        public IHttpActionResult PutContact(int id, Contact contact)
        {

1. Update the *Scripts* section of the *Views\Home\Index.cshtml* file to include code to get the XSRF tokens.

         @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                @functions{
                   public string TokenHeaderValue()
                   {
                      string cookieToken, formToken;
                      AntiForgery.GetTokens(null, out cookieToken, out formToken);
                      return cookieToken + ":" + formToken;                
                   }
                }

               function ContactsViewModel() {
                  var self = this;
                  self.contacts = ko.observableArray([]);
                  self.addContact = function () {

                     $.ajax({
                        type: "post",
                        url: "api/contacts",
                        data: $("#addContact").serialize(),
                        dataType: "json",
                        success: function (value) {
                           self.contacts.push(value);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }
                     });

                  }
                  self.removeContact = function (contact) {
                     $.ajax({
                        type: "DELETE",
                        url: contact.Self,
                        success: function () {
                           self.contacts.remove(contact);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }

                     });
                  }

                  $.getJSON("api/contacts", function (data) {
                     self.contacts(data);
                  });
               }
               ko.applyBindings(new ContactsViewModel());
            </script>


<h2><a name="bkmk_deploydatabaseupdate"></a>Публикация обновления приложения в Azure и базе данных SQL</h2>

Чтобы опубликовать приложение, повторите процедуры, которые описаны выше.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Опубликовать**.

	![Publish][rxP]

5. Перейдите на вкладку **Параметры**.
	

1. В разделе **ContactsManagerContext(ContactsManagerContext)** щелкните значок **v**, чтобы изменить строку удаленного подключения на строку подключения для базы данных контактов. Щелкните **ContactDB**.

	![Settings](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png)

7. Установите флажок **Выполнить Code First Migrations (при запуске приложения)**.

1. Щелкните **Далее**, затем **Предварительный просмотр**. В Visual Studio отображается список файлов, которые будут добавлены или обновлены.

8. Щелкните **Опубликовать**.
После завершения развертывания в браузере открывается главная страница приложения.

	![Index page with no contacts][intro001]

	Процесс публикации Visual Studio автоматически настроил строку подключения в развернутом файле Web.config для указания на базу данных SQL. Процесс также настроил Code First Migrations для автоматического обновления базы данных до последней версии при первом доступе приложения к базе данных после развертывания.

	В результате этой конфигурации Code First создает базу данных, запуская код в ранее созданном классе **Initial**. Это было сделано, когда приложение впервые попыталось получить доступ к базе данных после развертывания.

9. Введите контакт, как и при локальном запуске приложения, чтобы проверить успешность развертывания базы данных.

Когда вы увидите, что этот введенный элемент сохранен и показан на странице диспетчера контактов, вы будете знать, что он сохранен в базе данных.

![Index page with contacts][addwebapi004]

Теперь приложение работает в облаке и использует базу данных SQL для хранения данных. После завершения тестирования приложения в Azure его необходимо удалить. Приложение является общим и не имеет механизма для ограничения доступа.

<h2><a name="nextsteps"></a>Дальнейшие действия</h2>

Для фактического приложения потребуется проверка подлинности и авторизация, и для этого будет использоваться база данных членства. Учебник [Развертывание безопасного приложения ASP.NET MVC с членством, OAuth и базой данных SQL](http://www.windowsazure.com/ru-ru/develop/net/tutorials/web-site-with-sql-database/) основан на данном учебнике и демонстрирует, как выполнять развертывание веб-приложения с базой данных участников.

Другой способ хранения данных в приложении Azure - использование хранилища Azure, обеспечивающего хранение нереляционных данных в виде BLOB-объектов и таблиц. По следующим ссылкам приводятся дополнительные сведения о веб-интерфейсе API, ASP.NET MVC и Window Azure.
 

* [Общие сведения о работе с Entity Framework с использованием MVC][EFCodeFirstMVCTutorial]
* [Введение в ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Ваш первый веб-API ASP.NET](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)
* [Отладка веб-сайтов Azure](http://www.windowsazure.com/ru-ru/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/)

Автор этого учебника и примера приложения - [Рик Андерсон (Rick Anderson)](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)), ему помагали Том Дайкстра (Tom Dykstra) и Барри Дорранс (Barry Dorrans) (Twitter [@blowdart](https://twitter.com/blowdart)). 

Пожалуйста, оставьте отзыв о том, что вам понравилось или что вы хотели бы улучшить, не только о самом учебнике, но и о продуктах, которые он демонстрирует. Ваши отзывы помогут нам определить, какие улучшения наиболее приоритетные. Мы особенно заинтересованы узнать, насколько всем хочется получить дополнительные сведения об автоматизации процесса настройки и развертывании базы данных членства. 

<!-- bookmarks -->
[Добавление поставщика OAuth]: #addOauth
[Добавление ролей в базу данных членства]:#mbrDB
[Создание сценария развертывания данных]:#ppd
[Обновление базы данных членства]:#ppd2
[setupdbenv]: #bkmk_setupdevenv
[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[adddb]: #bkmk_addadatabase
[addcontroller]: #bkmk_addcontroller
[addwebapi]: #bkmk_addwebapi
[deploy2]: #bkmk_deploydatabaseupdate

<!-- links -->
[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[dbcontext-link]: http://msdn.microsoft.com/ru-ru/library/system.data.entity.dbcontext(v=VS.103).aspx


<!-- images-->
[rxE]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxE.png
[rxP]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png
[rx22]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/
[rxb2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxb2.png
[rxz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz.png
[rxzz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxzz.png
[rxz2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz2.png
[rxz3]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png
[rxStyle]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png
[rxz4]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz4.png
[rxz44]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz44.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxPrevDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPrevDB.png
[rxOverwrite]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxOverwrite.png
[rxPWS]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPWS.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxAddApiController]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxAddApiController.png
[rxFFchrome]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxFFchrome.png
[intro001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxCreateWSwithDB.png
[setup007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-setup-azure-site-004.png
[setup009]: ../Media/dntutmobile-setup-azure-site-006.png
[newapp002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-002.png
[newapp004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-004.png
[firsdeploy007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-007.png
[adddb001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-001.png
[adddb002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-context-menu.png
[addcode002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-controller-dialog.png
[addcode004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-index-context.png
[addcode005]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png
[addcode007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-bundleconfig-context.png
[addcode008]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-console.png
[addwebapi004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-added-contact.png
[addwebapi006]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-save-returned-contacts.png
[addwebapi007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-contacts-in-notepad.png
[Add XSRF Protection]: #xsrf
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[Add XSRF Protection]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[prevent-csrf-attacks]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks

<!--HONumber=35_1-->
