<properties linkid="develop-dotnet-rest-service-using-web-api" urlDisplayName="REST service using Web API" pageTitle=".NET REST service using Web API - Azure tutorial" metaKeywords="Azure tutorial web site, ASP.NET API web site, Azure VS" description="A tutorial that teaches you how to deploy an app that uses the ASP.NET Web API to an Azure website by using Visual Studio." metaCanonical="" services="web-sites" documentationCenter=".NET" title="REST service using ASP.NET Web API and SQL Database" authors="riande" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande"></tags>

# Служба REST с использованием веб-API ASP.NET и базы данных SQL

***Авторы: [Рик Андерсон (Rick Anderson)][Рик Андерсон (Rick Anderson)] и Том Дайкстра (Tom Dykstra). Обновление: март 2014 г.***

В этом учебнике показано, как развернуть веб-приложение ASP.NET на веб-сайте Azure с помощью мастера публикации веб-сайта в Visual Studio 2013 или Visual Studio 2013 for Web Express. Если вы работаете с Visual Studio 2012, ознакомьтесь с [предыдущей версией этого учебника][предыдущей версией этого учебника].

Можно бесплатно создать учетную запись Azure, а если у вас еще нет Visual Studio 2013, пакет SDK автоматически установит Visual Studio 2013 for Web Express. Благодаря этому вы можете приступить к разработке приложений для Azure совершенно бесплатно.

В данном учебнике предполагается, что у читателя нет опыта использования платформы Azure. По завершении изучения этого учебника вы получите простое веб-приложение, работающее в облаке.

Вы узнаете:

-   Как подготовить компьютер к разработке для Azure путем установки пакета Azure SDK.
-   Как создать проект Visual Studio ASP.NET MVC 5 и опубликовать его на веб-сайте Azure.
-   Как использовать веб-API ASP.NET для включения вызовов API Restful.
-   Как использовать базу данных SQL для хранения данных в Azure.
-   Как публиковать обновления приложений в Azure.

Вы создадите простое веб-приложение для списка контактов, основанное на платформе ASP.NET MVC 5 и использующее ADO.NET Entity Framework для доступа к базе данных. На следующем рисунке показано завершенное приложение:

![снимок экрана веб-сайта][снимок экрана веб-сайта]
В этом учебнике рассматриваются следующие темы:

-   [Настройка среды разработки][Настройка среды разработки]
-   [Настройка среды Azure][Настройка среды Azure]
-   [Создание приложения ASP.NET MVC 5][Создание приложения ASP.NET MVC 5]
-   [Развертывание приложения в Azure][Развертывание приложения в Azure]
-   [Добавление базы данных в приложение][Добавление базы данных в приложение]
-   [Добавление контроллера и представления для данных][Добавление контроллера и представления для данных]
-   [Добавление веб-интерфейса API Restful][Добавление веб-интерфейса API Restful]
-   [Добавление защиты XSRF][Добавление защиты XSRF]
-   [Публикация обновления приложения в Azure и базе данных SQL][Публикация обновления приложения в Azure и базе данных SQL]

<a name="bkmk_setupdevenv"></a>
<!-- the next line produces the "Set up the development environment" section as see at http://www.windowsazure.com/ru-ru/documentation/articles/web-sites-dotnet-get-started/ -->
[WACOM.INCLUDE [create-account-and-websites-note][create-account-and-websites-note]]

## <a name="bkmk_setupwindowsazure"></a>Настройка среды Azure

Затем настройте среду Azure, создав веб-сайт Azure и базу данных SQL.

### Создание веб-сайта и базы данных SQL в Azure

Следующим шагом будет создание веб-сайта Azure и базы данных SQL, используемых вашим приложением.

Ваш веб-сайт Azure будет работать в общей среде размещения, а это означает, что он работает на виртуальных машинах, используемых совместно с другими клиентами Azure. Общая среда размещения — недорогой способ начать работу в облаке. Если позднее ваш веб-трафик увеличится, приложение можно масштабировать по необходимости, запуская его на выделенных виртуальных машинах. Если требуется более сложная архитектура, можно перейти в облачную службу Azure. Облачные службы работают на выделенных виртуальных машинах, которые можно настроить в соответствии с конкретными потребностями.

