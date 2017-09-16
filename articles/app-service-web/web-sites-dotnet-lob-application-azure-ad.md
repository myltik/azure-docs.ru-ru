---
title: "Создание бизнес-приложения Azure с аутентификацией Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как создать бизнес-приложение ASP.NET MVC в службе приложений Azure, осуществляющее проверку подлинности с помощью Azure Active Directory"
services: app-service\web, active-directory
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: ad947bdb-4463-43ff-a5e3-91d9b2169b60
ms.service: app-service-web
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 09/01/2016
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 209766e4ebc902d78cf0c83fa8fe0f57c4da73e4
ms.contentlocale: ru-ru
ms.lasthandoff: 09/07/2017

---
# <a name="create-a-line-of-business-azure-app-with-azure-active-directory-authentication"></a>Создание бизнес-приложения Azure с проверкой подлинности Azure Active Directory
В этой статье показано, как создать бизнес-приложение .NET на основе [веб-приложения службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714) с использованием функции [Аутентификация или авторизация](../app-service/app-service-authentication-overview.md). Здесь также описано, как использовать [API Graph Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) , чтобы запросить данные каталога в приложении.

Используемый клиент Azure Active Directory может действовать исключительно как каталог Azure или может быть [синхронизирован с локальной службой Active Directory](../active-directory/active-directory-aadconnect.md), что обеспечит единый вход для локальных и удаленных сотрудников. В рамках этой статьи используется каталог учетной записи Azure по умолчанию.

<a name="bkmk_build"></a>

## <a name="what-you-will-build"></a>Что будет строиться
Вы создадите простое бизнес-приложение по шаблону «создать-прочитать-обновить-удалить» (CRUD) в веб-приложениях службы приложений, которое отслеживает рабочие элементы с помощью следующих компонентов:

* аутентификация пользователей в службе Azure Active Directory;
* запросы пользователей и групп каталога с помощью [API Graph Azure Active Directory](http://msdn.microsoft.com/library/azure/hh974476.aspx)
* использование шаблона ASP.NET MVC *без аутентификации* .

If you need role-based access control (RBAC) for your line-of-business app in Azure, see [Дальнейшее действие](#next).

<a name="bkmk_need"></a>

## <a name="what-you-need"></a>Необходимые элементы
[!INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Чтобы выполнить инструкции этого учебника, необходимо следующее:

* Клиент Azure Active Directory с пользователями в различных группах
* Разрешения на создание приложений в клиенте Azure Active Directory
* Visual Studio 2013 с обновлением 4 или более поздней версии.
* [Пакет Azure SDK версии 2.8.1 или более поздней.](https://azure.microsoft.com/downloads/)

<a name="bkmk_deploy"></a>

## <a name="create-and-deploy-a-web-app-to-azure"></a>Создание и развертывание веб-приложения в Azure
1. В Visual Studio выберите **Файл** > **Создать** > **Проект**.
2. Выберите **Веб-приложение ASP.NET**, введите имя проекта и нажмите кнопку **ОК**.
3. Выберите шаблон **MVC** и задайте для аутентификации значение **Нет проверки подлинности**. Установите флажок **Разместить в облаке** и нажмите кнопку **ОК**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)
4. В диалоговом окне **Создать службу приложений** щелкните **Добавить учетную запись** (а затем из раскрывающегося списка выберите **Добавить учетную запись**), чтобы войти в учетную запись Azure.
5. После этого перейдите на страницу "Настройка" веб-приложения. Нажмите кнопку **Создать** , чтобы создать группу ресурсов и план службы приложений. Чтобы продолжить, щелкните **Обзор дополнительных служб Azure** .
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)
6. Чтобы добавить для приложения базу данных SQL, на вкладке **Службы** щелкните **+**. 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)
7. Чтобы создать экземпляр SQL Server, на вкладке **Настроить базу данных SQL** щелкните **Создать**.
8. Настройте экземпляр SQL Server на вкладке **Настроить SQL Server**. Затем щелкните **ОК**, **ОК** и **Создать**, чтобы начать создание приложения в Azure.
9. Сведения о завершении создания приложения отображаются на вкладке **Действие службы приложений Azure**. Щелкните **Опубликовать &lt;*имя_приложения*> в этом веб-приложении**, а затем — **Опубликовать**. 
   
    После завершения Visual Studio откроет опубликованное приложение в окне браузера. 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>

## <a name="configure-authentication-and-directory-access"></a>Настройка проверки подлинности и доступа к каталогу
1. Войдите на [портал Azure](https://portal.azure.com).
2. В меню слева выберите **Службы приложений** > **&lt;*имя_приложения*>** > **Аутентификация или авторизация**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)
3. Чтобы включить аутентификацию Azure Active Directory, щелкните **Включено** > **Azure Active Directory** > **Экспресс** > **OК**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)
4. Щелкните в командной строке **Сохранить** .
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)
   
    После сохранения параметров проверки подлинности перейдите к своему приложению в браузере. В соответствии с параметрами по умолчанию проверка подлинности применяется для всего приложения. Если вход в систему не выполнен, откроется экран входа. После входа откроется приложение, защищенное по протоколу HTTPS. Далее необходимо включить доступ к данным каталога. 
