<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/18/2014" ms.author="Glenn="" Gailey" />

# Приступая к работе с проверкой подлинности в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure из универсального приложения Windows. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1.  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб][Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2.  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности][Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3.  [Добавление проверки подлинности к приложению][Добавление проверки подлинности к приложению]
4.  [Хранение маркеров проверки подлинности в клиенте][Хранение маркеров проверки подлинности в клиенте]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

> [WACOM.NOTE]В этом учебнике показано, как проводить проверку подлинности пользователей в приложениях Магазина Windows и Магазина Windows Phone 8.1. Инструкции для приложения Windows Phone 8.0 или Windows Phone Silverlight 8.1 см. в этой версии раздела [Приступая к работе с проверкой подлинности в мобильных службах][Приступая к работе с проверкой подлинности в мобильных службах].

> В этом учебнике показан поток проверки подлинности, управляемый мобильными службами с помощью различных поставщиков удостоверений. Этот метод можно легко настроить, и он поддерживает нескольких поставщиков. Сведения о том, как вместо этого использовать Live Connect для управляемой клиентом проверки подлинности и предоставления единого входа в приложении Windows Phone, см. в разделе [Единый вход для приложений магазина Windows с использованием Live Connect][Единый вход для приложений магазина Windows с использованием Live Connect]. При использовании управляемой клиентом проверки подлинности приложение имеет доступ к дополнительным данным пользователя, сохраняемым поставщиком удостоверений. Эти же данные можно получить в мобильной службе, вызвав функцию **user.getIdentities()** в серверном скрипте. Дополнительные сведения см. в [этой записи блога][этой записи блога].

## <a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Необязательно) Выполните шаги, указанные в пункте [Регистрация пакета приложения магазина Windows для проверки подлинности Microsoft][Регистрация пакета приложения магазина Windows для проверки подлинности Microsoft].

    <div class="dev-callout">

    **Примечание.**
    Этот шаг необязателен, так как он применяется только к поставщику входа с учетной записью Майкрософт. При регистрации сведений пакета приложений магазина Windows с помощью мобильных служб клиент сможет повторно использовать учетные данные для входа в учетную запись Майкрософт для осуществления единого входа. Если этого не сделать, пользователям входа учетной записи Майкрософт придется осуществлять вход в систему при каждом вызове метода входа в систему. Выполните этот шаг, если будет использоваться поставщик удостоверений учетной записи Майкрософт.

    </div>

## <a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  В Visual Studio щелкните правой кнопкой мыши проект приложения TodoList для Магазина Windows и выберите пункт **Назначить запускаемым проектом**.

2.  В общем проекте откройте файл проекта App.xaml.cs, найдите определение объекта [MobileServiceClient][MobileServiceClient] и убедитесь в том, что он настроен на подключение к мобильной службе, работающей в Azure.

    <div class="dev-callout">

    **Примечание.**
    При использовании средств Visual Studio для подключения приложения к мобильной службе создаются два набора определений **MobileServiceClient** — по одному для каждой клиентской платформы. На этом этапе вы легко можете упростить созданный код, объединив определения **MobileServiceClient** в оболочке `#if...#endif` в одно определение без оболочки, которое может использоваться обеими версиями приложения.

    </div>

3.  Нажмите клавишу F5, чтобы запустить приложение Магазина Windows. Убедитесь в том, что после его запуска возникает необработанное исключение с кодом состояния 401 (неавторизованный).

    Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)]

## <a name="tokens"></a>Хранение маркера проверки подлинности в клиенте

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Авторизация пользователей мобильных служб на стороне службы], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью .NET см. в разделе [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET].

<!-- Anchors. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
  [Добавление проверки подлинности к приложению]: #add-authentication
  [Хранение маркеров проверки подлинности в клиенте]: #tokens
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
  [Приступая к работе с проверкой подлинности в мобильных службах]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users
  [Единый вход для приложений магазина Windows с использованием Live Connect]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [этой записи блога]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [Регистрация пакета приложения магазина Windows для проверки подлинности Microsoft]: /ru-ru/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [MobileServiceClient]: http://msdn.microsoft.com/ru-ru/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [mobile-services-windows-universal-dotnet-authenticate-app]: ../includes/mobile-services-windows-universal-dotnet-authenticate-app.md
  [mobile-services-windows-store-dotnet-authenticate-app-with-token]: ../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md
  [Авторизация пользователей мобильных служб на стороне службы]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
