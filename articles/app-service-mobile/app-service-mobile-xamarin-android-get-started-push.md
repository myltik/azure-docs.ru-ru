<properties
	pageTitle="Добавление push-уведомлений в приложение Xamarin.Android | Служба приложений Azure"
	description="Узнайте, как использовать службу приложений и центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="glenga"/>

# Добавление push-уведомлений в приложение Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Обзор

В этом учебнике описывается добавление push-уведомлений в проект из учебника [Создание приложения Xamarin.Android], чтобы при вставке каждой новой записи отправлялось push-уведомление. Этот учебник использует материал учебника [Создание приложения Xamarin.Android], который необходимо предварительно изучить. Если вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Предварительные требования

Для работы с данным учебником требуется следующее:

+ Активная учетная запись Google. Вы можете зарегистрировать учетную запись Google на сайте [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

+ [Компонент клиента Google Cloud Messaging](http://components.xamarin.com/view/GCMClient/). Этот компонент будет добавлен при прохождении учебника.

##<a name="create-hub"></a>Создание центра уведомлений

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a id="register"></a>Включение Google Cloud Messaging

[AZURE.INCLUDE [Включение GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##Настройка серверной части мобильного приложения для отправки push-запросов

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-server"></a>Обновление серверного проекта для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>Настройка клиентского проекта для использования push-уведомлений

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Добавление кода push-уведомлений в приложение

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Тестирование push-уведомлений в приложении

Приложение можно проверить, используя виртуальное устройство в эмуляторе. Для запуска в эмуляторе необходимо выполнить дополнительную настройку.

1. Убедитесь, что для развертывания или отладки используется виртуальное устройство, на котором в качестве назначения заданы интерфейсы Google API, как показано ниже в диспетчере виртуальных устройств Android (AVD).

	![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Добавьте учетную запись Google на устройство Android, щелкнув **Приложения** > **Параметры** > **Добавить учетную запись**. Следуйте указаниям, чтобы использовать на устройстве существующую учетную запись Google или создать новую.

	![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Запустите приложение todolist, как ранее, и вставьте новый элемент списка дел. На этот раз в области уведомлений отображается значок уведомления. Вы можете открыть панель уведомлений, чтобы просмотреть полный текст уведомления.

	![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quickstart]: app-service-mobile-xamarin-android-get-started.md
[Создание приложения Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_0817_2016-->
