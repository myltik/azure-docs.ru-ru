<properties 
	pageTitle="Начало работы с проверкой подлинности (Windows Store) | Центр мобильных разработок" 
	description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения магазина Windows с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="glenga"/>

# Добавление проверки подлинности в приложение мобильных служб 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure из универсального приложения Windows. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и настройка мобильных служб]
2. [Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]
3. [Добавление проверки подлинности в приложение]
4. [Хранение маркеров проверки подлинности в клиенте]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

>[AZURE.NOTE]В этом учебнике показано, как проводить проверку подлинности пользователей в приложениях для Магазина Windows и Магазина Windows Phone 8.1. Инструкции для приложения Windows Phone 8.0 или Windows Phone Silverlight 8.1 см. в этой версии раздела [Приступая к работе с проверкой подлинности в мобильных службах](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users).

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>В Visual Studio щелкните правой кнопкой мыши проект приложения TodoList для Магазина Windows и выберите пункт <strong>Назначить запускаемым проектом</strong>.</p></li>
<li><p>В общем проекте откройте файл проекта App.xaml.cs, найдите определение объекта <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> и убедитесь в том, что он настроен на подключение к мобильной службе, работающей в Azure.</p>
<p>Обратите внимание на то, что при использовании средств Visual Studio для подключения приложения к мобильной службе создаются два набора определений<strong>MobileServiceClient</strong> - по одному для каждой клиентской платформы. На этом этапе вы легко можете упростить созданный код, объединив <code>#if...#endif</code> определения <strong>MobileServiceClient</strong> в оболочке в одно определение без оболочки, которое может использоваться обеими версиями приложения. Это не потребуется, если вы загрузили учебное приложение с портала управления Azure.</p>
</li> 
<li><p>Нажмите клавишу F5, чтобы запустить приложение Магазина Windows. Убедитесь в том, что после его запуска возникает необработанное исключение с кодом состояния 401 (неавторизованный).</p>
   
   	<p>Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

[AZURE.NOTE]В случае регистрации данных пакета приложения Магазина Windows с помощью мобильных служб необходимо вызвать метод <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a>, указав значение <strong>true</strong> для параметра <em>useSingleSignOn</em>. Если этого не сделать, пользователям придется осуществлять вход в систему при каждом вызове метода входа в систему.

##<a name="tokens"></a>Хранение маркеров авторизации в клиенте

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 


## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, [Авторизация пользователей мобильных служб на стороне службы][Авторизация пользователей с помощью скриптов], показано, как идентификатор пользователя, прошедшего проверку подлинности, который предоставлен мобильными службами, применяется для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью .NET см. в разделе [Справочник принципов использования мобильных служб .NET].

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Хранение маркеров проверки подлинности в клиенте]: #tokens
[Дальнейшие действия]:#next-steps


<!-- URLs. -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений Магазина Windows с использованием Live Connect]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
[Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript и HTML]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник по принципам использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[Регистрация пакета приложения для магазина Windows для проверки подлинности Microsoft]: /ru-ru/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/


<!--HONumber=42-->
