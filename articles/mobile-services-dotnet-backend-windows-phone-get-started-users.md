<properties pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="authentication, Facebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn Gailey"></tags>

# Приступая к работе с проверкой подлинности в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-users][mobile-services-selector-get-started-users]]

В этом разделе показано, как выполнять аутентификацию учетных данных пользователей в мобильных службах Azure из приложения Windows Phone. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1.  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб][Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2.  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности][Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3.  [Добавление проверки подлинности к приложению][Добавление проверки подлинности к приложению]
4.  [Хранение маркеров аутентификации в клиенте][Хранение маркеров аутентификации в клиенте]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

## <a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][mobile-services-dotnet-backend-aad-server-extension]]

## <a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

1.  В Visual Studio откройте проект клиентского приложения и убедитесь, что в файле App.xaml.cs экземпляр \*\*MobileServiceClient\*\* настроен для использования облачного URL-адреса мобильной службы.
2.  Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).

    Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app][mobile-services-windows-phone-authenticate-app]]

## <a name="tokens"></a>Хранение маркеров авторизации в клиенте

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token][mobile-services-windows-phone-authenticate-app-with-token]]

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Авторизация пользователей мобильных служб на стороне службы], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью .NET см. в разделе [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET].



  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
  [Добавление проверки подлинности к приложению]: #add-authentication
  [Хранение маркеров аутентификации в клиенте]: #tokens
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [mobile-services-windows-phone-authenticate-app]: ../includes/mobile-services-windows-phone-authenticate-app.md
  [mobile-services-windows-phone-authenticate-app-with-token]: ../includes/mobile-services-windows-phone-authenticate-app-with-token.md
  [Авторизация пользователей мобильных служб на стороне службы]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
