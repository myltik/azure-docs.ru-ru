<properties linkid="develop-dotnet-rest-service-using-web-api" urlDisplayName="Служба REST с использованием веб-интерфейса API" pageTitle="Служба .NET REST с использованием веб-интерфейса API — учебник по Windows Azure" metaKeywords="Веб-сайт учебника по Azure, веб-сайт интерфейса API ASP.NET, Azure VS" description="Учебник, в котором описывается, как развертывать приложение, в котором используется веб-интерфейс API ASP.NET, на веб-сайте Windows Azure с использованием Visual Studio." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Служба REST с использованием веб-интерфейса API ASP.NET и базы данных SQL" authors=""  solutions="" writer="riande" manager="" editor=""  />




#Служба REST с использованием веб-интерфейса API ASP.NET и базы данных SQL 

***Авторы: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT) и Том Дайкстра (Tom Dykstra). Обновлено 26 июня 2013 г.***

В этом учебнике показано, как развернуть веб-приложение ASP.NET, которое использует веб-интерфейс API ASP.NET, на веб-сайте Windows Azure с помощью мастера "Публикация веб-сайта" в Visual Studio 2012 или Visual Studio 2012 для Web Express.

Можно бесплатно создать учетную запись Windows Azure, а если у вас еще нет Visual Studio 2012, пакет SDK автоматически установит Visual Studio 2012 для Web Express. Так что вы можете приступить к разработке приложений для Windows Azure совершенно бесплатно.

Этот учебник разработан для читателей, не имеющих опыта использования платформы Windows Azure. По завершении изучения этого учебника вы создадите приложение на основе данных, выполняемое в облаке и использующее облачную базу данных.

Вы узнаете:

* Как подготовить компьютер к разработке для Windows Azure с помощью пакета Windows Azure SDK.
* Как создать проект Visual Studio ASP.NET MVC 4 и опубликовать его на веб-сайте Windows Azure.
* Как использовать веб-интерфейс API ASP.NET для включения вызовов Restful API.
* Как использовать базу данных SQL для хранения данных в Windows Azure.
* Как публиковать обновления приложений в Windows Azure.

Вы создадите простое веб-приложение для списка контактов, основанное на платформе ASP.NET MVC 4 и использующее ADO.NET Entity Framework для доступа к базе данных. На следующем рисунке показано завершенное приложение:

![снимок экрана веб-сайта][intro001]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

В этом учебнике рассматриваются следующие темы:

* [Настройка среды разработки][setupdbenv]
* [Настройка среды Windows Azure][setupwindowsazureenv]
* [Создание приложения ASP.NET MVC 4][createapplication]
* [Развертывание приложения в Windows Azure][deployapp1]
* [Добавление базы данных в приложение][adddb]
* [Добавление контроллера и представления для данных][addcontroller]
* [Добавление веб-интерфейса API Restful][addwebapi]
* [Добавление защиты XSRF][]
* [Публикация обновления приложения в Windows Azure и базе данных SQL][deploy2]

<h2><a name="bkmk_setupdevenv"></a>Настройка среды разработки</h2>

Для начала настройте среду разработки, установив пакет Windows Azure SDK для платформы .NET Framework. 


