<properties pageTitle="Начало работы с проверкой подлинности (Windows Store) | Центр мобильных разработок" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Добавление проверки подлинности к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять аутентификацию учетных данных пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]
4. [Сохранение маркеров аутентификации в своем приложении]

Этот учебник создан на основе краткого руководства по мобильным службам. Необходимо прежде всего пройти учебник [Начало работы с мобильными службами].

##<a name="register"></a>Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li><p>В Xcode откройте проект клиентского приложения быстрого запуска.</li>
<li><p>Нажмите кнопку <strong>Выполнить</strong> для построения проекта и запуска приложения в эмуляторе iPhone; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (Не санкционировано).<p>

   	<p>Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности к приложению

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Сохранение маркеров аутентификации в своем приложении

[WACOM.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, [Авторизация пользователей мобильных служб с помощью этих служб][Проверка подлинности пользователей на основе скриптов], показано, как идентификатор пользователя, прошедшего проверку подлинности, который предоставлен мобильными службами, применяется для фильтрации данных, возвращаемых мобильными службами.

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройки мобильных служб]: #register
[Предоставление разрешений на таблицу пользователям, прошедшим проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Следующие шаги]:#next-steps
[Сохранение маркеров проверки подлинности в своем приложении]:#store-authentication

<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts

[Портал управления Azure]: https://manage.windowsazure.com/
[Концептуальный справочник по мобильным службам на платформе .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
[Регистрация пакета приложений Windows Store для проверки подлинности Майкрософт]: /ru-ru/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
