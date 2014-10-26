<properties pageTitle="Register for single sign-on - Azure Mobile Services" metaKeywords="" description="Learn how to register for single sign-on authentication in your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store apps to use Windows Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Регистрация приложений для Магазина Windows для использования единого входа Windows Live Connect

В этом разделе показано, как зарегистрировать приложение в Магазине Windows, чтобы использовать для единого входа Live Connect в качестве поставщика проверки подлинности для мобильных служб Azure. Этот шаг также требуется при использовании push-уведомлений.

<div class="dev-callout"><b>Примечание.</b>
<p>Вам не нужно регистрировать приложение в Магазине Windows, чтобы иметь возможность использовать учетную запись Майкрософт для аутентификации, прежде чем опубликовать приложение. Если вашему приложению Магазина Windows единый вход или push-уведомления не требуются, можно просто зарегистрировать приложение в Live Connect, чтобы использовать для входа учетную запись Майкрософт.  Дополнительную информацию см. в разделе <a href="/ru-ru/develop/mobile/how-to-guides/register-for-microsoft-authentication">Регистрация приложений для Магазина Windows для входа с использованием учетной записи Майкрософт</a>.</p>
</div>

1.  Если вы еще не зарегистрировали свое приложение, перейдите к разделу [Отправить страницу приложения][Отправить страницу приложения] в центре разработчиков приложений для Магазина Windows, выполните вход с использованием учетной записи Майкрософт и затем щелкните **Имя приложения**.

    ![][]

2.  Введите имя приложения в поле **Имя приложения**, щелкните **Зарезервировать имя приложения** и затем щелкните **Сохранить**.

    ![][1]

    При этом создается новая регистрация в Магазине Windows для вашего приложения.

3.  Откройте проект, созданный после завершения обучения, в Visual Studio 2012 Express для Windows 8 [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

4.  В обозревателе решений щелкните правой кнопкой мыши проект, щелкните **Магазин** и затем щелкните **Связать приложение с магазином...**.

    ![][2]

    Откроется мастер **Связь приложений с Магазином Windows**.

5.  В окне мастера щелкните **Вход** и затем войдите в систему с учетной записью Майкрософт.

6.  Выберите приложение, которое зарегистрировано на шаге 2, щелкните **Далее**и затем щелкните **Связать**.

    ![][3]

    Это добавляет необходимые регистрационные данные Магазина Windows в манифест приложения.

7.  Перейдите на страницу [Мои приложения][Мои приложения] центра разработчиков Live Connect и щелкните свое приложение в списке **Мои приложения**.

    ![][4]

8.  Щелкните **Изменить параметры**, затем **Параметры API** и запишите значение **Секрет клиента**.

    ![][5]

    <div class="dev-callout"><b>Примечание о безопасности</b>
<p>Секрет клиента &mdash; это важные учетные данные безопасности. Не сообщайте никому этот секрет клиента и не распространяйте его вместе со своим приложением.</p>
</div>

9.  В поле **Домен перенаправления** введите URL-адрес мобильной службы, указанный на шаге 8, и щелкните **Сохранить**.

Теперь вы готовы интегрировать проверку подлинности с помощью Live Connect в свое веб-приложение. Мобильные службы предоставляют следующие два метода проверки подлинности пользователей с помощью Live Connect.

-   Единый вход для приложений для Магазина Windows. В этом методе пользователям нужно только один раз авторизовать проверку подлинности в приложении с помощью Live Connect, а затем учетные данные управляются операционной системой Windows на основе пользовательских установок. Дополнительные сведения см. в разделе [Единый вход для приложений Магазина Windows с использованием Live Connect][Единый вход для приложений Магазина Windows с использованием Live Connect].

-   Обычная проверка подлинности. Этот метод, поддерживающий множество поставщиков услуг проверки подлинности, требует от пользователей осуществлять вход каждый раз при запуске приложения. Дополнительные сведения см. в разделе [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].



  [Регистрация приложений для Магазина Windows для входа с использованием учетной записи Майкрософт]: /ru-ru/develop/mobile/how-to-guides/register-for-microsoft-authentication
  [Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  []: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
  [2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
  [3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png
  [Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [4]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
  [5]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png
  [Единый вход для приложений Магазина Windows с использованием Live Connect]: /ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
