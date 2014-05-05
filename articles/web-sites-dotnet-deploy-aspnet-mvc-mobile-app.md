<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="Мобильный веб-сайт ASP.NET MVC 4" pageTitle="Мобильный веб-сайт .NET ASP.NET MVC 4 — учебники Azure" metaKeywords="учебник Azure, учебник по веб-приложениям Azure, мобильное приложение Azure, Azure ASP.NET MVC 4, ASP.NET MVC" description="Учебный курс, объясняющий, как развертывать веб-приложение на веб-сайте Azure, используя в веб-приложении ASP.NET MVC 4 компоненты для мобильных устройств." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Развертывание мобильного веб-приложения ASP.NET MVC на веб-сайтах Azure" authors="tdykstra" solutions="" manager="" editor="" />





# Развертывание мобильного веб-приложения ASP.NET MVC на веб-сайтах Azure

***Автор: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT) Обновление: 26 июня 2013 г.***

Этот учебник помогает освоить основы развертывания веб-приложения на веб-сайте Azure. Для целей этого руководства в веб-приложении ASP.NET MVC 4 будут использоваться компоненты, предназначенные для мобильных устройств. Для выполнения шагов данного руководства можно использовать Microsoft Visual Studio 2012. Можно также использовать [Visual Studio Express 2012][] — бесплатную версию Microsoft Visual Studio. 

<h2>Вы узнаете следующее:</h2>

- Как использовать шаблоны ASP.NET MVC 4 атрибута окна просмотра HTML5 и адаптивную отрисовку для улучшения отображения на мобильных устройствах.
- Как создать представления для мобильных устройств.
- Как создать переключатель представления, позволяющий пользователям переключаться в приложении между представлением для мобильных устройств и представлением для настольных компьютеров.
- Как развернуть веб-приложение в Azure.

В данном учебнике предстоит добавить компоненты для мобильных устройств в простое приложение списка конференции, приведенное в начальном проекте. На следующем снимке экрана показана главная страница готового приложения, выводимая в эмуляторе Windows Phone 7.

![Главная страница приложения MVC4 для конференции.][AppMainPage]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2>Настройка среды разработки</h2>

Настройте среду разработки, установив пакет Azure SDK для платформы .NET Framework. 

