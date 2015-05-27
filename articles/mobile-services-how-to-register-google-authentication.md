<properties 
	pageTitle="Регистрация для проверки подлинности Google: мобильные службы" 
	description="Узнайте, как зарегистрировать приложения, чтобы использовать Google для проверки подлинности с помощью мобильных служб Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# Регистрация приложений для входа в мобильные службы с помощью Google

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Google для проверки подлинности с помощью мобильных служб Azure.

>[AZURE.NOTE]Это учебник о [мобильных службах Azure](http://azure.microsoft.com/services/mobile-services/) — решении, которое помогает создавать масштабируемые мобильные приложения для любых платформ. С мобильными службами легко синхронизировать данные, аутентифицировать пользователей и отправлять push-уведомления. Эта страница дополняет учебник <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Приступая к работе с проверкой подлинности</a>, который показывает, как настроить вход пользователей в приложение. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Приступая к работе с мобильными службами</a>.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Перейдите на веб-сайт <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a>, войдите с учетными данными Google, выберите **Create Project** (Создать проект), укажите имя проекта в поле **Project name** (Имя проекта), а затем нажмите кнопку **Create** (Создать).

   	![][1]

2. Щелкните **Consent screen** (Экран разрешения), выберите **Email Address** (Адрес электронной почты), введите **Product Name** (Название продукта) и нажмите кнопку **Save** (Сохранить).

3. Щелкните **API & Auth** (API и авторизация), затем **Credentials** (Учетные данные), а потом **Create new Client ID** (Создать новый идентификатор клиента).

   	![][2]

4. Выберите **Web application** (Веб-приложение), введите URL-адрес мобильной службы в поле **Authorized JavaScript Origins** (Авторизованные источники JavaScript), замените созданный URL-адрес в поле **Authorized Redirect URI** (Авторизованный универсальный код ресурса (URI) перенаправления) на URL-адрес мобильной службы, к которому добавлен путь _/login/google_, а затем нажмите кнопку **Create client ID** (Создать идентификатор клиента).

	>[AZURE.NOTE]Для серверной мобильной службы .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем _signin-google_ мобильной службы в качестве службы .NET, например <code>https://todolist.azure-mobile.net/signin-google</code>.

   	![][3]

5. В разделе **Client ID for web applications** (Идентификатор клиента для веб-приложений) запишите значения полей **Client ID** (Идентификатор клиента) и **Client secret** (Секрет клиента).

   	![][4]

    > [AZURE.IMPORTANT]Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе со своим приложением.

Теперь вы готовы использовать имя входа Google для проверки подлинности в вашем приложении, предоставляя мобильным службам код клиента и секрет клиента приложения.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/

<!--HONumber=54-->