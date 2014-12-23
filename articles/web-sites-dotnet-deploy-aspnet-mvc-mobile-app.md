<properties urlDisplayName="ASP.NET MVC 5 mobile website" pageTitle="Мобильный веб-сайт .NET ASP.NET MVC 5 - учебники Azure" metaKeywords="Учебник Azure, учебник по веб-приложению Azure, мобильное приложение Azure, Azure ASP.NET MVC 5,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure website using mobile features in ASP.NET MVC 5 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Websites" authors="cephalin,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="cephalin,riande" />


# Развертывание веб-приложения для мобильных устройств ASP.NET MVC 5 на веб-сайтах Azure

Этот учебник поможет вам освоить основы создания адаптированных для мобильных устройств веб-приложений ASP.NET MVC 5,
а также их развертывания в Windows Azure. Для работы с этим учебником вам потребуется 
[Visual Studio 2013 Express для Web][Visual Studio Express 2013]
или профессиональная версия Visual Studio, если она уже у вас
есть.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

### Что вы создадите

В данном учебнике вам предстоит добавить компоненты для мобильных устройств в простое приложение списка конференции, заготовка которого доступна для загрузки в [начальном проекте][StarterProject]. На снимке экрана ниже показаны сеансы ASP.NET в готовом приложении, отображаемые в эмуляторе браузера в средствах разработчика F12 Internet Explorer 11.

![][FixedSessionsByTag]

При помощи средств разработчика F12 в Internet Explorer 11 и [инструмента Fiddler][Fiddler] можно упростить отладку своего приложения.

### Чему вы научитесь

В этом учебнике вы узнаете:

-	Как использовать Visual Studio 2013 для публикации своего веб-приложения напрямую на веб-сайт Microsoft Azure.
-   Как шаблоны ASP.NET MVC 5 используют платформу Bootstrap CSS для улучшения отображения на мобильных устройствах.
-   Как создать представления для мобильных устройств с учетом особенностей конкретных браузеров мобильных устройств, таких как iPhone и Android.
-   Как создавать динамические представления (представления, соответствующие различным браузерам на разных устройствах).

<h2>Настройка среды разработки</h2>

Настройте среду разработки, установив пакет Azure SDK для платформы .NET Framework. 

1. Чтобы установить пакет Azure SDK для .NET, щелкните приведенную ниже ссылку. Если среда Visual Studio 2013 еще не установлена, ее можно установить по ссылке. Для этого учебника требуется Visual Studio 2013.[Пакет Azure SDK для Visual Studio 2013][AzureSDKVs2013]
1. Щелкните в окне установщика веб-платформы **Установить** и продолжайте установку.

	![Web Platform Installer - Azure SDK for .NET][WebPIAzureSdk23NetVS13]

Дополнительно понадобится эмулятор браузера мобильного устройства. Вы можете использовать любое из
следующих средств:

-   Эмулятор браузера в [средствах разработчика F12 в Internet Explorer 11 ][EmulatorIE11] (используется в снимках экрана всех
    браузеров мобильных устройств). Он содержит предустановки строк агента пользователя для Windows Phone 8, Windows Phone 7 и Apple iPad.
-	Эмулятор браузера в [Google Chrome DevTools][EmulatorChrome]. Содержит предустановки для многих устройств Android, а также Apple iPhone, Apple iPad и Amazon Kindle Fire. Также поддерживает эмуляцию событий прикосновения.
-   [Эмулятор Opera для мобильных устройств][EmulatorOpera]

К этой статье прилагаются следующие проекты Visual Studio
с исходным кодом C#:

-   [Загрузка начального проекта][StarterProject]
-   [Загрузка завершенного проекта][CompletedProject]

<h2>Этапы, описываемые в этом учебнике</h2>

- [Развертывание начального проекта на веб-сайте Microsoft Azure][]
- [Платформа Bootstrap CSS][]
- [Переопределение представлений, макетов и частичных представлений][]
- [Улучшение списка докладчиков][]
- [Улучшение списка тегов][]
- [Улучшение списка дат][]
- [Улучшение представления SessionsTable][]
- [Улучшение представления SessionByCode][]