1. Чтобы установить пакет Azure SDK для .NET, щелкните приведенную ниже ссылку. Если среда Visual Studio 2012 еще не установлена, ее можно установить по ссылке. Для этого учебного курса требуется Visual Studio 2012. 
[Azure SDK для Visual Studio 2012]( http://go.microsoft.com/fwlink/?LinkId=254364)
1. При появлении приглашения запустить или сохранить исполняемый файл установки выберите **Запустить**.
1. В окне установщика веб-платформы нажмите кнопку **Установить** и продолжайте установку.

![Установщик веб-платформы - Azure SDK для .NET][WebPIAzureSdk20NetVS12]

Кроме того, понадобится эмулятор браузера для мобильного устройства. Можно использовать любое из следующих средств:

- [Эмулятор Windows 7 Phone][Win7PhoneEmulator]. (Это эмулятор, используемый для большинства снимков экрана в этом руководстве.)
- Измените строку агента пользователя для эмуляции iPhone. См. [эту запись блога][setuseragent] компьютерных фанатов.
- [Эмулятор Opera для мобильных устройств][OperaMobileEmulator].
- [Apple Safari][AppleSafari] с агентом пользователя, настроенным для iPhone. Инструкции по настройке для агента пользователя в Safari значения "iPhone" см. в статье блога Дэвида Элисона (David Alison), описывающей, [как позволить Safari заменить IE][HowToSafari].
- [FireFox][FireFox] с [переключателем агента пользователя для FireFox][FireFoxUserAgentSwitcher].

Код в этом учебнике показывается на языке C#. Но начальный проект и завершенный проект также будут доступны на языке Visual Basic. К этой статье прилагаются следующие проекты Visual Studio с исходным кодом на языках Visual Basic и C#:

- [Загрузка начального проекта][MVC4StarterProject]
- [Загрузка завершенного проекта][FinishedProject]

<h2>Этапы, описываемые в этом учебнике</h2>

- [Создание веб-сайта Azure](#bkmk_CreateWebSite)
- [Настройка начального проекта](#bkmk_setupstarterproject)
- [Переопределение представлений, макетов и частичных представлений][]
- [Определение интерфейса браузера для мобильного устройства с помощью jQuery Mobile][]
- [Улучшение списка докладчиков][]
- [Создание представления докладчиков для мобильных устройств][]
- [Улучшение списка тегов][]
- [Улучшение списка дат][]
- [Улучшение представления SessionsTable][]
- [Улучшение представления SessionByCode][]
- [Развертывание приложения на веб-сайте Azure][]

<h3><a name="bkmk_CreateWebSite"></a>Создание веб-сайта в Azure</h3>

Ваш веб-сайт Azure будет работать в общей среде размещения, а это означает, что он работает на виртуальных машинах, используемых совместно с другими клиентами Azure. Общая среда размещения — недорогой способ начать работу в облаке. Если позднее ваш веб-трафик увеличится, приложение можно масштабировать по необходимости, запуская его на выделенных виртуальных машинах. Если требуется более сложная архитектура, можно перейти в облачную службу Azure. Облачные службы работают на выделенных виртуальных машинах, которые можно настроить в соответствии с конкретными потребностями.

1.	Выполните вход на [портал управления Azure][managementportal]. На портале управления выберите **Создать**.

	![][CreateWebSite1]

2.	Щелкните **Веб-сайт**, затем щелкните **Быстрое создание**.

	![][CreateWebSite2]

3.	В разделе **Создать новый веб-сайт** введите в поле **URL-адрес** строку, которая будет использоваться как уникальный URL-адрес приложения.

	![][CreateWebSite3]

	Полный URL-адрес будет состоять из введенной здесь строки и суффикса, который отображается под текстовым полем. На рисунке показана строка "MyMobileMVC4WebSite", но если этот URL-адрес уже занят, придется выбрать другой. Выберите **Область**, в которой вы находитесь.

4. Чтобы закончить работу, щелкните значок галочки в нижней части окна.

Портал управления возвращается на страницу веб-сайтов, а в столбце "Состояние" показывается, что идет создание веб-сайта. Через какое-то время (обычно меньше минуты) в столбце "Состояние" сообщается об успешном создании сайта. В строке навигации слева число сайтов для вашей учетной записи отображается в значке веб-сайтов, а число баз данных — в значке баз данных SQL.

![][CreateWebSite4]

<h3><a name="bkmk_setupstarterproject"></a>Настройте начальный проект.</h3>

1.	Загрузите [начальный проект приложения списка конференции][MVC4StarterProject].

2. 	Затем в проводнике Windows Explorer щелкните правой кнопкой мыши файл MvcMobileStarterBeta.zip и выберите *Свойства*.

3. 	В диалоговом окне "Свойства MvcMobileRTMStarter.zip" нажмите кнопку "Разблокировать". (Разблокировка устраняет предупреждение системы безопасности, выводимое при попытке использовать ZIP-файл, загруженный из Интернета.)

	![Диалоговое окно "Свойства".][PropertiesPopup]

4.	Щелкните правой кнопкой мыши файл MvcMobile.zip и выберите "Извлечь все", чтобы распаковать файл.

5. 	В Visual Studio откройте файл MvcMobile.sln.

<h3>Запуск начального проекта</h3>

1.	Нажмите сочетание клавиш CTRL + F5 для запуска приложения, которое будет отображаться в браузере настольного компьютера.
2.	Запустить эмулятор браузера мобильного устройства, скопируйте в эмулятор URL-адрес приложения конференции и щелкните ссылку "Поиск по тегу".
	- При использовании эмулятора Windows Phone щелкните в строке URL-адреса и нажмите кнопку паузы, чтобы получить доступ с клавиатуры. На следующем рисунке показано представление AllTags (выбираемое из списка "Поиск по тегу").

	![Страница "Поиск по тегу".][BrowseByTagWithCallout]

Этот экран является очень удобным для чтения на мобильном устройстве. Выберите ссылку ASP.NET.

![Просмотр сеансов, помеченных как ASP.NET.][ASPNetPage]

Просмотр тегов ASP.NET оказывается очень запутанным. Например, очень трудно читать столбец даты. Далее в этом руководстве будет создана версия представления AllTags, специально предназначенная для браузеров мобильных устройств и делающая изображение удобным для чтения.

<h2><a name="bkmk_overrideviews"></a>Переопределение представлений, макетов и частичных представлений</h2>

В этом разделе для мобильных устройств будет создан специальный файл макета.

Важный компонент в ASP.NET MVC 4 — это простой механизм, позволяющий переопределить любое представление (включая макеты и частичные представления) для браузеров мобильных устройств вообще, для отдельного браузера мобильных устройств или для любого конкретного браузера. Чтобы обеспечить представление для мобильных устройств, можно скопировать файл представления и добавить расширение .Mobile к имени файла. Например, чтобы создать мобильное представление Index, скопируйте *Views\Home\Index.cshtml* в *Views\Home\Index.Mobile.cshtml*.

Для начала скопируйте *Views\Shared\\_Layout.cshtml* в *Views\Shared\\_Layout.Mobile.cshtml*. Откройте *_Layout.Mobile.cshtml* и измените заголовок с **Конференция MVC4** на **Конференция (мобильная)**.

В каждом вызове **Html.ActionLink** удалите "Поиск по" для каждой ссылки ActionLink. В следующем коде показан законченный основной раздел (body) файла разметки для мобильных устройств.

     <body>
        <div class="page">
            <div id="header">
                <div id="logindisplay"></div>
                <div id="title">
                    <h1> Конференция (мобильная)</h1>
                </div>
                <div id="menucontainer">
                    <ul id="menu">
                        <li>@Html.ActionLink("Home", "Index", "Home")</li>
                        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
                        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
                        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
                    </ul>
                </div>
            </div>
            <div id="main">
                @RenderBody()
            </div>
            <div id="footer">
            </div>
        </div>
    </body>

Скопируйте файл *Views\Home\AllTags.cshtml* в файл *Views\Home\AllTags.Mobile.cshtml*. Откройте новый файл и измените элемент &lt;h2&gt; с "Tags" на "Tags (M)":

     <h2>Tags (M)</h2>

Перейдите на страницу тегов, используя браузер для настольного компьютера и эмулятор браузера для мобильных устройств. Эмулятор браузера для мобильных устройств показывает два внесенных изменения.

![Показать изменения на странице тегов][Overrideviews1]

Напротив, изображение для настольного компьютера не изменилось.

![Показать представление тегов для настольного компьютера][Overrideviews2]

<h2><a name="bkmk_usejquerymobile"></a>Определение интерфейса браузера для мобильного устройства с помощью jQuery Mobile</h2>

В этом разделе будет установлен пакет jQuery.Mobile.MVC NuGet, устанавливающий jQuery Mobile и графический элемент переключателя представлений.

Библиотека [JQuery Mobile][jquerydocs] предоставляет платформу пользовательского интерфейса, работающую на всех основных браузерах для мобильных устройств. jQuery Mobile использует последовательное улучшение в браузерах для мобильных устройств, поддерживающих CSS и JavaScript. Последовательное улучшение позволяет всем браузерам отображать базовый контент веб-страницы, при этом обеспечивая для более мощных браузеров и устройств более богатые возможности отображения. Файлы JavaScript и CSS, включенные в состав jQuery Mobile, без изменения разметки реализуют для многих элементов стиль, соответствующий браузерам для мобильных устройств.

1. Удалите созданный ранее файл *Shared\\_Layout.Mobile.cshtml*.

2. Переименуйте файл *Views\Home\AllTags.Mobile.cshtml* в *Views\Home\AllTags.Mobile.cshtml.hide* (этот файл будет позднее использован еще раз). Так как у этого файла больше нет расширения .cshtml, он не будет использоваться средой выполнения ASP.NET MVC для отображения представления *AllTags*.

3. Установите пакет jQuery.Mobile.MVC NuGet, выполнив следующие действия:

	a. В меню **Сервис** выберите консоль **Диспетчер пакетов**, а затем выберите **Диспетчер пакетов библиотек**.

		![Диспетчер пакетов библиотек][jquery1]
	
	b. В **Консоли диспетчера пакетов** введите *Install-Package jQuery.Mobile.MVC -version 1.0.0*

		![Консоль диспетчера пакетов][jquery2]

Пакет jQuery.Mobile.MVC NuGet устанавливает следующие элементы:

- Файл *App_Start\BundleMobileConfig.cs*, необходимый для ссылки на файлы JavaScript и CSS в jQuery. Необходимо выполнить следующие инструкции и сослаться на набор для мобильных устройств, определенный в этом файле.
- CSS-файлы jQuery Mobile.
- Графический элемент контроллера ViewSwitcher (*Controllers\ViewSwitcherController.cs)*. 
- JavaScript-файлы jQuery Mobile.
- Файл макета в стиле jQuery Mobile (*Views\Shared\_Layout.Mobile.cshtml*). 
- Частичное представление переключателя представлений (*MvcMobile\Views\Shared\_ViewSwitcher.cshtml*), содержащее в верхней части каждой страницы ссылку для переключения из представления для настольного компьютера в представление для мобильных устройств и обратно.
- Несколько файлов изображений .gif и .png в папке Content\images. 

Откройте файл *Global.asax* и добавьте следующий код в качестве последней строки метода Application_Start.

 	BundleMobileConfig.RegisterBundles(BundleTable.Bundles);

В следующем коде показан законченный файл Global.asax.

	using System; 
	using System.Web.Http; 
	using System.Web.Mvc; 
	using System.Web.Optimization; 
	using System.Web.Routing; 
	using System.Web.WebPages; 
	 
	namespace MvcMobile 
	{ 
	 
	    public class MvcApplication : System.Web.HttpApplication 
	    { 
	        protected void Application_Start() 
	        { 
	            DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone") 
	            { 
	                ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf 
	                    ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0) 
	            }); 
	            AreaRegistration.RegisterAllAreas(); 
	 
	            WebApiConfig.Register(GlobalConfiguration.Configuration); 
	            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters); 
	            RouteConfig.RegisterRoutes(RouteTable.Routes); 
	            BundleConfig.RegisterBundles(BundleTable.Bundles); 
	            BundleMobileConfig.RegisterBundles(BundleTable.Bundles); 
	        } 
	    } 
	}

