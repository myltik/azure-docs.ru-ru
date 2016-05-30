<properties
	pageTitle="Добавление проверки подлинности в универсальное приложение для Windows 8.1 | Microsoft Azure"
	description="Информация об использовании мобильных служб для проверки подлинности пользователей универсального приложения для Windows 8.1 с помощью разных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/06/2016"
	ms.author="glenga"/>

# Добавление проверки подлинности к приложению мобильных служб
[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Аналогичные сведения для мобильных приложений см. в статье [Добавление проверки подлинности в приложение Windows](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md).

## Обзор

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure из универсального приложения Windows. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

Этот учебник создан на основе краткого руководства по мобильным службам. Предварительно вам необходимо пройти учебник [Приступая к работе с мобильными службами] или [Добавление мобильных служб в существующее приложение](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md).

>[AZURE.NOTE]В этом учебнике показано, как проводить управляемую сервером проверку подлинности пользователей в приложениях для Магазина Windows и Магазина Windows Phone 8.1. Сведения о проверке подлинности, управляемой клиентом, см. в статье [Вход в мобильные службы Azure с помощью пакетов SDK Google, Facebook и корпорации Майкрософт](https://azure.microsoft.com/blog/2014/10/27/logging-in-with-google-microsoft-and-facebook-sdks-to-azure-mobile-services/).

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

& nbsp; & nbsp; 6. В Visual Studio щелкните правой кнопкой мыши проект приложения TodoList для Магазина Windows и выберите пункт **Назначить запускаемым проектом**.

&nbsp;&nbsp;7. В общем проекте откройте файл проекта App.xaml.cs, найдите определение объекта [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) и убедитесь в том, что он настроен на подключение к мобильной службе, работающей в Azure.

>[AZURE.NOTE]При использовании средств Visual Studio для подключения приложения к мобильной службе создаются два набора определений **MobileServiceClient** — по одному для каждой клиентской платформы. На этом этапе вы легко можете упростить созданный код, объединив определения **MobileServiceClient** в оболочке `#if...#endif` в одно определение без оболочки, которое может использоваться обеими версиями приложения. Этого не потребуется, если вы загрузили приложение быстрого запуска с [классического портала Azure].

&nbsp;&nbsp;8. Нажмите клавишу F5, чтобы запустить приложение Магазина Windows. Убедитесь в том, что после его запуска возникает необработанное исключение с кодом состояния 401 (неавторизованный).

&nbsp;&nbsp;Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения такой проверки.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]

>[AZURE.NOTE]При регистрации данных пакета приложения Магазина Windows с помощью мобильных служб необходимо вызвать метод <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a>, указав значение **Истина** для параметра *useSingleSignOn*. Если этого не сделать, пользователям придется осуществлять вход в систему при каждом вызове метода входа в систему.

##<a name="tokens"></a>Хранение маркеров проверки подлинности в клиенте

В предыдущем примере был показан стандартный вход, который требует, чтобы клиент подключался как к поставщику удостоверений, так и к мобильной службе каждый раз, когда приложение запускается. Мало того, что этот метод неэффективен, вы можете столкнуться с проблемами, связанными с использованием приложения, если большое количество клиентов попытаются запустить приложение одновременно. Несколько лучшим подходом является проверка подлинности удостоверений с кэшированием маркера, возвращенного мобильной службой, которая будет использоваться до входа на основе поставщика.

>[AZURE.NOTE]Можно кэшировать маркер, выданный мобильной службой, независимо от того, используете ли вы аутентификацию, управляемую клиентом или сервером. Этот учебник использует управляемую сервером проверку подлинности.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]


## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Authorize users with scripts], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами.

##Дополнительные материалы

+ [Расширенные возможности пользователей](https://azure.microsoft.com/blog/2014/10/02/custom-login-scopes-single-sign-on-new-asp-net-web-api-updates-to-the-azure-mobile-services-net-backend/)<br/> Вы можете получить дополнительные данные пользователя, которые хранит поставщик удостоверений в мобильной службе, вызвав в серверной части .NET метод **ServiceUser.GetIdentitiesAsync()**.

+ [Справочник принципов использования мобильных служб .NET]<br/> Узнайте больше об использовании мобильных служб с помощью клиента .NET.


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
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: mobile-services-dotnet-backend-service-side-authorization.md
[JavaScript and HTML]: mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[классического портала Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=AcomDC_0518_2016-->