<h3><a name="bkmk_DeployStarterProject"></a>Развертывание начального проекта на веб-сайте Microsoft Azure</h3>

1.	Загрузите [начальный проект][StarterProject] приложения списка конференции.

2. 	Затем в проводнике Windows щелкните правой кнопкой мыши файл Mvc5Mobile.zip и выберите *Свойства*.

3. 	В диалоговом окне **Свойства Mvc5Mobile.zip** 
нажмите кнопку **Разблокировать**. (Разблокировка устраняет предупреждение системы безопасности,
выводимое при попытке использовать *ZIP-файл*, загруженный
из Интернета).

4.	Щелкните правой кнопкой мыши файл *Mvc5Mobile.zip* и выберите **Извлечь все**, чтобы
распаковать файл. 

5. 	В Visual Studio откройте файл *Mvc5Mobile.sln*.

6.  В обозревателе решений щелкните правой кнопкой мыши проект и выберите **Опубликовать**.

	![][DeployClickPublish]

7.	В окне публикации веб-сайта нажмите **Веб-сайты Windows Azure**.

	![][DeployClickWebSites]

8.	Щелкните **Войти**.

	![][DeploySignIn]

9.	Введите свое имя пользователя Windows Azure и нажмите **Продолжить**.

	![][DeployUsername]

10.	Введите свой пароль и нажмите **Войти**.

	![][DeployPassword]

11. Когда вы войдете, отобразится диалоговое окно "Выбор существующего веб-сайта". Нажмите **Создать**.

	![][DeployNewWebsite]  

12. В поле**Имя сайта** введите уникальный префикс имени сайта. Полное имя сайта будет *&lt;префикс>*.azurewebsites.net. Также в поле **Регион** выберите регион. Затем нажмите **Создать**.

	![][DeploySiteSettings]

13.	Диалоговое окно публикации веб-сайта будет заполнено параметрами для нового веб-сайта. Щелкните **Опубликовать**.

	![][DeployPublishSite]

	Когда Visual Studio завершит публикацию начального проекта на веб-сайте Microsoft Azure, откроется классический браузер и отобразится действующий веб-сайт.

14.	Запустите эмулятор браузера мобильного устройства, скопируйте в эмулятор URL-адрес приложения конференции(*<префикс>*.azurewebsites.net), затем нажмите кнопку справа вверху и выберите **Поиск по тегу**. Если вы используете Internet Explorer 11 в качестве браузера по умолчанию, просто нажмите клавишу F12, затем нажмите клавиши Ctrl+8 и измените профиль браузера на **Windows Phone**. На рисунке ниже показано представление *AllTags* в портретной ориентации (выбирается в списке **Поиск по тегу**).

	![][AllTags]

>[WACOM.NOTE] Выполняя отладку своего приложения MVC 5 с помощью Visual Studio, вы можете повторно опубликовать веб-сайт в Windows Azure, чтобы проверить действующий сайт непосредственно из браузера мобильного устройства или эмулятора браузера.

Этот вид очень удобен для чтения на мобильном устройстве. Кроме того, вы уже можете просмотреть некоторые визуальные эффекты, применяемые платформой Bootstrap CSS. Щелкните ссылку **ASP.NET**.

![][SessionsByTagASP.NET]

Представление тегов ASP.NET адаптировано под размер экрана. Bootstrap делает это для вас автоматически. Однако вы можете изменить это представление, чтобы оно лучше подходило для браузера мобильного устройства. Например, столбец **Дата** читать неудобно. Позже из этого учебника вы узнаете, как изменить представление *AllTags*, чтобы адаптировать его для мобильных устройств.

<h2><a name="bkmk_bootstrap"></a>Платформа Bootstrap CSS</h2>

Встроенная поддержка Bootstrap - это новая функция в шаблонах MVC 5. Вы уже видели, как она моментально улучшает различные виды в приложении. Например, панель навигации в верхней части автоматически сворачивается, если ширина браузера меньше. Попробуйте изменить размер окна классического браузера, и вы увидите, как изменится вид и поведение панели навигации. Это элемент динамического веб-дизайна, встроенный в Bootstrap.