Откройте файл *MvcMobile\Views\Shared\\_Layout.Mobile.cshtml* и добавьте следующую разметку сразу же после вызова *Html.Partial*:

	<div data-role="header" align="center">
	    @Html.ActionLink("Home", "Index", "Home")
	    @Html.ActionLink("Date", "AllDates")
	    @Html.ActionLink("Speaker", "AllSpeakers")
	    @Html.ActionLink("Tag", "AllTags")
	</div>

Законченный раздел body выглядит следующим образом:

	<body>
	    <div data-role="page" data-theme="a">
	        @Html.Partial("_ViewSwitcher")
	        <div data-role="header" align="center">
	            @Html.ActionLink("Home", "Index", "Home")
	            @Html.ActionLink("Date", "AllDates")
	            @Html.ActionLink("Speaker", "AllSpeakers")
	            @Html.ActionLink("Tag", "AllTags")
	        </div>
	        <div data-role="header">
	            <h1>@ViewBag.Title</h1>
	        </div>
	        <div data-role="content">
	            @RenderSection("featured", false)
	            @RenderBody()
	        </div>
	    </div>
	</body>

Выполните сборку приложения и в используемом эмуляторе браузера для мобильных устройств перейдите в режим AllTags. Отображается следующее:

![После установки jquery с помощью nuget.][jquery3]