База данных SQL — это облачная служба реляционной базы данных, построенная на основе технологий SQL Server. Средства и приложения, работающие с SQL Server, работают и с базой данных SQL.

1.  На [портале управления Azure][портале управления Azure] щелкните **Веб-сайты** на левой вкладке, а затем щелкните **Создать**.

2.  Щелкните **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.

    ![Ссылка "Создать с базой данных" на портале управления][Ссылка "Создать с базой данных" на портале управления]

    Откроется мастер **Новый веб-сайт — настраиваемое создание**.

3.  На шаге **Создать веб-сайт** в мастере введите в поле **URL-адрес** строку, которая будет использоваться как уникальный URL-адрес для приложения. Полный URL-адрес будет состоять из введенной здесь строки и суффикса, который отображается под текстовым полем. На рисунке показан "contactmgr11", однако этот URL-адрес может быть занят, так что вам понадобится выбрать другой.

4.  В раскрывающемся списке **Регион** выберите ближайший к вам регион.

5.  В раскрывающемся списке **База данных** выберите **Создать бесплатную базу данных SQL 20 МБ**.

    ![Шаг создания нового веб-сайта в мастере «Новый веб-сайт — создать с базой данных»][Шаг создания нового веб-сайта в мастере «Новый веб-сайт — создать с базой данных»]

6.  Щелкните стрелку, указывающую вправо, в нижней части окна.

    Мастер перейдет к шагу **Параметры базы данных**.

7.  В поле **Имя** введите *ContactDB*.

8.  В поле **Сервер** выберите **Создать сервер базы данных SQL**. Если же вы ранее создали базу данных SQL Server, можно выбрать в раскрывающемся списке этот SQL Server.

9.  Щелкните стрелку, указывающую вправо, в нижней части окна.

10. Введите **ИМЯ ДЛЯ ВХОДА** и **ПАРОЛЬ** администратора. Если выбрано действие **Создать сервер базы данных SQL**, не используйте здесь существующие имя и пароль, введите новые, которые в дальнейшем будут использоваться при обращении к базе данных. Если выбран созданный ранее SQL Server, появится запрос на ввод пароля для ранее созданной учетной записи SQL Server. В этом учебнике поле **Дополнительно** не отмечается. Поле **Дополнительно** позволяет задавать размер БД (по умолчанию 1 ГБ, однако его можно увеличить до 150 ГБ), а также сортировку.

11. Щелкните флажок снизу от поля, чтобы указать завершение работы.

    ![Шаг настройки параметров базы данных в мастере «Новый веб-сайт — создать с базой данных»][Шаг настройки параметров базы данных в мастере «Новый веб-сайт — создать с базой данных»]

    На следующем изображении показано использование существующего SQL Server и входа в систему.

    ![Шаг настройки параметров базы данных в мастере «Новый веб-сайт — создать с базой данных»][1]

    Портал управления возвращается на страницу веб-сайтов, и в столбце **Состояние** указывается, что веб-сайт создается. Через какое-то время (обычно меньше минуты) в столбце **Состояние** указывается, что сайт успешно создан. В строке навигации слева число сайтов вашей учетной записи отображается рядом со значком **Веб-сайты**, а число баз данных — рядом со значком **Базы данных SQL**.

<!-- [Websites page of Management Portal, website created][setup009] -->

## <a name="bkmk_createmvc4app"></a>Создание приложения ASP.NET MVC 5

Веб-сайт Azure создан, но его контент еще отсутствует. Следующим шагом будет создание проекта веб-приложения Visual Studio, которое будет опубликовано в Azure.

### Создание проекта

1.  Откройте Visual Studio 2013.
2.  В меню **Файл** выберите **Новый проект**.
3.  В диалоговом окне **Новый проект** разверните узел **Visual C#**, выберите **Интернет**, а затем **Веб-приложение ASP.NET MVC 5**. Оставьте значение по умолчанию **.NET Framework 4.5**. Присвойте приложению имя **ContactManager** и нажмите **OK**.
    ![Диалоговое окно «Новый проект»][Диалоговое окно «Новый проект»]
