<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />
  
<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-active-directory-dotnet-getting-started/)
> - [Что произошло?](/documentation/articles/vs-active-directory-dotnet-what-happened/)

## Приступая к работе с Azure Active Directory (проекты .NET)
 
#####Требование проверки подлинности для доступа к контроллерам 

Ко всем контроллерам в проекте добавлен атрибут **Authorize**. Этот атрибут обеспечивает проверку подлинности пользователей перед их доступом к контроллерам. Для анонимного доступа к контроллеру удалить с него этот атрибут. Если вы хотите задать разрешения на более детальном уровне, примените атрибут к каждому методу, требующему проверки подлинности, а не к классу контроллера.
 
#####Добавление элементов управления SignIn и SignOut 

Чтобы добавить элементы управления SignIn и SignOut, можно использовать частичное представление **_LoginPartial.cshtml** для добавления функциональности одному из представлений. Вот пример функциональности, добавленной стандартному представлению **_Layout.cshtml**. (Обратите внимание на последний элемент в теге div с классом navbar-collapse):

<PRE class="prettyprint">
    &lt;!DOCTYPE html&gt; 
     &lt;html&gt; 
     &lt;head&gt; 
         &lt;meta charset="utf-8" /&gt; 
        &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt; 
        &lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
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
                    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
                &lt;/div&gt; 
                &lt;div class="navbar-collapse collapse"&gt; 
                    &lt;ul class="nav navbar-nav"&gt; 
                        &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt; 
                    &lt;/ul&gt; 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/div&gt; 
            &lt;/div&gt; 
        &lt;/div&gt; 
        &lt;div class="container body-content"&gt; 
            @RenderBody() 
            &lt;hr /&gt; 
            &lt;footer&gt; 
                &lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
            &lt;/footer&gt; 
        &lt;/div&gt; 
        @Scripts.Render("~/bundles/jquery") 
        @Scripts.Render("~/bundles/bootstrap") 
        @RenderSection("scripts", required: false) 
    &lt;/body&gt; 
    &lt;/html&gt;
</PRE>

[Дополнительные сведения о службе Azure Active Directory](http://azure.microsoft.com/services/active-directory/)
