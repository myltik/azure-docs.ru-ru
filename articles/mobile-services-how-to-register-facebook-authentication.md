<properties 
	pageTitle="Регистрация для проверки подлинности Facebook - мобильные службы" 
	description="Сведения об использовании проверки подлинности в Facebook в приложении мобильных служб Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Регистрация приложений для аутентификации в мобильных службах с помощью Facebook

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Facebook для аутентификации в мобильных службах Azure. 

> [AZURE.NOTE] Это учебник о [мобильных службах Azure](http://azure.microsoft.com/services/mobile-services/), решении, которое помогает создавать масштабируемые мобильные приложения для любых платформ. С мобильными службами легко синхронизировать данные, аутентифицировать пользователей и отправлять push-уведомления. Эта страница поддерживает учебник <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Приступая к работе с проверкой подлинности</a> , в котором показано, как вести журнал пользователей в приложении. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Приступая к работе с мобильными службами</a>.
	
Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Facebook с проверенным электронным адресом и номером мобильного телефона. Чтобы создать учетную запись Facebook, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>.

1. Перейдите на страницу <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Разработчики Facebook</a> и войдите с помощью учетной записи Facebook.

2. (Необязательно) Если вы еще не зарегистрированы, нажмите кнопку **Приложения**, затем **Зарегистрироваться в качестве разработчика**, примите политику и выполните действия по регистрации. 

   	![][0]

3. Нажмите кнопку **Приложения**, а затем щелкните **Создать новое приложение**

   	![][1]

4. Выберите уникальное имя для приложения, выберите **Приложения для страниц**, щелкните **Создать приложение** и выполните запрос.

   	![][2]

	При этом приложение зарегистрируется в Facebook 

5. Выберите пункт **Параметры**, введите домен своей мобильной службы в поле **Домены приложения**. Также введите **Контактный электронный адрес**, затем нажмите **Добавить платформу** и выберите **Веб-сайт**.

   	![][3]

6. Введите URL-адрес вашей мобильной службы в поле **URL-адрес сайта** и нажмите кнопку **Сохранить изменения**.

	![][4]

7. Нажмите кнопку **Показывать**, введите пароль при запросе, а затем запишите значения **ИД приложения** и **Секрет приложения**. 

   	![][5]

	> [AZURE.NOTE] **Примечание о безопасности**
	Секрет приложения - это важные учетные данные безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.


8. Щелкните вкладку **Дополнительно**, введите URL-адрес своей мобильной службы с добавлением пути _/login/facebook_ в разделе **Допустимые универсальные коды ресурса (URI)перенаправления OAuth)**, а затем щелкните **Сохранить изменения**. 

	> [AZURE.NOTE] ля серверной мобильной службы .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем _signin-facebook_ мобильной службы в качестве службы .NET, например <code>https://todolist.azure-mobile.net/signin-facebook</code>.  
	
	![][7]

9. Учетная запись Facebook, для которой вы определили новое приложение, является администратором приложения и обеспечивает доступ к нему от имени администратора. Чтобы аутентифицировать другие учетные записи Facebook, им необходим доступ к приложению. Этот шаг обеспечивает общий доступ, чтобы приложение могло аутентифицировать другие учетные записи Facebook. Щелкните **Состояние и проверка**. Затем щелкните **Yes** (Да), чтобы включить общий доступ.

    ![][6]



Теперь вы готовы использовать имя входа Facebook для проверки подлинности в вашем приложении, предоставляя мобильным службам значения ИД приложения и секретного кода приложения.  

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
[6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
[7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png

<!-- URLs. -->
[Разработчики Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Приступая к работе с проверкой подлинности]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Портал управления Azure]: https://manage.windowsazure.com/
[Мобильные службы Azure]: http://azure.microsoft.com/services/mobile-services/

<!--HONumber=47-->