4.  В диалоговом окне **Новый проект ASP.NET** выберите шаблон **MVC**, установите флажок **Веб-интерфейс API**, а затем щелкните **Изменить аутентификацию**.

    ![Диалоговое окно "Новый проект ASP.NET"][Диалоговое окно "Новый проект ASP.NET"]

5.  В диалоговом окне **Изменить аутентификацию** щелкните **Без аутентификации** и нажмите кнопку **ОК**.

    ![Без аутентификации][Без аутентификации]

    В создаваемом образце приложения не будет функций, требующих от пользователей входа в систему. Дополнительные сведения о способах реализации функций аутентификации и авторизации см. в разделе [Дальнейшие действия][Дальнейшие действия] в конце этого учебника.

6.  В диалоговом окне **Новый проект ASP.NET** нажмите кнопку **ОК**.

    ![Диалоговое окно "Новый проект ASP.NET"][2]

### Задание верхнего и нижнего колонтитула страницы

1.  В **Обозревателе решений** разверните папку *Views\\Shared* и откройте файл *_Layout.cshtml*.

    ![\_Layout.cshtml в обозревателе решений][\_Layout.cshtml в обозревателе решений]

2.  Замените содержимое файла *_Layout.cshtml* на код, приведенный ниже:

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

1.  Для запуска приложения нажмите клавиши CTRL+F5.
    Главная страница приложения откроется в браузере, используемом по умолчанию.
    ![Главная страница списка дел][Главная страница списка дел]

Это все, что требуется для создания приложения, которое будет развернуто в Azure. Далее вы добавите функции работы с базой данных.

## <a name="bkmk_deploytowindowsazure1"></a> Развертывание приложения в Azure

1.  В Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать** в контекстном меню.

    !["Опубликовать" в контекстном меню проекта]["Опубликовать" в контекстном меню проекта]

    Откроется мастер **Публикация веб-сайта**.

2.  На вкладке **Профиль** мастера **Публикация веб-сайта** щелкните **Импорт**.

    ![Параметры импорта публикации][Параметры импорта публикации]

    Появится диалоговое окно **Импорт профиля публикации**.

 3.  Выберите «Импорт с веб-сайта Azure». Если вы еще не выполнили вход в систему, сделайте это. Щелкните **Войти**. Укажите связанного с подпиской пользователя и выполните процедуру входа.

    ![войти][войти]

    Выберите веб-сайт в раскрывающемся списке и нажмите кнопку **ОК**.

    ![Импорт профиля публикации][Импорт профиля публикации]

4.  На вкладке **Подключение** щелкните **Проверить подключение**, чтобы убедиться в правильности определения параметров.

    ![Проверка подключения][Проверка подключения]

5.  После проверки подключения рядом с кнопкой **Проверить подключение** отображается зеленая галочка.

    ![Значок успешного подключения и кнопка "Далее" на вкладке "Подключение"][Значок успешного подключения и кнопка "Далее" на вкладке "Подключение"]

6.  Нажмите кнопку **Далее**.

    ![Вкладка "Параметры"][Вкладка "Параметры"]

    Можно принять заданные по умолчанию параметры, представленные на этой вкладке. Вы развертываете конфигурацию сборки для выпуска, и вам не требуется удалять файлы на целевом сервере, выполнять предварительную компиляцию приложения или исключать файлы из папки App\_Data. Чтобы выполнить отладку активного сайта Azure, вам необходимо развернуть конфигурацию отладки (не выпуск). См. раздел [Дальнейшие действия][Дальнейшие действия] в конце этого учебника.

7.  На вкладке **Предварительный просмотр** нажмите кнопку **Начало предварительного просмотра**.

    На вкладке отображается список файлов, которые будут копироваться на сервер. Предварительный просмотр не является обязательным для публикации приложения, но это полезная функция, о которой нужно знать. В этом случае вам не нужно ничего делать со списком показанных файлов. При следующей публикации список предварительного просмотра будет содержать только измененные файлы.

    ![Кнопка "Начало предварительного просмотра" на вкладке "Предварительный просмотр"][Кнопка "Начало предварительного просмотра" на вкладке "Предварительный просмотр"]

