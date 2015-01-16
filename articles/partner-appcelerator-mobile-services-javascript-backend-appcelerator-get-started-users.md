<properties pageTitle="Приступая к работе с аутентификацией (Appcelerator) | Центр мобильных разработок" metaKeywords="" description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения iOS с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="11/24/2014" ms.author="Appcelerator team;mahender" />

# Приступая к работе с аутентификацией в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Windows Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1.  [Регистрация приложения для аутентификации и настройка мобильных служб]
2.  [Ограничение разрешений таблицы пользователями, прошедшими аутентификацию]
3.  [Добавление аутентификации в приложение]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами].

Для работы с этим учебником необходимы Appcelerator Titanium Studio версии 3.2.1 или более поздней и iOS 7.0 или более поздней версии, а также Android 4.3 или более поздней версии.

##<a name="register"></a>Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a> Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

3.	В Appcelerator Titanium Studio откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами].

4.	Нажмите клавишу "Запуск" для построения проекта, после чего запустите приложение в эмуляторе iPhone. Убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).
    
    Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица TodoItem теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление аутентификации в приложение

1.	Откройте файл проекта index.js и в таблице событий найдите метод Lister с именем "case 2:"

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

>[WACOM.NOTE] Если используется поставщик удостоверений, отличный от Google, измените значение, передаваемое в метод **authorizeClient**, на одно из следующих: "microsoftaccount", "facebook", "twitter" или "windowsazureactivedirectory".

4.	Нажмите клавишу "Запуск", чтобы построить проект, запустите приложение в эмуляторе iPhone, а затем войдите с использованием выбранного поставщика удостоверений.

    После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.


<!-- Anchors. -->

[Регистрация приложения для аутентификации и настройка мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими аутентификацию]: #permissions
[Добавление аутентификации в приложение]: #add-authentication

<!-- Images. -->

<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started

<!--HONumber=35.1-->
