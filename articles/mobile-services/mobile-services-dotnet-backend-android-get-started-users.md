<properties 
	pageTitle="Начало работы по проверке подлинности (Android) | Центр мобильных разработок" 
	description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения магазина Windows с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="ricksal"/>

# Добавление проверки подлинности к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:



Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами].

## <a name="register"></a>Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

## <a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

3. Откройте проект, созданный при выполнении заданий учебника [Приступая к работе с мобильными службами]. 

4. В меню **Запуск** щелкните **Запуск приложения**; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 ("Не санкционировано").

	 Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение

[AZURE.INCLUDE [mobile-services-android-authenticate-app](../../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Кэширование маркеров проверки подлинности на клиенте

[AZURE.INCLUDE [mobile-services-android-authenticate-app-with-token](../../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>Обновление кэша маркеров

[AZURE.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../../includes/mobile-services-android-authenticate-app-refresh-token.md)] 

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Authorize users with scripts], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами.


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps

<!-- URLs. -->
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-android-get-started.md
[Get started with data]: mobile-services-dotnet-backend-android-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-android-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-android-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-android-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=54--> 