<div class="dev-callout"> 
<b>Примечание</b> 
<p>Можно отлаживать код, специфический для мобильных устройств, задавая строку агента пользователя для IE или Chrome, соответствующую для iPhone, а затем вызывая средства разработчика клавишей F12.  Если в браузере мобильного устройства ссылки <strong>Главная</strong>, <strong>Докладчик</strong>, <strong>Тег</strong> и <strong>Дата</strong> не отображаются как кнопки, возможно, указаны неправильные ссылки на скрипты jQuery Mobile и CSS-файлы.</p> 
</div>

Помимо изменений стиля, появятся **Отображение представления для мобильных устройств** и ссылка, которая позволяет переключаться между представлением для мобильных устройств и представлением для настольного компьютера. Выберите **Ссылка на представление для настольного компьютера**, чтобы появилось представление для настольного компьютера.

<!--![Display desktop view][jquery4]-->

Представление для настольного компьютера не позволяет вернуться прямо в представление для мобильных устройств. Но сейчас это будет исправлено. Откройте файл *Views\Shared\\_Layout.cshtml*. Прямо под элементом &lt;body> добавьте следующий код, выводящий графический элемент переключателя представлений:

    @Html.Partial("_ViewSwitcher")

Ниже приведен законченный код:

	<body>
	    @Html.Partial("_ViewSwitcher")
	
	    <div id="title">
	        <h1> Конференция MVC4 </h1>
	    </div>

		@*Для ясности элементы удалены.*@
	</body>

Обновление представления **AllTags** появляется в браузере для мобильных устройств. Теперь можно переключаться между представлениями для настольных компьютеров и мобильных устройств.

![Переход к представлениям для мобильных устройств.][jquery5]

<div class="dev-callout"> 
<b>Примечание</b> 
<p>В конец файла Views\Shared\_ViewSwitcher.cshtml можно добавить следующий код, чтобы упростить отладку представлений при использовании браузера, в котором строка агента пользователя настроена для мобильного устройства.</p> 

