<properties 
	pageTitle="Доступ к сведениям в Azure Active Directory Graph (Магазин Windows) | Центр разработчиков для мобильных устройств" 
	description="Узнайте, как получить доступ к информации Azure Active Directory с помощью API Graph в приложении Магазина Windows." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# Получение доступа к сведениям Graph Azure Active Directory


[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]
##Обзор

Как и другие поставщики удостоверений, которые предлагаются в мобильных службах, поставщик Azure Active Directory (AAD) также поддерживает полнофункциональный интерфейс API Graph, который можно использовать для программного доступа к каталогу. В этом учебнике вы обновите приложение ToDoList для персонализации интерфейса приложения прошедшего проверку подлинности пользователя, возвращая дополнительные сведения о пользователях, полученные из каталога с помощью [Graph REST API].

Дополнительные сведения об API Graph Azure AD см. в статье [Блог команды разработчиков Azure Active Directory Graph].

>[AZURE.NOTE]Цель этого учебника — предоставить дополнительные сведения о проверке подлинности с помощью Azure Active Directory. Предполагается, что вы выполнили задания учебника [Добавление проверки подлинности в приложение], используя поставщика проверки подлинности Azure Active Directory. В этом учебнике продолжается обновление приложения TodoItem, которое использовалось в учебнике [Добавление проверки подлинности в приложение].



##Предварительные требования 

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Добавление проверки подлинности в приложение]<br/>В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

+ [Учебник по пользовательским API]<br/>Способ вызова пользовательского API.



##Создание ключа доступа для регистрации приложения в AAD


В учебнике [Добавление проверки подлинности в приложение] во время выполнения шага [Регистрация для использования имени входа Azure Active Directory] вы создали регистрацию для встроенного приложения. В этом разделе предстоит создать ключ для использования при чтении сведений каталога с помощью идентификатора клиента встроенного приложения.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##Создание пользовательского интерфейса API GetUserInfo

В этом разделе предстоит создать пользовательский API GetUserInfo, который будет использовать [API REST Graph] для получения дополнительных сведений о пользователе из AAD.

Если вы не использовали пользовательские API с мобильными службами, следует ознакомиться с [учебником по пользовательским API], перед тем как переходить к этому разделу.

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

    Если заданы значения *tenant_domain*, встроенное приложение *client id* и приложение *key*, эта функция предоставляет токен доступа Graph, использованный для чтения сведений в каталоге.

4. Добавьте следующую функцию `getUser`, которая использует API Graph для возврата сведений о пользователе.

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

    Эта функция содержит тонкую оболочку вокруг конечной точки [Get User] API REST Graph. Она использует токен доступа Graph для получения сведений о пользователе с помощью идентификатора объекта пользователя.

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


##Обновление приложения для использования GetUserInfo


В этом разделе вам предстоит обновить метод `AuthenticateAsync`, применяемый в учебнике [Приступая к работе с проверкой подлинности] для вызова настраиваемого API и получения дополнительных сведений о пользователе из службы AAD.

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]


 


##Тестирование приложения

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]




##Дальнейшие действия

В следующем учебнике, [Управление доступом в мобильных службах на основе ролей с помощью AAD], предстоит использовать управление доступом на основе ролей с помощью Azure Active Directory (AAD) для проверки членства в группе перед разрешением доступа.



<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Добавление проверки подлинности в приложение]: ../mobile-services-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[портале управления Azure]: https://manage.windowsazure.com/
[Учебник по пользовательским API]: mobile-services-windows-store-dotnet-call-custom-api.md
[учебником по пользовательским API]: mobile-services-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Регистрация для использования имени входа Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[API REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Управление доступом в мобильных службах на основе ролей с помощью AAD]: mobile-services-javascript-backend-windows-store-dotnet-aad-rbac.md
[Блог команды разработчиков Azure Active Directory Graph]: http://go.microsoft.com/fwlink/?LinkId=510536

<!---HONumber=July15_HO2-->