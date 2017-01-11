---
title: "Создание приложения ASP.NET MVC с проверкой подлинности и базой данных SQL и развертывание службы приложений Azure"
description: "Узнайте, как разработать приложение ASP.NET MVC 5 с внутренней базой данных SQL, добавить проверку подлинности и авторизацию и развернуть его в службе Azure."
services: app-service\web
documentationcenter: .net
author: Rick-Anderson
writer: Rick-Anderson
manager: wpickett
editor: 
ms.assetid: c0de419d-db6f-4157-94ca-f75d0ba6c0e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2016
ms.author: riande
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: f0bffc2a77ef5f91c6c637e8ab247b559fb59d5c


---
# <a name="create-an-aspnet-mvc-app-with-auth-and-sql-db-and-deploy-to-azure-app-service"></a>Создание приложения ASP.NET MVC с проверкой подлинности и базой данных SQL и развертывание службы приложений Azure
В этом учебнике показано, как создать безопасное веб-приложение ASP.NET MVC 5, которое позволяет пользователям входить в систему с учетными данными из Google или Facebook. Это приложение — простой список контактов, использующий ADO.NET Entity Framework для доступа к базе данных. Приложение развертывается в [службе приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714). 

По завершении изучения этого учебника вы создадите безопасное приложение на основе данных, выполняемое в облаке и использующее облачную базу данных. На следующем рисунке показана страница входа для готового приложения:

![Страница входа][rxb]

Вы узнаете следующее:

