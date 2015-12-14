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
	ms.date="11/15/2015"
	ms.author="glenga"/>

#Регистрация приложений для входа в Twitter с помощью мобильных служб

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Twitter для проверки подлинности с помощью мобильных служб Azure.

>[AZURE.NOTE]Это учебник о [мобильных службах Azure](http://azure.microsoft.com/services/mobile-services/) — решении, которое помогает создавать масштабируемые мобильные приложения для любых платформ. С мобильными службами легко синхронизировать данные, аутентифицировать пользователей и отправлять push-уведомления. Эта страница является частью учебника [Добавление проверки подлинности к приложению](mobile-services-ios-get-started-users.md), в котором показано, как настроить вход пользователей в приложение. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами](mobile-services-ios-get-started).

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Twitter с проверенным электронным адресом. Чтобы создать учетную запись Twitter, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Перейдите на веб-сайт [Twitter Developers](http://go.microsoft.com/fwlink/p/?LinkId=268300) (Разработчики Twitter), войдите по учетным данным учетной записи Twitter и щелкните **Создать приложение**.

2. Введите значения в поля **Name** (Имя), **Description** (Описание) и **Website** (Веб-сайт) для приложения, а затем в поле **Callback URL** (URL-адрес обратного вызова) — URL-адрес в одном из следующих форматов.

	+ **Серверная служба .NET**: `https://<mobile_service>.azure-mobile.net/signin-twitter`;
	+ **Серверная служба JavaScript**: `https://<mobile_service>.azure-mobile.net/login/twitter`.

	 >[AZURE.NOTE]Убедитесь, что для типа интерфейса мобильных служб используется правильный формат пути URL-адреса перенаправления. Если он неправильный, проверка подлинности не будет успешной. &nbsp;

   	![][2]

3.  В нижней части страницы прочитайте и примите условия соглашения, а затем нажмите кнопку **Create your Twitter application** (Создать приложение Twitter).

   	После этого приложение будет зарегистрировано и появятся сведения о приложении.

6. Щелкните вкладку **Keys and Access Tokens** (Ключи и маркеры доступа) на панели мониторинга приложения и запишите значения параметров **Consumer key** (Ключ клиента) и **Consumer secret** (Секрет клиента).

    > [AZURE.NOTE]Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе со своим приложением.

7. Щелкните вкладку **Settings** (Параметры), прокрутите страницу вниз и убедитесь, что установлен флажок **Allow this application to be used to sign in with Twitter** (Разрешить использовать это приложение для входа в Twitter). Затем нажмите кнопку **Update Settings** (Обновить параметры).

Теперь вы готовы использовать имя входа Twitter для проверки подлинности в вашем приложении, предоставляя мобильным службам ключ клиента и секрет клиента.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

<!---HONumber=AcomDC_1203_2015-->