Чтобы увидеть, как может выглядеть веб-приложение без поддержки Bootstrap, откройте
файл *App\_Start\\BundleConfig.cs* и закомментируйте строки, содержащие
*bootstrap.js* и *bootstrap.css*. Следующий код демонстрирует последние
две инструкции метода "RegisterBundles" после изменения:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Нажмите клавиши "Ctrl+F5", чтобы запустить приложение.

Обратите внимание, что сворачиваемая панель навигации теперь стала обычным неупорядоченным списком. Еще раз щелкните **Поиск по тегу**, затем щелкните **ASP.NET**. В эмуляторе мобильного устройства вы можете видеть, что теперь его представление не адаптировано под размер экрана и приходится пользоваться прокруткой, чтобы увидеть правую часть таблицы.

![][SessionsByTagASP.NETNoBootstrap]

Отмените сделанные изменения и обновите браузер мобильного устройства, чтобы проверить, восстановился ли адаптированный для мобильных устройств вид.

Вы можете использовать преимущества Bootstrap не только в ASP.NET MVC 5, но и в любом другом веб-приложении. Теперь эта функция встроена в шаблон проекта ASP.NET MVC 5, поэтому ваше веб-приложение MVC 5 сможет по умолчанию использовать преимущества Bootstrap.

Дополнительные сведения о Bootstrap см. на сайте [Bootstrap][BootstrapSite].

В следующем разделе вы узнаете, как создавать отдельные представления для браузеров мобильных устройств
.

<h2><a name="bkmk_overrideviews"></a>Переопределение представлений, макетов и частичных представлений</h2>

Вы можете переопределить любой вид (включая макеты и частичные виды) для браузеров мобильных устройств в целом, для браузера отдельного мобильного устройства или для какого-либо конкретного браузера. Чтобы создать вид для мобильных устройств можно скопировать файл представления и добавить расширение *.Mobile* к имени файла. Например, чтобы создать мобильное представление *Index*, скопируйте *Views\\Home\\Index.cshtml* в *Views\\Home\\Index.Mobile.cshtml*.

В этом разделе для мобильных устройств будет создан специальный файл макета.

Сначала скопируйте *Views\\Shared\\\_Layout.cshtml* в *Views\\Shared\\\_Layout.Mobile.cshtml*. Откройте *\_Layout.Mobile.cshtml* и измените заголовок с **Приложение MVC5** на **Приложение MVC5(мобильное)**.

В каждом вызове "Html.ActionLink" панели навигации удалите "Поиск по" во всех ссылках *ActionLink*. Следующий программный код демонстрирует законченный тег <ul class="nav navbar-nav"> файла макета для мобильных устройств.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Скопируйте файл *Views\\Home\\AllTags.cshtml* в * Views\\Home\\AllTags.Mobile.cshtml*. Откройте новый файл и измените элемент "<h2>" с "Tags" на "Tags (M)".

    <h2>Tags (M)</h2>

Перейдите на страницу тегов, используя классический браузер и эмулятор браузера для мобильных устройств. Эмулятор браузера для мобильных устройств показывает два внесенных вами изменения (заголовки из *\_Layout.Mobile.cshtml* и *AllTags.Mobile.cshtml*).

![][AllTagsMobile_LayoutMobile]

Но изображение для настольного компьютера не изменилось (с заголовками из *\_Layout.cshtml* и *AllTags.cshtml*).

![][AllTagsMobile_LayoutMobileDesktop]

<h2><a name="bkmk_browserviews"></a>Создание особых представлений для браузера</h2>

Помимо представлений для мобильных устройств и настольных компьютеров также можно создавать особые виды для отдельных браузеров. Например, можно создать представления, адаптированные специально для браузера устройства iPhone или Android. В этом разделе вы узнаете, как создать макет для браузера iPhone и версию представления *AllTags* для iPhone.

Откройте файл *Global.asax* и добавьте следующие строки внизу метода Application_Start.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Этот код определяет новый режим отображения под названием "iPhone", который будет использоваться для каждого входящего запроса. Если входящий запрос удовлетворяет условию, которое вы задали (то есть, если агент пользователя содержит строку "iPhone"), ASP.NET MVC выполнит поиск представлений, которые содержат в своем имени суффикс "iPhone".