8.  Щелкните **Опубликовать**.

    Visual Studio начинает процесс копирования файлов на сервер Azure. В окне **Вывод** указывается, какие действия по развертыванию были выполнены, а также сообщается об успешном завершении развертывания.

9.  Браузер по умолчанию автоматически открывает URL-адрес развернутого сайта.

    Созданное вами приложение теперь выполняется в облаке.

    ![Главная страница списка дел на ресурсе Azure][Главная страница списка дел на ресурсе Azure]

## <a name="bkmk_addadatabase"></a>Добавление базы данных в приложение

Теперь вы обновите приложение MVC, чтобы добавить возможности для отображения и обновления контактов и сохранения данных в базе данных. Приложение будет использовать компонент Entity Framework для создания базы данных, а также чтения и обновления данных в базе данных.

### Добавление классов модели данных для контактов

Начните с создания простой модели данных в коде.

1.  В **обозревателе решений** щелкните правой кнопкой мыши папку "Модели" и выберите **Добавить**, а затем щелкните **Класс**.

    ![Контекстное меню добавления класса в папке "Модели"][Контекстное меню добавления класса в папке "Модели"]

2.  В диалоговом окне **Добавление нового элемента** присвойте новому файлу класса имя *Contact.cs*, затем щелкните **Добавить**.

    ![Диалоговое окно "Добавление нового элемента"][Диалоговое окно "Добавление нового элемента"]

3.  Замените содержимое файла Contacts.cs на код, приведенный ниже.

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

Класс **Contacts** определяет данные, которые будут храниться для каждого контакта, а также первичный ключ ContactID, необходимый для базы данных. Дополнительные сведения о моделях данных см. в разделе [Дальнейшие действия][Дальнейшие действия] в конце этого учебника.

### Создание веб-страниц, позволяющих пользователям приложений работать с контактами

Компонент формирования шаблонов в ASP.NET MVC может автоматически создавать код, выполняющий операции создания, чтения, обновления и удаления.

## <a name="bkmk_addcontroller"></a>Добавление контроллера и представления для данных

1.  В **Обозревателе решений** разверните папку "Контроллеры".

2.  Создайте проект **(Ctrl + Shift + B)** Проект необходимо создать перед использованием механизма формирования шаблонов.

3.  Щелкните правой кнопкой мыши папку «Контроллеры» и выберите **Добавить**, а затем щелкните **Контроллер**.

    ![Контекстное меню добавления контроллера в папке "Контроллеры"][Контекстное меню добавления контроллера в папке "Контроллеры"]

4.  В диалоговом окне **Добавление Scaffold** выберите **Контроллер MVC с представлениями, использующий Entity Framework** и щелкните **Добавить**.

    ![Добавление контролера][Добавление контролера]

1.  Укажите имя контроллера **HomeController**. Выберите класс модели **Contact**. Нажмите кнопку **Новый контекст данных** и оставьте значение "ContactManager.Models.ContactManagerContext", предлагаемое по умолчанию в разделе **Новый тип контекста данных**. Щелкните **Добавить**.

    ![Диалоговое окно "Добавление контроллера"][Диалоговое окно "Добавление контроллера"]

    В диалоговом окне появится сообщение: «Файл с именем HomeController уже существует. Вы хотите заменить его?». Щелкните **Да**. Контроллер Home, созданный для нового проекта, будет перезаписан В списке контактов будет использоваться новый контроллер Home.

    Visual Studio создает методы и представления контроллера для операций создания, чтения, обновления и удаления в базе данных для объектов **Контакт**.

## Включение Migrations, создание базы данных, добавление примера данных и инициализатора данных

Следующая задача заключается в активации компонента [Code First Migrations][Code First Migrations] для создания базы данных на основе созданной модели данных.

1.  В меню **Сервис** выберите **Диспетчер пакетов библиотеки**, а затем **Консоль диспетчера пакетов**.

    !["Консоль диспетчера пакетов" в меню "Сервис"]["Консоль диспетчера пакетов" в меню "Сервис"]

2.  В окне **Консоль диспетчера пакетов** введите следующую команду:

        enable-migrations 

    Команда **enable-migrations** создает папку *Migrations* и размещает в этой папке файл *Configuration.cs*, который можно использовать для настройки миграций.

