<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="ASP.NET MVC 5 mobile website" pageTitle=".NET ASP.NET MVC 5 mobile website - Azure tutorials" metaKeywords="Azure tutorial, Azure web app tutorial, Azure mobile app, Azure ASP.NET MVC 5,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure website using mobile features in ASP.NET MVC 5 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Websites" authors="cephalin,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="cephalin,riande" />

# Развертывание веб-приложения для мобильных устройств ASP.NET MVC 5 на веб-сайтах Azure

Этот учебник поможет вам освоить основы создания адаптированного для мобильных устройств веб-приложения ASP.NET MVC 5, а также его развертывания в Microsoft Azure. Для работы с этим учебником вам потребуется [Visual Studio Express 2013 для Web][Visual Studio Express 2013 для Web] или профессиональная версия Visual Studio, если она у вас уже есть.

[WACOM.INCLUDE [создать-учетная запись-и-веб-сайт-примечание](../includes/create-account-and-websites-note.md)]

### Что вы создадите

В данном учебнике предстоит добавить компоненты для мобильных устройств в простое приложение списка конференции, приведенное в [начальном проекте][начальном проекте]. На следующем снимке экрана показаны сеансы ASP.NET в готовом приложении, отображаемые в эмуляторе браузера в средствах разработчика F12 Internet Explorer 11.


![][0]

<p>Вы можете воспользоваться средствами разработчика F12 Internet Explorer 11 и <a href="http://www.fiddler2.com/fiddler2/">инструментом Fiddler</a>, чтобы упростить отладку своего приложения.&nbsp;</p>


### Чему вы научитесь

В этом учебнике вы узнаете:

-   Как использовать Visual Studio 2013 для публикации своего веб-приложения напрямую на веб-сайт Microsoft Azure.
-   Как шаблоны ASP.NET MVC 5 используют платформу Bootstrap CSS для
     улучшения отображения на мобильных устройствах.
-   Как создать представления для мобильных устройств с учетом особенностей конкретных браузеров мобильных
    устройств, таких как iPhone и Android.
-   Как создавать динамические представления (представления, соответствующие различным
    браузерам на разных устройствах).

## Настройка среды разработки

Настройте среду разработки, установив пакет Azure SDK для платформы .NET Framework.

1.  Чтобы установить пакет Azure SDK для .NET, щелкните приведенную ниже ссылку. Если среда Visual Studio 2013 еще не установлена, ее можно установить по ссылке. Для этого учебника требуется Visual Studio 2013.[Пакет Azure SDK для Visual Studio 2013][Пакет Azure SDK для Visual Studio 2013]
2.  Щелкните в окне установщика веб-платформы **Установить** и продолжайте установку.

    ![Установщик веб-платформы — Azure SDK для .NET][Установщик веб-платформы — Azure SDK для .NET]

Кроме того, понадобится эмулятор браузера для мобильного устройства. Вы можете использовать любое из следующих средств:

-   Эмулятор браузера в [средствах разработчика F12 Internet Explorer 11][средствах разработчика F12 Internet Explorer 11] (используется в снимках экрана всех браузеров
    мобильных устройств). Он содержит предустановки строк агента пользователя для Windows Phone 8, Windows Phone 7 и Apple iPad.
-   Эмулятор браузера в [Google Chrome DevTools][Google Chrome DevTools]. Содержит предустановки для многих устройств Android, а также Apple iPhone, Apple iPad и Amazon Kindle Fire. Также поддерживает эмуляцию событий прикосновения.
-   [Эмулятор Opera для мобильных устройств][Эмулятор Opera для мобильных устройств]

К этой статье прилагаются следующие проекты Visual Studio с исходным кодом C#:

-   [Загрузка начального проекта][начальном проекте]
-   [Загрузка завершенного проекта][Загрузка завершенного проекта]

## Этапы, описываемые в этом учебнике

