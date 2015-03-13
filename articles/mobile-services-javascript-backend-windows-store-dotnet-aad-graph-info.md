<properties 
	pageTitle="Доступ к сведениям в Azure Active Directory Graph (Магазин Windows) | Центр разработчиков для мобильных устройств" 
	description="Узнайте, как получить доступ к информации Azure Active Directory с помощью API Graph в приложении Магазина Windows." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="wesmc"/>

# Получение доступа к сведениям Graph Azure Active Directory

[AZURE.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]


Как и другие поставщики удостоверений, которые предлагаются в мобильных службах, поставщик Azure Active Directory (AAD) также поддерживает полнофункциональный интерфейс [API Graph], который можно использовать для программного доступа к каталогу. В этом учебнике предстоит обновить приложение ToDoList, персонализировав интерфейс приложения прошедшего проверку подлинности пользователя на основе сведений о пользователях, полученных из каталога с помощью [API Graph].

>[AZURE.NOTE] Цель этого учебника - предоставить дополнительные сведения о проверке подлинности с помощью Azure Active Directory. Предполагается, что вы прошли учебник [Приступая к работе с проверкой подлинности], используя поставщика проверки подлинности Azure Active Directory. В этом учебнике обновляется приложение TodoItem, использованное в учебнике [Приступая к работе с проверкой подлинности].



Данный учебник включает в себя следующие этапы.


1. [Создание ключа доступа для регистрации приложения в AAD] 
2. [Создание настраиваемого интерфейса API GetUserInfo] 
3. [Обновление приложения для использования настраиваемого интерфейса API]
4. [Тестирование приложения]

##Предварительные требования 

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Приступая к работе с проверкой подлинности]<br/>В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

+ [Учебник по применению настраиваемого API-интерфейса]<br/>Демонстрирует вызов настраиваемого интерфейса API. 



## <a name="generate-key"></a>Создание ключа доступа для регистрации приложения в AAD 


В учебнике [Приступая к работе с проверкой подлинности] во время выполнения шага [Регистрация для использования имени входа Azure Active Directory] вы создали регистрацию для встроенного приложения. В этом разделе предстоит создать ключ для использования при чтении сведений каталога с помощью идентификатора клиента встроенного приложения. 

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-api"></a>Создание настраиваемого интерфейса API GetUserInfo

В этом разделе предстоит создать настраиваемый API GetUserInfo, который будет использовать [API REST Graph] для получения дополнительных сведений о пользователе из AAD.

Если вы не использовали настраиваемые API с мобильными службами, следует ознакомиться с [учебником по настраиваемым API], перед тем как переходить к этому разделу.

1. На [портале управления Azure] создайте новый настраиваемый API GetUserInfo для мобильной службы и задайте для метода get разрешение **Только выполнившие проверку подлинности пользователи**.

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

    Если заданы значения  *tenant_domain*, встроенное приложение *client id* и приложение *key*, эта функция предоставляет маркер доступа Graph, использованный для чтения сведений в каталоге.

4. Добавьте следующую функцию  `getUser`, которая использует API Graph для возврата сведений о пользователе.

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

    Эта функция представляет собой тонкую оболочку вокруг конечной точки [Get User] Graph REST API. Она использует токен доступа Graph для получения сведений о пользователе с помощью идентификатора объекта пользователя.

5. Обновите экспортированный метод `get` следующим образом, чтобы вернуть сведения о пользователе с помощью других функций.

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


В этом разделе предстоит обновить метод  `AuthenticateAsync`, реализованный в учебнике [Приступая к работе с проверкой подлинности] для вызова настраиваемого API и возвращения дополнительных сведений о пользователе из AAD. 

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]


 


## <a name="test-app"></a>Тестирование приложения

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]




##<a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, [Управление доступом в мобильных службах на основе ролей с помощью AAD], предстоит использовать управление доступом на основе ролей с помощью Azure Active Directory (AAD) для проверки членства в группе перед разрешением доступа. 


<!-- Anchors. -->
[Создание ключа доступа для регистрации приложения в AAD]: #generate-key
[Создание настраиваемого интерфейса API GetUserInfo]: #create-api
[Обновление приложения для использования настраиваемого интерфейса API]: #update-app
[Тестирование приложения]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Регистрация в службе Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Портал управления Azure]: https://manage.windowsazure.com/
[Учебник по применению настраиваемого API-интерфейса]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
[Хранение серверных скриптов]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control/
[Регистрация для использования имени входа Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Получение пользователя]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Управление доступом на основе ролей в мобильных службах с помощью AAD]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/


<!--HONumber=42-->
