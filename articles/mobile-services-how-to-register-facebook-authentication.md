<properties linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Register for Facebook Authentication" pageTitle="Register for Facebook authentication - Mobile Services" metaKeywords="Azure Facebook, Azure Facebook, Azure authenticate Mobile Services" description="Learn how to use Facebook authentication in your Azure Mobile Services app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Facebook authentication with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Регистрация приложений для аутентификации в мобильных службах с помощью Facebook

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Facebook для аутентификации в мобильных службах Azure.

> [WACOM.NOTE] Это учебник о [мобильных службах Azure][мобильных службах Azure], решении, которое помогает создавать масштабируемые мобильные приложения для любых платформ. С мобильными службами легко синхронизировать данные, аутентифицировать пользователей и отправлять push-уведомления. Эта страница дополняет учебник [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией], который показывает, как настроить вход пользователей в приложение. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Facebook с проверенным электронным адресом и номером мобильного телефона. Чтобы создать новую учетную запись Facebook, перейдите по ссылке [facebook.com][facebook.com].

1.  Перейдите на веб-сайт [разработчиков Facebook][разработчиков Facebook] и войдите с помощью своих данных учетной записи Facebook.

2.  (Необязательно) Если вы еще не зарегистрированы, нажмите кнопку **Приложения**, затем **Зарегистрироваться в качестве разработчика**, примите политику и выполните действия по регистрации.

    ![][]

3.  Нажмите кнопку **Приложения**, а затем щелкните **Создать новое приложение**

    ![][1]

4.  Выберите уникальное имя для приложения, выберите **Приложения для страниц**, щелкните **Создать приложение** и выполните запрос.

    ![][2]

    При этом приложение зарегистрируется в Facebook

5.  Выберите пункт **Settings** (Параметры), введите домен своей мобильной службы в поле **App Domains** (Домены приложения). Также введите **Contact Email** (Контактный электронный адрес), затем нажмите **Add Platform** (Добавить платформу) и выберите **Website** (Веб-сайт).

    ![][3]

6.  Введите URL-адрес вашей мобильной службы в поле **URL-адрес сайта** и нажмите кнопку **Сохранить изменения**.

    ![][4]

7.  Нажмите кнопку **Показывать**, введите пароль при запросе, а затем запишите значения **ИД приложения** и **Секрет приложения**.

    ![][5]

    <div class="dev-callout"><b>Примечание о безопасности</b>
<p>Секрет приложения &mdash; это важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе со своим приложением.</p>
</div>

8.  Щелкните вкладку **Advanced** (Дополнительно), введите URL-адрес своей мобильной службы с добавлением пути */login/facebook* в разделе **Valid OAuth redirect URIs** (Допустимые универсальные коды ресурса (URI) перенаправления OAuth), а затем щелкните **Save Changes** (Сохранить изменения).

    > [WACOM.NOTE]Для серверной мобильной службы .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем *signin-facebook* мобильной службы как службы .NET, например <code><a href="https://todolist.azure-mobile.net/signin-google" class="uri">https://todolist.azure-mobile.net/signin-facebook</a></code>.

    ![][6]

9.  Учетная запись Facebook, для которой вы определили новое приложение, является администратором приложения и обеспечивает доступ к нему от имени администратора. Чтобы аутентифицировать другие учетные записи Facebook, им необходим доступ к приложению. Этот шаг обеспечивает общий доступ, чтобы приложение могло аутентифицировать другие учетные записи Facebook. Щелкните **Status & Review** (Состояние и проверка). Затем щелкните **Yes** (Да), чтобы включить общий доступ.

    ![][7]

Теперь вы готовы использовать имя входа Facebook для проверки подлинности в вашем приложении, предоставляя мобильным службам значения ИД приложения и секретного кода приложения.



  [мобильных службах Azure]: http://azure.microsoft.com/ru-ru/services/mobile-services/
  [Приступая к работе с аутентификацией]: http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-ios-get-started-users/
  [Приступая к работе с мобильными службами]: http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-ios-get-started/
  [facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
  [разработчиков Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
  []: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
  [1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
  [2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
  [3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
  [4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
  [5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
  [6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png
  [7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
