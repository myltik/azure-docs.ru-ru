<properties
	pageTitle="Добавление аутентификации в универсальное приложение для среды выполнения Windows 8.1 | Мобильные приложения Azure"
	description="Узнайте, как использовать мобильные приложения службы приложений Azure, чтобы выполнять аутентификацию пользователей вашего приложения для Windows с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="glenga"/>

# Добавление проверки подлинности в приложение Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

В этом разделе показано, как выполнить проверку подлинности пользователей мобильного приложения службы приложений из клиентского приложения. В этом учебнике вы добавите проверку подлинности в проект краткого руководства, используя поставщик удостоверений, поддерживаемый службой приложений. После успешной проверки подлинности и авторизации мобильным приложением отображается значение идентификатора пользователя.

Этот учебник создан на основе краткого руководства по мобильным приложениям. Вам необходимо сначала изучить учебник [Начало работы с мобильным приложением].

##<a name="review"></a>Проверка конфигурации сервера проекта (необязательно)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-auth-preview](../../includes/app-service-mobile-dotnet-backend-enable-auth-preview.md)]

##<a name="create-gateway"></a>Создание шлюза службы приложений

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка службы приложений

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. В Visual Studio откройте общий файл проекта App.xaml.cs в проекте приложения клиента и убедитесь, что экземпляр **MobileServiceClient** настроен для использования URL-адреса серверной части мобильного приложения и URL-адреса шлюза.

&nbsp;&nbsp;5. Назначив запускаемым проектом один из проектов приложения Windows, нажмите клавишу F5, чтобы запустить приложение. Убедитесь в том, что после его запуска порождается необработанное исключение с кодом состояния 401 (не авторизован).

&nbsp;&nbsp;Это происходит потому, что приложение пытается получить доступ к коду мобильного приложения от имени пользователя, не прошедшего аутентификацию, но таблице *TodoItem* теперь требуется аутентификация.

Далее вы обновите приложение, чтобы оно выполняло проверку подлинности пользователей перед запросом ресурсов из службы приложений.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>Сохранение токена проверки подлинности в клиенте

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)]

##Дальнейшие действия


<!-- URLs. -->
[Начало работы с мобильным приложением]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
 

<!---HONumber=August15_HO8-->