5. Перейдите на [классический портал](https://manage.windowsazure.com).
6. В меню слева выберите **Active Directory** > **Каталог по умолчанию** > **Приложения** > **&lt;*имя_приложения*>**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)
   
    Это приложение Azure Active Directory, созданное службой приложений для включения функции "Аутентификация или авторизация".
7. Чтобы проверить наличие пользователей и групп в каталоге, щелкните **Пользователи** и **Группы**. Если пользователи и группы отсутствуют, создайте несколько тестовых пользователей и групп.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)
8. Щелкните **Настройка** , чтобы настроить это приложение.
9. Прокрутите вниз до раздела **Ключи** и добавьте ключ, выбрав срок его действия. Затем щелкните раскрывающийся список **Делегированные разрешения** и выберите разрешение **Чтение данных каталога**. 
   Щелкните **Сохранить**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)
10. После сохранения параметров прокрутите вверх до раздела **Ключи** и нажмите кнопку **копирования**, чтобы скопировать клиентский ключ. 
    
     ![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)
    
    > [!IMPORTANT]
    > Если покинуть эту страницу сейчас, вы больше никогда не сможете получить доступ к этому клиентскому ключу.
    > 
    > 
11. Теперь необходимо настроить веб-приложение с этим ключом. Войдите в [обозреватель ресурсов Azure](https://resources.azure.com) , используя учетную запись Azure.
12. В верхней части страницы щелкните **Чтение и запись** , чтобы внести изменения в обозревателе ресурсов Azure.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)
13. Перейдите к параметрам аутентификации приложения: "Подписки" > **&lt;*имя_подписки*>** > **resourceGroups** > **&lt;*имя_группы_ресурсов*>** > **providers** > **Microsoft.Web** > **sites** > **&lt;*имя_приложения*>** > **config** > **authsettings**.
14. Нажмите кнопку **Изменить**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)
15. В области редактирования задайте свойство `clientSecret` и `additionalLoginParams`, как показано ниже.
    
        ...
        "clientSecret": "<client key from the Azure Active Directory application>",
        ...
        "additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
        ...
