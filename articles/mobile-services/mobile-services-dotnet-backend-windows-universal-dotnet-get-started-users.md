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
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="glenga"/>

# Добавление проверки подлинности к приложению мобильных служб 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## Обзор

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure из универсального приложения Windows. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами].

>[AZURE.NOTE]В этом учебнике показано, как проводить проверку подлинности пользователей в приложениях для Магазина Windows и Магазина Windows Phone 8.1. Инструкции для приложения Windows Phone 8.0 или Windows Phone Silverlight 8.1 см. в этой версии раздела [Приступая к работе с проверкой подлинности в мобильных службах](mobile-services-dotnet-backend-windows-phone-get-started-users.md).

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="5">
<li><p>В Visual Studio щелкните правой кнопкой мыши проект приложения TodoList для Магазина Windows и выберите пункт <strong>Назначить запускаемым проектом</strong>.</p></li>
<li><p>В общем проекте откройте файл проекта App.xaml.cs, найдите определение объекта <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> и убедитесь в том, что он настроен на подключение к мобильной службе, работающей в Azure.</p>
<p>Обратите внимание, что при использовании средств Visual Studio для подключения приложения к мобильной службе создаются два набора определений <strong>MobileServiceClient</strong>&#160;— по одному для каждой клиентской платформы. На этом этапе вы легко можете упростить созданный код, объединив в оболочке <code>#if...#endif</code> определения <strong>MobileServiceClient</strong> в одно определение без оболочки, которое может использоваться обеими версиями приложения. Это не потребуется, если вы загрузили учебное приложение с портала управления Azure.</p>
</li> 
<li><p>Нажмите клавишу F5, чтобы запустить приложение Магазина Windows. Убедитесь в том, что после его запуска возникает необработанное исключение с кодом состояния 401 (неавторизованный).</p>
   
   	<p>Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

>[AZURE.NOTE]При регистрации данных пакета приложения Магазина Windows с помощью мобильных служб необходимо вызвать метод <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a>, указав значение **Истина** для параметра *useSingleSignOn*. Если этого не сделать, пользователям придется осуществлять вход в систему при каждом вызове метода входа в систему.

##<a name="tokens"></a>Хранение маркеров проверки подлинности в клиенте

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 


## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Authorize users with scripts], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью .NET см. в разделе [Справочник принципов использования мобильных служб .NET].

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: mobile-services-windows-store-dotnet-single-sign-on.md
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=54--> 