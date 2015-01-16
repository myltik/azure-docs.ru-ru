<properties pageTitle="Начало работы по проверке подлинности (Android) | Центр мобильных разработок" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения магазина Windows с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="09/27/2014" ms.author="mahender" />

# Добавление проверки подлинности к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять аутентификацию учетных данных пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]
4. [Хранение маркеров проверки подлинности в клиенте]
5. [Обновление маркеров с истекшим сроком действия]


Этот учебник создан на основе краткого руководства по мобильным службам. Необходимо прежде всего пройти учебник [Начало работы с мобильными службами]. 

##<a name="register"></a>Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

3. В Eclipse откройте проект, созданный при прохождении учебника [Начало работы с мобильными службами]. 

4. Откройте меню **Выполнить** и щелкните **Выполнить**, чтобы запустить приложение; проверьте, происходит ли активация необработанного исключения с кодом состояния 401 (Не авторизовано) после запуска приложения. 

	 Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности к приложению

[WACOM.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Кэширование токенов проверки подлинности на клиенте

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>Обновление кэша токенов

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)] 

##<a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, [Авторизация пользователей мобильных служб с помощью этих служб][Проверка подлинности пользователей на основе скриптов], показано, как идентификатор пользователя, прошедшего проверку подлинности, который предоставлен мобильными службами, применяется для фильтрации данных, возвращаемых мобильными службами. 


<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройки мобильных служб]: #register
[Предоставление разрешений на таблицу пользователям, прошедшим проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Сохранение токенов проверки подлинности на клиенте]: #cache-tokens
[Обновление токенов с истекшим сроком действия]: #refresh-tokens
[Следующие шаги]:#next-steps

<!-- URLs. -->
[Приступая к работе с мобильными службами:]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started/
[Приступая к работе с данными:]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started/
[Начало работы с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[Начало работы с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/
[Авторизация пользователей с помощью скриптов:]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-authorize-users-in-scripts

[Портал управления Azure]: https://manage.windowsazure.com/
[Концептуальный справочник по мобильным службам на платформе .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
[Регистрация пакета приложений Windows Store для проверки подлинности Майкрософт]: /ru-ru/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
