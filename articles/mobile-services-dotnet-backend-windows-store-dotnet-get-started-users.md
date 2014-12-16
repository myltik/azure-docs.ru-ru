<properties pageTitle="Начало работы с проверкой подлинности (Windows Store) | Центр мобильных разработок" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn Gailey" />

# Добавление проверки подлинности к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-users-legacy](../includes/mobile-services-selector-get-started-users-legacy.md)]

В этом разделе показано, как выполнять аутентификацию учетных данных пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]
4. [Хранение маркеров проверки подлинности в клиенте]

Этот учебник создан на основе краткого руководства по мобильным службам. Необходимо прежде всего пройти учебник [Начало работы с мобильными службами]. 

##<a name="register"></a>Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li>В Visual Studio откройте проект клиентского приложения и убедитесь, что в файле App.xaml.cs экземпляр **MobileServiceClient** настроен для использования облачного URL-адреса мобильной службы.</li> 
<li><p>Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).</p>
   
   	<p>Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности к приложению

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

[WACOM.NOTE]При регистрации данных пакета приложения Магазина Windows с помощью мобильных служб необходимо вызвать метод <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a>, указав значение <strong>true</strong> для параметра <em>useSingleSignOn</em>. Если этого не сделать, пользователям придется осуществлять вход в систему при каждом вызове метода входа в систему.

##<a name="tokens"></a>Хранение маркеров проверки подлинности в клиенте

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 


## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, [Авторизация пользователей мобильных служб с помощью этих служб][Проверка подлинности пользователей на основе скриптов], показано, как идентификатор пользователя, прошедшего проверку подлинности, который предоставлен мобильными службами, применяется для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью .NET см. в разделе [Справочник принципов использования мобильных служб .NET].

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Хранение маркеров проверки подлинности в клиенте]: #tokens
[Дальнейшие действия]:#next-steps


<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений для магазина Windows с использованием Live Connect]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
[Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript и HTML]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
[Регистрация пакета приложения для магазина Windows для проверки подлинности Microsoft]: /ru-ru/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
