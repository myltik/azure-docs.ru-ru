<properties pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="mahender"></tags>

# Приступая к работе с проверкой подлинности в мобильных службах

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Магазин Windows C#">Магазин Windows C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS" >iOS</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android" class="current">Android</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/" title="Серверная часть .NET" class="current">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-android-get-started-users/"  title="Серверная часть JavaScript">Серверная часть JavaScript</a></div>

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1.  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб][Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2.  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности][Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3.  [Добавление проверки подлинности к приложению][Добавление проверки подлинности к приложению]
4.  [Сохранение токенов проверки подлинности в клиенте][Сохранение токенов проверки подлинности в клиенте]
5.  [Обновление токены с истекшим сроком действия][Обновление токены с истекшим сроком действия]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

## <a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][mobile-services-dotnet-backend-aad-server-extension]]

## <a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

1.  В Eclipse откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

2.  В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (Не санкционировано).

    Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение

[WACOM.INCLUDE [mobile-services-android-authenticate-app][mobile-services-android-authenticate-app]]

## <a name="cache-tokens"></a>Кэширование токенов проверки подлинности в клиенте

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token][mobile-services-android-authenticate-app-with-token]]

## <a name="refresh-tokens"></a>Обновление кэша токена

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token][mobile-services-android-authenticate-app-refresh-token]]

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Авторизация пользователей мобильных служб на стороне службы], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами.

<!-- Anchors. -->
<!-- URLs. -->

  [Магазин Windows C#]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Магазин Windows C#"
  [Магазин Windows JavaScript]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Магазин Windows JavaScript"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS"
  [Android]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-users "Android"
  [Xamarin.iOS]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users "Xamarin.Android"
  [Серверная часть .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/ "Серверная часть .NET"
  [Серверная часть JavaScript]: /ru-ru/documentation/articles/mobile-services-android-get-started-users/ "Серверная часть JavaScript"
  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
  [Добавление проверки подлинности к приложению]: #add-authentication
  [Сохранение токенов проверки подлинности в клиенте]: #cache-tokens
  [Обновление токены с истекшим сроком действия]: #refresh-tokens
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [mobile-services-android-authenticate-app]: ../includes/mobile-services-android-authenticate-app.md
  [mobile-services-android-authenticate-app-with-token]: ../includes/mobile-services-android-authenticate-app-with-token.md
  [mobile-services-android-authenticate-app-refresh-token]: ../includes/mobile-services-android-authenticate-app-refresh-token.md
  [Авторизация пользователей мобильных служб на стороне службы]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-authorize-users-in-scripts