1. Чтобы установить пакет Windows Azure SDK для .NET, щелкните приведенную ниже ссылку. Если среда Visual Studio 2012 еще не установлена, ее можно установить по ссылке. Для работы с этим учебником требуется Visual Studio 2012. <br/>

	[Windows Azure SDK для Visual Studio 2012]( http://go.microsoft.com/fwlink/?LinkId=254364)<br/>

1. При появлении запроса на выполнение или сохранение исполняемой установки нажмите **Выполнить**.<br/>

1. Щелкните в окне установщика веб-платформы **Установить** и продолжайте установку.<br/>

	![Установщик веб-платформы — Windows Azure SDK для .NET][WebPIAzureSdk20NetVS12]<br/>

После завершения установки вы получите все компоненты, необходимые для разработки.

<h2><a name="bkmk_setupwindowsazure"></a>Настройка среды Windows Azure</h2>

Настройте среду Windows Azure, для чего создайте веб-сайт Windows Azure и базу данных SQL.

### Создание веб-сайта и базы данных SQL в Windows Azure

Следующим шагом будет создание веб-сайта Windows Azure и базы данных SQL, используемые вашим приложением.

Ваш веб-сайт Windows Azure будет работать в общей среде размещения, а это означает, что он работает на виртуальных машинах, к которым имеют общий доступ другие клиенты Windows Azure. Общая среда размещения — недорогой способ начать работу в облаке. Если позднее ваш веб-трафик увеличится, приложение можно масштабировать по необходимости, запуская его на выделенных виртуальных машинах. Если требуется более сложная архитектура, можно перейти в облачную службу Windows Azure. Облачные службы работают на выделенных виртуальных машинах, которые можно настроить в соответствии с конкретными потребностями.

База данных SQL — это облачная служба реляционной базы данных, построенная на основе технологий SQL Server. Средства и приложения, работающие с SQL Server, работают и с базой данных SQL.

1. На [портале управления Windows Azure](https://manage.windowsazure.com) щелкните **Веб-сайты** на левой вкладке, а затем щелкните **Создать**.<br/>

2. Щелкните **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.<br/>

	![Ссылка "Создать с базой данных" на портале управления][rxCreateWSwithDB]<br/>

	Откроется мастер **Новый веб-сайт — настраиваемое создание**. 

3. На шаге **Новый веб-сайт** в мастере введите в поле **URL-адрес** строку, которая будет использоваться как уникальный URL-адрес для приложения. Полный URL-адрес будет состоять из введенной здесь строки и суффикса, который отображается под текстовым полем. На рисунке показан "contactmgr22", однако этот URL-адрес может быть занят, так что вам нужно будет выбрать другой.

1. В раскрывающемся списке **Регион** выберите ближайший к вам регион.<br/>

1. В раскрывающемся списке **База данных** выберите параметр **Создать базу данных SQL**.

	Примите строку подключения по умолчанию.

	![Шаг создания нового веб-сайта в мастере "Новый веб-сайт — создать с базой данных"][rxz]<br/>

6. Щелкните стрелку, указывающую вправо, в нижней части окна.

	Мастер перейдет к шагу **Параметры базы данных**.

7. В поле **Имя** введите *ContactDB*.

8. В поле **Сервер** выберите **Создать сервер базы данных SQL**. Если же вы ранее создали базу данных SQL Server, можно выбрать в раскрывающемся списке этот SQL Server.

9. Щелкните стрелку, указывающую вправо, в нижней части окна.<br/>

10. Введите **ИМЯ ДЛЯ ВХОДА** и **ПАРОЛЬ** администратора. Если выбрано действие **Создать сервер базы данных SQL**, не используйте здесь существующие имя и пароль, введите новые, которые в дальнейшем будут использоваться при обращении к базе данных. Если выбран созданный ранее SQL Server, появится запрос на ввод пароля для ранее созданной учетной записи SQL Server. В этом учебнике поле **Дополнительно** не отмечается. Поле **Дополнительно ** позволяет задавать размер БД (по умолчанию 1 ГБ, однако его можно увеличить до 150 ГБ), а также сортировку.

11. Щелкните флажок снизу от поля, чтобы указать завершение работы.

	![Шаг настройки базы данных в мастере "Новый веб-сайт — создать с базой данных"][setup007]<br/>

	<br/> На следующем изображении показано использование существующего SQL Server и входа в систему.
	
	![Шаг настройки базы данных в мастере "Новый веб-сайт — создать с базой данных"][rxPrevDB]<br/>

	Портал управления возвращается на страницу веб-сайтов, и в столбце **Состояние** указывается, что веб-сайт создается. Через какое-то время (обычно меньше минуты) в столбце **Состояние** указывается, что сайт успешно создан. В строке навигации слева число сайтов вашей учетной записи отображается рядом со значком **Веб-сайты**, а число баз данных — рядом со значком **Базы данных SQL**.<br/>

<!-- [Web Sites page of Management Portal, web site created][setup009] -->

<h2><a name="bkmk_createmvc4app"></a>Создание приложения ASP.NET MVC 4</h2>

Вы создали веб-сайт Windows Azure, но на нем еще нет контента. Следующим шагом будет создание проекта веб-приложения Visual Studio, которое будет опубликовано в Windows Azure.

### Создание проекта

1. Откройте Visual Studio 2012.
1. В меню **Файл** выберите **Новый проект**.
3. В диалоговом окне **Новый проект** разверните узел **Visual C#** и выберите **Интернет** в разделе **Установленные шаблоны**, а затем выберите **Веб-приложение ASP.NET MVC 4**. Оставьте значение по умолчанию **.NET Framework 4.5**. Присвойте приложению имя **ContactManager** и нажмите кнопку **ОК**.<br/>
	![Диалоговое окно "Новый проект"][newapp002]
6. В диалоговом окне **Новый проект ASP.NET MVC 4** выберите шаблон **Веб-приложение**. Оставьте **Обработчик представлений** Razor по умолчанию, затем нажмите кнопку **ОК**.<br/>
	![Диалоговое окно "Новый проект ASP.NET MVC 4"][rxb2]

### Задание верхнего и нижнего колонтитула страницы


1. В **Обозревателе решений** разверните папку *Представления\Общие* и откройте файл *_Layout.cshtml*.<br/>

	![_Layout.cshtml в обозревателе решений][newapp004]

1. Замените содержимое файла *_Layout.cshtml* на код, приведенный ниже.<br/>

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
			

### Локальный запуск приложения

1. Для запуска приложения нажмите сочетание клавиш CTRL+F5.
Главная страница приложения откроется в браузере, используемом по умолчанию.<br/>

	![Главная страница списка дел][rxzz]

Это все что требуется для создания приложения, которое будет развернуто в Windows Azure. Далее вы добавите функции работы с базой данных.

<h2><a name="bkmk_deploytowindowsazure1"></a>Развертывание приложения в Windows Azure</h2>

1. В **Обозревателе решений** Visual Studio щелкните правой кнопкой мыши проект и выберите **Опубликовать** в контекстном меню.<br/>

	!["Опубликовать" в контекстном меню проекта][PublishVSSolution]<br/>

	Откроется мастер **Публикация веб-сайта**.

2. На вкладке **Профиль** мастера **Публикация веб-сайта** щелкните **Импорт**.<br/>

	![Параметры импорта публикации][ImportPublishSettings]

	Появится диалоговое окно **Импорт профиля публикации**.

3. Если вы не добавили ранее подписку Windows Azure в Visual Studio, выполните следующие действия. Выполнив эти действия, вы сможете добавить подписку, чтобы в раскрывающемся списке, расположенном под заголовком **Импорт с веб-сайта Windows Azure**, содержался и ваш веб-сайт.

	-В диалоговом окне **Импорт профиля публикации** щелкните **Добавить подписку Windows**.<br/> 
    
	![add win az sub](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rzAddWAsub.png)
    
	-В диалоговом окне **Импорт подписок Windows Azure** щелкните **Загрузить файл подписки**.<br/>
    
	![download sub](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rzDownLoad.png)
    
	-В окне браузера сохраните файл *.publishsettings*.<br/>
    
	![download pub file](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rzDown2.png)
    
	[WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]

	-В диалоговом окне **Импорт подписок Windows Azure** щелкните **Обзор** и перейдите к файлу *.publishsettings*.<br/>
    
	![download sub](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rzDownLoad.png)
    
	-Щелкните **Импорт**.<br/>
    
	![импорт](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rzImp.png)

7. В диалоговом окне **Импорт профиля публикации** выберите **Импорт с веб-сайта Windows Azure**, выберите свой веб-сайт в раскрывающемся списке, затем нажмите кнопку **ОК**.<br/>

	![Импорт профиля публикации][ImportPublishProfile]

8. На вкладке **Подключение** щелкните **Проверить подключение**, чтобы убедиться в правильности определения параметров.<br/>

	![Проверка подключения][ValidateConnection]<br/>

9. После проверки подключения рядом с кнопкой **Проверить подключение** отображается зеленая галочка.<br/>

	![Значок успешного подключения и кнопка "Далее" на вкладке "Подключение"][firsdeploy007]

10. Можно принять все заданные по умолчанию параметры, представленные на этой странице.  Вы развертываете конфигурацию построения "Выпуск", и вам не требуется удалять файлы на целевом сервере. Запись **UsersContext (DefaultConnection)** в разделе **Базы данных** берется из класса *UsersContext:DbContext*, в котором используется строка DefaultConnection. <br/>

	Нажмите кнопку **Далее**.<br/>

	![Значок успешного подключения и кнопка "Далее" на вкладке "Подключение"][rxPWS]

12. На вкладке **Предварительный просмотр** нажмите кнопку **Начало предварительного просмотра**.<br/>

	На вкладке отображается список файлов, которые будут копироваться на сервер. Предварительный просмотр не является обязательным для публикации приложения, но это полезная функция, о которой нужно знать. В этом случае вам не нужно ничего делать со списком показанных файлов. При следующей публикации в списке предварительного просмотра будут содержаться только измененные файлы.<br/>

	![Кнопка "Начало предварительного просмотра" на вкладке "Предварительный просмотр"][firsdeploy009]<br/>

12. Щелкните **Опубликовать**.<br/>

	Visual Studio начинает процесс копирования файлов на сервер Windows Azure. В окне **Вывод** указывается, какие действия по развертыванию были выполнены, а также сообщается об успешном завершении развертывания.

14. URL-адрес развернутого сайта автоматически открывается в браузере по умолчанию.<br/>

	Созданное вами приложение теперь выполняется в облаке.
	
	![Главная страница списка дел на ресурсе Windows Azure][rxz2]

<h2><a name="bkmk_addadatabase"></a>Добавление базы данных в приложение</h2>

Теперь вы обновите приложение MVC, чтобы добавить возможности для отображения и обновления контактов и сохранения данных в базе данных. Приложение будет использовать компонент Entity Framework для создания базы данных, а также чтения и обновления данных в базе данных.

### Добавление классов модели данных для контактов

Начните с создания простой модели данных в коде.

1. В **Обозревателе решений** щелкните правой кнопкой мыши папку "Модели" и выберите **Добавить**, а затем щелкните **Класс**.<br/>

	![Контекстное меню добавления класса в папке "Модели"][adddb001]

2. В диалоговом окне **Добавление нового элемента** присвойте новому файлу класса имя *Contact.cs*, затем щелкните **Добавить **<br/>.

	![Диалоговое окно "Добавление нового элемента"][adddb002]

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

Класс **Contacts** определяет данные, которые будут храниться для каждого контакта, а также первичный ключ ContactID, необходимый для базы данных.

### Создание веб-страниц, позволяющих пользователям приложений работать с контактами

Компонент формирования шаблонов в ASP.NET MVC может автоматически создавать код, выполняющий операции создания, чтения, обновления и удаления.

<h2><a name="bkmk_addcontroller"></a>Добавление контроллера и представления для данных</h2>

1. В **Обозревателе решений** разверните папку "Контроллеры".

3. Создайте проект **(Ctrl + Shift + B)** (проект необходимо создать перед использованием механизма формирования шаблонов). <br/>

4. Щелкните правой кнопкой мыши папку "Контроллеры" и выберите **Добавить**, а затем щелкните **Контроллер**.<br/>

	![Контекстное меню добавления контроллера в папке "Контроллеры"][addcode001]<br/>

5. В диалоговом окне **Добавление контроллера** введите "Главный_контроллер" в качестве имени контроллера. Задайте для пункта **Шаблон параметров формирования шаблонов** значение  **Контроллер MVC с действиями чтения и записи и представлениями с использованием Entity Framework**.

6. Выберите **Контакт** как класс модели и **&lt;Новый контекст данных... >** как класс контекста данных.

	![Диалоговое окно "Добавление контроллера"][addcode002]<br/>

7. В диалоговом окне **Новый контекст данных** примите значение по умолчанию *ContactManager.Models.ContactManagerContext*.

	<br/>![Диалоговое окно "Добавление контроллера"][rxNewCtx]<br/>

8. Нажмите кнопку **ОК**, затем **Добавить** в диалоговом окне **Добавить контроллер**.<br/>

9. В диалоговом окне переопределения **Добавить контроллер** убедитесь, что все параметры выбраны и нажмите кнопку **ОК**.

	![Окно сообщения "Добавить контроллер"][rxOverwrite] <br/>

	Visual Studio создает методы и представления контроллера для операций создания, чтения, обновления и удаления в базе данных для объектов **Контакт**.

## Включение Migrations, создание базы данных, добавление примера данных и инициализатора данных ##

Следующая задача заключается в активации компонента [Code First Migrations](http://curah.microsoft.com/55220) для создания базы данных на основе созданной модели данных.

1. В меню **Сервис** выберите **Диспетчер пакетов библиотеки**, а затем **Консоль диспетчера пакетов**.

	!["Консоль диспетчера пакетов" в меню "Сервис"][addcode008]

2. В окне **Консоль диспетчера пакетов** введите следующую команду:<br/>

		enable-migrations -ContextTypeName ContactManagerContext

	<br/>![enable-migrations][rxE] <br/>
	
	Необходимо указать имя типа контекста (**ContactManagerContext**), так как проект содержит два производных класса [DbContext][dbcontext-link]: только что добавленный **ContactManagerContext** и класс **UsersContext**, который используется для базы данных участников. Класс **ContactManagerContext** был добавлен в мастер формирования шаблонов Visual Studio.<br/>
  
	Команда **enable-migrations** создает папку *Migrations* и размещает в этой папке файл *Configuration.cs*, который можно использовать для настройки миграций. <br/>

2. В окне **Консоль диспетчера пакетов** введите следующую команду:<br/>

		add-migration Initial

	Команда **add-migration Initial** создает класс с именем **&lt;date_stamp&gt;Initial**, который в свою очередь создает базу данных. Первый параметр ( *Initial*) является произвольным и используется для создания имени файла. Новые файлы классов можно просмотреть в **Обозревателе решений**.<br/>

	В классе **Initial** метод **Up** создается таблицу Contacts, а метод **Down** (используется при возвращении к предыдущему состоянию) удаляет эту таблицу.<br/>

3. Щелкните правой кнопкой мыши папку Migrations и откройте файл **Configuration.cs**. 

4. Добавьте следующее пространства имен. 

    	 использование ContactManager.Models;

5. Замените метод *Seed* следующим кодом:
		
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

	Приведенный выше код инициализирует базу данных с контактной информацией. Дополнительные сведения об инициализации базы данных см. в разделе [Инициализация баз данных Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. В окне **Консоль диспетчера пакетов** введите команду:

		update-database

	![Команды консоли диспетчера пакетов][addcode009]

	Команда **update-database** выполняет первую миграцию, при которой создается база данных. По умолчанию база данных создается как база данных LocalDB в SQL Server Express. (Если решение SQL Server Express не установлено, база данных создается с помощью экземпляра SQL Server Express.)

7. Для запуска приложения нажмите сочетание клавиш CTRL+F5. 

Приложение показывает начальные данные и предоставляет ссылки для изменения, удаления и просмотра подробных сведений.

<br/>![Представление MVC для данных][rxz3]

<h2><a name="bkmk_addview"></a>Редактирование просмотра</h2>

1. Перейдите в папку Views\Home и откройте файл Index.cshtml.
	
	<!--<br/>![Изменение index.cshtml в контекстном меню папок views\home][addcode004]-->

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

	<br/><br/>![Добавление таблицы стилей в контекстном меню папки Content][addcode005]

4. В диалоговом окне **Добавить новый элемент** введите **Стиль** в строке поиска, расположенной в правом верхнем углу, затем выберите **Таблица стилей**.
	<br/>![Диалоговое окно "Добавление нового элемента"][rxStyle]

5. Присвойте файлу имя Contacts.css и выберите **Добавить** Замените содержимое файла на код, приведенный ниже.
    
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

6. Разверните папку App\_Start и откройте файл BundleConfig.cs.

	<!--<br/>![Изменение BundleConfig.cs в контекстном меню папки App_Start][addcode007]-->

7. Добавьте следующую инструкцию для регистрации подключаемого модуля [Knockout](http://knockoutjs.com/index.html "KO").

		bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
		            "~/Scripts/knockout-{version}.js"));
	В этом примере knockout используется для упрощения динамического кода JavaScript, который обрабатывает шаблоны экрана.

8. Измените запись contents/css для регистрации таблицы стилей contacts.css. Измените следующую строку:

        bundles.Add(new StyleBundle("~/Content/css").Include("~/Content/site.css"));
на:

        bundles.Add(new StyleBundle("~/Content/css").Include(
                    "~/Content/site.css",
                    "~/Content/contacts.css"));

<h2><a name="bkmk_addwebapi"></a>Добавление контроллера для веб-интерфейса API Restful</h2>

1. В **Обозревателе решений** щелкните правой кнопкой мыши "Контроллеры" и выберите **Добавить**, а затем щелкните **Контроллер...**. 

4. В диалоговом окне **Добавить контроллер** введите ContactsController в качестве имени контроллера, выберите шаблон **Контроллер с действиями чтения и записи и представлениями, использующий Entity Framework**. 

5. В поле **Класс модели** выберите *Contact (ContactManager.Models)*, а в поле **Класс контекста данных** выберите *ContactManagerContext (ContactManager.Models)*.

	<br/>![Добавление контролера API][rxAddApiController]<br/>

6. Щелкните **Добавить**.

### Локальный запуск приложения

1. Для запуска приложения нажмите сочетание клавиш CTRL+F5.<br/>

	![Страница индексации][intro001]

2. Введите контакт и нажмите кнопку **Добавить** Приложение возвращается на главную страницу и отображает введенный контакт.<br/>

	![Страница индексации с элементами списка дел][addwebapi004]

3. В браузере добавьте к URL-адресу /api/contacts.

	Итоговый URL-адрес будет выглядеть аналогично http://localhost:1234/api/contacts. Добавленный Веб-интерфейс API RESTful возвращает сохраненные контакты.<br/> FireFox и Chrome отображают данные в формате XML.<br/>

	![Страница индексации с элементами списка дел][rxFFchrome]
	

	IE запрашивает открытие и сохранение контактов.

	![Диалоговое окно сохранения веб-интерфейса API][addwebapi006]
	
	
	Возвращенные контакты можно открыть в блокноте или браузере.
	
	Этот выход используется другим приложением, например мобильной веб-страницей или приложением.<br/>

	![Диалоговое окно сохранения веб-интерфейса API][addwebapi007]

<h2><a name="xsrf"></a><span class="short-header">XSRF</span>Добавление защиты XSRF</h2>

Подделка межсайтовых запросов (также называемая XSRF или CSRF) является атакой, направленной на размещенные веб-приложения, при которой вредоносный веб-сайт может влиять на взаимодействие между клиентским браузером и веб-сайтом, которому доверяет этот браузер. Эти атаки становятся возможными, потому что веб-браузеры отправляют маркеры проверки подлинности автоматически вместе с каждым запросом веб-сайта. Типичный пример — файл cookie для проверки подлинности, например билет проверки форм ASP.NET. Однако веб-сайты, которые используют какой-либо постоянный механизм поверки подлинности (например, проверку подлинности Windows или обычную проверку подлинности), могут стать уязвимыми для этого вида атак.

Атака XSRF отличается от фишинга. При проведении фишинговой атаки требуется взаимодействие пользователя. В фишинговой атаке вредоносный веб-сайт практически идентичен целевому веб-сайту, поэтому жертва предоставляет атакующей стороне свои конфиденциальные данные. При атаке XSRF очень часто никаких действий со стороны жертвы не требуется. Злоумышленник больше полагается на то, что браузер автоматически отправляет все необходимые файлы cookie на целевой веб-сайт.

Дополнительные сведения см. в разделе [Открытый проект безопасности веб-приложений](https://www.owasp.org/index.php/Main_Page) (OWASP) [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).

1. В **Обозревателе решений** щелкните правой кнопкой пункт **Фильтры**, затем щелкните **Добавить** и **Класс**.

2. Назовите файл *ValidateHttpAntiForgeryTokenAttribute.cs* и добавьте следующий код:

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

1. Можно добавить атрибут **[ValidateHttpAntiForgeryToken]** в  **ContactsController** для защиты его от угроз XSRF. Более правильным подходом является добавление атрибута **ValidateHttpAntiForgeryToken** в файл *App_Start\WebApiConfig.cs* на глобальном уровне, как показано ниже:
  
        public static class WebApiConfig
        {
            public static void Register(HttpConfiguration config)
            {
                config.Routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { id = RouteParameter.Optional }
                );

                GlobalConfiguration.Configuration.Filters.Add(new ValidateHttpAntiForgeryTokenAttribute());
                //config.EnableQuerySupport();
            }
        }


<h2><a name="bkmk_deploydatabaseupdate"></a>Публикация обновления приложения в Windows Azure и базе данных SQL</h2>

Чтобы опубликовать приложение, повторите процедуры, которые описаны выше.

1. Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Опубликовать**.

	<br/>![Опубликовать][rxP]<br/><br/>

5. Перейдите на вкладку **Параметры**.

	![Вкладка "Параметры" мастера веб-публикации][rxz4]<br/>

1. В разделе **ContactsManagerContext(ContactsManagerContext)** щелкните значок **v**, чтобы изменить *Строку удаленного подключения* на строку подключения для базы данных контактов.

6. Поле строки удаленного подключения для базы данных **ContactsManagerContext(ContactsManagerContext)** теперь содержит строку подключения к базе данных SQL, которая была предоставлена в файле *publishsettings*. Щелкните троеточие (**...**), чтобы посмотреть параметры *ContactDB*.<br/>

	<br/>![Параметры DB][rx22]<br/><br/>

7. Закройте **Диалоговое окно строки подключений к месту назначения**, затем в диалоговом окне **Публикация веб-сайта** установите флажок **Выполнить Code First Migrations (выполняется при запуске приложения)** для базы данных **UsersContext(DefaultConnection)**.<br/>

	![Вкладка "Параметры" мастера веб-публикации][rxz44]<br/>

1. Можно щелкнуть значок **^** рядом с базой данных **UsersContext(DefaultConnection)**, это сведения о подключении к базе данных членства, и мы не используем ее в этом учебнике. Для фактического приложения потребуется проверка подлинности и авторизация, и для этого будет использоваться база данных членства. См. раздел [Развертывание безопасного приложения ASP.NET MVC с OAuth, членством и базой данных SQL](http://www.windowsazure.com/ru-ru/develop/net/tutorials/web-site-with-sql-database/), который основан на данном учебнике и содержит сведения о развертывании веб-приложения с базой данных членства.

1. Щелкните **Далее**, затем **Предварительный просмотр**. В Visual Studio отображается список файлов, которые будут добавлены или обновлены.

8. Щелкните **Опубликовать**.<br/>
После завершения развертывания в браузере открывается главная страница приложения.<br/>

	![Страница индексации без контактов][intro001]

	Процесс публикации Visual Studio автоматически настроил строку подключения в развернутом файле *Web.config* для указания на базу данных SQL. Процесс также настроил Code First Migrations для автоматического обновления базы данных до последней версии при первом доступе приложения к базе данных после развертывания.

	В результате этой конфигурации Code First создает базу данных путем запуска кода в ранее созданном классе **Initial**. Это было сделано, когда приложение впервые попыталось получить доступ к базе данных после развертывания.

9. Введите контакт, как и при локальном запуске приложения, чтобы проверить успешность развертывания базы данных.

Когда вы увидите, что этот введенный элемент сохранен и показан на странице диспетчера контактов, вы будете знать, что он сохранен в базе данных.<br/>

![Страница индексации с контактами][addwebapi004]

Теперь приложение работает в облаке и использует базу данных SQL для хранения данных. После завершения тестирования приложения в Windows Azure, его необходимо удалить. Приложение является общим и не имеет механизма для ограничения доступа.

<h2><a name="nextsteps"></a>Следующие шаги</h2>

Для фактического приложения потребуется проверка подлинности и авторизация, и для этого будет использоваться база данных членства. Учебник [Развертывание безопасного приложения ASP.NET MVC с OAuth, членством и базой данных SQL](http://www.windowsazure.com/ru-ru/develop/net/tutorials/web-site-with-sql-database/) основан на данном учебнике и содержит сведения о развертывании веб-приложения с базой данных членства.
<br/>
Другой способ хранения данных в приложении Windows Azure — использование хранилища Windows Azure, которое обеспечивает хранение нереляционных данных в виде BLOB-объектов и таблиц. По следующим ссылкам приводятся дополнительные сведения о веб-интерфейсе API, ASP.NET MVC и Window Azure.
 

* [Многоуровневое приложение .NET, использующее таблицы хранилища, очереди и BLOB-объекты](http://www.windowsazure.com/ru-ru/develop/net/tutorials/multi-tier-web-site/1-overview/).
* [Общие сведения о работе с Entity Framework с использованием MVC][EFCodeFirstMVCTutorial]
* [Введение в ASP.NET MVC 4](http://www.asp.net/mvc/tutorials/mvc-4/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4)
* [Первый веб-интерфейса API ASP.NET](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)


Этот учебник и пример приложения были написаны [Риком Андерсоном (Rick Anderson)](http://blogs.msdn.com/b/rickandy/) (Твиттер [@RickAndMSFT](https://twitter.com/RickAndMSFT)) в соавторстве с Томом Дайкстрой (Tom Dykstra), Томом ФитцМакеном (FitzMacken) и Бэрри Доррансом (Barry Dorrans) (Твиттер [@blowdart](https://twitter.com/blowdart)). 

Пожалуйста, оставьте отзыв о том, что вам понравилось или что вы хотели бы улучшить, не только о самом учебнике, но и о продуктах, которые он демонстрирует. Ваши отзывы помогут нам определить, какие улучшения наиболее приоритеты. Мы особенно заинтересованы узнать, насколько всем хочется получить дополнительные сведения об автоматизации процесса настройки и развертывании базы данных членства. 

<!-- bookmarks -->
[Добавление поставщика OAuth]: #addOauth
[Добавление роли в базу данных членства]:#mbrDB
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
[rx22]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rx22.png
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


