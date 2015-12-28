<properties
	pageTitle="Добавление проверки подлинности на Android с помощью мобильных приложений | Служба приложений Azure"
	description="Узнайте, как использовать мобильные приложения в службе приложений Azure для аутентификации пользователей приложения Android с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт."
	services="app-service\mobile"
	documentationCenter="android"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/06/2015"
	ms.author="glenga"/>

# Добавление проверки подлинности в приложение Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Сводка

В этом учебнике вы добавляете проверку подлинности в учебный проект ToDoList для Android с помощью поддерживаемого поставщика удостоверений. Этот учебник создан на основе учебника [Начало работы с мобильными службами], который необходимо изучить в первую очередь.

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка службы приложений

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ В Android Studio откройте проект, созданный при прохождении учебника [Начало работы с мобильными приложениями], а затем в меню **Запуск** выберите пункт **Запуск приложения** и убедитесь, что при запуске приложения выдается необработанное исключение с кодом состояния 401 (Не авторизован).

	 Это происходит, потому что приложение пытается получить доступ к серверной части как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из серверной части мобильного приложения.

## Добавление проверки подлинности в приложение

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Кэширование маркеров проверки подлинности на клиенте

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##Дальнейшие действия

Вы прошли этот учебник по обычной проверке подлинности и теперь можете перейти к одному из следующих учебников:

+ [Добавление push-уведомлений в приложение Android](app-service-mobile-android-get-started-push.md) Узнайте, как добавить поддержку push-уведомлений в приложение и настроить в серверной части мобильного приложения использование центров уведомлений Azure для отправки push-уведомлений.

+ [Включение автономной синхронизации для приложения Android](app-service-mobile-android-get-started-offline-data.md) Узнайте, как добавить поддержку автономной работы приложения с помощью серверной части мобильного приложения. Автономная синхронизация позволяет конечным пользователям взаимодействовать с мобильным приложением (просматривать, добавлять или изменять данные) даже при отсутствии подключения к сети.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Начало работы с мобильными приложениями]: app-service-mobile-android-get-started.md
[Начало работы с мобильными службами]: app-service-mobile-android-get-started.md

<!----HONumber=AcomDC_1217_2015-->