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
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Регистрация приложений для входа в мобильные службы с помощью Google

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Google для проверки подлинности с помощью мобильных служб Azure.

>[AZURE.NOTE] Это учебник о [мобильных службах Azure](http://azure.microsoft.com/services/mobile-services/), решении, которое помогает создавать масштабируемые мобильные приложения для любых платформ. С мобильными службами легко синхронизировать данные, аутентифицировать пользователей и отправлять push-уведомления. Эта страница поддерживает учебник <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Приступая к работе с проверкой подлинности</a> , в котором показано, как вести журнал пользователей в приложении. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Приступая к работе с мобильными службами</a>.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Перейдите на страницу <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">API-интерфейсы Google,</a> войдите с учетными данными Google, выберите **Создать проект**, укажите имя проекта в поле **Имя проекта**, а затем нажмите кнопку **Создать**.

   	![][1]

2. Щелкните **Экран разрешения**, выберите свой **Адрес электронной почты**, введите **Название продукта** и нажмите кнопку **Сохранить**. 

3. Щелкните **API и авторизация**, затем **Учетные данные**, а потом **Создать новый идентификатор клиента**.

   	![][2]

4. Выберите **Веб-приложение**, введите URL-адрес мобильной службы в поле **Aвторизованные источники JavaScript**, замените созданный URL-адрес в поле **Авторизованный универсальный код ресурса (URI) перенаправления** на URL-адрес мобильной службы, к которому добавлен путь _/login/google_, а затем нажмите кнопку **Создать идентификатор клиента**.

	>[AZURE.NOTE] Для серверной мобильной службы .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем _signin-google_ мобильной службы в качестве службы .NET, например <code>https://todolist.azure-mobile.net/signin-google</code>. 

   	![][3]

5. В разделе **Идентификатор клиента для веб-приложений** запишите значения полей **Идентификатор клиента** и **Секрет клиента**. 

   	![][4]

    > [AZURE.IMPORTANT] Секрет клиента - это важные учетные данные безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.

Теперь вы готовы использовать имя входа Google для проверки подлинности в вашем приложении, предоставляя мобильным службам код клиента и секрет клиента приложения.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[API-интерфейсы Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Приступая к работе с проверкой подлинности]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Портал управления Azure]: https://manage.windowsazure.com/

<!--HONumber=47-->
