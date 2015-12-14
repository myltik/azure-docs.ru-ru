<properties 
	pageTitle="Добавление push-уведомлений в универсальное приложение среды выполнения Windows 8.1 | Мобильные приложения Azure" 
	description="Узнайте, как использовать мобильные приложения службы приложений Azure и центры уведомлений Azure для отправки push-уведомлений в приложение для Windows." 
	services="app-service\mobile,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/25/2015" 
	ms.author="glenga"/>

# Добавление push-уведомлений в универсальное приложение среды выполнения Windows 8.1

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Обзор

В этом разделе показано, как отправлять push-уведомления в универсальное приложение среды выполнения Windows 8.1 с помощью мобильных приложений службы приложений Azure и центров уведомлений Azure. В этом сценарии при добавлении нового элемента серверная часть мобильного приложения отправляет push-уведомление всем приложениям для Windows, которые зарегистрированы в службе уведомлений Windows (WNS).

Этот учебник создан на основе краткого руководства по мобильным приложениям службы приложений. Перед началом работы с этим учебником необходимо изучить краткое руководство [Создание приложения Windows](../app-service-mobile-windows-store-dotnet-get-started.md). Если вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Предварительные требования

Для работы с этим учебником требуется:

* активная [учетная запись Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045);
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934);
* Изучение [краткого руководства](../app-service-mobile-windows-store-dotnet-get-started.md).  


##<a name="create-hub"></a>Создание центра уведомлений

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Регистрация приложения для работы с push-уведомлениями

Прежде чем вы сможете отправлять push-уведомления в приложения для Windows из Azure, необходимо отправить приложение в Магазин Windows. Затем можно будет настроить серверный проект для интеграции с WNS.

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


##Настройка серверной части для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>Обновление сервера для отправки push-уведомлений

После включения push-уведомлений в приложении необходимо обновить внутренний сервер приложения так, чтобы он отправлял push-уведомления.

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


## <a name="publish-the-service"></a>Публикация мобильного внутреннего сервера в Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##<a id="update-service"></a>Добавление push-уведомлений в приложение

1. Откройте общий файл проекта **App.xaml.cs** и добавьте следующие операторы `using`:

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

2. В том же файле добавьте следующее определение метода **InitNotificationsAsync** в класс **App**:
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Этот код возвращает ChannelURI для приложения из WNS, а затем регистрирует ChannelURI в мобильном приложении службы приложений.
    
3. В верхней части обработчика событий **OnLaunched** в файле **App.xaml.cs** добавьте в определение метода модификатор **async**, а в новый метод **InitNotificationsAsync** добавьте следующий вызов (как в приведенном ниже примере):

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Это обеспечит регистрацию кратковременного ChannelURI при каждом запуске приложения.

4. В обозревателе решений дважды щелкните файл **Package.appxmanifest** приложения для Магазина Windows и в разделе **Уведомления** установите для параметра **Всплывающие уведомления** значение **Да**.

    В меню **Файл** выберите **Сохранить все**.

5. Для проекта приложения Магазина Windows Phone повторите предыдущий шаг.

Теперь приложение готово к получению всплывающих уведомлений.

##<a id="test"></a>Тестирование push-уведомлений в приложении

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

##Дальнейшие действия

Наконец, в учебнике [Отправка кроссплатформенных уведомлений определенному пользователю](app-service-mobile-windows-store-dotnet-push-notifications-to-users.md) объясняется, как отправить push-уведомления на все зарегистрированные устройства на любой платформе, принадлежащие определенному пользователю, который прошел проверку подлинности.

##<a id="more"></a>Дополнительные сведения

* Шаблоны обеспечивают гибкость при отправке push-уведомлений локально и между различными платформами. В статье [Использование управляемого клиента для мобильных приложений Azure](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications) описано, как зарегистрировать шаблоны.
* Теги позволяют отправлять push-уведомления клиентам в зависимости от их сегмента. В статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) показано, как добавлять теги для установки устройства.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=AcomDC_1203_2015--->