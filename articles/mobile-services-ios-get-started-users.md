<properties linkid="develop-mobile-tutorials-get-started-with-users-ios" urlDisplayName="Get Started with Authentication (iOS)" pageTitle="Get started with authentication (iOS) | Mobile Dev Center" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services iOS" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/23/2014" ms.author="krisragh"></tags>

# Приступая к работе с проверкой подлинности в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-users][mobile-services-selector-get-started-users]]

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении для iOS. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1.  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб][Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2.  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности][Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3.  [Добавление проверки подлинности к приложению][Добавление проверки подлинности к приложению]
4.  [Сохранение маркеров аутентификации в своем приложении][Сохранение маркеров аутентификации в своем приложении]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

Для изучения этого учебника необходимы XCode версии 4.5 и iOS версии 5.0 или более поздних версий.

## <a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

## <a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][mobile-services-restrict-permissions-javascript-backend]]

1.  В Xcode откройте проект, созданный после завершения изучения учебника [Приступая к работе с мобильными службами][1].

2.  Нажмите кнопку **Запуск**, чтобы выполнить сборку проекта и запустить приложение в эмуляторе iPhone. Убедитесь, что после запуска приложения порождается необработанное исключение с кодом состояния «401 (Не авторизовано)».

    Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение

[WACOM.INCLUDE [mobile-services-ios-authenticate-app][mobile-services-ios-authenticate-app]]

## <a name="store-authentication"></a>Сохранение маркеров аутентификации в своем приложении

[WACOM.INCLUDE [mobile-services-ios-authenticate-app-with-token][mobile-services-ios-authenticate-app-with-token]]

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Авторизация пользователей мобильных служб на стороне службы], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами.

<!-- Anchors. Images.  URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
  [Добавление проверки подлинности к приложению]: #add-authentication
  [Сохранение маркеров аутентификации в своем приложении]: #store-authentication
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-ios
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /ru-ru/documentation/articles/mobile-services-ios-get-started
  [mobile-services-ios-authenticate-app]: ../includes/mobile-services-ios-authenticate-app.md
  [mobile-services-ios-authenticate-app-with-token]: ../includes/mobile-services-ios-authenticate-app-with-token.md
  [Авторизация пользователей мобильных служб на стороне службы]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-ios