<pre>
	else 
	{ 
	     @:Not Mobile/Get 
	} 
</pre>
<p>и добавление следующего заголовка в файл Views\Shared\_Layout.cshtml.</p> 
<pre>
	&lt;h1>Конференция MVC4 с макетом не для мобильных устройств&lt;/h1>
</pre>
</div>

Перейдите на страницу AllTags в браузере для настольного компьютера. Графический элемент переключателя вида не отображается в браузере для настольного компьютера, поскольку он добавляется только на страницу макета для мобильных устройств. Далее в этом руководстве будет показано, как добавить графический элемент переключателя представления для настольного компьютера.

![Просмотр представления для настольного компьютера.][jquery6]

<h2><a name="bkmk_Improvespeakerslist"></a> Улучшение списка докладчиков</h2>

В браузере для мобильных устройств выберите ссылку **Докладчики**. Так как представление для мобильных устройств (*AllSpeakers.Mobile.cshtml*) отсутствует, докладчики по умолчанию (*AllSpeakers.cshtml*) отображаются, используя представление с макетом для мобильных устройств (*_Layout.Mobile.cshtml*).

![Просмотр списка докладчиков в представлении для мобильных устройств.][SpeakerList1]

Можно глобально отключить представление по умолчанию (не для мобильных устройств) из отображения в макете для мобильных устройств, задав для параметра RequireConsistentDisplayMode значение true в файле *Views\_ViewStart.cshtml*, подобно следующему:

![][SpeakerList2]

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModes.RequireConsistentDisplayMode = true;
    }
Если значение *RequireConsistentDisplayMode* равно true, макет для мобильных устройств (*_Layout.Mobile.cshtml*) используется только для мобильных представлений. (То есть для файла представления используется форма ViewName.Mobile.cshtml.) Может понадобиться установить для *RequireConsistentDisplayMode* значение true, если макет для мобильных устройств не работает с представлениями, не предназначенными для мобильных устройств. На следующем снимке экрана показано, как страница "Докладчики" отображается при значении *RequireConsistentDisplayMode*, равном true.

![][SpeakerList4]

Согласованный режим отображения можно отключить, задав в файле представления для *RequireConsistentDisplayMode* значение false. Следующая разметка в файле *Views\Home\AllSpeakers.cshtml* устанавливает для *RequireConsistentDisplayMode* значение false:

    @model IEnumerable<string>
    @{
        ViewBag.Title = "All speakers";
        DisplayModes.RequireConsistentDisplayMode = false;
    }
<h2><a name="bkmk_mobilespeakersview"></a>Создание представления докладчиков для мобильных устройств</h2>

Как было только что показано, представление "Докладчики" удобно для чтения, но ссылки малы, и касаться их на мобильном устройстве трудно. В этом разделе будет создано представление "Докладчики", специфическое для браузеров мобильных устройств и выглядящее как современное мобильное приложение - оно содержит большие, удобные для касания ссылки и поле поиска, позволяющее быстро находить докладчиков.

1. Скопируйте файл *AllSpeakers.cshtml* в *AllSpeakers.Mobile.cshtml.* Откройте файл *AllSpeakers.Mobile.cshtml* и удалите элемент заголовка &lt;h2&gt;.
2. В тег **&lt;ul&gt;** добавьте атрибут data-role и задайте для него значение *listview*. Как и другие атрибуты *data-**, *data-role="listview"* создает большие элементы списка, удобные для касания. Законченная разметка выглядит примерно так:

	    @model IEnumerable<string>
	    @{
	        ViewBag.Title = "Все докладчики";
	    }
	    <ul data-role="listview">
	        @foreach(var speaker in Model) {
	            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
	        }
	    </ul>

3.	Обновите браузер для мобильных устройств. Обновленное представление выглядит следующим образом:

	![][MobileSpeakersView1]

4.	В тег **&lt;ul&gt;** добавьте атрибут data-filter и задайте для него значение true. В следующем примере кода показана разметка ul.

		<ul data-role="listview" data-filter="true">

На следующем рисунке показано поле фильтра поиска в верхней части страницы, создаваемое атрибутом data-filter.

![][MobileSpeakersView2]

При вводе каждой буквы в поле поиска jQuery Mobile фильтрует отображаемый список, как показано на следующем рисунке.

![][MobileSpeakersView3]

<h2><a name="bkmk_improvetags"></a> Улучшение списка тегов</h2>

