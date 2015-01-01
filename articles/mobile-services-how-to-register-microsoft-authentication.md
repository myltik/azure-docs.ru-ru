<properties pageTitle="Регистрация для аутентификации Microsoft: мобильные службы" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Регистрация приложений для входа с использованием учетной записи Майкрософт

В этом разделе показано, как зарегистрировать приложения для использования Live Connect в качестве поставщика проверки подлинности для мобильных служб Azure. 

>[WACOM.NOTE]Информацию о том, как настроить аутентификацию учетной записи Майкрософт для универсального приложения для Windows или обеспечить возможность единого входа в приложение для Магазина Windows, см. в статье [Регистрация пакета приложения для Магазина Windows для проверки подлинности Microsoft](/ru-ru/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication).

1. Перейдите к странице <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Мои приложения</a> в Центре разработчиков Live Connect и, если необходимо, выполните вход с использованием учетной записи Майкрософт. 

2. Щелкните **Создать приложение**, введите **Имя приложения** и нажмите кнопку **Принимаю**.

   	![][1] 

   	Эта процедура регистрирует приложение в Live Connect.

3. Щелкните **Параметры API**, введите значение https://<мобильная_служба>.azure-mobile.net/login/microsoftaccount в поле **URL-адрес перенаправления**, а затем нажмите кнопку **Сохранить**.

	>[WACOM.NOTE]Для серверной мобильной службы .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем _signin-microsoft_ мобильной службы как службы .NET, например <code>https://todolist.azure-mobile.net/signin-microsoft</code>.  

	![][3]

	Это включит аутентификацию учетных записей Майкрософт для вашего приложения.

	>[WACOM.NOTE]Возможно, для существующей регистрации приложения Live Connect понадобится сперва включить параметр **Расширенная безопасность перенаправления**.

4. Щелкните **Параметры приложений** и запишите значения, указанные в полях **Идентификатор клиента** и **Секрет клиента**. 

   	![][2]

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет клиента - это важные учетные данные безопасности. Не сообщайте никому этот секрет клиента и не распространяйте его вместе со своим приложением.</p>
    </div>

Теперь вы готовы использовать учетную запись Майкрософт для проверки подлинности в вашем приложении, предоставляя мобильным службам код клиента и секрет клиента приложения.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
[3]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png

<!-- URLs. -->

[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Портал управления Azure]: https://manage.windowsazure.com/

<!--HONumber=35_1-->
