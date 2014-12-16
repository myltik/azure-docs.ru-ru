<properties urlDisplayName="Get Started with Authentication (iOS)" pageTitle="Приступая к работе с проверкой подлинности (iOS) | Центр мобильных разработок" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services iOS" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Добавление проверки подлинности к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей мобильных служб Azure в приложении iOS.  В этом учебнике вы научитесь добавлять проверку подлинности в проект быстрого запуска с помощью поставщика удостоверений, поддерживаемого мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.  

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]
4. [Сохранение маркеров аутентификации в своем приложении]

Этот учебник создан на основе краткого руководства по мобильным службам. Необходимо прежде всего пройти учебник [Начало работы с мобильными службами].

Для изучения этого учебника необходимы XCode версии 4.5 и iOS версии 5.0 или более поздних версий.

##<a name="register"></a>Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a> Предоставление разрешений только пользователям, прошедшим проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>В Xcode откройте проект, созданный после завершения учебника <a href="/ru-ru/documentation/articles/mobile-services-ios-get-started">Приступая к работе с мобильными службами</a>.</p></li>
<li><p>Нажмите кнопку <strong>Выполнить</strong> для построения проекта и запуска приложения в эмуляторе iPhone; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (Не санкционировано).<p>

   	<p>Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности к приложению

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Сохранение маркеров аутентификации в своем приложении

[WACOM.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, [Авторизация пользователей мобильных служб с помощью этих служб][Проверка подлинности пользователей на основе скриптов], показано, как идентификатор пользователя, прошедшего проверку подлинности, который предоставлен мобильными службами, применяется для фильтрации данных, возвращаемых мобильными службами.

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Дальнейшие действия]:#next-steps
[Сохранение маркеров аутентификации в своем приложении]:#store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений для магазина Windows с использованием Live Connect]: /ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-ios
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-ios

[Портал управления Azure]: https://manage.windowsazure.com/