Подобно представлению "Докладчики" по умолчанию, представление "Теги" удобно для чтения, но ссылки малы, и касаться их на мобильном устройстве трудно. В этом разделе представление "Теги" будет исправлено, аналогично представлению "Докладчики".

1. Переименуйте файл *Views\Home\AllTags.Mobile.cshtml.hide* в *Views\Home\AllTags.Mobile.cshtml*. Откройте переименованный файл и удалите элемент **&lt;h2&gt;**.

2. Добавьте атрибуты data-role и data-filter в тег **&lt;ul&gt;**, как показано ниже:

		<ul data-role="listview" data-filter="true">
На следующем рисунке показана страница тегов, отфильтрованная по букве J.

![][TagsList1]

<h2><a name="bkmk_improvedates"></a> Улучшение списка дат</h2>

Представление дат можно улучшить, аналогично представлениям **Докладчики** и **Теги**, чтобы упростить его использование на мобильном устройстве.

1. Скопируйте файл *Views\Home\AllDates.Mobile.cshtml* в *Views\Home\AllDates.Mobile.cshtml*.
2. Откройте новый файл и удалите элемент **&lt;h2&gt;**.
3. Добавьте *data-role="listview"* в тег &lt;ul&gt; подобно следующему:

		<ul data-role="listview">

На следующем рисунке показано, как выглядит страница **Дата** с добавленным атрибутом data-role.

![][DatesList1]

Замените содержимое файла *Views\Home\AllDates.Mobile.cshtml* следующим кодом:

    @model IEnumerable<DateTime>
    @{
        ViewBag.Title = "All dates";
        DateTime lastDay = default(DateTime);
    }
    <ul data-role="listview">
        @foreach(var date in Model) {
            if (date.Date != lastDay) {
                lastDay = date.Date;
                <li data-role="list-divider">@date.Date.ToString("ddd, MMM dd")</li>
            }
            <li>@Html.ActionLink(date.ToString("h:mm tt"), "SessionsByDate", new { date })</li>
        }
    </ul>
Этот код группирует все сеансы по дням. Он создает разделитель списка для каждого нового дня и выводит под разделителем список всех сеансов для каждого дня. Вот как выглядит выполнение этого кода:

![][DatesList2]

<h2><a name="bkmk_improvesessionstable"></a> Улучшение представления SessionsTable</h2>

В этом разделе для мобильных устройств будет создано специальное представление сеансов. Вносимые изменения будут серьезнее, чем в других, уже созданных представлениях.

В браузере мобильного устройства коснитесь кнопки **Докладчик**, а затем введите Sc в поле поиска.

![][SessionView1]

Коснитесь ссылки **Scott Hanselman** (Скотт Хансельман).

![][SessionView2]

Как можно видеть, изображение трудно читать в браузере для мобильного устройства. Столбец даты трудно читать, а столбец тегов оказывается вне представления. Для исправления скопируйте файл Views\*Home\SessionsTable.cshtml* в *Views\Home\SessionsTable.Mobile.cshtml*, а затем замените содержание файла следующим кодом:

    @using MvcMobile.Models
    @model IEnumerable<Session>
	
    <ul data-role="listview">
        @foreach(var session in Model) {
            <li>
                <a href="@Url.Action("SessionByCode", new { session.Code })">
                    <h3>@session.Title</h3>
                    <p><strong>@string.Join(", ", session.Speakers)</strong></p>
                    <p>@session.DateText</p>
                </a>
            </li>
        }
    </ul>
Этот код удаляет столбцы помещений и тегов, а также форматирует заголовок, докладчика и дату вертикально, чтобы все эти сведения были удобны для чтения в браузере мобильного устройства. На следующем рисунке показаны изменения кода.

![][SessionView3]

<h2><a name="bkmk_improvesessionbycode"></a> Улучшение представления SessionByCode</h2>

И, наконец, будет создано представление **SessionByCode**, предназначенное специально для мобильных устройств. В браузере мобильного устройства коснитесь кнопки **Докладчик**, а затем введите Sc в поле поиска.

![][SessionByCode1]

Коснитесь ссылки **Scott Hanselman** (Скотт Хансельман). Появляются сеансы для Скотта Хансельмана.

![][SessionByCode2]

Выберите ссылку **An Overview of the MS Web Stack of Love** (Обзор любимого интернет-стека Майкрософт).

![][SessionByCode3]

Представление для настольного компьютера выглядит замечательно, но его можно улучшить.

