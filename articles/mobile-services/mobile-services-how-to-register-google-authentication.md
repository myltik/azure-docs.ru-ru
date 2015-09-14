<properties 
	pageTitle="Регистрация для проверки подлинности Google | Microsoft Azure"
	description="Узнайте, как зарегистрировать приложения, чтобы использовать Google для проверки подлинности с помощью мобильных служб Azure."
	services="mobile-services"
	documentationCenter="android"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/27/2015"
	ms.author="glenga"/>

# Регистрация приложений для входа в мобильные службы с помощью Google

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Google для проверки подлинности с помощью мобильных служб Azure.

>[AZURE.NOTE]Это учебник о [мобильных службах Azure](http://azure.microsoft.com/services/mobile-services/) — решении, которое помогает создавать масштабируемые мобильные приложения для любых платформ. С мобильными службами легко синхронизировать данные, аутентифицировать пользователей и отправлять push-уведомления. Эта страница дополняет статью [Добавление проверки подлинности в существующее приложение мобильных служб Azure](mobile-services-ios-get-started-users.md), в которой рассказано, как настроить пользовательский вход в приложение. <br/>Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь со статье [Приступая к работе с мобильными службами](mobile-services-ios-get-started.md).

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

3. Перейдите на веб-сайт [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303), войдите с учетными данными Google, выберите **Create Project** (Создать проект), укажите имя проекта в поле **Project name** (Имя проекта), а затем нажмите кнопку **Create** (Создать).

4. На панели навигации слева щелкните **API & Auth** (API и авторизация Auth), а затем в разделе **Social APIs** (API социальных сетей) щелкните **Google+ API** (API Google+) > **Enable API** (Включить API).

5. Щелкните **API & Auth** (API и авторизация) > **Credentials** (Учетные данные) > **OAuth consent screen** (Экран согласия OAuth), а затем выберите **Email address** (Электронный адрес), заполните поле **Product Name** (Имя продукта) и нажмите кнопку **Save** (Сохранить).

6. На вкладке **Credentials** (Учетные данные) щелкните **Add credentials** (Добавить учетные данные) > **OAuth 2.0 client ID** (Идентификатор клиента OAuth 2.0), а затем выберите **Web application** (Веб-приложение).

7. Введите URL-адрес мобильной службы в поле **Authorized JavaScript Origins** (Авторизованные источники JavaScript), замените созданный URL-адрес в поле **Authorized Redirect URI** (Авторизованный универсальный код ресурса (URI) перенаправления) на URL-адрес мобильной службы, к которому добавлен путь `/login/google`, а затем нажмите кнопку **Create client ID** (Создать идентификатор клиента).

	>[AZURE.NOTE]Для серверной мобильной службы .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем _signin-google_ мобильной службы в качестве службы .NET, например `https://todolist.azure-mobile.net/signin-google`.
	
8. На следующем экране запишите идентификатор и секрет клиента.

    > [AZURE.IMPORTANT]Секрет клиента — это важные учетные данные безопасности. Не сообщайте этот секрет никому и не раскрывайте его в клиентском приложении.

Теперь вы можете настроить в своей мобильной службе использование проверки подлинности Google для входа в приложение.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=September15_HO1-->