-   [Развертывание начального проекта на веб-сайте Microsoft Azure][Развертывание начального проекта на веб-сайте Microsoft Azure]
-   [Платформа Bootstrap CSS][Платформа Bootstrap CSS]
-   [Переопределение представлений, макетов и частичных представлений][Переопределение представлений, макетов и частичных представлений]
-   [Улучшение списка докладчиков][Улучшение списка докладчиков]
-   [Улучшение списка тегов][Улучшение списка тегов]
-   [Улучшение списка дат][Улучшение списка дат]
-   [Улучшение представления SessionsTable][Улучшение представления SessionsTable]
-   [Улучшение представления SessionByCode][Улучшение представления SessionByCode]

### <a name="bkmk_DeployStarterProject"></a>Развертывание начального проекта на веб-сайте Microsoft Azure

1.  Скачайте [начальный проект][начальном проекте] приложения списка конференции.

2.  Затем в проводнике Windows щелкните правой кнопкой мыши файл Mvc5Mobile.zip и выберите *Свойства*.

3.  В диалоговом окне **Свойства Mvc5Mobile.zip**     нажмите кнопку **Разблокировать**. (Разблокировка устраняет предупреждение системы безопасности,
    выводимое при попытке использовать *ZIP-файл*, скачанный из     Интернета.)

4.  Щелкните правой кнопкой мыши файл *Mvc5Mobile.zip* и выберите **Извлечь все**, чтобы распаковать файл.

5.  В Visual Studio откройте файл *Mvc5Mobile.sln*.

6.  В обозревателе решений щелкните правой кнопкой мыши проект и выберите **Опубликовать**.

    ![][1]

7.  В окне публикации веб-сайта щелкните **Веб-сайты Microsoft Azure**.

    ![][2]

8.  Щелкните **Войти**.

    ![][3]

9.  Введите свое имя пользователя Microsoft Azure и щелкните **Продолжить**.

    ![][4]

10. Введите свой пароль и щелкните **Войти**.

    ![][5]

11. Когда вы войдете, отобразится диалоговое окно «Выбор существующего веб-сайта». Щелкните **Создать**.

    ![][6]

12. В поле **Имя сайта** задайте уникальный префикс имени сайта. Полное имя сайта будет *\<префикс\>*.azurewebsites.net. Также в поле **Регион** выберите регион. Затем щелкните **Создать**.

    ![][7]

13. Диалоговое окно публикации веб-сайта будет заполнено параметрами для нового веб-сайта. Щелкните **Опубликовать**.

    ![][8]

    Когда Visual Studio завершит публикацию начального проекта на веб-сайте Microsoft Azure, откроется классический браузер и отобразится действующий веб-сайт.

14. Запустите эмулятор браузера мобильного устройства, скопируйте в эмулятор URL-адрес приложения конференции (*<prefix>*.azurewebsites.net), затем нажмите кнопку справа вверху и выберите **Поиск по тегу**. Если вы используете Internet Explorer 11 как браузер по умолчанию, просто введите `F12`, затем нажмите `Ctrl+8` и измените профиль браузера на **Windows Phone**. На следующем рисунке показано представление *AllTags* в книжной ориентации (выбираемое из списка **Поиск по тегу**).

    ![][9]

> [WACOM.NOTE] Выполняя отладку своего приложения MVC 5 с помощью Visual Studio, вы можете повторно опубликовать веб-сайт в Microsoft Azure, чтобы проверить действующий сайт непосредственно из браузера мобильного устройства или эмулятора браузера.

Этот экран является очень удобным для чтения на мобильном устройстве. Кроме того, вы уже можете просмотреть некоторые визуальные эффекты, применяемые платформой Bootstrap CSS. Щелкните ссылку **ASP.NET**.

![][10]

Представление тегов ASP.NET адаптировано под размер экрана. Bootstrap делает это для вас автоматически. Однако вы можете изменить это представление, чтобы оно лучше подходило для браузера мобильного устройства. Например, трудно читать столбец **Дата**. Далее в этом учебнике вы узнаете, как изменить представление *AllTags*, чтобы адаптировать его для мобильных устройств.

## <a name="bkmk_bootstrap"></a>Платформа Bootstrap CSS

Встроенная поддержка Bootstrap — это новая функция в шаблонах MVC 5. Вы уже видели, как она моментально улучшает различные представления в приложении. Например, панель навигации в верхней части автоматически сворачивается, если ширина браузера меньше. Попробуйте изменить размер окна классического браузера, и вы увидите, как изменится вид и поведение панели навигации. Это элемент динамического веб-дизайна, встроенный в Bootstrap.