Скопируйте файл *Views\Home\SessionByCode.cshtml* в *Views\Home\SessionByCode.Mobile.cshtml* и замените содержание файла *Views\Home\SessionByCode.Mobile.cshtml* следующей разметкой:


    @model MvcMobile.Models.Session
	
    @{
        ViewBag.Title = "Сведения о сеансах";
    }
    <h2>@Model.Title</h2>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>
	
    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Speakers</li>
        @foreach (var speaker in Model.Speakers) {
            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
        }
    </ul>
	
    <p>@Model.Description</p>
    <h4>Code: @Model.Code</h4>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Tags</li>
        @foreach (var tag in Model.Tags) {
            <li>@Html.ActionLink(tag, "SessionsByTag", new { tag })</li>
        }
    </ul>
Новая разметка использует атрибут **data-role** для улучшения макета представления.

Обновите браузер для мобильных устройств. Следующий рисунок отражает только что внесенные в код изменения:

![][SessionByCode4]

<h2><a name="bkmk_deployapplciation"></a> Развертывание приложения на веб-сайте Azure</h2>

1. В Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать** в контекстном меню.

	!["Опубликовать" в контекстном меню проекта][PublishVSSolution]

	Откроется мастер **Публикация веб-сайта**.
2. На вкладке **Профиль** мастера **Публикация веб-сайта** выберите **Импорт**.

	![Параметры импорта публикации][ImportPublishSettings]

	Появится диалоговое окно **Импорт профиля публикации**.

3. Если подписка Azure еще не добавлена в Visual Studio, выполните следующие действия. После добавления существующей подписки в раскрывающемся списке в разделе **Импорт с веб-сайта Azure** появится и ваш веб-сайт.
	1. В диалоговом окне **Импорт профиля публикации** щелкните **Добавить подписку Azure**. 

	![add win az sub](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzAddWAsub.png)

	1. В диалоговом окне **Импорт подписок Azure** щелкните **Загрузить файл подписки**.
    
	![download sub](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)
    
	1. В окне браузера сохраните файл *.publishsettings*.
    
	![download pub file](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDown2.png)
    
	[WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]
</br>
    
	1. В диалоговом окне **Импорт подписок Azure** нажмите кнопку **Обзор** и перейдите к файлу *.publishsettings*.
    
	![download sub](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)
    
	1. Щелкните **Импорт**.
    
	![импорт](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzImp.png)

	7. В диалоговом окне **Импорт профиля публикации** выберите **Импорт с веб-сайта Azure**, выберите свой веб-сайт в раскрывающемся списке, затем нажмите кнопку **ОК**.

	![Импорт профиля публикации][ImportPublishProfile]


	8. На вкладке **Подключение** щелкните **Проверить подключение**, чтобы убедиться в правильности задания параметров.

	![Проверка подключения][ValidateConnection]

	9. После проверки подключения рядом с кнопкой **Проверить подключение** отображается зеленая галочка.
	![Значок успешного подключения и кнопка "Далее" на вкладке "Подключение"][firsdeploy007]

	10. На этой странице можно принять все значения по умолчанию.  Вы развертываете конфигурацию построения "Выпуск", и вам не требуется удалять файлы на целевом сервере. Запись **UsersContext (DefaultConnection)** в разделе **Базы данных** берется из класса *UsersContext:DbContext*, в котором используется строка DefaultConnection. 
Нажмите кнопку **Далее**.

	![Значок успешного подключения и кнопка "Далее" на вкладке "Подключение"][rxPWS]

	12. На вкладке **Предварительный просмотр** нажмите кнопку **Начало предварительного просмотра**.
На вкладке отображается список файлов, которые будут копироваться на сервер. Предварительный просмотра не является обязательным для публикации приложения, но это полезная функция, о которой нужно знать. В этом случае вам не нужно ничего делать со списком показанных файлов. При следующей публикации список предварительного просмотра будет содержать только измененные файлы.

	![Кнопка "Начало предварительного просмотра" на вкладке "Предварительный просмотр"][firsdeploy009]

	12. Щелкните **Опубликовать**.

	Visual Studio начинает процесс копирования файлов на сервер Azure. В окне **Вывод** указывается, какие действия по развертыванию были выполнены, а также сообщается об успешном завершении развертывания.

	15. Браузер по умолчанию автоматически открывает URL-адрес развернутого сайта. Созданное вами приложение теперь выполняется в облаке.

	![][DeployApplication10]

Можно проверить работу веб-сайта с помощью эмулятора телефона, перейдя по URL-адресу сайта в браузере для мобильных устройств.