>[WACOM.NOTE] При добавлении особых режимов отображения для браузеров, например для iPhone и Android, обязательно установите первый аргумент равным 0 (вставьте в начале списка), чтобы особый режим для браузеров имел приоритет над шаблоном для мобильных устройств (*.Mobile.cshtml). Если первым в списке будет шаблон для мобильных устройств, именно он будет выбираться вместо того режима отображения, который вы хотите использовать (приоритет получает первое соответствие, а шаблон для мобильных устройств соответствует всем браузерам мобильных устройств). 

В редакторе кода щелкните правой кнопкой мыши DefaultDisplayMode, выберите **Разрешить**, и
затем выберите "using System.Web.WebPages;". Это действие добавит ссылку на
пространство имен "System.Web.WebPages", в котором
определены типы DisplayModeProvider и DefaultDisplayMode.

![][ResolveDefaultDisplayMode]

Кроме того, можно вручную добавить следующую строку в раздел
"using" этого файла.

    using System.Web.WebPages;

Сохраните изменения. Скопируйте файл *Views\\Shared\\\_Layout.Mobile.cshtml* в *Views\\Shared\\\_Layout.iPhone.cshtml*. Откройте новый файл и измените заголовок с "MVC5 Application (Mobile)" на "MVC5 Application (iPhone)".

Скопируйте файл *Views\\Home\\AllTags.Mobile.cshtml* в *Views\\Home\\AllTags.iPhone.cshtml*. В новом файле измените значение элемента "<h2>" с "Tags (M)" на "Tags (iPhone)".

Запустите приложение. Запустите эмулятор браузера мобильного устройства. Убедитесь, что для его агента пользователя задано значение "iPhone", и перейдите к представлению *AllTags*. Если вы используете эмулятор браузера в средствах разработчика F12 Internet Explorer 11, задайте следующие параметры эмуляции:

-   Профиль браузера = **Windows Phone**
-   Строка агента пользователя = **Указать**
-   Специальная строка = **Apple-iPhone5C1/1001.525**

На следующем снимке экрана показано представление *AllTags*, отображаемое в эмуляторе в средствах разработчика F12 Internet Explorer 11 со специальной строкой агента пользователя (это строка агента пользователя iPhone 5C).

![][AllTagsIPhone_LayoutIPhone]

В браузере мобильного устройства выберите ссылку **Speakers**. Ввиду отсутствия представления для мобильных устройств (*AllSpeakers.Mobile.cshtml*), докладчики по умолчанию (*AllSpeakers.cshtml*) отображаются с использованием представления макета для мобильных устройств (*\_Layout.Mobile.cshtml*). Как показано ниже, заголовок **Приложение MVC5 (мобильное)** задается в *\_Layout.Mobile.cshtml*.

![][AllSpeakers_LayoutMobile]

