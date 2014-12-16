<properties pageTitle="Приступая к работе с проверкой подлинности (Appcelerator) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="Appcelerator team;mahender" />

# Приступая к работе с проверкой подлинности в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Windows Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1.  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2.  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3.  [Добавление проверки подлинности к приложению]

Этот учебник создан на основе краткого руководства по мобильным службам. Необходимо прежде всего пройти учебник [Начало работы с мобильными службами].

Для работы с этим учебником необходимы Appcelerator Titanium Studio версии 3.2.1 или более поздние версии и iOS 7.0 или более поздние версии Android 4.3 или более поздние версии.

##<a name="register"></a>Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a> Предоставление разрешений только пользователям, прошедшим проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

3.	В среде Appcelerator Titanium Studio откройте проект, созданный в ходе работы с учебником [Приступая к работе с мобильными службами].

4.	Нажмите клавишу "Выполнить" для построения проекта, после чего запустите приложение в эмуляторе iPhone. Убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный доступ).
    
    Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица TodoItem теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности к приложению

1.	Откройте файл проекта index.js и в методе Lister таблицы событий найдите `case 2:`

    В приложении вы можете либо запросить пользователя о выборе поставщика удостоверений, либо автоматически выбрать одного из поставщиков удостоверений.

2.	Для выбора всех поставщиков удостоверений, используйте код:

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

3.	Для выбора конкретного поставщика удостоверений, используйте код:

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var authorizeClient = "Google"; //Replace "Google" with identity provider.
        authorizeClient = authorizeClient.toLowerCase();
        azureMobileServices.authorizeClient(authorizeClient, function(result) {
            if (result == 'true') {
                Alloy.createController('TableData');
            }
        });

>[WACOM.NOTE] Если используется поставщик удостоверений, отличный от Google, измените значение, передаваемое в метод **authorizeClient**, на одно из следующих: *microsoftaccount*, *facebook*, *twitter* или *windowsazureactivedirectory*.

4.	Нажмите клавишу "Запуск", чтобы построить проект, запустите приложение в эмуляторе iPhone, а затем войдите с использованием выбранного поставщика удостоверений.

    После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.


<!-- Anchors. -->

[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication

<!-- Images. -->

<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
