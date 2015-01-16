<properties urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Доступ к сведениям в Azure Active Directory Graph (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как получить доступ к информации Azure Active Directory с помощью API Graph в приложении Магазина Windows." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="wesmc" />

# Получение доступа к сведениям Graph Azure Active Directory

[WACOM.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]


Как и другие поставщики удостоверений, предоставляемые мобильными службами, поставщик Azure Active Directory (AAD) также поддерживает полнофункциональный интерфейс [Graph API], который можно использовать для программного доступа к каталогу. В этом учебнике предстоит обновить приложение ToDoList путем персонализации интерфейса приложения прошедшего проверку подлинности пользователя на основе сведений о пользователях, полученных из каталога с помощью [Graph API].

>[WACOM.NOTE] Цель этого учебника - предоставить дополнительные сведения о проверке подлинности с помощью Azure Active Directory. Предполагается, что вы прошли учебник [Начало работы с проверкой подлинности], используя поставщика проверки подлинности Azure Active Directory. В этом учебнике обновляется приложение TodoItem, использованное в учебнике [Начало работы с проверкой подлинности].



Данный учебник включает в себя следующие этапы.


1. [Создание ключа доступа для регистрации приложения в AAD] 
2. [Создание пользовательского интерфейса API GetUserInfo] 
3. [Обновление приложения для использования пользовательского API]
4. [Тестирование приложения]

##Предварительные требования 

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Начало работы с проверкой подлинности]<br/>Добавление требования по регистрации в пример приложения TodoList.

+ [Учебник по пользовательским API-интерфейсам]<br/>Показывает способ вызова пользовательского API-интерфейса. 



## <a name="generate-key"></a>Создание ключа доступа для регистрации приложения в AAD


В учебнике [Начало работы с проверкой подлинности] во время выполнения шага [Регистрация для использования входа Azure Active Directory] вы создали регистрацию для встроенного приложения. В этом разделе предстоит создать ключ, который будет использоваться при чтении сведений каталога с помощью идентификатора клиента встроенного приложения. 

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-api"></a>Создание пользовательского интерфейса API GetUserInfo

В этом разделе предстоит создать пользовательский API GetUserInfo, который будет использовать [Graph REST API] для получения дополнительных сведений о пользователе из AAD.

Если вы не использовали пользовательские API с мобильными службами, следует ознакомиться с [учебником по пользовательским API], прежде чем переходить к этому разделу.

1. На [портале управления Azure] создайте новый пользовательский API GetUserInfo для мобильной службы и задайте для метода get разрешение **Только выполнившие проверку подлинности пользователи**.

    ![][0]

2. Откройте редактор скриптов для нового API GetUserInfo и добавьте в начало скрипта следующие переменные.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;



3. Добавьте следующее определение для функции `getAADToken`.

        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
            });
        }

    В зависимости от*tenant_domain*, *идентификатора клиента* встроенного приложения и *ключа* приложения эта функция предоставляет маркер доступа Graph, используемый для считывания информации каталога.

4. Добавьте следующую функцию `getUser`, которая использует Graph API для возврата сведений о пользователе.

        function getUser(access_token, objectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/users/" + objectId + "?api-version=1.0",
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + access_token
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body));
            });
        };

    Эта функция представляет собой тонкую оболочку вокруг конечной точки [Get User] Graph REST API. Она использует маркер доступа Graph для получения сведений о пользователе с помощью идентификатора объекта пользователя.

5. Обновите экспортированный метод `get` следующим образом, чтобы получать сведения о пользователе с помощью других функций.

        exports.get = function (request, response) {
            if (request.user.level == 'anonymous') {
                response.send(statusCodes.UNAUTHORIZED, null);
                return;
            }
            var errorHandler = function (err) {
                console.log(err);
                response.send(statusCodes.INTERNAL_SERVER_ERROR, err);
            };
            request.user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) errorHandler(err);
                        else getUser(access_token, objectId, function (err, user_info) {
                            if (err) errorHandler(err);
                            else {
                                console.log('GetUserInfo: ' + JSON.stringify(user_info, null, 4));
                                response.send(statusCodes.OK, user_info);
                              }
                            });
                    });
                },
                error: errorHandler
            });
        };


## <a name="update-app"></a>Обновление приложения для использования GetUserInfo


В этом разделе предстоит обновить метод `AuthenticateAsync`, реализованный в учебнике [Начало работы с проверкой подлинности] для вызова пользовательского API и возвращения дополнительных сведений о пользователе из AAD. 

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]


 


## <a name="test-app"></a>Тестирование приложения

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]




##<a name="next-steps"></a>Дальнейшие действия

В следующем учебнике [Управление доступом в мобильных службах на основе ролей с помощью AAD], предстоит перед разрешением доступа использовать управление доступом на основе ролей с помощью Azure Active Directory (AAD) для проверки членства в группе. 


<!-- Anchors. -->
[Создание ключа доступа для регистрации приложения в AAD]: #generate-key
[Создание пользовательского интерфейса API GetUserInfo]: #create-api
[Обновление приложения для использования пользовательского API]: #update-app
[Тестирование приложения]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Начало работы с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Регистрация в службе Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Портал управления Azure]: https://manage.windowsazure.com/
[Учебник по применению пользовательского API-интерфейса]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
[Хранение серверных скриптов]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control/
[Регистрация для использования имени входа Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph REST API]: http://msdn.microsoft.com/ru-ru/library/azure/hh974478.aspx
[Получение пользователя]: http://msdn.microsoft.com/ru-ru/library/azure/dn151678.aspx
[Управление доступом в мобильных службах с помощью AAD на основе ролей]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/