16. Чтобы отправить изменения, в верхней части страницы нажмите кнопку **Поместить** .
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)
17. Теперь, чтобы проверить наличие маркера авторизации для доступа к API Graph Azure Active Directory, просто перейдите в браузере по адресу **https://&lt;*имя_приложения*>.azurewebsites.net/.auth/me**. Если вы настроили все правильно, вы увидите свойство `access_token` в ответе JSON.
    
    Путь URL-адреса `~/.auth/me` управляется функцией проверки подлинности и авторизации в службе приложений, благодаря чему вы можете получать сведения о сеансе с проверкой подлинности. Дополнительные сведения см. в статье [Проверка подлинности и авторизация в службе приложений Azure](../app-service/app-service-authentication-overview.md).
    
    > [!NOTE]
    > `access_token` имеет срок действия. Тем не менее функция аутентификации и авторизации в службе приложений обеспечивает обновление маркера с использованием `~/.auth/refresh`. Чтобы узнать больше об использовании этой функции, ознакомьтесь с [хранилищем маркеров службы приложений](https://cgillum.tech/2016/03/07/app-service-token-store/).
    > 
    > 

В следующем разделе описываются действия, выполняемые с данными каталога.

<a name="bkmk_crud"></a>

## <a name="add-line-of-business-functionality-to-your-app"></a>Добавление функциональности бизнес-приложения к приложению
Теперь необходимо создать простое приложения CRUD для отслеживания рабочих элементов.  

1. В папке ~\Models создайте файл класса с именем WorkItem.cs и замените `public class WorkItem {...}` следующим кодом.
   
     using System.ComponentModel.DataAnnotations;
   
     public class WorkItem   {
   
         [Key]
         public int ItemID { get; set; }
         public string AssignedToID { get; set; }
         public string AssignedToName { get; set; }
         public string Description { get; set; }
         public WorkItemStatus Status { get; set; }
     }
   
     public enum WorkItemStatus   {
   
         Open,
         Investigating,
         Resolved,
         Closed
     }
2. Постройте проект, чтобы сделать новую модель доступной для логики формирования шаблона в Visual Studio.
3. Добавьте новый шаблонный элемент `WorkItemsController` в папку ~\Controllers (щелкните правой кнопкой мыши **Контроллеры**, выберите **Добавить**, а затем — **Создать шаблонный элемент**). 
4. Выберите **Контроллер MVC 5 с представлениями, использующий Entity Framework** и щелкните **Добавить**.
5. Выберите созданную модель, щелкните **+** и **Добавить**, чтобы добавить контекст данных, а затем нажмите кнопку **Добавить**.
   
   ![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)
6. В файле ~\Views\WorkItems\Create.cshtml (автоматически сформированный на основе шаблона элемент) найдите вспомогательный метод `Html.BeginForm` и добавьте следующие выделенные строки.  
   
   <pre class="prettyprint">
   @model WebApplication1.Models.WorkItem
   
   @{
    ViewBag.Title = &quot;Create&quot;;
   }
   
   &lt;h2&gt;Create&lt;/h2&gt;
   
   @using (Html.BeginForm(<mark>&quot;Create&quot;, &quot;WorkItems&quot;, FormMethod.Post, new { id = &quot;main-form&quot; }</mark>)) 
   {
    @Html.AntiForgeryToken()
   
    &lt;div class=&quot;form-horizontal&quot;&gt;
        &lt;h4&gt;WorkItem&lt;/h4&gt;
        &lt;hr /&gt;
        @Html.ValidationSummary(true, &quot;&quot;, new { @class = &quot;text-danger&quot; })
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToID, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = &quot;form-control&quot;<mark>, @type = &quot;hidden&quot;</mark> } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToID, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.Description, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;form-control&quot; })
                @Html.ValidationMessageFor(model =&gt; model.Status, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            &lt;div class=&quot;col-md-offset-2 col-md-10&quot;&gt;
                &lt;input type=&quot;submit&quot; value=&quot;Create&quot; class=&quot;btn btn-default&quot;<mark> id=&quot;submit-button&quot;</mark> /&gt;
            &lt;/div&gt;
        &lt;/div&gt;
    &lt;/div&gt;
   }
   
   &lt;div&gt;
    @Html.ActionLink(&quot;Back to List&quot;, &quot;Index&quot;)
   &lt;/div&gt;
   
   @section Scripts {
    @Scripts.Render(&quot;~/bundles/jqueryval&quot;)
    <mark>&lt;script&gt;
        // People/Group Picker Code
        var maxResultsPerPage = 14;
        var input = document.getElementById(&quot;AssignedToName&quot;);
   
        // Access token from request header, and tenantID from claims identity
        var token = &quot;@Request.Headers[&quot;X-MS-TOKEN-AAD-ACCESS-TOKEN&quot;]&quot;;
        var tenant =&quot;@(System.Security.Claims.ClaimsPrincipal.Current.Claims
                        .Where(c => c.Type == &quot;http://schemas.microsoft.com/identity/claims/tenantid&quot;)
                        .Select(c => c.Value).SingleOrDefault())&quot;;
   
        var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
   
        // Submit the selected user/group to be asssigned.
        $(&quot;#submit-button&quot;).click({ picker: picker }, function () {
            if (!picker.Selected())
                return;
            $(&quot;#main-form&quot;).get()[0].elements[&quot;AssignedToID&quot;].value = picker.Selected().objectId;
        });
    &lt;/script&gt;</mark>
   }
   </pre>
   
   Обратите внимание, что `token` и `tenant` используются объектом `AadPicker` при вызовах API Graph Azure Active Directory. Объект `AadPicker` добавим позже.     
   
   > [!NOTE]
   > Точно так же `token` и `tenant` можно получить с помощью `~/.auth/me` на стороне клиента, но это будет дополнительный вызов сервера. Например:
   > 
   > $.ajax({ dataType: "json", url: "/.auth/me", success: function (data) { var token = data[0].access_token; var tenant = data[0].user_claims .find(c => c.typ === 'http://schemas.microsoft.com/identity/claims/tenantid') .val; } });
   > 
   > 
7. Внесите аналогичные изменения в файл ~\Views\WorkItems\Edit.cshtml.
8. Объект `AadPicker` определяется в сценарии, который необходимо добавить в проект. Щелкните папку ~\Scripts правой кнопкой мыши, выберите **Добавить**, а затем щелкните **Файл JavaScript**. Ведите для имени файла значение `AadPickerLibrary` и нажмите кнопку **ОК**.
9. Скопируйте содержимое [отсюда](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js) и вставьте его в файл ~\Scripts\AadPickerLibrary.js.
   
   В сценарии объект `AadPicker` вызывает [API Graph Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) для поиска пользователей и групп, которые соответствуют введенным данным.  
10. В файле ~\Scripts\AadPickerLibrary.js также используется [мини-приложение автозаполнения пользовательского интерфейса jQuery](https://jqueryui.com/autocomplete/). Поэтому в проект необходимо добавить пользовательский интерфейс jQuery. Щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**.
11. В диспетчере пакетов NuGet щелкните "Обзор", введите в строке поиска **jquery-ui** и выберите **jQuery.UI.Combined**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)
12. В области справа щелкните **Установить** и нажмите кнопку **ОК**, чтобы продолжить.
13. Откройте файл ~\App_Start\BundleConfig.cs и добавьте следующие выделенные строки.  
    
    <pre class="prettyprint">
    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;).Include(
                    &quot;~/Scripts/jquery-{version}.js&quot;<mark>,
                    &quot;~/Scripts/jquery-ui-{version}.js&quot;,
                    &quot;~/Scripts/AadPickerLibrary.js&quot;</mark>));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;).Include(
                    &quot;~/Scripts/jquery.validate*&quot;));
    
        // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
        // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
        bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;).Include(
                    &quot;~/Scripts/modernizr-*&quot;));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;).Include(
                    &quot;~/Scripts/bootstrap.js&quot;,
                    &quot;~/Scripts/respond.js&quot;));
    
        bundles.Add(new StyleBundle(&quot;~/Content/css&quot;).Include(
                    &quot;~/Content/bootstrap.css&quot;,
                    &quot;~/Content/site.css&quot;<mark>,
                    &quot;~/Content/themes/base/jquery-ui.css&quot;</mark>));
    }
    </pre>
    
    Существуют и более производительные способы управления файлами CSS и JavaScript в приложении. Однако для простоты используйте пакеты, предоставленные с каждым представлением.
