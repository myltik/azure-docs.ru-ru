<properties linkid="develop-mobile-tutorials-javascript-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc" />

# Получение доступа к сведениям Graph Azure Active Directory

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-aad-graph-info/" title="Магазин Windows C#" class="current">Магазин Windows C#</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title="Сервер .NET">Сервер .NET</a> |
    <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="Сервер JavaScript" class="current">Сервер JavaScript</a>
</div>

Как и другие поставщики удостоверений, которые предлагаются в мобильных службах, поставщик Azure Active Directory (AAD) также поддерживает полнофункциональный интерфейс [API Graph][API Graph], который можно использовать для программного доступа к каталогу. В этом учебнике предстоит обновить приложение ToDoList, персонализировав интерфейс приложения прошедшего проверку подлинности пользователя на основе сведений о пользователях, полученных из каталога с помощью [API Graph][API Graph].

> [WACOM.NOTE] Цель этого учебника — предоставить дополнительные сведения о проверке подлинности с помощью Azure Active Directory. Предполагается, что вы прошли учебник [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности], используя поставщика проверки подлинности Azure Active Directory. В этом учебнике обновляется приложение TodoItem, использованное в учебнике [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности].

Данный учебник включает в себя следующие этапы.

1.  [Создание ключа доступа для регистрации приложения в AAD][Создание ключа доступа для регистрации приложения в AAD]
2.  [Создание пользовательского интерфейса API GetUserInfo][Создание пользовательского интерфейса API GetUserInfo]
3.  [Обновление приложения для использования пользовательского API][Обновление приложения для использования пользовательского API]
4.  [Тестирование приложения][Тестирование приложения]

## Предварительные требования

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

-   [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности]
    Добавление обязательного входа в демонстрационное приложение TodoList.

-   [Учебник по пользовательским API][Учебник по пользовательским API]
    Способ вызова пользовательского API.

## <a name="generate-key"></a>Создание ключа доступа для регистрации приложения в AAD

В учебнике [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности] во время выполнения шага [Регистрация для использования входа Azure Active Directory][Регистрация для использования входа Azure Active Directory] вы создали регистрацию для встроенного приложения. В этом разделе предстоит создать ключ для использования при чтении сведений каталога с помощью идентификатора клиента встроенного приложения.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]

## <a name="create-api"></a>Создание пользовательского интерфейса API GetUserInfo

В этом разделе предстоит создать пользовательский API GetUserInfo, который будет использовать [API REST Graph][API REST Graph] для получения дополнительных сведений о пользователе из AAD.

Если вы не использовали пользовательские API с мобильными службами, следует ознакомиться с [учебником по пользовательским API][Учебник по пользовательским API], перед тем как переходить к этому разделу.

1.  На [портале управления Azure][портале управления Azure] создайте новый пользовательский API GetUserInfo для мобильной службы и задайте для метода get разрешение **Только выполнившие проверку подлинности пользователи**.

    ![][0]

2.  Откройте редактор скриптов для нового API GetUserInfo и добавьте в начало скрипта следующие переменные.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;

3.  Добавьте следующее определение для функции `getAADToken`.

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

    Если заданы значения *tenant\_domain*, встроенное приложение *client id* и приложение *key*, эта функция предоставляет токен доступа Graph, использованный для чтения сведений в каталоге.

4.  Добавьте следующую функцию `getUser`, которая использует API Graph для возврата сведений о пользователе.

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

    Эта функция содержит тонкую оболочку вокруг конечной точки [Get User][Get User] API REST Graph. Она использует токен доступа Graph для получения сведений о пользователе с помощью идентификатора объекта пользователя.

5.  Обновите экспортированный метод `get` следующим образом, чтобы вернуть сведения о пользователе с помощью других функций.

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

В этом разделе предстоит обновить метод `AuthenticateAsync`, реализованный в учебнике [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности] для вызова пользовательского API и возвращения дополнительных сведений о пользователе из AAD.

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]

## <a name="test-app"></a> Тестирование приложения

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, [Управление доступом в мобильных службах на основе ролей с помощью AAD][Управление доступом в мобильных службах на основе ролей с помощью AAD], предстоит использовать управление доступом на основе ролей с помощью Azure Active Directory (AAD) для проверки членства в группе перед разрешением доступа.


<!-- Images -->


  [API Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
  [Начало работы с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Создание ключа доступа для регистрации приложения в AAD]: #generate-key
  [Создание пользовательского интерфейса API GetUserInfo]: #create-api
  [Обновление приложения для использования пользовательского API]: #update-app
  [Тестирование приложения]: #test-app
  [Учебник по пользовательским API]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [Регистрация для использования входа Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [API REST Graph]: http://msdn.microsoft.com/ru-ru/library/azure/hh974478.aspx
  [портале управления Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png
  [Get User]: http://msdn.microsoft.com/ru-ru/library/azure/dn151678.aspx
  [Управление доступом в мобильных службах на основе ролей с помощью AAD]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/
