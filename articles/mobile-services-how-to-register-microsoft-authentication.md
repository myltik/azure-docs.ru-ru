<properties pageTitle="Register for Microsoft authentication - Mobile Services" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Регистрация приложений для входа с использованием учетной записи Майкрософт

В этом разделе показано, как зарегистрировать приложения для использования Live Connect в качестве поставщика проверки подлинности для мобильных служб Azure.

> [WACOM.NOTE]Чтобы настроить учетную запись Майкрософт для приложений для Windows 8.1 или Windows Phone 8.1, см. раздел [Регистрация пакета приложения для Магазина Windows для аутентификации Майкрософт][Регистрация пакета приложения для Магазина Windows для аутентификации Майкрософт].

1.  Перейдите к странице [Мои приложения][Мои приложения] в Центре разработки Live Connect и, если необходимо, выполните вход с использованием учетной записи Майкрософт.

2.  Щелкните элемент **Создать приложение**, введите **Имя приложения** и нажмите кнопку **Я принимаю**.

    ![][]

    Эта процедура регистрирует приложение в Live Connect.

3.  Щелкните **Параметры API**, введите значение `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` в поле **URL-адрес перенаправления**, затем нажмите кнопку **Сохранить**.

    > [WACOM.NOTE]Для серверной мобильной службы .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем *signin-microsoft* мобильной службы как службы .NET, например <code><a href="https://todolist.azure-mobile.net/signin-microsoft" class="uri">https://todolist.azure-mobile.net/signin-microsoft</a></code>.

    ![][1]

    Это включит аутентификацию учетных записей Майкрософт для вашего приложения.

    > [WACOM.NOTE]Для существующей регистрации приложения Live Connect может потребоваться сперва включить параметр **Enhanced redirection security** (Расширенная безопасность перенаправления).

4.  Щелкните **Параметры приложений** и запишите значения **Идентификатор клиента** и **Секрет клиента**.

    ![][2]

    <div class="dev-callout"><b>Примечание о безопасности</b>
<p>Секрет клиента &mdash; это важные учетные данные безопасности. Не сообщайте никому этот секрет клиента и не распространяйте его вместе со своим приложением.</p>
</div>

Теперь вы готовы использовать учетную запись Майкрософт для проверки подлинности в вашем приложении, предоставляя мобильным службам код клиента и секрет клиента приложения.



  [Регистрация пакета приложения для Магазина Windows для аутентификации Майкрософт]: /ru-ru/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
  [Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  []: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png
  [2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