<!-- Internal Links -->
[Создание веб-сайта Azure]: #bkmk_createaccount
[Настройка начального проекта]: #bbkmk_setupstarterproject
[Переопределение представлений, макетов и частичных представлений]: #bkmk_overrideviews
[Определение интерфейса браузера для мобильного устройства с помощью jQuery Mobile]: #bkmk_usejquerymobile
[Улучшение списка докладчиков]: #bkmk_Improvespeakerslis
[Создание представления докладчиков для мобильных устройств]: #bkmk_mobilespeakersview
[Улучшение списка тегов]: #bkmk_improvetags
[Улучшение списка дат]: #bkmk_improvedates
[Улучшение представления SessionsTable]: #bkmk_improvesessionstable
[Улучшение представления SessionByCode]: #bkmk_improvesessionbycode
[Развертывание приложения на веб-сайте Azure]: #bkmk_deployapplciation

<!-- Images -->
[CreateWebSite1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_1.png
[CreateWebSite2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_2.png
[CreateWebSite3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_3.png
[CreateWebSite4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_4.png
[AppMainPage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/FinishedAPPMainScreen.png
[PropertiesPopup]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/propertiespopup.png
[BrowseByTagWithCallout]:./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/BrowseByTagWithCallout.png
[ASPNetPage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ASPNetPage.png
[Overrideviews1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p2m_layouttags_mobile_thumb.png
[Overrideviews2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p2_layoutTagsDesktop_thumb.png
[jquery1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-mobile-open-packagmanager.png
[jquery2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-mobile-open-install-jquey.png
[jquery3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_afternuget_thumb.png
[jquery4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png
[jquery5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png
[jquery6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p3_desktopBrowser_thumb.png
[SpeakerList1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersdesktop_thumb.png
[SpeakerList2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersconsistent_thumb.png
[SpeakerList3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_updatedspeakerview1_thumb.png
[SpeakerList4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png
[MobileSpeakersView1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_updatedspeakerview1_thumb.png
[MobileSpeakersView2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png
[MobileSpeakersView3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb.png
[TagsList1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_tags_j_thumb.png
[DatesList1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates1_thumb.png
[DatesList2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates2_thumb.png
[SessionView1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_1.png
[SessionView2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png
[SessionView3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_sessionsbyscottha_thumb.png
[SessionByCode1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_2.png
[SessionByCode2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png
[SessionByCode3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_love_thumb.png
[SessionByCode4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_love2_thumb.png
[DeployApplication1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_5.png
[DeployApplication2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_6.png
[DeployApplication3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_7.png
[DeployApplication4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_8.png
[DeployApplication5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_9.png
[DeployApplication6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_10.png
[DeployApplication7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_12.png
[DeployApplication8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_13.png
[DeployApplication9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_14.png
[DeployApplication10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_15.png

<!-- External Links -->
[MVC4DeveloperPreview]: http://www.asp.net/mvc/mvc4
[WebDeployUpdate]: http://www.windowsazure.com/ru-ru/develop/net/
[Visual Studio Express 2012]: http://www.microsoft.com/visualstudio/eng/products/visual-studio-express-products
[MVC4StarterProject]: http://go.microsoft.com/fwlink/?LinkId=228307
[FinishedProject]: http://go.microsoft.com/fwlink/?LinkId=228306
[Win7PhoneEmulator]: http://msdn.microsoft.com/ru-ru/library/ff402530(VS.92).aspx
[OperaMobileEmulator]: http://www.opera.com/developer/tools/mobile/
[AppleSafari]: http://www.apple.com/safari/download/
[HowToSafari]: http://www.davidalison.com/2008/05/how-to-let-safari-pretend-its-ie.html
[FireFox]: http://www.bing.com/search?q=firefox+download
[FireFoxUserAgentSwitcher]: https://addons.mozilla.org/ru-ru/firefox/addon/user-agent-switcher/
[CSSMediaQuries]: http://www.w3.org/TR/css3-mediaqueries/
[jquerydocs]: http://jquerymobile.com/demos/1.0b3/#/demos/1.0b3/docs/about/intro.html
[setuseragent]: http://www.howtogeek.com/113439/how-to-change-your-browsers-user-agent-without-installing-any-extensions/
[managementportal]: https://manage.windowsazure.com
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk20NetVS12.png
[Add XSRF Protection]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk20NetVS12.png
[firsdeploy007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-007.png
[rxPWS]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rxPWS.png


