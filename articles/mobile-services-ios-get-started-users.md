<properties
	pageTitle="Добавление проверки подлинности в существующее приложение мобильных служб Azure (iOS) | Центр мобильных разработок"
	description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения iOS с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт."
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

В этом учебнике вы добавляете проверку подлинности в проект быстрого запуска с помощью поддерживаемого поставщика удостоверений.

Этот учебник создан на основе [краткого учебника по мобильным службам], который необходимо пройти первым.

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

Находясь в Xcode, откройте проект. Нажмите кнопку **Выполнить**, чтобы запустить приложение. Убедитесь, что после запуска приложения возникает исключение с кодом состояния 401 (недостаточно прав). Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Сохранение маркеров проверки подлинности в приложении

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы], вы воспользуетесь ИД пользователя для фильтрации данных.

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Дальнейшие действия]:#next-steps
[Сохранение маркеров аутентификации в своем приложении]:#store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Авторизация пользователей мобильных служб на стороне службы]: mobile-services-javascript-backend-service-side-authorization.md
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений Магазина Windows с использованием Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Краткий учебник по мобильным службам]: /develop/mobile/tutorials/get-started-ios
[Приступая к работе с данными]: /develop/mobile/tutorials/get-started-with-data-ios
[Приступая к работе с проверкой подлинности]: /develop/mobile/tutorials/get-started-with-users-ios
[Приступая к работе с push-уведомлениями]: /develop/mobile/tutorials/get-started-with-push-ios
[Авторизация пользователей с помощью скриптов]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[Портал управления Azure]: https://manage.windowsazure.com/

<!--HONumber=49-->