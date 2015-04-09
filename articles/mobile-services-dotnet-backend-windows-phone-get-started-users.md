<properties 
	pageTitle="Знакомство с механизмом проверки подлинности (Windows Phone) | Центр разработчиков для мобильных устройств" 
	description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения Windows Phone с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="glenga"/>

# Добавление проверки подлинности в приложение мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure из приложения Windows Phone. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

>[AZURE.NOTE] Этот учебник поддерживает только приложения Silverlight Windows Phone 8.0 и Windows Phone 8.1. При наличии Магазина Windows Phone 8.1 или универсального приложения Windows следуйте [версии этого учебника для универсального приложения Windows](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md).

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и настройка мобильных служб]
2. [Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]
3. [Добавление проверки подлинности в приложение]
4. [Хранение маркеров проверки подлинности в клиенте]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 


##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

## <a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li>В Visual Studio откройте проект клиентского приложения и убедитесь, что в файле App.xaml.cs экземпляр <strong>MobileServiceClient</strong> настроен для использования облачного URL-адреса мобильной службы.</li> 
<li><p>Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).</p>
   
   	<p>Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>Хранение маркеров авторизации в клиенте

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, [Авторизация пользователей мобильных служб на стороне службы][Авторизация пользователей с помощью скриптов], показано, как идентификатор пользователя, прошедшего проверку подлинности, который предоставлен мобильными службами, применяется для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью .NET см. в разделе [Справочник принципов использования мобильных служб .NET].

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Хранение маркеров проверки подлинности в клиенте]: #tokens
[Дальнейшие действия]: #next-steps


<!-- URLs. -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений Windows Phone с использованием Live Connect]: mobile-services-windows-phone-single-sign-on.md
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-windows-phone-get-started.md
[Приступая к работе с данными]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[Приступая к работе с проверкой подлинности]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Приступая к работе с push-уведомлениями]: mobile-services-dotnet-backend-windows-phone-get-started-push.md
[Авторизация пользователей с помощью скриптов]: mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts.md
[JavaScript и HTML]: mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб .NET]: /develop/mobile/how-to-guides/work-with-net-client-library
[Регистрация пакета приложения для Магазина Windows для проверки подлинности Майкрософт]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=49-->