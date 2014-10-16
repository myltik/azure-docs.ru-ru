<properties linkid="Deploy_a_Secure_ASP.NET_Web_Forms_App_with_Membership_OAuth_and_SQL_Database_to_an_Azure_Web_Site" pageTitle="Deploy a Secure ASP.NET Web Forms App with Membership, OAuth, and SQL Database to an Azure Website" metaKeywords="Azure WebForms Hello World Tutorial, Azure Getting Started with WebForms Tutorial, Azure C# WebForms Hello World Tutorial, SQL Azure C# WebForms Tutorial" description="This tutorial shows you how to build a secure ASP.NET 4.5 Web Forms web app that incorporates a SQL Database and deploy the application to Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy a secure ASP.NET 4.5 Web Forms app to an Azure Website" authors="erikre"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="erikre"></tags>

# Развертывание безопасного приложения веб-форм ASP.NET с членством, OAuth и базой данных SQL на веб-сайте Azure.

В данном учебнике показано, как создать безопасное веб-приложение веб-форм ASP.NET 4.5, использующее базу данных SQL, и развернуть приложение в Azure.

> [WACOM.NOTE]
> Версию MVC этого учебника см. в [Развертывание безопасного приложения ASP.NET MVC 5 с членством, OAuth и базой данных SQL на веб-сайте Azure][].

Можно бесплатно создать учетную запись Azure, а если у вас еще нет Visual Studio 2013, пакет SDK автоматически установит Visual Studio 2013 for Web Express. Вы можете приступить к разработке приложений для Azure совершенно бесплатно.

В данном учебнике предполагается, что у читателя нет опыта использования платформы Azure. По завершении изучения этого учебника вы создадите веб-приложение на основе данных, выполняемое в облаке и использующее облачную базу данных.

Вы узнаете:

-   Создание проекта веб-форм ASP.NET 4.5 и его публикация на веб-сайте Azure.
-   Использование OAuth и базы данных членства ASP.NET для защиты приложения.
-   Использование одной базы данных для членства и данных приложения.
-   Использование формирования шаблонов веб-форм для создания веб-страниц, позволяющих изменять данные.
-   Как использовать новый API членства для добавления пользователей и ролей.
-   Как использовать базу данных SQL для хранения данных в Azure.

Вы создадите простое веб-приложение для списка контактов, основанное на платформе веб-форм ASP.NET 4.5 и использующее Entity Framework для доступа к базе данных. На следующее изображение приводится страница веб-форм, позволяющая выполнять операции чтения и записи с базой данных:

![Страница Контакты — изменить][]