3.  В окне **Консоль диспетчера пакетов** введите следующую команду:

        add-migration Initial

    Команда **add-migration Initial** создает класс с именем **\<date\_stamp\>Initial**, который в свою очередь создает базу данных. Первый параметр (*Initial*) является произвольным и используется для создания имени файла. Новые файлы классов можно просмотреть в **обозревателе решений**.

    В классе **Initial** метод **Up** создает таблицу Contacts, а метод **Down** (используется при возвращении к предыдущему состоянию) удаляет эту таблицу.

4.  Откройте файл *Migrations\\Configuration.cs*.

5.  Добавьте следующее пространства имен.

         using ContactManager.Models;

6.  Замените метод *Seed* следующим кодом:

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

    Приведенный выше код инициализирует базу данных с контактной информацией. Дополнительные сведения об инициализации базы данных см. в разделе [Инициализация баз данных Entity Framework (EF)][Инициализация баз данных Entity Framework (EF)].

7.  В окне **Консоль диспетчера пакетов** введите команду:

        update-database

    ![Команды консоли диспетчера пакетов][Команды консоли диспетчера пакетов]

    Команда **update-database** выполняет первую миграцию, при которой создается база данных. По умолчанию база данных создается как база данных LocalDB в SQL Server Express.

8.  Для запуска приложения нажмите сочетание клавиш CTRL+F5.

Приложение показывает начальные данные и предоставляет ссылки для изменения, удаления и просмотра подробных сведений.

![Представление MVC для данных][Представление MVC для данных]

## <a name="bkmk_addview"></a>Редактирование представления

1.  Откройте файл *Views\\Home\\Index.cshtml*. На следующем шаге мы заменим созданную разметку кодом, в котором используются [jQuery][jQuery] и [Knockout.js][Knockout.js]. Новый код извлекает список контактов с помощью веб-API и JSON, а затем привязывает данные контактов к пользовательскому интерфейсу с использованием knockout.js. Дополнительную информацию см. в разделе [Дальнейшие действия][Дальнейшие действия] в конце этого учебника.

2.  Замените содержимое файла на код, приведенный ниже.

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

3.  Щелкните правой кнопкой мыши папку Content и выберите **Добавить**, а затем щелкните **Новый элемент...**.

    ![Добавление таблицы стилей в контекстном меню папки Content][Добавление таблицы стилей в контекстном меню папки Content]

4.  В диалоговом окне **Добавить новый элемент** введите **Стиль** в строке поиска, расположенной в правом верхнем углу, затем выберите **Таблица стилей**.
    ![Диалоговое окно «Добавление нового элемента»][Диалоговое окно «Добавление нового элемента»]

5.  Присвойте файлу имя *Contacts.css* и выберите **Добавить**. Замените содержимое файла на код, приведенный ниже.

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

6.  Откройте файл *App\_Start\\BundleConfig.cs*.

7.  Добавьте следующий код для регистрации подключаемого модуля [Knockout][Knockout].

        bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
                    "~/Scripts/knockout-{version}.js"));

    В этом примере knockout используется для упрощения динамического кода JavaScript, который обрабатывает шаблоны экрана.

8.  Измените запись contents/css для регистрации таблицы стилей *contacts.css*. Измените следующую строку:

                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));

    на:

        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));

9.  В консоли диспетчера пакетов выполните следующую команду для установки подключаемого модуля Knockout.

    Install-Package knockoutjs

## <a name="bkmk_addwebapi"></a>Добавление контроллера для веб-API Restful

1.  В **Обозревателе решений** щелкните правой кнопкой мыши "Контроллеры" и выберите **Добавить**, а затем щелкните **Контроллер...**.

2.  В диалоговом окне **Добавление Scaffold** введите **Контроллер веб-интерфейса API 2 с действиями, использующий Entity Framework** и щелкните **Добавить**.

    ![Добавление контролера API][Добавление контролера API]

3.  В диалоговом окне **Добавление контроллера** введите «ContactsController» в качестве имени контроллера. В раскрывающемся списке **Класс модели** выберите "Contact (ContactManager.Models)". Оставьте значение по умолчанию для элемента **Класс контекста данных**.

