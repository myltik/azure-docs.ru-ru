<properties
	pageTitle="Добавление проверки подлинности в существующее приложение мобильных служб Azure (iOS) | Центр мобильных разработок"
	description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения Vista с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="2/16/2015"
	ms.author="krisragh"/>

# Добавление проверки подлинности в существующее приложение мобильных служб Azure

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом учебнике вы добавляете проверку подлинности в проект быстрого запуска с помощью поддерживаемого поставщика удостоверений. Этот учебник создан на основе [краткого учебника по мобильным службам], который необходимо пройти первым.

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

Находясь в Xcode, откройте проект. Нажмите кнопку **Выполнить**, чтобы запустить приложение. Убедитесь, что после запуска приложения возникает исключение с кодом состояния 401 (Недостаточно прав). Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Сохранение маркеров проверки подлинности в приложении

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы], вы воспользуетесь ИД пользователя для фильтрации данных.

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Дальнейшие действия]:#next-steps
[Сохранение маркеров аутентификации в своем приложении]:#store-authentication

<!-- URLs. -->
[Авторизация пользователей мобильных служб на стороне службы]: mobile-services-dotnet-backend-service-side-authorization.md
[краткого учебника по мобильным службам]: mobile-services-dotnet-backend-ios-get-started.md
[Приступая к работе с данными]: mobile-services-dotnet-backend-ios-get-started-data.md
[Приступая к работе с проверкой подлинности]: mobile-services-dotnet-backend-ios-get-started-users.md
[Приступая к работе с push-уведомлениями]: mobile-services-dotnet-backend-ios-get-started-push.md
[Авторизация пользователей с помощью скриптов]: mobile-services-dotnet-backend-ios-authorize-users-in-scripts.md

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник по принципам использования мобильных служб .NET]: /develop/mobile/how-to-guides/work-with-net-client-library
[Регистрация пакета приложения для Магазина Windows для проверки подлинности Майкрософт]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=49-->