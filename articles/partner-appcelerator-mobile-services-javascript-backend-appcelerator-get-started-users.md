<properties pageTitle="Get started with authentication (Appcelerator) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator team;mahender"></tags>

# Приступая к работе с проверкой подлинности в мобильных службах

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Магазин Windows C#">Магазин Windows C#</a>
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
    <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
    <a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-users" title="iOS">iOS</a>
    <a href="/ru-ru/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a>
    <a href="/ru-ru/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a>
    <a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>
    <a href="/ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users" title="Appcelerator" class="current">Appcelerator</a>
</div>

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Windows Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1.  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб][Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2.  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности][Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3.  [Добавление проверки подлинности к приложению][Добавление проверки подлинности к приложению]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

Для работы с этим учебником необходимы Appcelerator Titanium Studio версии 3.2.1 или более поздние версии и iOS 7.0 или более поздние версии Android 4.3 или более поздние версии.

## <a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

## <a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][mobile-services-restrict-permissions-javascript-backend]]

1.  В Appcelerator Titanium Studio откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

2.  Нажмите клавишу «Запуск» для построения проекта, после чего запустите приложение в эмуляторе iPhone. Убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).

    Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица TodoItem теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение

1.  Откройте файл проекта index.js и в таблице событий найдите метод Lister`case 2:`

    В приложении вы можете либо запросить пользователя о выборе поставщика удостоверений, либо автоматически выбрать одного из поставщиков удостоверений.

2.  Для выбора всех поставщиков удостоверений, используйте код:

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var appName = 'appctest';
        azureMobileServices.setAppName(appName);
        var authenticationClients = ['Google', 'Facebook', 'Twitter', 'Microsoft Account', 'Active Directory', 'Cancel'];
        var dialog = Ti.UI.createOptionDialog({
            options : authenticationClients,
            title : 'Select a client'
        });
        dialog.addEventListener('click', function(evt) {
            if (evt.index == 0 || evt.index == 1 || evt.index == 2 || evt.index == 3 || evt.index == 4) {
                var str = authenticationClients[evt.index];
                str = str.replace(/ /g, '');
                var authorizeClient = str.toLowerCase();
                if (authorizeClient == 'activedirectory') authorizeClient = 'aad';
                azureMobileServices.authorizeClient(authorizeClient, function(result) {
                    if (result == 'true') {
                        Alloy.createController('TableData');
                    }
                });
            } else {
                dialog.hide();
            }
        });
        dialog.show();

3.  Для выбора конкретного поставщика удостоверений, используйте код:

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var authorizeClient = "Google"; //Replace "Google" with identity provider.
        authorizeClient = authorizeClient.toLowerCase();
        azureMobileServices.authorizeClient(authorizeClient, function(result) {
            if (result == 'true') {
                Alloy.createController('TableData');
            }
        });

> Если используется поставщик удостоверений, отличный от Google, измените значение, передаваемое в метод **authorizeClient**, на одно из следующих:. *MicrosoftAccount*, *Facebook*, *Twitter* или *windowsazureactivedirectory*.

1.  Нажмите клавишу «Запуск», чтобы построить проект, запустите приложение в эмуляторе iPhone, а затем войдите с использованием выбранного поставщика удостоверений.

    После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Магазин Windows C#]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "Магазин Windows C#"
  [Магазин Windows JavaScript]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users "Магазин Windows JavaScript"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/mobile-services-ios-get-started-users "iOS"
  [Android]: /ru-ru/documentation/articles/mobile-services-android-get-started-users "Android"
  [HTML]: /ru-ru/documentation/articles/mobile-services-html-get-started-users "HTML"
  [Xamarin.iOS]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android"
  [Appcelerator]: /ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users "Appcelerator"
  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
  [Добавление проверки подлинности к приложению]: #add-authentication
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