4.  Щелкните **Добавить**.

### Локальный запуск приложения

1.  Для запуска приложения нажмите сочетание клавиш CTRL+F5.

    ![Страница индексации][снимок экрана веб-сайта]

2.  Введите контакт и нажмите кнопку **Добавить** Приложение возвращается на главную страницу и отображает введенный контакт.

    ![Страница индексации с элементами списка дел][Страница индексации с элементами списка дел]

3.  В браузере добавьте к URL-адресу **/api/contacts**.

    Итоговый URL-адрес будет выглядеть аналогично <http://localhost:1234/api/contacts>. Добавленный веб-интерфейс API RESTful возвращает сохраненные контакты. FireFox и Chrome отображают данные в формате XML.

    ![Страница индексации с элементами списка дел][3]

    IE запрашивает открытие и сохранение контактов.

    ![Диалоговое окно сохранения веб-интерфейса API][Диалоговое окно сохранения веб-интерфейса API]

    Возвращенные контакты можно открыть в блокноте или браузере.

    Эти выходные данные могут использоваться другим приложением, например мобильной веб-страницей или приложением.

    ![Диалоговое окно сохранения веб-интерфейса API][4]

    **Предупреждение системы безопасности**: На этом этапе ваше приложение не защищено и уязвимо для атак с подделкой межсайтовых запросов (CSRF). Эта уязвимость будет удалена позднее в рамках этого учебника. Дополнительные сведения см. в разделе [Предотвращение атак с подделкой межсайтовых запросов (CSRF)][Предотвращение атак с подделкой межсайтовых запросов (CSRF)].

## <a name="xsrf"></a><span class="short-header">XSRF</span>Добавление защиты от подделки межсайтовых запросов (XSRF)

Подделка межсайтовых запросов (также называемая XSRF или CSRF) является атакой, направленной на размещенные веб-приложения, при которой вредоносный веб-сайт может влиять на взаимодействие между клиентским браузером и веб-сайтом, которому доверяет этот браузер. Эти атаки становятся возможными, потому что браузеры отправляют маркеры аутентификации автоматически вместе с каждым запросом веб-сайта. Типичный пример — файл cookie для проверки подлинности, например билет проверки форм ASP.NET. Однако веб-сайты, которые используют какой-либо постоянный механизм аутентификации (например, аутентификацию Windows или обычную аутентификацию), могут стать уязвимыми для этого вида атак.

Атака XSRF отличается от фишинга. При проведении фишинговой атаки требуется взаимодействие пользователя. В фишинговой атаке вредоносный веб-сайт практически идентичен целевому веб-сайту, поэтому жертва предоставляет атакующей стороне свои конфиденциальные данные. При атаке XSRF очень часто никаких действий со стороны жертвы не требуется. Злоумышленник больше полагается на то, что браузер автоматически отправляет все необходимые файлы cookie на целевой веб-сайт.

Дополнительную информацию см. в разделе [Открытый проект безопасности веб-приложений][Открытый проект безопасности веб-приложений] (OWASP) [XSRF][XSRF].

1.  В **Обозревателе решений** щелкните правой кнопкой пункт **ContactManager**, затем щелкните **Добавить** и **Класс**.

2.  Назовите файл *ValidateHttpAntiForgeryTokenAttribute.cs* и добавьте следующий код:

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

3.  Добавьте в контроллер контактов следующую инструкцию *using*, которая обеспечивает доступ к атрибуту **[ValidateHttpAntiForgeryToken]**.

    using ContactManager.Filters;