Чтобы увидеть, как бы выглядело веб-приложение без поддержки Bootstrap, откройте *App\_Start\\BundleConfig.cs* и закомментируйте строки, содержащие *bootstrap.js* и *bootstrap.css*. Следующий код показывает последние две инструкции метода `RegisterBundles` после изменения:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Нажмите клавиши `Ctrl+F5`, чтобы запустить приложение.

Обратите внимание, что сворачиваемая панель навигации теперь стала обычным неупорядоченным списком. Еще раз щелкните **Поиск по тегу**, затем щелкните **ASP.NET**. В эмуляторе мобильного устройства вы можете видеть, что теперь его представление не адаптировано под размер экрана и приходится пользоваться прокруткой, чтобы увидеть правую часть таблицы.

![][11]

Отмените свои изменения и обновите браузер мобильного устройства, чтобы проверить, восстановился ли адаптированный для мобильных устройств дисплей.

Вы можете использовать преимущества Bootstrap не только в ASP.NET MVC 5, но и в любом другом веб-приложении. Теперь эта функция встроена в шаблон проекта ASP.NET MVC 5, таким образом, ваше веб-приложение MVC 5 сможет по умолчанию использовать преимущества Bootstrap.

Дополнительную информацию о Bootstrap см. на сайте [Bootstrap][Bootstrap].

В следующем разделе вы узнаете, как обеспечить представления для браузеров мобильных устройств.

## <a name="bkmk_overrideviews"></a>Переопределение представлений, макетов и частичных представлений

Вы можете переопределить любое представление (включая макеты и частичные представления) для браузеров мобильных устройств вообще, для отдельного браузера мобильных устройств или для любого конкретного браузера. Чтобы обеспечить представление для мобильных устройств, вы можете скопировать файл представления и добавить расширение *.Mobile* к имени файла. Например, чтобы создать мобильное представление *Index*, скопируйте *Views\\Home\\Index.cshtml* в *Views\\Home\\Index.Mobile.cshtml*.

В этом разделе для мобильных устройств будет создан специальный файл макета.

Для начала скопируйте *Views\\Shared\\\_Layout.cshtml* в *Views\\Shared\\\_Layout.Mobile.cshtml*. Откройте *\_Layout.Mobile.cshtml* и измените заголовок с **Приложение MVC5** на **Приложение MVC5 (мобильное)**.

В каждом вызове `Html.ActionLink` панели навигации удалите «Поиск по» в каждой ссылке 
*ActionLink*. В следующем коде показан законченный тег `<ul class="nav navbar-nav">` файла макета для мобильных устройств.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Скопируйте файл *Views\\Home\\AllTags.cshtml* в файл *Views\\Home\\AllTags.Mobile.cshtml*. Откройте новый файл и измените элемент `<h2>` с Tags на Tags (M):

    <h2>Tags (M)</h2>

Перейдите на страницу тегов, используя классический браузер и эмулятор браузера для мобильных устройств. Эмулятор браузера для мобильных устройств показывает два внесенных вами изменения (заголовки из *\_Layout.Mobile.cshtml* и *AllTags.Mobile.cshtml*).

![][12]

Напротив, изображение для настольного компьютера не изменилось (с заголовками из *\_Layout.cshtml* и *AllTags.cshtml*).

![][13]

## <a name="bkmk_browserviews"></a>Создание представлений для браузера

Кроме представлений для мобильных устройств и настольных компьютеров вы также можете создавать представления для отдельных браузеров. Например, вы можете создать представления, адаптированные специально для браузера iPhone или Android. В этом разделе вы узнаете, как создать макет для браузера iPhone и версию представления *AllTags* для iPhone.

Откройте файл *Global.asax* и добавьте следующий код в нижнюю часть метода `Application_Start`.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Этот код определяет новый режим отображения с именем iPhone, который будет использоваться для каждого входящего запроса. Если входящий запрос удовлетворяет условию, которое вы задали (то есть, если агент пользователя содержит строку iPhone), ASP.NET MVC выполнит поиск представлений, которые содержат в своем имени суффикс iPhone.

