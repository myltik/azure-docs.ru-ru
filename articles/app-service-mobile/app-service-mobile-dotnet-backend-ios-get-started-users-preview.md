<properties
	pageTitle="Начало работы с проверкой подлинности для мобильных приложений в iOS"
	description="Использование мобильных приложений для проверки подлинности пользователей приложения iOS с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="mahender"/>

# Добавление проверки подлинности в приложение iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

В этом разделе показано, как выполнить проверку подлинности пользователей мобильного приложения службы приложений из клиентского приложения. В этом учебнике вы добавите проверку подлинности в проект краткого руководства, используя поставщик удостоверений, поддерживаемый службой приложений. После успешной проверки подлинности и авторизации мобильным приложением отображается значение идентификатора пользователя.

Этот учебник создан на основе краткого руководства по мобильным приложениям. Необходимо также сначала пройти учебник [Создание приложения iOS].

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка служб приложений

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="7">
<li><p>Откройте проект в Xcode. Нажмите кнопку <b>Выполнить</b>, чтобы открыть приложение. Убедитесь, что после запуска приложения возникло исключение с кодом состояния 401 (неавторизованный доступ).</p>

   	<p>Это происходит потому, что приложение пытается получить доступ к коду мобильного приложения от имени пользователя, не прошедшего проверку подлинности, но таблице <em>TodoItem</em> теперь требуется проверка подлинности.</p></li>
</ol>

Далее вы обновите приложение, чтобы оно выполняло проверку подлинности пользователей перед запросом ресурсов из службы приложений.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Хранение токенов проверки подлинности в приложении

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app-with-token](../../includes/app-service-mobile-ios-authenticate-app-with-token.md)]


<!-- URLs. -->

[Создание приложения iOS]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=July15_HO3-->