4.  Добавьте атрибут **[ValidateHttpAntiForgeryToken]** в методы Post объекта **ContactsController**, чтобы защитить его от угроз XSRF. Этот атрибут необходимо добавить в методы действий "PutContact", "PostContact" и **DeleteContact**.

    [ValidateHttpAntiForgeryToken]
     public IHttpActionResult PutContact(int id, Contact contact)
     {

5.  Обновите раздел *Scripts* файла *Views\\Home\\Index.cshtml*, включив в него код для получения токенов XSRF.

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

## <a name="bkmk_deploydatabaseupdate"></a>Публикация обновления приложения в Azure и базе данных SQL

Чтобы опубликовать приложение, повторите процедуры, которые описаны выше.

1.  Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Опубликовать**.

    ![Опубликовать][Опубликовать]

2.  Перейдите на вкладку **Параметры**.

3.  В разделе **ContactsManagerContext(ContactsManagerContext)** щелкните значок **v**, чтобы изменить *Строку удаленного подключения* на строку подключения для базы данных контактов. Щелкните **ContactDB**.

    ![Параметры][Параметры]

4.  Установите флажок **Выполнить Code First Migrations (при запуске приложения)**.

5.  Щелкните **Далее**, затем **Предварительный просмотр**. В Visual Studio отображается список файлов, которые будут добавлены или обновлены.

6.  Нажмите кнопку **Опубликовать**.
    После завершения развертывания в браузере открывается главная страница приложения.

    ![Страница индексации без контактов][снимок экрана веб-сайта]

    Процесс публикации Visual Studio автоматически настроил строку подключения в развернутом файле *Web.config* для указания на базу данных SQL. Процесс также настроил Code First Migrations для автоматического обновления базы данных до последней версии при первом доступе приложения к базе данных после развертывания.

    В результате этой конфигурации Code First создает базу данных путем запуска кода в ранее созданном классе **Initial**. Это было сделано, когда приложение впервые попыталось получить доступ к базе данных после развертывания.

7.  Введите контакт, как и при локальном запуске приложения, чтобы проверить успешность развертывания базы данных.

Когда вы увидите, что этот введенный элемент сохранен и показан на странице диспетчера контактов, вы будете знать, что он сохранен в базе данных.

![Страница индексации с контактами][Страница индексации с элементами списка дел]

Теперь приложение работает в облаке и использует базу данных SQL для хранения данных. После завершения тестирования приложения в Azure его необходимо удалить. Приложение является общим и не имеет механизма для ограничения доступа.

## <a name="nextsteps"></a> Дальнейшие действия

Для фактического приложения потребуется проверка подлинности и авторизация, и для этого будет использоваться база данных членства. Учебник [Развертывание безопасного приложения ASP.NET MVC с OAuth, членством и базой данных SQL][Развертывание безопасного приложения ASP.NET MVC с OAuth, членством и базой данных SQL] основан на данном учебнике и содержит сведения о развертывании веб-приложения с базой данных членства.

Другой способ хранения данных в приложении Azure — использование хранилища Azure, обеспечивающего хранение нереляционных данных в виде BLOB-объектов и таблиц. По следующим ссылкам приводятся дополнительные сведения о веб-интерфейсе API, ASP.NET MVC и Window Azure.

-   [Общие сведения о работе с Entity Framework с использованием MVC][Общие сведения о работе с Entity Framework с использованием MVC]
-   [Введение в ASP.NET MVC 5][Введение в ASP.NET MVC 5]
-   [Первый веб-интерфейса API ASP.NET][Первый веб-интерфейса API ASP.NET]
-   [Отладка веб-сайтов Azure][Отладка веб-сайтов Azure]

Этот учебник и пример приложения были написаны [Риком Андерсоном (Rick Anderson)][Риком Андерсоном (Rick Anderson)] (Twitter [@RickAndMSFT][Рик Андерсон (Rick Anderson)]) в соавторстве с Томом Дейкстрой (Tom Dykstra) и Бэрри Доррансом (Barry Dorrans)(Twitter [@blowdart][@blowdart]).

Пожалуйста, оставьте отзыв о том, что вам понравилось или что вы хотели бы улучшить, не только о самом учебнике, но и о продуктах, которые он демонстрирует. Ваши отзывы помогут нам определить, какие улучшения наиболее приоритетные. Мы особенно заинтересованы узнать, насколько всем хочется получить дополнительные сведения об автоматизации процесса настройки и развертывании базы данных членства.



  [Рик Андерсон (Rick Anderson)]: https://twitter.com/RickAndMSFT
  [предыдущей версией этого учебника]: /ru-ru/develop/net/tutorials/get-started-vs2012/
  [снимок экрана веб-сайта]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png
  [Настройка среды разработки]: #bkmk_setupdevenv
  [Настройка среды Azure]: #bkmk_setupwindowsazure
  [Создание приложения ASP.NET MVC 5]: #bkmk_createmvc4app
  [Развертывание приложения в Azure]: #bkmk_deploytowindowsazure1
  [Добавление базы данных в приложение]: #bkmk_addadatabase
  [Добавление контроллера и представления для данных]: #bkmk_addcontroller
  [Добавление веб-интерфейса API Restful]: #bkmk_addwebapi
  [Добавление защиты XSRF]: #xsrf
  [Публикация обновления приложения в Azure и базе данных SQL]: #bkmk_deploydatabaseupdate
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [портале управления Azure]: https://manage.windowsazure.com
  [Ссылка "Создать с базой данных" на портале управления]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr6.PNG
  [Шаг создания нового веб-сайта в мастере «Новый веб-сайт — создать с базой данных»]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrCWS.png
  [Шаг настройки параметров базы данных в мастере «Новый веб-сайт — создать с базой данных»]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-setup-azure-site-004.png
  [1]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPrevDB.png
  [Диалоговое окно «Новый проект»]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.PNG
  [Диалоговое окно "Новый проект ASP.NET"]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG
  [Без аутентификации]: ./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png
  [Дальнейшие действия]: #nextsteps
  [2]: ./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png
  [\_Layout.cshtml в обозревателе решений]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-004.png
  [Главная страница списка дел]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.PNG
  ["Опубликовать" в контекстном меню проекта]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/PublishVSSolution.png
  [Параметры импорта публикации]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishSettings.png
  [войти]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr7.png
  [Импорт профиля публикации]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr8.png
  [Проверка подключения]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png
  [Значок успешного подключения и кнопка "Далее" на вкладке "Подключение"]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-005.png
  [Вкладка "Параметры"]: ./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png
  [Кнопка "Начало предварительного просмотра" на вкладке "Предварительный просмотр"]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-007.png
  [Главная страница списка дел на ресурсе Azure]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz2.png
  [Контекстное меню добавления класса в папке "Модели"]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-001.png
  [Диалоговое окно "Добавление нового элемента"]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-002.png
  [Контекстное меню добавления контроллера в папке "Контроллеры"]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-context-menu.png
  [Добавление контролера]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.PNG
  [Диалоговое окно "Добавление контроллера"]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr9.PNG
  [Code First Migrations]: http://curah.microsoft.com/55220
  ["Консоль диспетчера пакетов" в меню "Сервис"]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-menu.png
  [Инициализация баз данных Entity Framework (EF)]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
  [Команды консоли диспетчера пакетов]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-console.png
  [Представление MVC для данных]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png
  [jQuery]: http://jquery.com/
  [Knockout.js]: http://knockoutjs.com/
  [Добавление таблицы стилей в контекстном меню папки Content]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png
  [Диалоговое окно «Добавление нового элемента»]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png
  [Knockout]: http://knockoutjs.com/index.html "KO"
  [Добавление контролера API]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.PNG
  [Страница индексации с элементами списка дел]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-added-contact.png
  [3]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxFFchrome.png
  [Диалоговое окно сохранения веб-интерфейса API]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-save-returned-contacts.png
  [4]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-contacts-in-notepad.png
  [Предотвращение атак с подделкой межсайтовых запросов (CSRF)]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks
  [Открытый проект безопасности веб-приложений]: https://www.owasp.org/index.php/Main_Page
  [XSRF]: https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)
  [Опубликовать]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png
  [Параметры]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png
  [Развертывание безопасного приложения ASP.NET MVC с OAuth, членством и базой данных SQL]: http://www.windowsazure.com/ru-ru/develop/net/tutorials/web-site-with-sql-database/
  [Общие сведения о работе с Entity Framework с использованием MVC]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
  [Введение в ASP.NET MVC 5]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
  [Первый веб-интерфейса API ASP.NET]: http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
  [Отладка веб-сайтов Azure]: http://www.windowsazure.com/ru-ru/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/
  [Риком Андерсоном (Rick Anderson)]: http://blogs.msdn.com/b/rickandy/
  [@blowdart]: https://twitter.com/blowdart