> [WACOM.NOTE] Когда вы добавляете режимы отображения для браузеров мобильных устройств, таких как iPhone и Android, обязательно установите первый аргумент `0` (вставьте в верхней части списка), чтобы режим отображения для браузеров имел приоритет над шаблоном для мобильных устройств (\*.Mobile.cshtml). Если первым в списке будет шаблон для мобильных устройств, именно он будет выбираться вместо того режима отображения, который вы хотите использовать (приоритет получает первое соответствие, а шаблон для мобильных устройств соответствует всем браузерам мобильных устройств).

В коде щелкните правой кнопкой мыши `DefaultDisplayMode`, выберите **Разрешить**, а затем выберите `using System.Web.WebPages;`. Это действие добавит ссылку на пространство имен `System.Web.WebPages`, в котором определяются типы `DisplayModeProvider` и `DefaultDisplayMode`.

![][14]

Кроме того, вы можете вручную добавить следующую строку в раздел `using` файла.

    using System.Web.WebPages;

Сохраните изменения. Скопируйте файл *Views\\Shared\\\_Layout.Mobile.cshtml* в файл *Views\\Shared\\\_Layout.iPhone.cshtml*. Откройте новый файл и измените заголовок с `MVC5 Application (Mobile)` на `MVC5 Application (iPhone)`.

Скопируйте файл *Views\\Home\\AllTags.Mobile.cshtml* в файл *Views\\Home\\AllTags.iPhone.cshtml*. В новом файле измените элемент `<h2>` с Tags (M) на Tags (iPhone).

Запустите приложение. Запустите эмулятор браузера мобильного устройства. Убедитесь, что для его агента пользователя задано значение iPhone, и перейдите к представлению *AllTags*. Если вы используете эмулятор браузера в средствах разработчика F12 Internet Explorer 11, задайте такие параметры эмуляции:

-   профиль браузера — **Windows Phone**;
-   строка агента пользователя — **Custom**;
-   настраиваемая строка — **Apple-iPhone5C1/1001.525**.

На следующем снимке экрана показано представление *AllTags*, отображаемое в эмуляторе в средствах разработчика F12 Internet Explorer 11 с настраиваемой строкой агента пользователя (это строка агента пользователя iPhone 5C).

![][15]

В браузере мобильного устройства выберите ссылку **Speakers**. Так как представление для мобильных устройств (*AllSpeakers.Mobile.cshtml*) отсутствует, докладчики по умолчанию (*AllSpeakers.cshtml*) отображаются, используя представление с макетом для мобильных устройств (*\_Layout.Mobile.cshtml*). Как показано ниже, заголовок **Приложение MVC5 (мобильное)** определяется в *\_Layout.Mobile.cshtml*.

![][16]

Вы можете глобально отключить представление по умолчанию (не для мобильных устройств) из отображения в макете для мобильных устройств, задав для параметра `RequireConsistentDisplayMode` значение `true` в файле *Views\\\_ViewStart.cshtml*, подобно следующему:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Если значение `RequireConsistentDisplayMode` равно `true`, макет для мобильных устройств (*\_Layout.Mobile.cshtml*) используется только для мобильных представлений (то есть для файла представления используется форма ***ViewName**.Mobile.cshtml*). Возможно, вы захотите установить для `RequireConsistentDisplayMode` значение `true`, если макет для мобильных устройств не работает с представлениями, не предназначенными для мобильных устройств. На следующем снимке экрана показано, как страница *Speakers* отображается при значении `RequireConsistentDisplayMode`, равном `true` (без строки «(мобильное)» на панели навигации вверху страницы).

![][17]

Вы можете отключить согласованный режим отображения, задав в файле представления для
`RequireConsistentDisplayMode` значение `false`. Следующая разметка в файле *Views\\Home\\AllSpeakers.cshtml* устанавливает для `RequireConsistentDisplayMode` значение `false`:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