* Как создать защищенный веб-проект ASP.NET MVC 5 в Visual Studio.
* Как выполнять аутентификацию и авторизацию пользователей, которые входят с помощью учетных данных из Google или Facebook (аутентификация поставщиков социальных сетей с помощью [OAuth 2.0](http://oauth.net/2 "http://oauth.net/2 ")).
* Как выполнить проверку подлинности и авторизацию пользователей, которые регистрируются в базе данных, управляемой приложением (локальная проверка подлинности с помощью [удостоверения ASP.NET](http://asp.net/identity/)).
* Как использовать ADO.NET Entity Framework 6 Code First для чтения и записи данных в базе данных SQL.
* Как использовать Entity Framework Code First Migrations для развертывания базы данных.
* Как хранить реляционные данные в облаке с помощью Базы данных SQL Azure.
* Как развернуть веб-проект, которые использует базу данных, в [веб-приложении](http://go.microsoft.com/fwlink/?LinkId=529714) в службе приложений Azure.

> [!NOTE]
> Это обширное руководство. Если вас интересует краткое введение в службу приложений Azure и веб-проекты Visual Studio, см. статью [Развертывание веб-приложения ASP.NET в службе приложений Azure с помощью Visual Studio](web-sites-dotnet-get-started.md). Сведения об устранении неполадок см. в разделе [Устранение неполадок](#troubleshooting).
> 
> Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите на сайт с [пробной версией службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы сможете быстро создать в службе приложений базовое веб-приложение для кратковременного использования. Никаких кредитных карт и обязательств.
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим учебником необходимо использовать учетную запись Microsoft Azure. Если у вас нет учетной записи, можно [активировать преимущества для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) или [подписаться на бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).

Чтобы настроить среду разработки, необходимо установить [Visual Studio 2013 с обновлением 5](http://go.microsoft.com/fwlink/?LinkId=390521) или более поздней версии, а также последнюю версию [пакета SDK Azure для .NET](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Эта статья написана для версии Visual Studio с обновлением 4 и пакета SDK 2.8.1. Содержащиеся в ней инструкции применимы к Visual Studio 2015 с последней версией пакета [SDK Azure для .NET](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409) , но некоторые экраны на рисунках будут отличаться от реальных.

## <a name="create-an-aspnet-mvc-5-application"></a>Создание приложения ASP.NET MVC 5
### <a name="create-the-project"></a>Создание проекта
1. В меню **Файл** выберите **Новый проект**.
   
    !["Новый проект" в меню "Файл"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/gs13newproj.png)
2. В диалоговом окне **Новый проект** разверните **C#** и выберите **Веб** в **Установленные шаблоны**, а затем выберите **Веб-приложение ASP.NET**. Присвойте приложению имя **ContactManager** и нажмите кнопку **ОК**.
   
    ![Диалоговое окно "Новый проект"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13newprojdb.png)
   
    **Примечание.** Необходимо ввести именно ContactManager. В блоках кода, которые вы будете копировать позже, предполагается, что проект имеет имя ContactManager. 
3. В диалоговом окне **Новый проект ASP.NET** выберите шаблон **MVC**. Убедитесь, что параметру **Проверка подлинности** задано значение **Индивидуальные учетные записи пользователей**. Кроме того, должен быть установлен флажок **Разместить в облаке** и выбран пункт **Служба приложений**.
   
    ![Диалоговое окно "Новый проект ASP.NET"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newproject.png)
4. Нажмите кнопку **ОК**.
5. Появится диалоговое окно **Настройка параметров веб-приложения Microsoft Azure** . Может потребоваться выполнить вход, если вы еще не сделали это, или повторный ввод учетных данных, если истек срок действия входа.
6. Необязательно: измените значение в поле **Имя веб-приложения** (см. рисунок ниже).
   
    URL-адрес веб-приложения будет иметь вид {имя}.azurewebsites.net, поэтому имя должно быть уникальным в домене azurewebsites.net. Мастер настройки предлагает уникальное имя, добавляя номер к имени проекта ContactManager. Этого достаточно для целей данного учебника.
7. В раскрывающемся списке **Группа ресурсов** выберите имеющуюся группу или пункт **Создать группу ресурсов** (см. рисунок ниже). 
   
    При желании можно выбрать существующую группу ресурсов. Однако если создать новую группу ресурсов и использовать ее только для этого учебника, вы сможете легко удалить все ресурсы Azure, созданные для учебника, после завершения работы с ними. Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 
8. В раскрывающемся списке **План служб приложений** выберите имеющийся план или пункт **Создать план служб приложений** (см. рисунок ниже).
   
    При желании можно выбрать существующий план службы приложений. Дополнительные сведения о планах службы приложений см. в статье [Подробный обзор планов службы приложений Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 
9. Коснитесь элемента **Исследовать дополнительные службы Azure** , чтобы добавить базу данных SQL.
   
    ![Добавление новых служб](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/n2.png)
10. Коснитесь значка **+** , чтобы добавить базу данных SQL.
    
     ![Новая база данных SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/nsql.png)
11. Коснитесь кнопки **Создать** в диалоговом окне **Настроить базу данных SQL**.
    
     ![Имя и пароль администратора SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/nc.png)
12. Введите имя для администратора и надежный пароль.
    
     ![Новая база данных SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/np.png)
    
     Имя сервера должно быть уникальным. Оно может содержать строчные буквы, цифры и дефисы. В конце имени дефис недопустим. Имя пользователя и пароль — это новые учетные данные, которые создаются для нового сервера. 
    
     Если у вас уже есть сервер базы данных, его можно выбрать и не создавать новый. Серверы баз данных являются ценным ресурсом, и обычно удобнее создать сразу несколько баз данных на одном сервере для тестирования и разработки, чем создавать по одному серверу на каждую базу данных. Однако в этом учебнике сервер нужен только временно. Если создать сервер в той же группе ресурсов, что и веб-сайт, после завершения работы с учебником вы сможете легко удалить веб-приложение и ресурсы базы данных, удалив группу ресурсов. 
    
     Если вы решили выбрать существующий сервер базы данных, убедитесь, что веб-приложение и база данных находятся в одном регионе.
    
     ![Использование новой базы данных](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newdb.png)
13. Нажмите кнопку **Создать**.
    
     Visual Studio создает веб-проект ContactManager, указанные вами группу ресурсов и план службы приложений, а также веб-приложение в службе приложений Azure с указанным именем.

### <a name="set-the-page-header-and-footer"></a>Задание верхнего и нижнего колонтитула страницы
1. В **обозревателе решений** откройте файл *Layout.cshtml* в папке *Views\Shared*.
   
    ![_Layout.cshtml в обозревателе решений][newapp004]
2. Замените ActionLink в файле *Layout.cshtml* на следующий код.

    @Html.ActionLink("CM Demo", "Index", "Contacts", new { area = "" }, new { @class = "navbar-brand" })


    Убедитесь, что изменили третий параметр с "Home" на "Contacts". Приведенная выше разметка создаст ссылку "Contacts" на каждой странице в метод Index контроллера контактов. Измените имя приложения в верхнем и нижнем колонтитуле с "My ASP.NET Application" и "Application name" на "Contact Manager" и "CM Demo" соответственно. 

### <a name="run-the-application-locally"></a>Локальный запуск приложения
1. Для запуска приложения нажмите сочетание клавиш CTRL+F5.
   
    Главная страница приложения откроется в браузере, используемом по умолчанию.
   
    ![Веб-приложение, выполняющееся локально](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr2.png)

Это все, что требуется для создания приложения, которое будет развернуто в Azure. 

## <a name="deploy-the-application-to-azure"></a>Развертывание приложения в Azure
1. В Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать** в контекстном меню.
   
    !["Опубликовать" в контекстном меню проекта](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
   
    Откроется мастер **Публикация веб-сайта** .
2. В диалоговом окне **Публикация веб-сайта** щелкните **Опубликовать**.
   
    ![Опубликовать](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr3.png)
   
    Созданное вами приложение теперь выполняется в облаке. При следующем развертывании приложения будут развернуты только измененные (или новые) файлы.
   
    ![Запуск в облаке](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss4.PNG)

## <a name="enable-ssl-for-the-project"></a>Включение SSL для проекта
1. В **обозревателе решений** щелкните проект **ContactManager**, а затем нажмите клавишу F4, чтобы открыть диалоговое окно **Свойства**.
2. Для параметра **SSL включен** измените значение на **True**. 
3. Скопируйте **URL-адрес SSL**.
   
    Если вы ранее не создали веб-приложения SSL, URL-адресом SSL будет https://localhost:44300/.
   
    ![Включение SSL][rxSSL]
4. В **обозревателе решений** щелкните правой кнопкой мыши проект **Contact Manager** и щелкните **Свойства**.
5. Щелкните вкладку **Интернет** .
6. Измените **URL-адрес проекта** на **URL-адрес SSL** и сохраните страницу (Ctrl+S).
   
    ![Включение SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr1.png)
7. Убедитесь, что обозреватель Visual Studio открывает браузер Internet Explorer, как показано на рисунке ниже.
   
    ![браузер по умолчанию](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss12.PNG)
   
    Средство выбора браузера позволяет указать используемый Visual Studio браузер. Вы можете выбрать несколько браузеров и обновлять их при внесении изменений. Дополнительные сведения см. в статье [Using Browser Link in Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link) (Использование ссылки на браузер в Visual Studio 2013).
   
     ![средство выбора браузера](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss13.png)
8. Для запуска приложения нажмите сочетание клавиш CTRL+F5. Щелкните **Да** , чтобы запустить процесс подтверждения доверия к самозаверяющему сертификату, созданному IIS Express.
   
     ![инструкции для подтверждения доверия к самозаверяющему сертификату, созданному IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss26.PNG)
9. Прочитайте текст в диалоговом окне **Предупреждение системы безопасности** и нажмите кнопку **Да**, если необходимо установить сертификат, представляющий **localhost**.
   
     ![Предупреждение о сертификате localhost в IIS Express ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss27.PNG)
10. В IE откроется *Главная* страница без предупреждений SSL.
    
      ![IE с SSL-сертификатом localhost и без предупреждений](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss28.PNG)
    
      При использовании SSL мы рекомендуем выбирать Internet Explorer, так как этот браузер принимает сертификат и показывает HTTPS-содержимое без предупреждения. Браузеры Microsoft Edge и Google Chrome тоже принимают сертификат. Firefox использует собственное хранилище сертификатов, поэтому отображается предупреждение.
    
      ![Предупреждение сертификата FireFox](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss30.PNG)

## <a name="add-a-database-to-the-application"></a>Добавление базы данных в приложение
Далее вам предстоит обновить приложение, чтобы добавить возможности отображения и обновления контактов и сохранения данных в базе данных. Приложение будет использовать компонент Entity Framework (EF) для создания базы данных, а также чтения и обновления данных в базе данных.

### <a name="add-data-model-classes-for-the-contacts"></a>Добавление классов модели данных для контактов
Начните с создания простой модели данных в коде.

1. В **обозревателе решений** щелкните правой кнопкой мыши папку "Модели" и выберите **Добавить**, а затем щелкните **Класс**.
   
    ![Контекстное меню добавления класса в папке "Модели"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr5.png)
2. В диалоговом окне **Добавление нового элемента** присвойте новому файлу класса имя *Contact.cs*, затем щелкните **Добавить**.
   
    ![Диалоговое окно "Добавление нового элемента"][adddb002]
3. Замените содержимое файла Contact.cs на код, приведенный ниже.
   
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
   Класс **Contact** определяет данные, которые будут храниться для каждого контакта, а также первичный ключ *ContactID*, необходимый для базы данных.

### <a name="create-web-pages-that-enable-app-users-to-work-with-the-contacts"></a>Создание веб-страниц, позволяющих пользователям приложений работать с контактами
Компонент формирования шаблонов в ASP.NET MVC может автоматически создавать код, выполняющий операции создания, чтения, обновления и удаления. 

1. Выполните сборку проекта**(Ctrl+Shift+B)**. (проект необходимо создать перед использованием механизма формирования шаблонов).
2. В **обозревателе решений** щелкните правой кнопкой мыши папку "Контроллеры" и выберите **Добавить**, а затем щелкните **Контроллер**.
   
    ![Контекстное меню добавления контроллера в папке "Контроллеры"][addcode001]
3. В диалоговом окне **Добавление шаблона** выберите **MVC 5 Controller with views, using EF** (Контроллер MVC 5 с представлениями, использующий EF) и щелкните **Добавить**.
   
    ![Диалоговое окно "Добавление Scaffold"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr6.png)
4. В раскрывающемся списке **Класс модели** выберите **Contact (ContactManager.Models)**. (См. изображение ниже.)
5. В поле **Класс контекста данных** выберите **ApplicationDbContext (ContactManager.Models)**. **ApplicationDbContext** будет использоваться и для базы данных членства, и для наших контактных данных.

    ![Диалоговое окно "Новый контекст данных"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss5.PNG)

1. Щелкните **Добавить**.
   
   Visual Studio создаст контроллер с методами и представлениями для CRUD-операций базы данных для объектов класса **Contact** .

## <a name="enable-migrations-create-the-database-add-sample-data-and-a-data-initializer"></a>Включение Migrations, создание базы данных, добавление примера данных и инициализатора данных
Следующая задача заключается в активации компонента [Code First Migrations](http://msdn.microsoft.com/library/hh770484.aspx) для создания таблиц базы данных на основе созданной модели данных.

1. В меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**.
   
    !["Консоль диспетчера пакетов" в меню "Сервис"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/SS6.png)
2. В окне **Консоль диспетчера пакетов** введите следующую команду:
   
        enable-migrations
   
    Команда **enable-migrations** создает папку *Миграции* и помещает в нее файл *Configuration.cs*, который можно изменять и использовать для заполнения базы данных и настройки миграций. 
3. В окне **Консоль диспетчера пакетов** введите следующую команду:
   
        add-migration Initial

    Команда **add-migration Initial** создает файл с именем **&lt;date_stamp&gt;Initial** в папке *Миграции*. Код в этом файле создает таблицы базы данных. Первый параметр ( **Initial** ) используется для создания имени файла. Новые файлы классов можно просмотреть в **обозревателе решений**.

    В классе **Initial** метод **Up** создает таблицу контактов, а метод **Down** (используется при возвращении к предыдущему состоянию) удаляет эту таблицу.

1. Откройте файл *Migrations\Configuration.cs*. 
2. Добавьте следующий оператор `using` . 
   
         using ContactManager.Models;
3. Замените метод *Seed* следующим кодом:
   
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
   
    Этот код инициализирует базу данных с контактной информацией. Дополнительные сведения об инициализации базы данных см. в статье [Seeding and Debugging Entity Framework (EF) DBs](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) (Инициализация и отладка баз данных Entity Framework (EF)). Выполните сборку проекта и убедитесь в отсутствии ошибок компиляции.
4. В окне **Консоль диспетчера пакетов** введите команду:
   
        update-database
   
    ![Команды консоли диспетчера пакетов][addcode009]
   
    Команда **update-database** выполняет первую миграцию, при которой создается база данных. По умолчанию база данных создается как база данных LocalDB в SQL Server Express. 
5. Нажмите клавиши Ctrl+F5 для запуска приложения, а затем щелкните ссылку **CM Demo** или перейдите по адресу https://localhost:(port#)/Cm. 
   
    Приложение показывает начальные данные и предоставляет ссылки для изменения, удаления и просмотра подробных сведений. Можно создавать, изменять, удалять и просматривать данные.
   
    ![Представление MVC для данных][rx2]

## <a name="add-an-oauth2-provider"></a>Добавление поставщика OAuth2
> [!NOTE]
> Подробные инструкции по использованию сайтов порталов разработчиков Google и Facebook см. по ссылкам на руководства на веб-сайте ASP.NET. Однако Google и Facebook вносят изменения в свои сайты чаще, чем обновляют эти руководства, поэтому в настоящее время они являются устаревшими. При наличии проблем, связанных с выполнением инструкций, см. выделенный комментарий Disqus в конце этого руководства, содержащий список изменений. 
> 
> 

[OAuth](http://oauth.net/ "http://oauth.net/") — это открытый протокол, обеспечивающий безопасную авторизацию простым и стандартным способом из мобильных, настольных и веб-приложений. В интернет-шаблоне ASP.NET MVC используется OAuth для предоставления Facebook, Twitter, Google и Microsoft в качестве поставщиков проверки подлинности. В этом учебнике в качестве поставщика проверки подлинности используется только Google, но вы легко можете изменить код, чтобы использовать любой другой поставщик. Шаги по реализации других поставщиков очень похожи на шаги, представленные в этом учебнике. Сведения об использовании Facebook в качестве поставщика проверки подлинности см. в руководстве [MVC 5 App with Facebook, Twitter, LinkedIn and Google OAuth2 Sign-on](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) (Приложение MVC 5 со входом через Facebook, Twitter, LinkedIn и Google OAuth2).

Помимо проверки подлинности, в этом учебнике используются роли для реализации авторизации. Только пользователи, которым назначена роль *canEdit*, могут изменять данные (т. е. создавать, изменять и удалять контакты).

1. Выполните инструкции из руководства [MVC 5 App with Facebook, Twitter, LinkedIn and Google OAuth2 Sign-on](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) (Приложение MVC 5 со входом через Facebook, Twitter, LinkedIn и Google OAuth2) (раздел **Creating a Google app for OAuth 2 to set up a Google app for OAuth2** (Создание приложения Google для OAuth2 с целью настройки приложения Google для OAuth2)).
2. Запустите и протестируйте приложение, чтобы убедиться, что вы можете войти в систему с использованием проверки подлинности Google.
3. Если вам нужно создать кнопки входа из социальных сетей с соответствующими значками поставщиков, см. публикацию [Pretty social login buttons for ASP.NET MVC 5](http://www.jerriepelser.com/blog/pretty-social-login-buttons-for-asp-net-mvc-5) (Удобные кнопки входа социальных сетей для ASP.NET MVC 5).

## <a name="using-the-membership-api"></a>Использование API членства
В этом разделе в базу данных членства будут добавлены локальный пользователь и роль *canEdit* . Только пользователи, входящие в роль *canEdit* , смогут изменять данные. Рекомендуется именовать роли по выполняемым ими действиям, например имя *canEdit* предпочтительнее имени *admin*. По мере развития приложения можно добавлять новые роли, например роль *canDeleteMembers*, имя которой предпочтительнее, чем менее описательное имя *superAdmin*.

1. Откройте файл *migrations\configuration.cs* и добавьте следующие инструкции `using`:
   
        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;
2. Добавьте следующий метод **AddUserAndRole** в класс:
   
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
3. Вызовите новый метод из метода **Seed** :
   
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            AddUserAndRole(context);
            context.Contacts.AddOrUpdate(p => p.Name,
                // Code removed for brevity
        }
   
    Ниже представлены изменения, внесенные в метод *Seed* .
   
    ![изображение кода](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss24.PNG)
   
    Этот код создает роль с именем *canEdit*, локального пользователя *user1@contoso.com* и добавляет *user1@contoso.com* к роли *canEdit*. Дополнительные сведения см. в [руководствах по удостоверениям ASP.NET](http://www.asp.net/identity/overview/features-api) на сайте ASP.NET.

## <a name="use-temporary-code-to-add-new-social-login-users-to-the-canedit-role"></a>Использование временного кода для добавления новых пользователей социальных сетей к роли canEdit
В этом разделе будет временно изменен метод **ExternalLoginConfirmation** в контроллере Account для добавления новых пользователей, регистрирующихся с помощью поставщика OAuth, к роли *canEdit* . Мы надеемся в будущем предоставить средство, аналогичное [WSAT](http://msdn.microsoft.com/library/ms228053.aspx) , которое позволит вам создавать и изменять учетные записи пользователей и роли. А пока то же самое можно делать с помощью временного кода.

1. Откройте файл **Controllers\AccountController.cs** и перейдите к методу **ExternalLoginConfirmation**.
2. Добавьте следующий вызов в **AddToRoleAsync** прямо перед вызовом **SignInAsync**.
   
        await UserManager.AddToRoleAsync(user.Id, "canEdit");
   
   Приведенный выше код добавляет вновь зарегистрированного пользователя к роли "canEdit", которая предоставляет доступ к методам действий, изменяющих данные. В следующем фрагменте показана новая строка кода в контексте.
   
          // POST: /Account/ExternalLoginConfirmation
          [HttpPost]
          [AllowAnonymous]
          [ValidateAntiForgeryToken]
          public async Task ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl)
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
                      await UserManager.AddToRoleAsync(user.Id, "canEdit");
                      await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false);
                      return RedirectToLocal(returnUrl);
                   }
                }
                AddErrors(result);
             }
             ViewBag.ReturnUrl = returnUrl;
             return View(model);
          }

Далее в этом учебнике будет развернуто приложение в Azure, благодаря которому можно будет войти с помощью Google или другого стороннего поставщика проверки подлинности. Это добавит вновь зарегистрированную учетную запись к роли *canEdit* . Каждый, кому известен URL-адрес вашего веб-приложения, сможет использовать свой идентификатор Google для регистрации и обновления вашей базы данных. Чтобы предотвратить подобные действия со стороны других лиц, можно остановить сайт. Изучив базу данных, вы сможете проверить, кто входит в роль *canEdit* .

На **консоли диспетчера пакетов** нажмите клавишу со стрелкой вверх, чтобы открыть следующую команду:

        Update-Database

Команда **Update-Database** запускает метод **Seed**, который запускает добавленный ранее метод **AddUserAndRole**. Метод **AddUserAndRole** создает пользователя *user1@contoso.com* и назначает ему роль *canEdit*.

## <a name="protect-the-application-with-ssl-and-the-authorize-attribute"></a>Защита приложения с помощью SSL и атрибута Authorize
В этом разделе применяется атрибут [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) для ограничения доступа к методам действий. Анонимные пользователи смогут просматривать только метод действия **Index** контроллера Home. Зарегистрированные пользователи смогут просматривать контактные данные (страницы **Индекс** и **Сведения** контроллера Cm), страницу сведений и страницу контактов. Только пользователи с назначенной ролью *canEdit* получат доступ к методам действий, изменяющих данные.

1. Откройте файл *App_Start\FilterConfig.cs* и замените метод *RegisterGlobalFilters* следующим кодом (который добавляет два фильтра):
   
        public static void RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());
        }
   
    Этот код добавляет в приложение фильтры [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) и [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx). Фильтр [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) не позволяет анонимным пользователям получить доступ к методам в приложении. С помощью атрибута [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) вы сможете отказаться от обязательной авторизации в соответствующих методах, чтобы анонимные пользователи могли входить в систему и просматривать главную страницу. Фильтр [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) требует использования HTTPS для доступа к веб-приложению.
   
    В качестве альтернативы можно добавить атрибуты [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) и [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) для каждого контроллера, но с точки зрения безопасности рекомендуется применять их ко всему приложению. В случае глобального добавления атрибутов каждый новый контроллер и метод действия будут защищены автоматически. Вам не обязательно помнить о необходимости их применения. Дополнительные сведения см. в статье [Securing your ASP.NET MVC App and the new AllowAnonymous Attribute](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) (Защита приложений ASP.NET MVC и новый атрибут AllowAnonymous). 
2. Добавьте атрибут [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) в метод **Index** контроллера Home. С помощью атрибута [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) можно создать белый список методов, для которых не нужна авторизация. 
   
        public class HomeController : Controller
        {
          [AllowAnonymous]
          public ActionResult Index()
          {
             return View();
          }
   
    Выполнив глобальный поиск атрибута *AllowAnonymous*, вы увидите, что он используется в методах входа и регистрации контроллера Account.
3. В файле *CmController.cs* добавьте `[Authorize(Roles = "canEdit")]` к методам HttpGet и HttpPost, которые изменяют данные (Create, Edit, Delete, все методы действия, кроме Index и Details), в контроллере *Cm*. Ниже показана часть готового кода. 
   
        // GET: Cm/Create
        [Authorize(Roles = "canEdit")]
        public ActionResult Create()
        {
           return View(new Contact { Address = "123 N 456 W",
            City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT",
           Zip = "59405"});
        }
        // POST: Cm/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
         [Authorize(Roles = "canEdit")]
        public ActionResult Create([Bind(Include = "ContactId,Name,Address,City,State,Zip,Email")] Contact contact)
        {
            if (ModelState.IsValid)
            {
                db.Contacts.Add(contact);
                db.SaveChanges();
                return RedirectToAction("Index");
            }
            return View(contact);
        }
        // GET: Cm/Edit/5
        [Authorize(Roles = "canEdit")]
        public ActionResult Edit(int? id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
            Contact contact = db.Contacts.Find(id);
            if (contact == null)
            {
                return HttpNotFound();
            }
            return View(contact);
        }
4. Для запуска приложения нажмите сочетание клавиш CTRL+F5.
5. Если вы все еще не вышли из предыдущего сеанса, нажмите ссылку **Выход** .
6. Щелкните ссылку **About** (О программе) или **Контакт**. Вы перейдете на страницу входа, так как анонимные пользователи не могут просматривать эти страницы.
7. Щелкните ссылку **Регистрация нового пользователя** и добавьте локального пользователя с электронным адресом *joe@contoso.com*. Проверьте, может ли *Joe* просматривать главную страницу, страницу сведений и страницу контактов. 
   
    ![вход](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss14.PNG)
8. Щелкните ссылку *CM Demo* и убедитесь, что данные отображаются.
9. Щелкнув ссылку «Изменить» на странице, вы перейдете на страницу входа (так как новому локальному пользователю нельзя назначить роль *canEdit* ).
10. Войдите как *user1@contoso.com* с паролем P_assw0rd1 (0 в слове word — это нуль). Вы перейдете на ранее выбранную страницу редактирования. 
11.  Если не получается войти в систему с этой учетной записью и этим паролем, попробуйте скопировать пароль из исходного кода и вставить его. Если войти все равно не удается, убедитесь, что пользователь *user1@contoso.com* есть в столбце **UserName** таблицы **AspNetUsers**. 
12. Убедитесь, что можете изменять данные.

## <a name="deploy-the-app-to-azure"></a>Развертывание приложения в Azure
1. В Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать** в контекстном меню.
   
    !["Опубликовать" в контекстном меню проекта][firsdeploy003]
   
    Откроется мастер **Публикация веб-сайта** .
2. Откройте вкладку **Параметры** в левой части диалогового окна **Публикация веб-сайта**. 
3. В разделе **ApplicationDbContext** выберите базу данных, созданную при создании проекта.
4. В разделе **ContactManagerContext** выберите **Execute Code First Migrations** (Выполнить миграции Code First).
   
    ![Параметры](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc2.png)
5. Щелкните **Опубликовать**.
6. Войдите в систему как *user1@contoso.com* (с паролем P_assw0rd1) и убедитесь, что можете изменять данные.
7. Выйдите из системы.
8. Перейдите в [консоль разработчиков Google](https://console.developers.google.com/) и на вкладке **Учетные данные** обновите URI перенаправления и JavaScript Orgins для использования URL-адреса Azure.
9. Войдите с помощью Google или Facebook. При этом учетная запись Google или Facebook добавится к роли **canEdit** . Если появится ошибка HTTP 400 с сообщением *The redirect URI in the request: https://contactmanager{my version}.azurewebsites.net/signin-google did not match a registered redirect URI* (URI перенаправления в запросе https://contactmanager {моя версия}.azurewebsites.net/signin-google не совпадает с зарегистрированным URI перенаправления), вам придется подождать, пока распространятся изменения. Если эта ошибка будет появляться несколько минут, проверьте правильность универсальных кодов ресурсов.

### <a name="stop-the-web-app-to-prevent-other-people-from-registering"></a>Остановка веб-приложения для предотвращения регистрации других пользователей
1. В **обозревателе серверов** выберите **Azure > Служба приложений > {группа ресурсов} > {веб-приложение}**.
2. Щелкните веб-приложение правой кнопкой мыши и выберите пункт **Остановить**. 
   
    Кроме того, вы можете выбрать колонку веб-приложения на [портале Azure](https://portal.azure.com/), а затем щелкнуть значок **Остановить** в верхней части колонки.
   
    ![остановка портала веб-приложений](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/stopweb.png)

### <a name="remove-addtoroleasync-publish-and-test"></a>Удаление AddToRoleAsync, публикация и тестирование
1. Закомментируйте или удалите следующий код в методе **ExternalLoginConfirmation** в контроллере Account.
   
        await UserManager.AddToRoleAsync(user.Id, "canEdit");
2. Создайте проект (который сохраняет изменения файла и проверяет наличие ошибок компиляции).
3. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать**.
   
       ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
4. Нажмите кнопку **Начало предварительного просмотра** . Развертываются только файлы, которые необходимо обновить.
5. Запустите веб-приложение в Visual Studio или на портале. **Вы не сможете опубликовать веб-приложение, если оно остановлено**.
   
    ![запуск веб-приложения](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss15.png)
6. Вернитесь в Visual Studio и щелкните **Опубликовать**.
7. Приложение Azure открывается в браузере по умолчанию. Если вы вошли в систему, выйдите, чтобы просмотреть главную страницу как анонимный пользователь.  
8. Щелкните ссылку **О программе** . Вы перейдете на страницу входа.
9. Щелкните ссылку **Регистрация** на странице входа и создайте локальную учетную запись. С помощью этой локальной учетной записи мы убедимся, что вы имеете доступ к страницам, предназначенным только для чтения, но не имеете доступа к страницам, где изменяются данные (которые защищены ролью *canEdit* ). Далее в этом учебнике мы удалим доступ локальной учетной записи. 
   
    ![Регистрация](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss16.PNG)
10. Проверьте, можете ли вы перейти на страницы *сведений* и *контактов*.
    
     ![Выход из системы](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss17.PNG)
11. Щелкните ссылку **CM Demo** для перехода к контроллеру **Cm**. Можно также добавить *Cm* в URL-адрес. 
    
     ![Страница CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr4.png)
12. Щелкните ссылку "Изменить". 
    
     Вы перейдете на страницу входа. 
13. В разделе **Использовать другую службу для входа**щелкните Google или Facebook и войдите с использованием ранее зарегистрированной учетной записи. (Если вы работаете быстро и файл cookie сеанса еще действует, вы автоматически войдете с ранее использованной учетной записью Google или Facebook.)
14. Убедитесь, что можете изменять данные при входе с этой учетной записью.
    
     **Примечание.** С помощью этого приложения вы не можете выйти из Google и войти в другую учетную запись Google в том же браузере. Если используется один браузер, необходимо перейти в Google и выйти из системы. Используя другой браузер, вы можете войти с другой учетной записью из того же стороннего приложения проверки подлинности (например, Google).
    
     Если вы не указали имя и фамилию в информации учетной записи Google, возникнет исключение NullReferenceException.

## <a name="examine-the-sql-azure-db"></a>Проверка базы данных SQL Azure
1. В **обозревателе сервера** выберите **Azure > Базы данных SQL > {база данных}**.
2. Щелкните базу данных правой кнопкой мыши и выберите пункт **Открыть в обозревателе объектов SQL Server**.
   
    ![Открытие в SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr12.png)
3. Если вы не подключились к этой базе данных ранее, возможно, система попросит добавить правило брандмауэра, чтобы разрешить доступ для текущего IP-адреса. IP-адрес будет указан системой. Чтобы разрешить доступ, просто щелкните **Добавить правило брандмауэра** .
   
    ![Добавить правило брандмауэра](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/addfirewallrule.png)
4. Войдите в базу данных с именем пользователя и паролем, указанными при создании сервера базы данных. 
5. Щелкните правой кнопкой мыши таблицу **AspNetUsers** и выберите пункт **Просмотр данных**.
   
    ![Страница CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr8.png)
6. Запишите идентификатор учетной записи Google, с которой вы зарегистрировались для назначения роли **canEdit**, а также идентификатор пользователя *user1@contoso.com*. Только эти пользователи должны входить в роль **canEdit**. (Вы проверите это на следующем шаге.)
   
    ![Страница CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s2.png)
7. В **обозревателе объектов SQL Server** щелкните правой кнопкой мыши элемент **AspNetUserRoles** и выберите **Просмотр данных**.
   
    ![Страница CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rs1.png)
8. Убедитесь, что идентификатор **UserId** принадлежит пользователю *user1@contoso.com* и зарегистрированной вами учетной записи Google. 

## <a name="troubleshooting"></a>Устранение неполадок
Если у вас возникли проблемы, здесь вы найдете некоторые рекомендации по их устранению.

* Ошибки при подготовке базы данных SQL. Убедитесь, что у вас установлена актуальная версия пакета SDK. В версиях до 2.8.1 есть ошибка, которая в некоторых случаях приводит к ошибкам (когда Visual Studio пытается создать сервер базы данных или базу данных).
* Сообщение об ошибке с текстом "Операция не поддерживается для вашего типа предложения подписки" при создании ресурсов Azure — выше.
* Ошибки при развертывании. Ознакомьтесь со статьей, описывающей [базовое развертывание ASP.NET](web-sites-dotnet-get-started.md). Так как этот сценарий развертывания является более простым, при возникновении такой же проблемы вам будет проще ее изолировать. Например, в некоторых корпоративных средах корпоративный брандмауэр может помешать создавать при веб-развертывании разные типы необходимых подключений к Azure.
* При развертывании отсутствует возможность выбора строки подключения в мастере "Публикация веб-сайта". Если для создания ресурсов Azure используется другой метод (например, вы пытаетесь развернуть веб-приложение и базу данных SQL, созданные на портале), База данных SQL не будет связана с веб-приложением. Самым простым решением является создание нового веб-приложения и базы данных с помощью Visual Studio, как описано в этом руководстве. Вам не нужно начинать работу с руководством сначала. В мастере веб-публикации можно выбрать вариант создания нового веб-приложения, после чего появится диалоговое окно создания ресурсов Azure, отображаемое при создании проекта.
* Указания для портала разработчиков Google или Facebook являются устаревшими. См. выделенный комментарий Disqus в конце этого руководства.

## <a name="next-steps"></a>Дальнейшие действия
Вы создали простое веб-приложение ASP.NET MVC, проверяющее подлинность пользователей. Дополнительные сведения о стандартных задачах проверки подлинности и защите конфиденциальных данных см. в следующих учебниках.

* [Создание безопасного веб-приложения ASP.NET MVC 5 со входом, подтверждением электронной почты и сбросом пароля](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
* [Приложение ASP.NET MVC 5 с двухфакторной проверкой подлинности с использованием SMS и электронной почты](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
* [Рекомендации по развертыванию паролей и других конфиденциальных данных в ASP.NET и Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
* Инструкции по добавлению данных профиля в базу данных регистрации пользователей и подробные инструкции по использованию Facebook в качестве поставщика проверки подлинности приведены в руководстве [Создание приложения ASP.NET MVC 5 с использованием Facebook и Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).
* [Приступая к работе с ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Расширенные инструкции по использованию Entity Framework см. в руководстве [Начало работы с EF и MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

Это руководство написал [Рик Андерсон](http://blogs.msdn.com/b/rickandy/) (Rick Anderson) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) в соавторстве с Томом Дейкстрой (Tom Dykstra) и Бэрри Доррансом (Barry Dorrans) (Twitter [@blowdart](https://twitter.com/blowdart)). 

***Оставьте отзыв*** о том, что вам понравилось или что вы хотели бы улучшить, не только о самом руководстве, но и о продуктах, которые оно демонстрирует. Ваши отзывы помогут нам определить, какие улучшения наиболее приоритетные. Вы также можете запросить и проголосовать за новые темы на странице [Покажите, как это сделать в коде](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

## <a name="whats-changed"></a>Изменения
* Руководство по переходу от веб-сайтов к службе приложений см. в статье [Служба приложений Azure и существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]:#mbrDB
[Create a Data Deployment Script]:#ppd
[Update the Membership Database]:#ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxSSL.png

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

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure web apps]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png




<!--HONumber=Dec16_HO1-->


