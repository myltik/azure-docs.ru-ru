<properties 
	pageTitle="Регистрация для проверки подлинности Twitter | Microsoft Azure" 
	description="Узнайте, как использовать аутентификацию Twitter с приложением мобильных служб Azure." 
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
	ms.date="08/08/2015" 
	ms.author="glenga"/>

#Регистрация приложений для входа в Twitter с помощью мобильных служб

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Twitter для проверки подлинности с помощью мобильных служб Azure.

>[AZURE.NOTE]Это учебник о [мобильных службах Azure](http://azure.microsoft.com/services/mobile-services/) — решении, которое помогает создавать масштабируемые мобильные приложения для любых платформ. С мобильными службами легко синхронизировать данные, аутентифицировать пользователей и отправлять push-уведомления. Эта страница дополняет учебник <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Приступая к работе с проверкой подлинности</a>, в котором показано, как настроить вход пользователей в приложение. Если это ваш первый опыт работы с мобильными службами, сначала выполните задания учебника <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Приступая к работе с мобильными службами</a>.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Twitter с проверенным электронным адресом. Чтобы создать учетную запись Twitter, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Перейдите на веб-сайт <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">разработчиков Twitter</a> и войдите с помощью учетной записи Twitter. Нажмите кнопку **Создать приложение**.

   	![][1]

2. Введите значения **Name** (Имя), **Description** (Описание) и **Website** (Веб-сайт) для своего приложения, затем введите URL-адрес мобильной службы, к которому добавлен путь _/login/twitter_ в поле **Callback URL** (URL-адрес обратного вызова).

	>[AZURE.NOTE]Для серверной мобильной службы на основе .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы с суффиксом _signin-twitter_. В нашем примере у мобильной службы будет URL-адрес обратного вызова ```https://todolist.azure-mobile.net/signin-twitter```.

   	![][2]

3.  В нижней части страницы прочитайте и примите условия соглашения и нажмите кнопку **Создать приложение для Twitter**.

   	После этого приложение будет зарегистрировано и появятся сведения о приложении.

6. Щелкните вкладку **Ключи и маркеры доступа** на панели мониторинга приложения и запишите значения параметров **Ключ клиента** и **Секрет клиента**.

    > [AZURE.NOTE]Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе со своим приложением.

7. Откройте вкладку **Параметры**, прокрутите страницу вниз и убедитесь, что флажок **Разрешить этому приложению вход с помощью Twitter**. Затем нажмите кнопку **Обновить параметры**.

Теперь вы готовы использовать имя входа Twitter для проверки подлинности в вашем приложении, предоставляя мобильным службам ключ клиента и секрет клиента.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=Oct15_HO3-->