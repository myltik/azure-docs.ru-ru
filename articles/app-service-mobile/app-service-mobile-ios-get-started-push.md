<properties
	pageTitle="Добавление push-уведомлений в приложение iOS с помощью мобильных приложений Azure"
	description="Узнайте, как использовать мобильные приложения Azure для отправки push-уведомлений в приложение iOS."
	services="app-service\mobile"
	documentationCenter="ios"
	manager="dwrede"
	editor=""
	authors="krisragh"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/22/2015"
	ms.author="krisragh"/>


# Добавление push-уведомлений в приложение iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Обзор
В этом учебнике описывается добавление push-уведомлений в проект учебника по [быстрому запуску iOS], чтобы при вставке каждой новой записи отправлялось push-уведомление. Этот учебник использует материал учебника по [быстрому запуску iOS], который необходимо пройти в первую очередь. Если вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

[Симулятор iOS не поддерживает push-уведомления](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), поэтому для работы с данным учебником вам потребуется физическое устройство iOS и [участие в программе для разработчиков Apple](https://developer.apple.com/programs/ios/).

##<a name="create-hub"></a>Создание концентратора уведомлений

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## <a id="register"></a>Регистрация приложения для работы с push-уведомлениями

[AZURE.INCLUDE [Включение push-уведомлений Apple](../../includes/enable-apple-push-notifications.md)]

## Настройка Azure для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Обновление серверного проекта для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="publish-the-service"></a>Развертывание серверного проекта в Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## <a id="add-push"></a>Добавление push-уведомлений в приложение

[AZURE.INCLUDE [Добавление push-уведомлений в приложение](../../includes/app-service-add-push-notifications-to-app.md)]

## <a id="test"></a>Тестирование push-уведомлений в приложении

[AZURE.INCLUDE [Тестирование push-уведомлений в приложении](../../includes/test-push-notifications-in-app.md)]

<!-- Anchors.  -->
[Generate iOS certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Add push notifications to the app]: #add-push
[Configure your mobile backend to send push requests]: #configure
[Update the server to send push notifications]: #update-server
[Publish the mobile backend to Azure]: #publish-mobile-service
[Test your app]: #test-the-service

<!-- Images. -->

<!-- URLs. -->
[быстрому запуску iOS]: app-service-mobile-ios-get-started.md

<!---HONumber=Nov15_HO2-->