В данном разделе мы узнали, как создавать макеты и представления для мобильных устройств, а также как создавать макеты и представления для определенных устройств, таких как iPhone. При этом главное преимущество платформы Bootstrap CSS — это динамический макет, позволяющий применять одну таблицу стилей на в браузерах настольных компьютеров, телефонов и планшетных устройств, создавая единый стиль отображения и поведения. В следующем разделе вы узнаете, как воспользоваться платформой Bootstrap для создания представлений, адаптированных для мобильных устройств.

## <a name="bkmk_Improvespeakerslist"></a> Улучшение списка докладчиков

Как вы только что увидели, представление *Speakers* удобно для чтения, но ссылки малы, и касаться их на мобильном устройстве трудно. В этом разделе вы узнаете, как создать представление *AllSpeakers*, адаптированное для мобильных устройств. Оно содержит большие, удобные для касания ссылки, а также поле поиска, позволяющее быстро находить докладчиков.

Вы можете воспользоваться стилем [группы связанного списка][группы связанного списка] Bootstrap, чтобы улучшить представление *Speakers*. В *Views\\Home\\AllSpeakers.cshtml* замените содержимое файла Razor кодом, приведенным ниже.

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

Атрибут `class="list-group"` в теге `<div>` применяет стиль списка Bootstrap, а атрибут `class="input-group-item"` применяет стиль элементов списка Bootstrap к каждой ссылке.

Обновите браузер для мобильных устройств. Обновленное представление выглядит следующим образом:

![][18]

Стиль [группы связанного списка][группы связанного списка] Bootstrap делает поле каждой ссылки полностью доступным для щелчка, что значительно удобней для пользователя. Переключитесь в представление для настольного компьютера, чтобы проверить единый стиль отображения и поведения.

![][19]

Мы улучшили представление браузера для мобильных устройств, но перемещаться по длинному списку докладчиков все еще неудобно. Исходные настройки Bootstrap не предусматривают функцию фильтра поиска, но вы можете ее добавить с помощью нескольких строк кода. Сначала вы добавите в представление поле поиска, а затем с помощью кода JavaScript подключите функцию фильтра. В *Views\\Home\\AllSpeakers.cshtml* добавьте тег \<form\> сразу после тега \<h2\>, как показано ниже:

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

Обратите внимание, что к тегам `<form>` и `<input>` применены стили Bootstrap. Элемент `<span>` добавляет [глиф][глиф] Bootstrap в поле поиска.

В папку *Scripts* добавьте файл JavaScript с именем *filter.js*. Откройте файл и вставьте в него следующий код:

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

Также включите filter.js в свои зарегистрированные наборы. Откройте *App\_Start\\BundleConfig.cs* и измените первые наборы. Измените первую инструкцию `bundles.Add` (для набора **jquery**), чтобы включить *Scripts\\filter.js*, как показано ниже:

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

Набор **jquery** уже отображается представлением по умолчанию *\_Layout* . Затем вы сможете использовать тот же код JavaScript для применения функции фильтра к другим представлениям списка.

Обновите браузер мобильного устройства и перейдите к представлению *AllSpeakers*. В поле поиска введите «sc». Теперь список докладчиков должен быть отфильтрован по вашей строке поиска.

![][20]

## <a name="bkmk_improvetags"></a> Улучшение списка тегов

Как и представление *Speakers*, представление *Tags* удобно для чтения, но ссылки малы, и касаться их на мобильном устройстве трудно. Вы можете исправить представление *Tags* таким же образом, как вы сделали с представлением *Speakers*, внеся описанные выше изменения в код. Но при этом необходимо использовать следующий синтаксис метода `Html.ActionLink` в *Views\\Home\\AllTags.cshtml*:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

Вот как выглядит обновленный классический браузер:

![][21]

А вот как выглядит обновленный браузер мобильного устройства:

![][22]

> [WACOM.NOTE] Возможно, вы обнаружите, что в браузере мобильного устройства по-прежнему отображается исходный формат списка. Что же случилось с вашим привлекательным стилем Bootstrap? Это следствие ранее выполненного действия при создании представлений для мобильных устройств. Тем не менее, теперь вы используете платформу Bootstrap CSS и создаете динамический веб-дизайн, поэтому перейдите к следующему шагу и удалите эти представления и представления с макетом для мобильных устройств. После выполнения этих действий обновленный браузер мобильного устройства отобразит стиль Bootstrap.

