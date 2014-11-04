<properties title="Приступая к работе с проверкой подлинности на основе Active Directory" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Приступая к работе][Приступая к работе]
> -   [Что произошло?][Что произошло?]

## Приступая к работе с Azure Active Directory (проекты .NET)

##### Требование проверки подлинности для доступа к контроллерам

Ко всем контроллерам в проекте добавлен атрибут **Authorize**. Этот атрибут обеспечивает проверку подлинности пользователей перед их доступом к контроллерам. Для анонимного доступа к контроллеру удалить с него этот атрибут. Если необходимо задать разрешения на более детальном уровне, примените атрибут к каждому методу, требующему проверки подлинности, а не к классу контроллера.

##### Добавление элементов управления SignIn и SignOut

Чтобы добавить элементы управления SignIn и SignOut, можно использовать частичное представление \*\*\_LoginPartial.cshtml\*\* для добавления функций к одному из представлений. Вот пример функций, добавленных к стандартному представлению \*\*\_Layout.cshtml\*\* (обратите внимание на последний элемент в теге div с классом navbar-collapse):

<pre class="prettyprint">
    &lt;!DOCTYPE html&gt; 
     &lt;html&gt; 
     &lt;head&gt; 
         &lt;meta charset=&quot;utf-8&quot; /&gt; 
        &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot;&gt; 
        &lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
        @Styles.Render(&quot;~/Content/css&quot;) 
        @Scripts.Render(&quot;~/bundles/modernizr&quot;) 
    &lt;/head&gt; 
    &lt;body&gt; 
        &lt;div class=&quot;navbar navbar-inverse navbar-fixed-top&quot;&gt; 
            &lt;div class=&quot;container&quot;&gt; 
                &lt;div class=&quot;navbar-header&quot;&gt; 
                    &lt;button type=&quot;button&quot; class=&quot;navbar-toggle&quot; data-toggle=&quot;collapse&quot; data-target=&quot;.navbar-collapse&quot;&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                    &lt;/button&gt; 
                    @Html.ActionLink(&quot;Application name&quot;, &quot;Index&quot;, &quot;Home&quot;, new { area = &quot;&quot; }, new { @class = &quot;navbar-brand&quot; }) 
                &lt;/div&gt; 
                &lt;div class=&quot;navbar-collapse collapse&quot;&gt; 
                    &lt;ul class=&quot;nav navbar-nav&quot;&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;Home&quot;, &quot;Index&quot;, &quot;Home&quot;)&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;About&quot;, &quot;About&quot;, &quot;Home&quot;)&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;Contact&quot;, &quot;Contact&quot;, &quot;Home&quot;)&lt;/li&gt; 
                    &lt;/ul&gt; 
                    @Html.Partial(&quot;_LoginPartial&quot;) 
                &lt;/div&gt; 
            &lt;/div&gt; 
        &lt;/div&gt; 
        &lt;div class=&quot;container body-content&quot;&gt; 
            @RenderBody() 
            &lt;hr /&gt; 
            &lt;footer&gt; 
                &lt;p&gt;© @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
            &lt;/footer&gt; 
        &lt;/div&gt; 
        @Scripts.Render(&quot;~/bundles/jquery&quot;) 
        @Scripts.Render(&quot;~/bundles/bootstrap&quot;) 
        @RenderSection(&quot;scripts&quot;, required: false) 
    &lt;/body&gt; 
    &lt;/html&gt;
</pre>

[Дополнительные сведения о службе Azure Active Directory][Дополнительные сведения о службе Azure Active Directory]

  [Приступая к работе]: /documentation/articles/vs-active-directory-dotnet-getting-started/
  [Что произошло?]: /documentation/articles/vs-active-directory-dotnet-what-happened/
  [Дополнительные сведения о службе Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