14. И, наконец, в файле ~\Global.asax добавьте следующую строку кода в метод `Application_Start()`. Чтобы исправить все ошибки, связанные с разрешением имен, щелкните каждую ошибку и нажмите клавиши `Ctrl`+`.` .
    
        AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
    
    > [!NOTE]
    > Вам нужна эта строка кода, так как шаблон MVC по умолчанию использует в некоторых действиях декорирование <code>[ValidateAntiForgeryToken]</code>. Из-за поведения, описанного [Алленом Броком (Brock Allen)](https://twitter.com/BrockLAllen) в статье [MVC 4, AntiForgeryToken and Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) (MVC 4, маркер защиты от подделки и утверждения), команда HTTP POST может завершиться ошибкой при проверке маркеров защиты от подделки по следующей причине:
    > 
    > * Azure Active Directory не отправляет http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, что требуется по умолчанию для маркера защиты от подделки.
    > * Если Azure Active Directory является каталогом, синхронизированным с AD FS, то средство доверия AD FS по умолчанию также не отправляет утверждение http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, хотя можно настроить вручную AD FS для отправки такого утверждения.
    > 
    > `ClaimTypes.NameIdentifies` определяет утверждение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, которое служба Azure Active Directory не предоставляет.  
    > 
    > 
15. Теперь опубликуйте изменения. Щелкните свой проект правой кнопкой мыши и выберите **Опубликовать**.
16. Щелкните **Параметры**, убедитесь в наличии строки подключения к базе данных SQL, выберите **Обновить базу данных**, чтобы внести изменения в схему модели, и нажмите кнопку **Опубликовать**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)
17. В браузере перейдите на страницу https://&lt;*имя_приложения*>.azurewebsites.net/workitemss и щелкните **Создать**.
18. Щелкните поле **AssignedToName** . В раскрывающемся списке должны отобразиться пользователи и группы из клиента Azure Active Directory. Задайте условия фильтрации или используйте клавиши `Up` и `Down`, чтобы выбрать пользователя или группу. 
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)
19. Нажмите кнопку **Создать** , чтобы сохранить изменения. Затем в созданном рабочем элементе щелкните **Изменить** , и вы сможете наблюдать за таким же поведением.