> [WACOM.NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно [активировать преимущества для подписчиков MSDN][] или [подписаться на бесплатную пробную версию][].

В этом учебнике содержатся следующие разделы:

-   [Настройка среды разработки][]
-   [Настройка среды Azure][]
-   [Создание приложения веб-форм ASP.NET][]
-   [Добавление базы данных в приложение][]
-   [Включение SSL для проекта][]
-   [Добавление поставщика OAuth 2.0][]
-   [Использование API членства для ограничения доступа][]
-   [Развертывание приложения с базой данных в Azure][]
-   [Просмотр базы данных][]
-   [Дальнейшие действия][]

## Настройка среды разработки

Для начала настройте среду разработки, установив Visual Studio 2013 и Azure SDK для платформы .NET.

1.  Установите [Visual Studio 2013][], если вы еще этого не сделали.
2.  Установите [Azure SDK для Visual Studio 2013][]. Для этого учебника требуется Visual Studio 2013 перед установкой Azure SDK для Visual Studio 2013.

    > [WACOM.NOTE]
    >  В зависимости от того, сколько зависимостей пакета SDK уже имеется на компьютере, установка пакета SDK может занять много времени, от нескольких минут до получаса или более.

3.  При появлении запроса на выполнение или сохранение исполняемой установки нажмите **Выполнить**.
4.  В окне **Установщика веб-платформы** щелкните **Установить** и продолжите установку.
    ![Установщик веб-платформы][]

    > [WACOM.NOTE]
    > Если пакет SDK уже установлен, то установке подлежит 0 компонентов. Количество компонентов для установки указывается в нижнем левом углу окна **Установщик веб-платформы**.

5.  Если не установлено **Visual Studio с обновлением 2**, загрузите и установите **[Visual Studio 2013 с обновлением 2][]** или выше.

    > [WACOM.NOTE]
    > Необходимо установить Visual Studio 2013 с обновлением 2 или выше, чтобы использовать Google OAuth 2.0 и использовать SSL локально без предупреждений. Кроме того, обновление 2 необходимо, чтобы использовать формирование шаблонов веб-форм.

После завершения установки вы получите все компоненты, необходимые для разработки.

## Настройка среды Azure

В этом разделе мы настроим среду Azure, создав веб-сайт Azure и базу данных SQL в Azure.

### Создание веб-сайта и базы данных SQL в Azure

Ваш веб-сайт Azure будет работать в общей среде размещения, а это означает, что он работает на виртуальных машинах, используемых совместно с другими веб-сайтами Azure. Общая среда размещения — недорогой способ начать работу в облаке. Если позднее ваш веб-трафик увеличится, приложение можно масштабировать по необходимости, запуская его на выделенных виртуальных машинах. Если требуется более сложная архитектура, можно перейти в облачную службу Azure. Облачные службы работают на выделенных виртуальных машинах, которые можно настроить в соответствии с конкретными потребностями.

База данных SQL в Azure — это облачная служба реляционной базы данных, построенная на основе технологий SQL Server. Средства и приложения, работающие с SQL Server, работают и с базой данных SQL.

1.  На [портале управления Azure][] щелкните **Веб-сайты** на левой вкладке, а затем щелкните **Создать**.
    ![ Установщик веб-платформы][]
2.  Щелкните **Веб-сайты**, а затем щелкните **Настраиваемое создание** .
    ![Настраиваемое создание ][]
    Открывается мастер **Новый веб-сайт — Настраиваемое создание**.

3.  На шаге **Создать веб-сайт** в мастере введите в поле **URL-адрес** строку, которая будет использоваться как уникальный URL-адрес для приложения. Полный URL-адрес будет состоять из введенной здесь строки и суффикса, который отображается рядом с текстовым полем. На иллюстрации показан URL-адрес, который вероятно уже используется. Поэтому **необходимо выбрать другой URL-адрес** .
    ![Контакты — Создать новый веб-сайт][]
4.  В раскрывающемся списке плана расположения веб-сайтов выберите регион, который расположен к вам ближе всего. Этот параметр определяет, в каком центре обработки данных будет работать ваша виртуальная машина.
5.  В раскрывающемся списке **База данных** выберите **Создать бесплатную базу данных SQL 20 МБ**.
6.  В поле **Имя строки подключения к базе данных** оставьте значение по умолчанию *DefaultConnection*.
7.  В нижней части окна щелкните стрелку, указывающую вправо.
    Мастер перейдет к шагу **Указать параметры базы данных**.
8.  В поле **Имя** введите *`ContactDB`*.
    ![Параметры базы данных][]
9.  В поле **Сервер** выберите **Создать сервер базы данных SQL**.
     Если же вы ранее создали базу данных SQL Server, можно выбрать в раскрывающемся списке этот SQL Server.
10. Укажите в поле **Область** ту же область, в которой создали веб-сайт.
11. Введите **Имя для входа** и **Пароль** администратора.
    Если выбрано действие **Создать сервер базы данных SQL**, не используйте здесь существующие имя и пароль, введите новые, которые в дальнейшем будут использоваться при обращении к базе данных. Если выбран созданный ранее SQL Server, появится запрос на ввод пароля для ранее созданной учетной записи SQL Server. В этом учебнике флажок **Дополнительно** не устанавливается.
12. Щелкните флажок внизу справа от поля, чтобы указать завершение работы.

**Портал управления Azure** возвращается на страницу **Веб-сайты**, а в столбце **Состояние** указывается, что сайт создается. Через какое-то время (обычно меньше минуты) в столбце **Состояние** указывается, что сайт успешно создан. В строке навигации слева число сайтов вашей учетной записи отображается рядом со значком **Веб-сайты**, а число баз данных — рядом со значком **Базы данных SQL**.

## Создание приложения веб-форм ASP.NET

Веб-сайт Azure создан, но его контент еще отсутствует. Следующим шагом будет создание веб-приложения Visual Studio, которое будет опубликовано в Azure.

### Создание проекта

1.  Выберите **Новый проект** в меню **Файл** в Visual Studio.
    ![Меню Файл — Новый проект][]
2.  Выберите группу шаблонов слева **Шаблоны** -\> **Visual C\#** -\> **Интернет**.
3.  Выберите шаблон **Веб-приложения ASP.NET** в центральном столбце.
4.  Присвойте проекту имя *ContactManager* и нажмите **OK**.
    ![Диалоговое окно Новый проект][]

    > [WACOM.NOTE]
    >  В этой обучающей серии используется имя проекта **ContactManager**. Рекомендуется использовать именно это имя проекта, чтобы код из учебной серии работал нормально.

5.  В диалоговом окне **Новый проект ASP.NET** выберите шаблон **Веб-приложение**. Снимите флажок **Разместить в облаке**, если он установлен, и нажмите **OK**.
    ![ Диалоговое окно Новый проект ASP.NET][]
     Создается приложение веб-форм.

    ### Обновление главной страницы

    В среде веб-форм ASP.NET главные страницы позволяют создавать устойчивый макет страниц приложения. Одна главная страница определяет внешний вид и стандартное поведение всех страниц (или группы страниц) приложения. Далее можно приступить к созданию отдельных страниц контента с содержимым, которое вы хотите отобразить. При поступлении запросов на страницы контента от пользователей ASP.NET объединяет их с главной страницей, чтобы получить на выходе макет главной страницы с содержимым страницы контента.
    Новому сайту необходимо обновить имя приложения и ссылку. Ссылка указывает на страницу, на которой будут отображаться сведения о контактах. Чтобы выполнить эти изменения, необходимо изменить HTML на главной странице.

6.  В **Обозревателе решений** найдите и откройте страницу *Site.Master*.
7.  Если страница находится в представлении **Конструирование**, измените его на представление **Просмотр исходного кода**.
8.  Обновите главную страницу, изменив или добавив разметку, выделенную желтым цветом:

``` prettyprint
<%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site.master.cs" Inherits="ContactManager.SiteMaster" %>

<!DOCTYPE html>

<html lang="en">
<head runat="server">
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title><%: Page.Title %> — Contact Manager</title>

    <asp:PlaceHolder runat="server">
        <%: Scripts.Render("~/bundles/modernizr") %>
    </asp:PlaceHolder>
    <webopt:bundlereference runat="server" path="~/Content/css" />
    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />

</head>
<body>
    <form runat="server">
        <asp:ScriptManager runat="server">
            <Scripts>
                <%-- Для получения дополнительной информации о связывающих скриптах в ScriptManager см. http://go.microsoft.com/fwlink/?LinkID=301884 --%>
                <%--Framework Scripts--%>
                <asp:ScriptReference Name="MsAjaxBundle" />
                <asp:ScriptReference Name="jquery" />
                <asp:ScriptReference Name="bootstrap" />
                <asp:ScriptReference Name="respond" />
                <asp:ScriptReference Name="WebForms.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebForms.js" />
                <asp:ScriptReference Name="WebUIValidation.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebUIValidation.js" />
                <asp:ScriptReference Name="MenuStandards.js" Assembly="System.Web" Path="~/Scripts/WebForms/MenuStandards.js" />
                <asp:ScriptReference Name="GridView.js" Assembly="System.Web" Path="~/Scripts/WebForms/GridView.js" />
                <asp:ScriptReference Name="DetailsView.js" Assembly="System.Web" Path="~/Scripts/WebForms/DetailsView.js" />
                <asp:ScriptReference Name="TreeView.js" Assembly="System.Web" Path="~/Scripts/WebForms/TreeView.js" />
                <asp:ScriptReference Name="WebParts.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebParts.js" />
                <asp:ScriptReference Name="Focus.js" Assembly="System.Web" Path="~/Scripts/WebForms/Focus.js" />
                <asp:ScriptReference Name="WebFormsBundle" />
                <%--Site Scripts--%>
            </Scripts>
        </asp:ScriptManager>

        <div class="navbar navbar-inverse navbar-fixed-top">
            <div class="container">
                <div class="navbar-header">
                    <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    <a class="navbar-brand" runat="server" id="ContactDemoLink" href="~/Contacts/Default.aspx">Contact Demo</a>
                </div>
                <div class="navbar-collapse collapse">
                    <ul class="nav navbar-nav">
                        <li><a runat="server" href="~/">Home</a></li>
                        <li><a runat="server" href="~/About">About</a></li>
                        <li><a runat="server" href="~/Contact">Contact</a></li>
                    </ul>
                    <asp:LoginView runat="server" ViewStateMode="Disabled">
                        <AnonymousTemplate>
                            <ul class="nav navbar-nav navbar-right">
                                <li><a runat="server" href="~/Account/Register">Register</a></li>
                                <li><a runat="server" href="~/Account/Login">Log in</a></li>
                            </ul>
                        </AnonymousTemplate>
                        <LoggedInTemplate>
                            <ul class="nav navbar-nav navbar-right">
                                <li><a runat="server" href="~/Account/Manage" title="Manage your account">Hello, <%: Context.User.Identity.GetUserName() %> !</a></li>
                                <li>
                                    <asp:LoginStatus runat="server" LogoutAction="Redirect" LogoutText="Log off" LogoutPageUrl="~/" OnLoggingOut="Unnamed_LoggingOut" />
                                </li>
                            </ul>
                        </LoggedInTemplate>
                    </asp:LoginView>
                </div>
            </div>
        </div>
        <div class="container body-content">
            <asp:ContentPlaceHolder ID="MainContent" runat="server">
            </asp:ContentPlaceHolder>
            <hr />
            <footer>
                <p>© <%: DateTime.Now.Year %> — Contact Manager</p>
            </footer>
        </div>
    </form>
</body>
</html>
```

Далее в этом учебнике будет добавлено формирование шаблонов веб-форм. При формировании шаблонов создается страница, на которую ссылается указанная выше ссылка “Contact Demo”.

### Локальный запуск приложения

1.  В **Обозревателе решений** щелкните правой кнопкой мыши страницу *Default.aspx* и выберите **Задать как начальную страницу**.
2.  Далее нажмите **CTRL+F5**, чтобы запустить приложение.
    Появляется страница приложения по умолчанию в окне браузера по умолчанию.
    ![Контакты — Создать новый веб-сайт][1]

Это все, что требуется для создания приложения, которое будет развернуто в Azure. Несколько позднее мы добавим функциональность базы данных, а также необходимы страницы для отображения и изменения контактных данных.

### Развертывание приложения в Azure

После создания и запуска приложения локально можно приступить к его развертыванию в Azure.

1.  В **Обозревателе решений** Visual Studio щелкните правой кнопкой мыши проект и выберите **Опубликовать** в контекстном меню.
    ![Выберите Опубликовать][]
     Отображается диалоговое окно **Публикация веб-сайта**.

2.  На вкладке **Профиль** диалогового окна **Публикация веб-сайта** щелкните **Веб-сайты Azure**.
    ![ Диалоговое окно Публикация веб-сайтов][]
3.  Если вы еще не выполнили вход, нажмите кнопку **Вход** в диалоговом окне **Выбрать существующие веб-сайты**. После выполнения входа выберите веб-сайт, который был создан в первой части учебника. Нажмите **OK**, чтобы продолжить.
    ![Диалоговое окно Выбрать существующий веб-сайт][]
     Visual Studio загрузит ваши параметры публикации.
4.  В диалоговом окне **Опубликовать веб-сайт** нажмите **Опубликовать**.
    ![Диалоговое окно Опубликовать веб-сайт][]
     Вы сможете просмотреть общее состояние публикации и окне **Действие веб-публикации** в Visual Studio:
    ![ Действие веб-публикации][]

Созданное вами приложение теперь выполняется в облаке. При следующем развертывании приложения из Visual Studio будут развернуты только измененные (или новые) файлы.
 ![Приложение в браузере][]

> [WACOM.NOTE]
>  При появлении ошибки во время публикации на уже созданный веб-сайт, можно очистить местоположение перед добавлением новых файлов.
>  Опубликуйте свое приложение еще раз, но в диалоговом окне **Опубликовать веб-сайт** выберите вкладку **Параметры**. Далее установите значение **Отладка** для конфигурации и выберите параметр **Удалить дополнительные файлы в месте назначения**. Выберите **Опубликовать**, чтобы развернуть приложение еще раз.
>  ![Диалоговое окно Опубликовать веб-сайт][2]

## Добавление базы данных в приложение

Теперь вы обновите приложение веб-форм, чтобы добавить возможности для отображения и обновления контактов, а также сохранения данных в базе данных по умолчанию. При создании проекта веб-форм база данных создается по умолчанию. Приложение будет использовать компонент Entity Framework для доступа к базе данных, а также чтения и обновления данных в базе данных.

### Добавление класса модели данных

Начните с создания простой модели данных в коде. Эта модель данных будет содержаться в классе с именем `Contacts`. Имя класса `Contacts` было выбрано, чтобы избежать конфликта с именем класса `Contact`, содержащегося в файле Contact.aspx.cs, созданном шаблоном веб-форм.

1.  В **Обозревателе решений** щелкните правой кнопкой мыши папку *Models* и выберите **Добавить** -\> **Класс**.
    ![ Выбрать класс][]
     Отображается диалоговое окно **Добавить новый элемент**.

2.  Присвойте этому новому классу имя *Contacts.cs*.
    ![ Диалоговое окно Добавить новый элемент][]
3.  Замените код по умолчанию на приведенный ниже:

	<pre class="prettyprint">
	using System.ComponentModel.DataAnnotations;
	using System.Globalization;
	
	namespace ContactManager.Models
	{
	    public class Contacts
	    {
	        [ScaffoldColumn(false)]
	        [Key]
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
	</pre>

Класс **Contacts** определяет данные, которые будут храниться для каждого контакта, а также первичный ключ (`ContactID`) необходимый для базы данных. Класс **Contacts** отражает контактные данные, которые будут отображаться. Каждый экземпляр объекта Contacts будет соответствовать ряду в таблице реляционной базы данных, а каждое свойство класса Contacts — столбцу в таблице реляционной базе данных. Далее в этом учебнике вы просмотрите контактные данные, содержащиеся в базе данных.

### Формирование шаблонов веб-форм

Выше вы создали класс модели **Contacts**. Теперь можно использовать формирование шаблонов веб-форм для создания страниц *List*, *Insert*, *Edit* и *Delete*, которые используются при работе с этими данными. При формировании шаблонов веб-форм используется среда Entity Framework, Bootstrap и динамические данные. По умолчанию компонент формирования шаблонов веб-форм устанавливается как расширение к проекту в виде части шаблона проекта при использовании Visual Studio 2013.

В последующих шагах рассматривается использование компонента формирования шаблонов веб-форм.

1.  В Visual Studio из строки меню выберите **Средства** -\> **Расширения и обновления**.
     Отображается диалоговое окно **Расширения и обновления**.
2.  В левой панели диалогового окна выберите **Онлайн** -\> **Коллекция Visual Studio** -\> **Средства** -\> **Формирование шаблонов**.
3.  Если «Формирование шаблонов веб-форм» в списке отсутствует, введите 'Web Forms Scaffolding' в поле поиска в правой части диалогового окна.
4.  Если компонент формирования шаблонов веб-форм еще не установлен, выберите **Загрузить**, чтобы загрузить и установить его. При необходимости перезапустите Visual Studio. Не забудьте сохранить изменения в проекте при получении запроса.
    ![ Диалоговое окно Расширения и обновления][]
5.  Создайте проект (**Ctrl+Shift+B**).
    Перед тем, как использовать механизм формирования шаблонов, необходимо создать проект.
6.  В **Обозревателе решений** щелкните правой кнопкой мыши *project* и выберите **Добавить** -\> **Новый элемент формирования шаблонов** .
    Появляется диалоговое окно **Добавить формирование шаблонов**.
7.  Выберите **Веб-формы** из левой панели и выберите **Страницы веб-форм с использованием Entity Framework** из центральной панели. Далее щелкните **Добавить**.
    ![ Диалоговое окно Добавить формирование шаблонов][]
     Отображается диалоговое окно **Добавить страницы веб-форм**.

8.  В диалоговом окне **Добавление страниц веб-форм** установите для поля **Класс модели** значение `Contact (ContactManager.Models)`. В поле **Класс контекста данных** выберите `ApplicationDbContext (ContactManager.Models)`. Далее щелкните **Добавить** .
    ![ Диалоговое окно Добавить страницы веб-форм][]

Компонент формирования шаблонов веб-форм добавляет новую папку, которая содержит страницы *Default.aspx*, *Delete.aspx*, *Edit.aspx* и *Insert.aspx*. Кроме того, он создает папку *DynamicData*, которая содержит папки *EntityTemplates* и *FieldTemplates*. `ApplicationDbContext` будет использоваться и для базы данных членства, и для наших контактных данных.

### Настройка приложения для использования модели данных

Следующая задача заключается в активации компонента Code First Migrations для создания базы данных на основе созданной модели данных. Кроме этого, вы добавите простые данные и инициализатор данных.

1.  В меню **Средства** выберите **Диспетчер пакетов NuGet**, а затем **Консоль диспетчера пакетов** .
    ![ Диалоговое окно Добавить страницы веб-форм][3]
2.  В окне Консоль диспетчера пакетов введите следующую команду:

    ``` prettyprint
    enable-migrations 
    ```

    Команда enable-migrations создает папку *Migrations* и помещает в нее файл *Configuration.cs*, который можно изменять и использовать для заполнения базы данных, а также настройки миграций.

3.  В окне **Консоль диспетчера пакетов** введите следующую команду:

    ``` prettyprint
    add-migration Initial
    ```

    Команда `add-migration Initial` создает файл с инициалом <date_stamp>в папке *Migrations*, которая создает базу данных. Первый параметр (инициал) является произвольным и используется для создания имени файла. Новые файлы классов можно просмотреть в **обозревателе решений**. В классе `Initial` метод `Up` создает таблицу `Contact`, а метод `Down` (используется при возвращении к предыдущему состоянию) удаляет эту таблицу.

4.  Откройте файл *Migrations\\Configuration.cs*.
5.  Добавьте следующее пространство имен:

    ``` prettyprint
    использование ContactManager.Models;
    ```

6.  Замените метод `Seed` следующим кодом:

	<pre class="prettyprint">
	protected override void Seed(ContactManager.Models.ApplicationDbContext context)
	{
	    context.Contacts.AddOrUpdate(p => p.Name,
	       new Contacts
	       {
	           ContactId = 1,
	           Name = "Ivan Irons",
	           Address = "One Microsoft Way",
	           City = "Redmond",
	           State = "WA",
	           Zip = "10999",
	           Email = "ivani@wideworldimporters.com",
	       },
	       new Contacts
	        {
	            ContactId = 2,
	            Name = "Brent Scholl",
	            Address = "5678 1st Ave W",
	            City = "Redmond",
	            State = "WA",
	            Zip = "10999",
	            Email = "brents@wideworldimporters.com",
	        },
	        new Contacts
	        {
	            ContactId = 3,
	            Name = "Terrell Bettis",
	            Address = "9012 State St",
	            City = "Redmond",
	            State = "WA",
	            Zip = "10999",
	            Email = "terrellb@wideworldimporters.com",
	        },
	        new Contacts
	        {
	            ContactId = 4,
	            Name = "Jo Cooper",
	            Address = "3456 Maple St",
	            City = "Redmond",
	            State = "WA",
	            Zip = "10999",
	            Email = "joc@wideworldimporters.com",
	        },
	        new Contacts
	        {
	            ContactId = 5,
	            Name = "Ines Burnett",
	            Address = "7890 2nd Ave E",
	            City = "Redmond",
	            State = "WA",
	            Zip = "10999",
	            Email = "inesb@wideworldimporters.com",
	        }
	        );
	}
	</pre>

    Этот код инициализирует базу данных с контактной информацией. Дополнительные сведения об инициализации базы данных см. в разделе [Заполнение и отладка баз данных Entity Framework (EF)][].

7.  В окне **Консоль диспетчера пакетов** введите команду:

    ``` prettyprint
    update-database
    ```

    Команда `update-database` выполняет первую миграцию, при которой создается база данных. По умолчанию база данных создается как база данных LocalDB в SQL Server Express.
    ![ Консоль диспетчера пакетов][]

### Локальный запуск приложения и отображение данных

Запустите приложение и просмотрите, как видны контакты.

1.  Сначала постройте проект (**Ctrl+Shift+B**).
2.  Нажмите **Ctrl+F5**, чтобы запустить приложение.
     Открывается браузер и отображается страница *Default.aspx*.
3.  Выберите ссылку **Contact Demo** в верхней части страницы, чтобы отобразить страницу *Contact List*.
    ![ Страница Список контактов][]

## Включение SSL для проекта

Secure Sockets Layer (SSL) — это протокол, позволяющие веб-серверам и веб-клиентам более безопасно обмениваться данными с помощью шифрования. Если протокол SSL не используется, пакеты данных, передаваемые между клиентом и сервером, открыты для сканирования любому человеку с физическим доступом к сети. Кроме того, некоторые распространенные схемы проверки подлинности небезопасны при использовании простого HTTP. В частности, базовая проверка подлинности и формы проверки подлинности отправляют незашифрованные учетные данные. Чтобы обеспечить безопасность, эти схемы проверки подлинности должны использовать протокол SSL.

1.  В **Обозревателе решений** щелкните проект **ContactManager**, затем нажмите **F4**, чтобы отобразить окно **Свойства**.
2.  Для параметра **SSL включен** измените значение на `true`.
3.  Скопируйте **URL-адрес SSL**, чтобы использовать его в дальнейшем.
    URL-адрес SSL будет `https://localhost:44300/` если только вы раньше не создавали веб-сайты SSL (как показано ниже).
    ![Свойства проекта][]
4.  В **Обозревателе решений** щелкните правой кнопкой мыши проект **Contact Manager** и щелкните **Свойства**.
5.  На левой вкладке щелкните **Интернет**.
6.  Измените **Url проекта** на **URL-адрес SSL**, который вы сохранили ранее.

    ![Веб-свойства проекта][]
7.  Сохраните страницу, нажав **CTRL+S**.
8.  Нажмите **Ctrl+F5**, чтобы запустить приложение.
    Visual Studio отобразит параметр, позволяющий избежать предупреждений SSL.
9.  Нажмите **Yes** для подтверждения доверия сертификату IIS Express SSL и продолжения.
    ![Сведения о сертификате IIS Express SSL][]
     Отображается предупреждение системы безопасности.

10. Нажмите **Yes**, чтобы установить сертификат в localhost.
    ![ Диалоговое окно Предупреждение системы безопасности][]
     Отображается окно браузера.

Теперь можно просто протестировать веб-приложение локально с использованием SSL.

## Добавление поставщика OAuth 2.0

Веб-формы ASP.NET предлагают расширенные параметры для членства и проверки подлинности. К этим расширениям относится OAuth. OAuth — это открытый протокол, обеспечивающий безопасную авторизацию простым и стандартным методом из веб-приложений, мобильных и настольных приложений. В интернет-шаблоне ASP.NET MVC используется OAuth для предоставления Facebook, Twitter, Google и Microsoft в качестве поставщиков проверки подлинности. В этом учебнике в качестве поставщика проверки подлинности используется только Google, но вы легко можете изменить код, чтобы использовать любой другой поставщик. Шаги по реализации других поставщиков очень похожи на шаги, представленные в этом учебнике.

Помимо проверки подлинности в учебнике будут использоваться роли для реализации авторизации. Только пользователи, добавленные в роль `canEdit`, смогут изменять данные (создавать, изменять и удалять контакты).

В последующих шагах вы сможете добавить поставщика проверки подлинности Google.

1.  Откройте файл *App\_Start\\Startup.Auth.cs*.
2.  Удалите символы комментариев из метода `app.UseGoogleAuthentication()` чтобы метод выглядел следующим образом:

	<pre class="prettyprint">
	            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
	            {
	                ClientId = "",
	                ClientSecret = ""
	            });
	</pre>

3.  Перейдите к [Консоли разработчиков Google][]. Вам также потребуется выполнить вход со своей учетной записью электронной почты разработчика Google (gmail.com). Если у вас нет учетной записи Google, перейдите по ссылке **Создать учетную запись**.
     Далее вы увидите **Консоль разработчиков Google**.
    ![ Консоль разработчиков Google][]

4.  Нажмите кнопку **Создать проект** и введите имя и идентификатор проекта (можно использовать значения по умолчанию). Далее установите флажок **agreement checkbox** и нажмите кнопку **Создать** .
    ![ Google — Новый проект][]
     Через несколько секунд новый проект будет создан, а браузер отобразит страницу новых проектов.
5.  На левой вкладке щелкните **APIs & auth**, а затем щелкните **Учетные данные**.
6.  Щелкните **Создать идентификатор нового клиента** в разделе **OAuth**.
     Отображается диалоговое окно **Создать идентификатор клиента**.
    ![Google — Создать идентификатор клиента][]
7.  В диалоговом окне **Создать идентификатор клиента** сохраните значение по умолчанию **Веб-приложение** для типа приложения.
8.  Установите в поле **Авторизованные источники JavaScript** URL-адрес SSL, который использовался ранее в этом учебнике (**<https://localhost:44300/>**, если только не создавались другие проекты SSL).
    Этот URL-адрес служит источником вашего приложения. В нашем примере вводится только тестовый URL-адрес localhost. Тем не менее, можно ввести несколько URL-адресов учетной записи для localhost и производства.

9.  Установите в поле **Авторизованный URI перенаправления** следующее значение:

    ``` prettyprint
    https://localhost:44300/signin-google
    ```

    Это значение представляет собой URI, который ASP.NET OAuth использует для обмена данными с сервером Google OAuth. Не забудьте URL-адрес SSL, который вы использовали выше (**<https://localhost:44300/>**, если только не создавались другие проекты SSL).

10. Нажмите кнопку **Создать идентификатор клиента**.
11. В Visual Studio обновите метод `UseGoogleAuthentication` страницы *Startup.Auth.cs* путем копирования и вставки **AppId** и **App Secret** в метод. Значения **AppId** и **App Secret**, показанные ниже, приводятся в качестве примера и работать не будут.

	<pre class="prettyprint">  
	using System;
	using Microsoft.AspNet.Identity;
	using Microsoft.AspNet.Identity.EntityFramework;
	using Microsoft.AspNet.Identity.Owin;
	using Microsoft.Owin;
	using Microsoft.Owin.Security.Cookies;
	using Microsoft.Owin.Security.DataProtection;
	using Microsoft.Owin.Security.Google;
	using Owin;
	using ContactManager.Models;
	
	namespace ContactManager
	{
	    public partial class Startup {
	
	        // For more information on configuring authentication, please visit http://go.microsoft.com/fwlink/?LinkId=301883
	        public void ConfigureAuth(IAppBuilder app)
	        {
	            // Configure the db context and user manager to use a single instance per request
	            app.CreatePerOwinContext(ApplicationDbContext.Create);
	            app.CreatePerOwinContext<ApplicationUserManager>(ApplicationUserManager.Create);
	
	            // Enable the application to use a cookie to store information for the signed in user
	            // and to use a cookie to temporarily store information about a user logging in with a third party login provider
	            // Configure the sign in cookie
	            app.UseCookieAuthentication(new CookieAuthenticationOptions
	            {
	                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
	                LoginPath = new PathString("/Account/Login"),
	                Provider = new CookieAuthenticationProvider
	                {
	                    OnValidateIdentity = SecurityStampValidator.OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
	                        validateInterval: TimeSpan.FromMinutes(20),
	                        regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
	                }
	            });
	            // Use a cookie to temporarily store information about a user logging in with a third party login provider
	            app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);
	
	            // Uncomment the following lines to enable logging in with third party login providers
	            //app.UseMicrosoftAccountAuthentication(
	            //    clientId: "",
	            //    clientSecret: "");
	
	            //app.UseTwitterAuthentication(
	            //   consumerKey: "",
	            //   consumerSecret: "");
	
	            //app.UseFacebookAuthentication(
	            //   appId: "",
	            //   appSecret: "");
	
	            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
	            {
	                ClientId = "<mark>000000000000.apps.googleusercontent.com</mark>",
	                ClientSecret = "<mark>00000000000</mark>"
	            });
	        }
	    }
	}
	</pre>  

12. Нажмите **CTRL+F5**, чтобы построить и запустить приложение. Щелкните ссылку **Войти в систему**.
13. В разделе **Использовать другую службу для входа** нажмите кнопку **Google**.
    ![Вход][]
14. Если будет необходимо ввести учетные данные, произойдет перенаправление на сайт google, где вы сможете ввести свои учетные данные.
    ![ Google — Вход][]
15. После ввода учетных данных появится запрос на получение разрешений для веб-приложения, которое было только что создано:
    ![ Учетная запись службы проекта по умолчанию][]
16. Нажмите кнопку **Принимаю**. Теперь произойдет перенаправление снова на страницу **Регистрация** приложения **ContactManager**, на которой можно зарегистрировать учетную запись Google.
    ![Регистрация с учетной записью Google][]
17. Имеется возможность изменить локальное имя регистрации электронной почты, использованного для учетной записи Gmail. Однако, как правило, рекомендуется сохранять псевдоним электронной почты по умолчанию (то есть тот, который использовался для проверки подлинности). Щелкните **Вход**.

## Использование API членства для ограничения доступа

ASP.NET Identity — система членства, используемая для проверки подлинности при построении веб-приложения ASP.NET. Она позволяет упростить интеграцию данных профиля пользователя с данными приложения. Кроме того, ASP.NET Identity позволяет выбирать модель сохраняемости для профилей пользователей в приложении. Данные можно сохранять в базе данных SQL Server или другом хранилище, в том числе хранилищах данных *NoSQL*, таких как таблица хранилища Azure.

При использовании шаблона веб-форм ASP.NET по умолчанию, вы получает встроенную функциональность членства, которую можно немедленно использовать при работе приложения. ASP.NET Identity будет использоваться для добавления роли администратора и назначения пользователя на эту роль. Далее вы узнаете, как ограничивать доступ к папке администрирования и страницам в этой папке, которые используются для изменения контактных данных.

### Добавление администратора

Используя ASP.NET Identity, можно добавить роль администратора и назначить пользователя на эту роль с помощью кода.

1.  В **Обозревателе решений** откройте файл *Configuration.cs* в папке *Migrations*.
2.  Добавьте следующие операторы `using` в пространство имен `ContactManger.Migrations`:

	<pre class="prettyprint">
	using Microsoft.AspNet.Identity;
	using Microsoft.AspNet.Identity.EntityFramework;
	</pre>

3.  Добавьте следующий метод `AddUserAndRole` в класс `Configuration` после метода `Seed`:

    ``` prettyprint
     public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context) { IdentityResult IdRoleResult; IdentityResult IdUserResult;
        var roleStore = new RoleStore&lt;IdentityRole&gt;(context);
        var roleMgr = new RoleManager&lt;IdentityRole&gt;(roleStore);

        if (!roleMgr.RoleExists(&quot;canEdit&quot;))
        {
            IdRoleResult = roleMgr.Create(new IdentityRole { Name = &quot;canEdit&quot; });
        }

        //var userStore = new UserStore&lt;ApplicationUser&gt;(context);
        //var userMgr = new UserManager&lt;ApplicationUser&gt;(userStore);
        var userMgr = new UserManager&lt;ApplicationUser&gt;(new UserStore&lt;ApplicationUser&gt;(context));

        var appUser = new ApplicationUser
        {
            UserName = &quot;canEditUser@wideworldimporters.com&quot;,
            Email = &quot;canEditUser@wideworldimporters.com&quot;
        };
        IdUserResult = userMgr.Create(appUser, &quot;Pa$$word1&quot;);

        if (!userMgr.IsInRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;))
        {
          //  IdUserResult = userMgr.AddToRole(appUser.Id, &quot;canEdit&quot;);
            IdUserResult = userMgr.AddToRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;);
        }
    }
    ```

    </pre>
4.  Добавьте вызов метода `AddUserAndRole` в начале метода `Seed`. Обратите внимание, что показано только начало метода `Seed`.

	<pre class="prettyprint">
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
	</pre>  

5.  После сохранения всех изменений в **Консоли диспетчера пакетов** выполните следующие команды:

    ``` prettyprint
    update-database
    ```

    Этот код создает новую роль с именем `canEdit` и создает нового пользователя с адресом электронной почты <canEditUser@wideworldimporters.com>. Далее код добавляет <canEditUser@wideworldimporters.com> в роль `canEdit`. Дополнительные сведения см. на [странице ресурсов ASP.NET Identity][].

    </p>

### Ограничение доступа в папку администрирования

Примерное приложение **ContactManager** позволяет видеть контакты как анонимным, так и выполнившим вход пользователям. Однако после завершения этого раздела только пользователи, выполнившие вход и назначенные на роль "canEdit", смогут изменять контакты.

Вы создадите папку с именем *Admin*, доступ к которой смогут получить только пользователи назначенные на роль "canEdit".

1.  В **Обозревателе решений** добавьте в папке *Contacts* подпапку и присвойте ей имя *Admin*.
2.  Переместите следующие файлы из папки *Contacts* в папку *Contacts/Admin*:

    -   *Delete.aspx* и \* Delete.aspx.cs\*
    -   *Edit.aspx* и \* Edit.aspx.cs\*
    -   *Insert.aspx* и \* Insert.aspx.cs\*

3.  Обновите ссылки связи в *Contacts/Default.aspx*, добавив "Admin/" перед ссылками страниц, которые связывают с *Insert.aspx*, *Edit.aspx* и *Delete.aspx*:

	<pre class="prettyprint">
	&lt;%@ Page Title=&quot;ContactsList&quot; Language=&quot;C#&quot; MasterPageFile=&quot;~/Site.Master&quot; CodeBehind=&quot;Default.aspx.cs&quot; Inherits=&quot;ContactManager.Contacts.Default&quot; ViewStateMode=&quot;Disabled&quot; %&gt;
	&lt;%@ Register TagPrefix=&quot;FriendlyUrls&quot; Namespace=&quot;Microsoft.AspNet.FriendlyUrls&quot; %&gt;
	
	&lt;asp:Content runat=&quot;server&quot; ContentPlaceHolderID=&quot;MainContent&quot;&gt;
	    &lt;h2&gt;Contacts List&lt;/h2&gt;
	    &lt;p&gt;
	        &lt;asp:HyperLink runat=&quot;server&quot; NavigateUrl=&quot;<mark>Admin/</mark>Insert.aspx&quot; Text=&quot;Create new&quot; /&gt;
	    &lt;/p&gt;
	    &lt;div&gt;
	        &lt;asp:ListView runat=&quot;server&quot;
	            DataKeyNames=&quot;ContactId&quot; ItemType=&quot;ContactManager.Models.Contacts&quot;
	            AutoGenerateColumns=&quot;false&quot;
	            AllowPaging=&quot;true&quot; AllowSorting=&quot;true&quot;
	            SelectMethod=&quot;GetData&quot;&gt;
	            &lt;EmptyDataTemplate&gt;
	                There are no entries found for Contacts
	            &lt;/EmptyDataTemplate&gt;
	            &lt;LayoutTemplate&gt;
	                &lt;table class=&quot;table&quot;&gt;
	                    &lt;thead&gt;
	                        &lt;tr&gt;
	                            &lt;th&gt;Name&lt;/th&gt;
	                            &lt;th&gt;Address&lt;/th&gt;
	                            &lt;th&gt;City&lt;/th&gt;
	                            &lt;th&gt;State&lt;/th&gt;
	                            &lt;th&gt;Zip&lt;/th&gt;
	                            &lt;th&gt;Email&lt;/th&gt;
	                            &lt;th&gt;&amp;nbsp;&lt;/th&gt;
	                        &lt;/tr&gt;
	                    &lt;/thead&gt;
	                    &lt;tbody&gt;
	                        &lt;tr runat=&quot;server&quot; id=&quot;itemPlaceholder&quot; /&gt;
	                    &lt;/tbody&gt;
	                &lt;/table&gt;
	            &lt;/LayoutTemplate&gt;
	            &lt;ItemTemplate&gt;
	                &lt;tr&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Name&quot; ID=&quot;Name&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Address&quot; ID=&quot;Address&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;City&quot; ID=&quot;City&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;State&quot; ID=&quot;State&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Zip&quot; ID=&quot;Zip&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Email&quot; ID=&quot;Email&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;a href=&quot;<mark>Admin/</mark>Edit.aspx?ContactId=&lt;%#: Item.ContactId%&gt;&quot;&gt;Edit&lt;/a&gt; | 
	                        &lt;a href=&quot;<mark>Admin/</mark>Delete.aspx?ContactId=&lt;%#: Item.ContactId%&gt;&quot;&gt;Delete&lt;/a&gt;
	                    &lt;/td&gt;
	                &lt;/tr&gt;
	            &lt;/ItemTemplate&gt;
	        &lt;/asp:ListView&gt;
	    &lt;/div&gt;
	&lt;/asp:Content&gt;
	</pre>

4.  Измените шесть ссылок кода `Response.Redirect("Default.aspx")` на `Response.Redirect("~/Contacts/Default.aspx")` для следующих трех файлов:

    -   *Delete.aspx.cs*
    -   *Edit.aspx.cs*
    -   *Insert.aspx.cs*

    Теперь все ссылки будут работать правильно при отображении и обновлении контактных данных.

5.  Чтобы ограничить доступ к папке *Admin*, в **Обозревателе решений** щелкните правой кнопкой мыши папку *Admin* м выберите **Добавить новый элемент**.
6.  Из списка веб-шаблонов Visual C# выберите **Файл веб-конфигурации** из среднего списка, примите имя по умолчанию *Web.config*, а затем выберите **Добавить**.
7.  Замените существующий контент XML в файле *Web.config* на следующий:

    ``` prettyprint
    <?xml version="1.0"?><configuration> <system.web> <authorization> <allow roles="canEdit"/> <deny users="*"/> </authorization> </system.web></configuration>
    ```

8.  Сохраните файл *Web.config*.
     Файл *Web.config* определяет только пользователей, что только пользователи, назначенный на роль "canEdit", могут получать доступ к страницам в папке *Admin*.

Когда пользователи, не являющиеся частью роли "canEdit", пытаются изменить данные, они перенаправляются на страницу *Вход*.

## Развертывание приложения с базой данных в Azure

После создания веб-приложения можно его опубликовать в Azure.

### Публикация приложения

1.  В Visual Studio постройте проект (**Ctrl+Shift+B**).
2.  Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Опубликовать**.

    ![ Параметр меню Публикация][]
     Отображается диалоговое окно **Публикация веб-сайта**.

    ![ Диалоговое окно Публикация веб-сайта][]
3.  На вкладке **Профиль** выберите **Веб-сайты Azure** в качестве цели публикации, если они уже не были выбраны.

    ![ Диалоговое окно Публикация веб-сайтов][4]
4.  Щелкните **Вход**, если вы еще не вошли в систему.
5.  Выберите существующий веб-сайт, созданный ранее в этом учебнике, из раскрывающегося списка **Существующие веб-сайты** и нажмите кнопку **OK**.

    ![ Диалоговое окно Выбор существующего веб-сайта][]

     При появлении запроса на сохранение сделанных в профиле изменений, выберите **Yes**.
6.  Щелкните вкладку **Параметры**.

    ![ Диалоговое окно Выбор существующего веб-сайта][5]
7.  Установите в раскрывающемся списке **Конфигурация** значение **Отладка**.
8.  Щелкните значок **стрелка вниз** рядом с **ApplicationDbContext** и установите в этом поле значение **ContactDB**.
9.  Установите флажок **Выполнить Code First Migrations**.

    > [WACOM.NOTE]
    >  В данном примере этот флажок нужно устанавливать только при первой публикации приложения. Благодаря этому метод *Seed* в файле *Configuration.cs* будет вызываться только один раз.

10. Затем щелкните **Опубликовать**.
     Приложение будет опубликовано в Azure.

> [WACOM.NOTE]
>  Если закрыть и снова открыть Visual Studio после создания профиля публикации, вы можете не увидеть строку подключения в раскрывающемся списке. В этом случае не изменяйте ранее созданный профиль публикации, а точно таким же способом создайте новый, а затем выполните следующие шаги на вкладке **Параметры**.)

### Просмотр приложения в Azure

1.  В браузере перейдите по ссылке **Contact Demo**.
     Отображается список контактов.
    ![Список контактов в браузере][]

2.  Выберите **Создать новый** на странице **Список контактов**.
    ![Список контактов в браузере][6]
     Происходит перенаправление на страницу **Вход** потому, что вы не вошли в систему с учетной записью, позволяющей изменять контакты.
3.  После ввода указанных ниже адреса электронной почты и пароля щелкните кнопку **Вход**.
     **Адрес электронной почты**: `canEditUser@wideworldimporters.com`
    **Пароль**: `Pa$$word1`
    ![Страница Входа][]

4.  Введите новые данные для каждого поля и нажмите кнопку **Вставить**.
    ![ Страница добавления нового контакта][]
     Отображается страница *EditContactList.aspx* с новой записью.
    ![ Страница добавления нового контакта][7]

5.  Щелкните ссылку **Выход**.

### Остановка приложения

Остановка веб-сайта для предотвращения регистрации других пользователей и использования ими приложения.

1.  В Visual Studio в меню **Просмотр** выберите **Обозреватель сервера**.
2.  В **Обозревателе сервера** перейдите к элементу **Веб-сайты**.
3.  Щелкните правой кнопкой мыши каждый экземпляр веб-сайта и выберите **Остановить веб-сайт**.
    ![ Элемент меню Остановить веб-сайт][]

    Можно также выбрать веб-сайт на портале управления Azure, а затем щелкнуть значок **Остановить** в нижней части страницы.
    ![ Страница добавления нового контакта][8]

## Просмотр базы данных

Важно знать, просматривать и изменять базу данных напрямую. Умение работать с базой данных напрямую позволяет подтверждать данные в базе данных, а также иметь представление о том, как хранятся данные в каждой таблице.

### Проверка базы данных SQL Azure

1.  В Visual Studio откройте **Обозреватель сервера** и перейдите к **ContactDB**.
2.  Щелкните **ContactDB** правой кнопкой мыши и выберите **Открыть в обозревателе объектов SQL Server**.
    ![ Элемент меню Открыть в обозревателе объектов SQL Server][]
3.  В диалоговом окне **Добавление правила брандмауэра** выберите **Добавить правило брандмауэра**.

    > [WACOM.NOTE]
    >  Если не удается развернуть **Базы данных SQL** и не удается увидеть **ContactDB** в Visual Studio, необходимо выполнить приведенные ниже инструкции, чтобы открыть порт или диапазон портов. Чтобы это сделать, выполните инструкции в разделе **Настройка правил брандмауэра Azure** в конце [Учебника МVC][Развертывание безопасного приложения ASP.NET MVC 5 с членством, OAuth и базой данных SQL на веб-сайте Azure]. В качестве альтернативы можно также просмотреть данные локальной базы данных путем построения, запуска и добавления данных к приложению локально (**CTRL+F5** из Visual Studio).

4.  Если отображается диалоговое окно **Подключение к серверу** введите **пароль**, созданный в начале этого учебника, и нажмите кнопку **Подключиться**.

    > [WACOM.NOTE]
    > Если вы не помните пароля, его можно найти в локальном файле проекта. В **Обозревателе решений** разверните папку *Properties* и затем раз папку *PublishProfiles*. Откройте файл *contactmanager.pubxml* (у вашего файла может быть другое имя). Найдите пароль для публикации в файле.

5.  Разверните базу данных **contactDB**, а затем разверните **Таблицы**.
6.  Щелкните правой кнопкой мыши таблицу **dbo.AspNetUsers** и выберите **Просмотр данных**.

    ![ Элемент меню Просмотр данных][]

     Теперь вы можете просмотреть данные, связанные с пользователем <canEditUser@contoso.com>.

    ![Окно ContactManager][]

### Добавление пользователя к роли Admin путем изменения базы данных

Ранее в этом учебнике вы использовали код для добавления пользователей к роли canEdit. Альтернативой является непосредственное изменение данных в таблицах членства. В следующих шагах показано, как использовать этот альтернативный метод для добавления пользователя к роли.

1.  В **обозревателе объектов SQL Server** щелкните правой кнопкой мыши элемент **dbo.AspNetUserRoles** и выберите **Просмотр данных**.

    ![ Данные AspNetUserRoles][]
2.  Скопируйте идентификатор *RoleId* и вставьте его в пустую (новую) строку.

    ![ Данные AspNetUserRoles][9]
3.  В таблице **dbo.AspNetUsers** найдите пользователя, которого вы хотите добавить к роли, и скопируйте его *идентификатор*.
4.  Вставьте скопированный *идентификатор* в поле **UserId** нового ряда таблицы **AspNetUserRoles**.

> [WACOM.NOTE]
> Мы работаем над средством, которое позволит значительно упростить управление пользователями и ролями.

## Дальнейшие действия

Дополнительную информацию о веб-формах ASP.NET см. в разделе [Знакомьтесь с веб-формами ASP.NET][] на веб-сайте ASP.NET и в [Microsoft Azure: учебники и руководства][].

Этот учебник построен на основе учебника MVC [Развертывание безопасного приложения ASP.NET MVC 5 с членством, OAuth и базой данных SQL на веб-сайте Azure][], написанным Риком Андерсоном (Rick Anderson) (Twitter [@RickAndMSFT][]) при помощи Тома Дейкстры (Tom Dykstra) и Бэрри Дорранса (Barry Dorrans) (Twitter [@blowdart][]).

Пожалуйста, оставьте отзыв о том, что вам понравилось или что вы хотели бы улучшить, не только о самом учебнике, но и о продуктах, которые он демонстрирует. Ваши отзывы помогут нам определить, какие улучшения наиболее приоритетные. Вы также можете запросить и проголосовать за новые темы на странице [Покажите, как это сделать в коде][].

  [Развертывание безопасного приложения ASP.NET MVC 5 с членством, OAuth и базой данных SQL на веб-сайте Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [Страница Контакты — изменить]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms00.png
  [активировать преимущества для подписчиков MSDN]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [подписаться на бесплатную пробную версию]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [Настройка среды разработки]: #set-up-the-development-environment
  [Настройка среды Azure]: #Set-up-the-Azure-environment
  [Создание приложения веб-форм ASP.NET]: #Create-an-ASP.NET-Web-Forms-Application
  [Добавление базы данных в приложение]: #Add-a-Database-to-the-Application
  [Включение SSL для проекта]: #Enable-SSL-for-the-Project
  [Добавление поставщика OAuth 2.0]: #Add-an-OAuth-2.0-Provider
  [Использование API членства для ограничения доступа]: #Use-the-Membership-API-to-Restrict-Access
  [Развертывание приложения с базой данных в Azure]: #Deploy-the-Application-with-the-Database-to-Azure
  [Просмотр базы данных]: #Review-the-Database
  [Дальнейшие действия]: #Next-Steps
  [Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=306566
  [Azure SDK для Visual Studio 2013]: http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409
  [Установщик веб-платформы]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-01.png
  [Visual Studio 2013 с обновлением 2]: http://www.microsoft.com/download/details.aspx?id=42666
  [портале управления Azure]: https://manage.windowsazure.com/
  [ Установщик веб-платформы]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-02.png
  [Настраиваемое создание ]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-03.png
  [Контакты — Создать новый веб-сайт]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-04.png
  [Параметры базы данных]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-05.png
  [Меню Файл — Новый проект]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms01.png
  [Диалоговое окно Новый проект]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms02.png
  [ Диалоговое окно Новый проект ASP.NET]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms03.png
  [1]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms04.png
  [Выберите Опубликовать]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms05.png
  [ Диалоговое окно Публикация веб-сайтов]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms06.png
  [Диалоговое окно Выбрать существующий веб-сайт]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms07.png
  [Диалоговое окно Опубликовать веб-сайт]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms08.png
  [ Действие веб-публикации]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms09.png
  [Приложение в браузере]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms10.png
  [2]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms11.png
  [ Выбрать класс]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms12.png
  [ Диалоговое окно Добавить новый элемент]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13.png
  [ Диалоговое окно Расширения и обновления]: ./media/web-sites-dotnet-web-forms-secure/ExtensionsAndUpdatesDB.png
  [ Диалоговое окно Добавить формирование шаблонов]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13a.png
  [ Диалоговое окно Добавить страницы веб-форм]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13b.png
  [3]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13c.png
  [Заполнение и отладка баз данных Entity Framework (EF)]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
  [ Консоль диспетчера пакетов]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13d.png
  [ Страница Список контактов]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms17.png
  [Свойства проекта]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms18.png
  [Веб-свойства проекта]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms19.png
  [Сведения о сертификате IIS Express SSL]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms20.png
  [ Диалоговое окно Предупреждение системы безопасности]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21.png
  [Консоли разработчиков Google]: https://console.developers.google.com/
  [ Консоль разработчиков Google]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21a.png
  [ Google — Новый проект]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21b.png
  [Google — Создать идентификатор клиента]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21c.png
  [Вход]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21d.png
  [ Google — Вход]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21e.png
  [ Учетная запись службы проекта по умолчанию]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21f.png
  [Регистрация с учетной записью Google]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21g.png
  [странице ресурсов ASP.NET Identity]: http://www.asp.net/identity
  [ Параметр меню Публикация]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms22.png
  [ Диалоговое окно Публикация веб-сайта]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms22a.png
  [4]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms23.png
  [ Диалоговое окно Выбор существующего веб-сайта]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms25.png
  [5]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26.png
  [Список контактов в браузере]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms27.png
  [6]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms29.png
  [Страница Входа]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms28.png
  [ Страница добавления нового контакта]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms30.png
  [7]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms31.png
  [ Элемент меню Остановить веб-сайт]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26a.png
  [8]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26b.png
  [ Элемент меню Открыть в обозревателе объектов SQL Server]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms32.png
  [ Элемент меню Просмотр данных]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms34.png
  [Окно ContactManager]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms35.png
  [ Данные AspNetUserRoles]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms36.png
  [9]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms37.png
  [Знакомьтесь с веб-формами ASP.NET]: http://www.asp.net/web-forms
  [Microsoft Azure: учебники и руководства]: http://azure.microsoft.com/ru-ru/documentation/services/web-sites/#net
  [@RickAndMSFT]: https://twitter.com/RickAndMSFT
  [@blowdart]: https://twitter.com/blowdart
  [Покажите, как это сделать в коде]: http://aspnet.uservoice.com/forums/228522-show-me-how-with-code