## <a name="bkmk_improvedates"></a> Улучшение списка дат

Вы можете улучшить представление *Dates* таким же образом, как вы сделали с представлениями *Speakers* и *Tags*, внеся описанные выше изменения в код. Но при этом необходимо использовать следующий синтаксис метода `Html.ActionLink` в *Views\\Home\\AllDates.cshtml*:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

Вот как будет выглядеть обновленное представление браузера мобильного устройства:

![][23]

Вы также можете улучшить представление *Даты*, упорядочив отображение значений «дата-время» по дате. Вы можете это сделать с помощью стиля [панелей][панелей] Bootstrap. Замените содержимое файла *Views\\Home\\AllDates.cshtml* следующим кодом:

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

Этот код создает отдельный тег `<div class="panel panel-primary">` для каждой отличной даты в списке и, как и ранее, использует [группу связанного списка][группы связанного списка] для соответствующих ссылок. Вот как выглядит браузер мобильного устройства при выполнении этого кода:

![][24]

Переключитесь в классический браузер. Также обратите внимание на единый стиль отображения.

![][25]

## <a name="bkmk_improvesessionstable"></a> Улучшение представления SessionsTable

В этом разделе вы узнаете, как сделать представление *SessionsTable* более адаптированным для мобильных устройств. Для этого необходимо внести более обширные изменения, чем описанные выше.

В браузере мобильного устройства коснитесь кнопки **Тег**, а затем введите `asp` в поле поиска.

![][26]

Коснитесь ссылки **ASP.NET**.

![][27]

Как вы видите, отображение имеет формат таблицы, которая сейчас предназначена для просмотра в классическом браузере. При этом чтение в браузере мобильного устройства немного затруднено. Чтобы это исправить, откройте *Views\\Home\\SessionsTable.cshtml* и замените содержание файла следующим кодом:

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

-   использует [настраиваемую группу связанного списка][настраиваемую группу связанного списка] Bootstrap для форматирования данных сеанса по вертикали, чтобы все эти данные можно было читать в браузере мобильного устройства (используя классы, такие как list-group-item-text);
-   применяет к макету [систему сетки][систему сетки], чтобы элементы сеанса отображались в классическом браузере по горизонтали, а в браузере мобильного устройства — по вертикали (используя класс col-md-4); 
-   использует [динамические служебные программы][динамические служебные программы], чтобы
    скрыть теги сеансов при просмотре в браузере мобильного устройства (используя класс hidden-xs).

Вы также можете коснуться ссылки на заголовок, чтобы перейти к соответствующему сеансу. На следующем рисунке показаны изменения кода.

![][0]

Система сетки Bootstrap, которую вы применили, автоматически упорядочит сеансы в браузере мобильного устройства по вертикали. Кроме того, обратите внимание, что теги не отображаются. Переключитесь в классический браузер.

![][28]

Обратите внимание, что в классическом браузере теги отображаются. Также вы можете видеть, что система сетки Bootstrap, которую вы применили, упорядочивает элементы сеанса в два столбца. Если вы увеличите браузер, то увидите, что упорядочение изменится на три столбца.

## <a name="bkmk_improvesessionbycode"></a> Улучшение представления SessionByCode

Наконец, вы можете исправить представление *SessionByCode*, чтобы адаптировать его для мобильных устройств.

В браузере мобильного устройства коснитесь кнопки **Тег**, а затем введите `asp` в поле поиска.

![][26]

Коснитесь ссылки **ASP.NET**. Отобразятся сеансы для тега ASP.NET.

![][0]

Щелкните ссылку **Building a Single Page Application with ASP.NET and AngularJS** (Создание приложения на одной странице с помощью ASP.NET и AngularJS).

![][29]

Представление для классического браузера выглядит замечательно, но вы можете его улучшить, используя некоторые компоненты графического пользовательского интерфейса Bootstrap.

Откройте *Views\\Home\\SessionByCode.cshtml* и замените содержимое следующей разметкой:

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

Новая разметка использует стиль панелей Bootstrap, чтобы улучшить представление для мобильных устройств.