Можно глобально отключить представление по умолчанию (не для мобильных устройств) из отображения в макете для мобильных устройств, задав для параметра "RequireConsistentDisplayMode" значение "true" в файле *Views\\\_ViewStart.cshtml* следующим образом:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Если "RequireConsistentDisplayMode" имеет значение "true", макет для мобильных устройств (*\_Layout.Mobile.cshtml*) используется только для мобильных представлений (т.е., если файл представления имеет вид ***ViewName**.Mobile.cshtml*). Может понадобиться установить для "RequireConsistentDisplayMode" значение "true", если макет для мобильных устройств не работает с представлениями, не предназначенными для мобильных устройств. На снимке экрана ниже показано, как страница *Speakers* отображается, при значении "RequireConsistentDisplayMode" равном "true" (без слова "мобильный" на панели навигации вверху страницы.

![][AllSpeakers_LayoutMobileOverridden]

Можно отключить согласованный режим отображения, задав в файле представления для "RequireConsistentDisplayMode" значение "false". Следующее исправление файла *Views\\Home\\AllSpeakers.cshtml* установит "RequireConsistentDisplayMode" равным "false":

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

В этом разделе мы узнали, как создавать макеты и представления для мобильных устройств, а также создавать макеты и представления для определенных устройств, таких как iPhone. При этом главное преимущество платформы Bootstrap CSS - это динамический макет, позволяющий применять одну таблицу стилей в браузерах настольных компьютеров, телефонов и планшетных устройств, создавая единый стиль отображения и поведения. В следующем разделе вы узнаете, как воспользоваться платформой Bootstrap для создания представлений, адаптированных для мобильных устройств.

<h2><a name="bkmk_Improvespeakerslist"></a> Улучшение списка докладчиков</h2>

Как вы только что увидели, представление *Speakers* удобно для чтения, но ссылки малы, и касаться их на мобильном устройстве трудно. В этом разделе вы узнаете, как создать представление *AllSpeakers*, адаптированное для мобильных устройств. Оно содержит большие, удобные для касания ссылки, а также поле поиска, позволяющее быстро находить докладчиков.

Вы можете воспользоваться [стилем группы связанного списка][] Bootstrap, чтобы улучшить представление *Speakers*. В *Views\\Home\\AllSpeakers.cshtml* замените содержимое файла Razor следующим кодом.

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

Атрибут class="list-group" тега "<div>" применяет стиль элементов списка
Bootstrap, а атрибут class="input-group-item" - 
применяет стиль элементов списка Bootstrap к каждой ссылке.

Обновите браузер мобильного устройства. Обновленное представление выглядит следующим образом:

![][AllSpeakersFixed]

Стиль [группы связанного списка][] Bootstrap делает поле каждой ссылки полностью интерактивным, что значительно удобней для пользователя. Переключитесь в представление для настольного компьютера, чтобы проверить единый стиль отображения и поведения.

![][AllSpeakersFixedDesktop]

Мы улучшили представление браузера для мобильных устройств, но перемещаться по длинному списку докладчиков все еще неудобно. Исходные настройки Bootstrap не предусматривают функцию фильтра поиска, но вы можете ее добавить с помощью нескольких строк кода. Сначала вы добавите в представление поле поиска, а затем с помощью кода JavaScript подключите функцию фильтра. В *Views\\Home\\AllSpeakers.cshtml* добавьте тег \<form\> сразу после тега \<h2\>, как показано ниже.

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

Обратите внимание, что к тегам <form> и <input> применены стили Bootstrap. Элемент <span> добавляет [глиф][] Bootstrap в search box.

In the *Scripts* folder, add a JavaScript file called *filter.js*. Open
the file and paste the following code into it:

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

Также включите filter.js в свои зарегистрированные наборы. Откройте *App\_Start\\BundleConfig.cs* и измените первые наборы. Измените первую инструкцию "bundles.Add" (для набора **jquery**), чтобы включить *Scripts\\filter.js* следующим образом. 

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

Набор **jquery** уже отображается представлением по умолчанию *\_Layout*
. Позже вы сможете использовать тот же код JavaScript для применения
функции фильтра к другим представлениям списка.

Refresh the mobile browser and go to the *AllSpeakers* view. In the
search box, type "sc". The speakers list should now be filtered
according to your search string.

![][AllSpeakersFixedSearchBySC]

<h2><a name="bkmk_improvetags"></a> Улучшение списка тегов</h2>

Как и представление *Speakers*, представление *Tags* удобно для чтения, но ссылки
маленькие, и нажимать на них на мобильном устройстве трудно. Можно исправить представление *Tags* так же, как это было сделано с представлением *Speakers*, с использованием описанных выше изменений в коде и синтаксиса метода "Html.ActionLink" в *Views\\Home\\AllTags.cshtml*:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

Вот как выглядит обновленный классический браузер:

![][AllTagsFixedDesktop]

А вот как выглядит обновленный браузер мобильного устройства. 

![][AllTagsFixed]

>[WACOM.NOTE] Возможно, вы обнаружите, что в браузере мобильного устройства по-прежнему отображается исходный формат списка. Что же случилось с вашим привлекательным стилем Bootstrap? Это следствие ранее выполненного действия при создании представлений для мобильных устройств. Тем не менее, теперь вы используете платформу Bootstrap CSS и создаете динамический веб-дизайн, поэтому перейдите к следующему шагу и удалите эти представления и представления с макетом для мобильных устройств. После выполнения этих действий обновленный браузер мобильного устройства отобразит стиль Bootstrap.

<h2><a name="bkmk_improvedates"></a> Улучшение списка дат</h2>

Можно улучшить представление *Dates* так же, как и *Speakers* и
*Tags*, если использовать описанные выше изменения кода, но соблюдать синтаксис метода`Html.ActionLink` в *Views\\Home\\AllDates.cshtml*.

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

Вот как будет выглядеть обновленное представление браузера мобильного устройства:

![][AllDatesFixed]

Можно дополнительно улучшить представление *Dates*, настроив сортировку величин даты-времени
по дате. Сделать это можно при помощи стилей
[панелей][] Bootstrap. Замените
содержимое файла *Views\\Home\\AllDates.cshtml*
на следующий код.

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

Этот код создает отдельный тег "<div class="panel panel-primary">" для 
каждой отдельной даты списка и использует [группу связанного списка][] для
соответствующих ссылок, как и до этого. Вот как выглядит браузер мобильного устройства
при выполнении этого кода.

![][AllDatesFixed2]

Переключитесь в классический браузер. Также обратите внимание на единый стиль отображения.

![][AllDatesFixed2Desktop]

<h2><a name="bkmk_improvesessionstable"></a> Улучшение представления SessionsTable</h2>

В этом разделе вы узнаете, как адаптировать представление *SessionsTable* для мобильных устройств. Для этого необходимо внести более обширные изменения, чем в предыдущих случаях.

В браузере мобильного устройства нажмите кнопку **Тег** и введите в поле поиска "asp".

![][AllTagsFixedSearchByASP]

Нажмите на ссылку **ASP.NET**.

![][SessionsTableTagASP.NET]

Как видите, данные выводятся в формате таблицы, которая сейчас предназначена для просмотра в классическом браузере. При этом чтение в браузере мобильного устройства немного затруднено. Чтобы исправить эту проблему откройте *Views\\Home\\SessionsTable.cshtml* и замените содержимое файла следующим кодом.

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

Этот код выполняет 3 действия:

-   использует [настраиваемую группу связанного списка][] Bootstrap для форматирования данных сеанса по вертикали, чтобы все эти данные можно было читать в браузере мобильного устройства (используя классы, такие как list-group-item-text);
-   применяет к макету [систему сетки][], чтобы элементы сеанса отображались в классическом браузере по горизонтали, а в браузере мобильного устройства - по вертикали (используя класс col-md-4);
-   использует [динамические служебные программы][], чтобы скрыть теги сеансов при просмотре в браузере мобильного устройства (используя класс hidden-xs).

Можно перейти к нужному сеансу нажатием на ссылку в заголовке. На рисунке ниже приведены изменения кода.

![][FixedSessionsByTag]

Использованная система сетки Bootstrap автоматически упорядочивает сеансы по вертикали в браузере мобильного устройства. Кроме того, обратите внимание, что теги не отображаются. Переключитесь в классический браузер.

![][SessionsTableFixedTagASP.NETDesktop]

Обратите внимание, что в классическом браузере теги отображаются. Также вы можете видеть, что используемая система сетки Bootstrap, упорядочивает элементы сеанса в два столбца. Если изменить масштаб страницы, можно увидеть, как данные выстраиваются в три столбца.

<h2><a name="bkmk_improvesessionbycode"></a> Улучшение представления SessionByCode</h2>

Наконец, вы можете исправить представление *SessionByCode*, чтобы адаптировать его для мобильных устройств.

В браузере мобильного устройства нажмите кнопку **Тег** и введите `asp`
в поле поиска.

![][AllTagsFixedSearchByASP]

Нажмите на ссылку **ASP.NET**. Отобразятся сеансы для тега ASP.NET.

![][FixedSessionsByTag]

Выберите **Создание одностраничного приложения с ASP.NET и
AngularJS**.

![][SessionByCode3-644]

Представление для классического браузера выглядит замечательно, но вы можете его улучшить, используя некоторые компоненты графического пользовательского интерфейса Bootstrap.

Откройте *Views\\Home\\SessionByCode.cshtml* и замените его содержимое
следующими строками:

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

Новая разметка использует стиль панелей Bootstrap для улучшения вида на мобильных устройствах. 

Обновите браузер мобильного устройства. На рисунке ниже отражены
изменения кода.

![][SessionByCodeFixed3-644]

Заключение и сводка
------------------

Данный учебник продемонстрировал применение ASP.NET MVC 5 для разработки
мобильных веб-приложений. В частности, описаны такие возможности:

-	развертывание приложения ASP.NET MVC 5 на веб-сайте Microsoft Azure;
-   использование Bootstrap для создания динамического веб-макета в
    приложении MVC 5;
-   переопределение макетов, представлений и частичных представлений, как глобально, так и для
    отдельного представления;
-   управление макетом и принудительное частичное переопределение с помощью
    свойства "RequireConsistentDisplayMode";
-   создание представлений для конкретных браузеров, таких как
    браузер iPhone;
-   применение стиля Bootstrap в коде Razor.

См. также
--------

-   [Сайт Bootstrap][BootstrapSite]
-   [Официальный блог Bootstrap][]
-   [Учебник по Twitter Bootstrap от Tutorial Republic][]
-   [Игровая площадка Bootstrap][]
-   [Рекомендации от W3C по веб-приложениям для мобильных устройств][]
-   [Проектные рекомендации от W3C по запросам мультимедиа][]

<!-- Internal Links -->
[Развертывание начального проекта на веб-сайте Microsoft Azure]: #bkmk_DeployStarterProject
[Платформа Bootstrap CSS]: #bkmk_bootstrap
[Переопределение представлений, макетов и частичных представлений]: #bkmk_overrideviews
[Создание особых представлений для браузера]:#bkmk_browserviews
[Улучшение списка докладчиков]: #bkmk_Improvespeakerslist
[Улучшение списка тегов]: #bkmk_improvetags
[Улучшение списка дат]: #bkmk_improvedates
[Улучшение представления SessionsTable]: #bkmk_improvesessionstable
[Улучшение представления SessionByCode]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/ru-ru/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[группа связанных списков]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[панели]: http://getbootstrap.com/components/#panels
[пользовательская группа связанных списков]: http://getbootstrap.com/components/#list-group-custom-content
[система сетки]: http://getbootstrap.com/css/#grid
[динамические служебные программы]: http://getbootstrap.com/css/#responsive-utilities
[Официальный блог Bootstrap]: http://blog.getbootstrap.com/
[Учебник по Twitter Bootstrap от Tutorial Republic]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[Игровая площадка Bootstrap]: http://www.bootply.com/
[Рекомендации от W3C по веб-приложениям для мобильных устройств]: http://www.w3.org/TR/mwabp/
[Проектные рекомендации от W3C по запросам мультимедиа]: http://www.w3.org/TR/css3-mediaqueries/

<!-- Images -->
[DeployClickPublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
[DeployClickWebSites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
[DeploySignIn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
[DeployUsername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
[DeployPassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
[DeployNewWebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
[DeploySiteSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
[DeployPublishSite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
[MobileHomePage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png
[FixedSessionsByTag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
[AllTags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
[SessionsByTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
[SessionsByTagASP.NETNoBootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
[AllTagsMobile_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
[AllTagsMobile_LayoutMobileDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
[ResolveDefaultDisplayMode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
[AllTagsIPhone_LayoutIPhone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
[AllSpeakers_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
[AllSpeakers_LayoutMobileOverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
[AllSpeakersFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
[AllSpeakersFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
[AllSpeakersFixedSearchBySC]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
[AllTagsFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png 
[AllTagsFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
[AllDatesFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
[AllDatesFixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
[AllDatesFixed2Desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
[AllTagsFixedSearchByASP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
[SessionsTableTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
[SessionsTableFixedTagASP.NETDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
[SessionByCode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
[SessionByCodeFixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
