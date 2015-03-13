<properties 
	pageTitle="Регистрация для использования единого входа: мобильные службы Azure" 
	description="Узнайте, как зарегистрироваться, чтобы использовать аутентификацию с помощью единого входа в приложении мобильных служб Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Регистрация приложений для Магазина Windows для использования единого входа Windows Live Connect

В этом разделе показано, как зарегистрировать приложение в Магазине Windows, чтобы использовать для единого входа Live Connect в качестве поставщика проверки подлинности для мобильных служб Azure. Этот шаг также требуется при использовании push-уведомлений.

> [AZURE.IMPORTANT] Вам не нужно регистрировать приложение в Магазине Windows, чтобы иметь возможность использовать учетную запись Майкрософт для аутентификации, прежде чем опубликовать приложение. Если вашему приложению Магазина Windows единый вход или push-уведомления не требуются, можно просто зарегистрировать приложение в Live Connect, чтобы использовать для входа учетную запись Майкрософт.  Дополнительную информацию см. в разделе [Регистрация приложений Магазина Windows для входа с использованием учетной записи Майкрософт](/ru-ru/develop/mobile/how-to-guides/register-for-microsoft-authentication).

1. Если вы еще не зарегистрировали свое приложение, перейдите к разделу [Отправить страницу приложения] в Центре разработки для приложений Магазина Windows, выполните вход с использованием вашей учетной записи Майкрософт и затем щелкните **Имя приложения**.

   	![][0]

2. Введите имя приложения в поле **Имя приложения**, щелкните **Зарезервировать имя приложения** и затем щелкните **Сохранить**.

   	![][1]

   	При этом создается новая регистрация в Магазине Windows для вашего приложения.

3. Откройте проект, созданный после завершения обучения, в Visual Studio 2012 Express для Windows 8 [Приступая к работе с мобильными службами].

4. В обозревателе решений щелкните правой кнопкой мыши проект, выберите пункт **Магазин**, а затем щелкните **Связать приложение с магазином...** 

  	![][2]

   	Откроется мастер **Связь приложений с Магазином Windows**.

5. В окне мастера щелкните **Вход** и затем войдите в систему с учетной записью Майкрософт.

6. Выберите приложение, которое зарегистрировано на шаге 2, щелкните **Далее** и затем щелкните **Связать**.

   	![][3]

   	При этом в манифест приложения будут добавлены необходимые регистрационные данные Магазина Windows.    

9. Перейдите на страницу [Мои приложения] центра разработчиков Live Connect и щелкните свое приложение в списке **Мои приложения**.

   	![][6] 

10. Щелкните элемент **Изменить параметры**, затем **Параметры API** и запишите значение **Секрет клиента**. 

   	![][7]

    > [AZURE.NOTE] Секрет клиента - это важные учетные данные безопасности. Не сообщайте никому этот секрет клиента и не распространяйте его вместе со своим приложением.

11. В поле **Домен перенаправления** введите URL-адрес мобильной службы, указанный на шаге 8, и щелкните **Сохранить**.

Теперь вы готовы интегрировать проверку подлинности с помощью Live Connect в свое веб-приложение. Мобильные службы предоставляют следующие два метода проверки подлинности пользователей с помощью Live Connect.

   - Единый вход для приложений для Магазина Windows. В этом методе пользователям нужно только один раз авторизовать проверку подлинности в приложении с помощью Live Connect, а затем учетные данные управляются операционной системой Windows на основе пользовательских установок. Дополнительные сведения см. в разделе [Единый вход для приложений Магазина Windows с использованием Live Connect].

   - Обычная проверка подлинности. Этот метод, поддерживающий множество поставщиков услуг проверки подлинности, требует от пользователей осуществлять вход каждый раз при запуске приложения. Дополнительные сведения см. в разделе [Приступая к работе с аутентификацией].

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png


[6]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png



<!-- URLs. -->
[Единый вход для приложений Магазина Windows с использованием Live Connect]: /ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js/
[Портал управления Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