Обновите браузер для мобильных устройств. Следующий рисунок отражает только что внесенные в код изменения:

![][30]

## Заключение и сводка

В этом учебнике вы узнали, как использовать ASP.NET MVC 5 для разработки адаптированных для мобильных устройств веб-приложений. В частности, описаны такие возможности:

-   развертывание приложения ASP.NET MVC 5 на веб-сайте Microsoft Azure;
-   использование Bootstrap для создания динамического веб-макета в приложении MVC 5;
-   переопределение макетов, представлений и частичных представлений, как глобально, так и для отдельного
    представления;
-   управления макетом и принудительное частичное переопределение с помощью свойства `RequireConsistentDisplayMode`;
-   создание представлений для конкретных браузеров, таких как браузер
    iPhone;
-   применение стиля Bootstrap в коде Razor.

## См. также

-   Сайт [Bootstrap][Bootstrap]
-   [Официальный блог Bootstrap][Официальный блог Bootstrap]
-   [Учебник по Twitter Bootstrap от Tutorial Republic][Учебник по Twitter Bootstrap от Tutorial Republic]
-   [Игровая площадка Bootstrap][Игровая площадка Bootstrap]
-   [Рекомендации от W3C по веб-приложениям для мобильных устройств][Рекомендации от W3C по веб-приложениям для мобильных устройств]
-   [Проектные рекомендации от W3C по запросам мультимедиа][Проектные рекомендации от W3C по запросам мультимедиа]

<!-- Internal Links --> 
<!-- External Links --> 
<!-- Images -->

  [Visual Studio Express 2013 для Web]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
  [создать-учетная запись-и-веб-сайт-примечание]: ../includes/create-account-and-websites-note.md
  [начальном проекте]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
  [0]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
  [Пакет Azure SDK для Visual Studio 2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [Установщик веб-платформы — Azure SDK для .NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
  [средствах разработчика F12 Internet Explorer 11]: http://msdn.microsoft.com/ru-ru/library/ie/dn255001.aspx
  [Google Chrome DevTools]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
  [Эмулятор Opera для мобильных устройств]: http://www.opera.com/developer/tools/mobile/
  [Загрузка завершенного проекта]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
  [Развертывание начального проекта на веб-сайте Microsoft Azure]: #bkmk_DeployStarterProject
  [Платформа Bootstrap CSS]: #bkmk_bootstrap
  [Переопределение представлений, макетов и частичных представлений]: #bkmk_overrideviews
  [Улучшение списка докладчиков]: #bkmk_Improvespeakerslist
  [Улучшение списка тегов]: #bkmk_improvetags
  [Улучшение списка дат]: #bkmk_improvedates
  [Улучшение представления SessionsTable]: #bkmk_improvesessionstable
  [Улучшение представления SessionByCode]: #bkmk_improvesessionbycode
  [1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
  [2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
  [4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
  [10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
  [11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
  [Bootstrap]: http://getbootstrap.com/
  [12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
  [13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
  [14]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
  [15]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
  [16]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
  [17]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
  [группы связанного списка]: http://getbootstrap.com/components/#list-group-linked
  [18]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
  [19]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
  [глиф]: http://getbootstrap.com/components/#glyphicons
  [20]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
  [21]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png
  [22]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
  [23]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
  [панелей]: http://getbootstrap.com/components/#panels
  [24]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
  [25]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
  [26]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
  [27]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
  [настраиваемую группу связанного списка]: http://getbootstrap.com/components/#list-group-custom-content
  [систему сетки]: http://getbootstrap.com/css/#grid
  [динамические служебные программы]: http://getbootstrap.com/css/#responsive-utilities
  [28]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
  [29]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
  [30]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
  [Официальный блог Bootstrap]: http://blog.getbootstrap.com/
  [Учебник по Twitter Bootstrap от Tutorial Republic]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
  [Игровая площадка Bootstrap]: http://www.bootply.com/
  [Рекомендации от W3C по веб-приложениям для мобильных устройств]: http://www.w3.org/TR/mwabp/
  [Проектные рекомендации от W3C по запросам мультимедиа]: http://www.w3.org/TR/css3-mediaqueries/
