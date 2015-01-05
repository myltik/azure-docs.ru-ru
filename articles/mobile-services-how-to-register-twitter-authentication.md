<properties urlDisplayName="Register for Twitter Authentication" pageTitle="Регистрация для аутентификации Twitter: мобильные службы" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Twitter login with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

#Регистрация приложений для входа в Twitter с помощью мобильных служб

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Twitter для проверки подлинности с помощью мобильных служб Azure.

>[WACOM.NOTE] В этом учебнике представлена информация о [мобильных службах Azure] -(http://azure.microsoft.com/ru-ru/services/mobile-services/)решении, которое поможет вам создавать масштабируемые мобильные приложения для любой платформы. С мобильными службами легко синхронизировать данные, аутентифицировать пользователей и отправлять push-уведомления. Эта страница поддерживает учебник <a href="http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-ios-get-started-users/">Приступая к работе с аутентификацией</a> , в котором показано, как вести журнал пользователей в приложении. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-ios-get-started/">Приступая к работе с мобильными службами</a>.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Twitter с проверенным электронным адресом. Чтобы создать учетную запись Twitter, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Перейдите к <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Разработчики Twitter</a> , войдите с помощью учетной записи Twitter и щелкните **Create a new application** (Создать приложение).

   	![][1]

2. Введите значения **Name** (Имя), **Description** (Описание) и **Website** (Веб-сайт) для своего приложения. Затем введите URL-адрес своей мобильной службы в поле **Callback URL** (URL-адрес обратного вызова), добавив к нему путь _/login/twitter_.

	>[WACOM.NOTE]Для серверной мобильной службы .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем _signin-twitter_ мобильной службы в качестве службы .NET, например <code>https://todolist.azure-mobile.net/signin-twitter</code>.

   	![][2]

3.  В нижней части страницы прочитайте и примите условия соглашения, введите текст проверки CAPTCHA и нажмите кнопку **Create your Twitter application** (Создать приложение Twitter). 

   	![][3]

   	После этого приложение будет зарегистрировано и появятся сведения о приложении.

6. Запомните значения полей **Consumer key** (Ключ клиента) и **Consumer secret** (Секрет клиента). 

   	![][4]

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет клиента - это важные учетные данные безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.</p>
    </div>

7. Откройте вкладку **Settings** (Параметры), перейдите вниз и установите флажок **Allow this application to be used to sign in with Twitter** (Разрешить этому приложению вход с помощью Twitter). Затем нажмите кнопку **Update this Twitter application's settings** (Обновить параметры приложения Twitter).

	![][5]

Теперь вы готовы использовать имя входа Twitter для проверки подлинности в вашем приложении, предоставляя мобильным службам ключ клиента и секрет клиента.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Разработчики Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/

[Портал управления Azure]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