Поздравляем, теперь бизнес-приложение с доступом к каталогу запущено в Azure! API Graph предлагает гораздо больше возможностей. Ознакомьтесь со [справочником по API Graph Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog).

<a name="next"></a>

## <a name="next-step"></a>Дальнейшее действие
Чтобы создать бизнес-приложение Azure с поддержкой управления доступом на основе ролей (RBAC), воспользуйтесь [образцом WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) , предоставленным рабочей группой Azure Active Directory. В нем показано, как включить роли для приложения Azure Active Directory, а затем авторизовать пользователей с оформлением `[Authorize]` .

<!-- If your line-of-business app needs access to on-premises data, see 
[Access on-premises resources using hybrid connections in Azure App Service](web-sites-hybrid-connection-get-started.md). -->

<a name="bkmk_resources"></a>

## <a name="further-resources"></a>Дополнительные ресурсы
* [Проверка подлинности и авторизация в службе приложений Azure](../app-service/app-service-authentication-overview.md)
* [Проверка подлинности в приложении Azure с помощью локального каталога Active Directory](web-sites-authentication-authorization.md)
* [Создание веб-приложения .NET MVC в службе приложений Azure с аутентификацией AD FS](web-sites-dotnet-lob-application-adfs.md)
* [App Service Auth and the Azure AD Graph API (Проверка подлинности службы приложений и API Graph Azure AD)](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/)
* [Примеры и документация Microsoft Azure Active Directory](https://github.com/AzureADSamples)
* [Поддерживаемые типы маркеров и утверждений](http://msdn.microsoft.com/library/azure/dn195587.aspx)

