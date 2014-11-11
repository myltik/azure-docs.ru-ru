<properties linkid="develop-mobile-tutorials-js-rbac-with-aad" urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="Role Based Access Control in Mobile Services and Azure Active Directory (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to control access based on Azure Active Directory roles in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/21/2014" ms.author="wesmc" />

# Управление доступом на основе ролей в мобильных службах и Azure Active Directory

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="Магазин Windows C#" class="current">Магазин Windows C#</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title="Сервер .NET">Сервер .NET</a> |
    <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="Сервер JavaScript" class="current">Сервер JavaScript</a>
</div>

Управление доступом на основе ролей (RBAC) — это методика назначения разрешений ролям, которые могут хранить пользователи, с четко определенными границами разрешенных и запрещенных действий для определенных классов пользователей. В этом учебнике содержатся инструкции по добавлению базового RBAC в мобильные службы Azure.

В этом учебнике демонстрируется управление доступом на основе ролей с проверкой членства в группе продаж, определенного в Azure Active Directory (AAD), для каждого пользователя. Проверка доступа будет осуществляться на внутреннем сервере мобильной службы JavaScript с использованием [API Graph][API Graph] для Azure Active Directory. Запрашивать данные смогут только пользователи, принадлежащие группе продаж.

> [WACOM.NOTE] Цель этого учебника — предоставить дополнительные рекомендации по авторизации в проверке подлинности. Предполагается, что вы сначала пройдете учебник [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности], используя поставщика проверки подлинности Azure Active Directory. В этом учебнике обновляется приложение TodoItem, использованное в учебнике [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности].

Данный учебник включает в себя следующие этапы.

1.  [Создание группы продаж с членством][Создание группы продаж с членством]
2.  [Создание ключа для встроенного приложения][Создание ключа для встроенного приложения]
3.  [Добавление общего скрипта, который проверяет членство][Добавление общего скрипта, который проверяет членство]
4.  [Добавление проверки доступа на основе ролей в операции базы данных][Добавление проверки доступа на основе ролей в операции базы данных]
5.  [Тестирование клиентского доступа][Тестирование клиентского доступа]

Для работы с данным учебником требуется следующее:

-   Visual Studio 2013 в Windows 8.1.
-   Прохождение учебника [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности] с использованием поставщика проверки подлинности Azure Active Directory.
-   Прохождение учебника [Скрипты сервера хранилища][Скрипты сервера хранилища] для ознакомления с вариантами использования репозитория Git для хранения скриптов сервера.

## <a name="create-group"></a>Создание группы продаж с членством

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]

## <a name="generate-key"></a>Создание ключа для встроенного приложения

В учебнике [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности] во время выполнения шага [Регистрация для использования входа Azure Active Directory][Регистрация для использования входа Azure Active Directory] вы создали регистрацию для встроенного приложения. В этом разделе предстоит создать ключ для использования при чтении сведений каталога с помощью идентификатора клиента встроенного приложения.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]

## <a name="add-shared-script"></a>Добавление в мобильную службу общего скрипта, который проверяет членство

В этом разделе предстоит использовать Git для развертывания файла общего скрипта с именем *rbac.js* в мобильной службе. Этот файл общего скрипта будет содержать функции, которые используют [API Graph][API Graph] для проверки членства в группе пользователя.

Если вы не знакомы со скриптами развертывания в мобильной службе с помощью Git, ознакомьтесь с учебником [Скрипты сервера хранилища][Скрипты сервера хранилища] перед тем, как перейти к этому разделу.

1.  Создайте файл скрипта с именем *rbac.js* в каталоге *./service/shared/* локального репозитория мобильной службы.
2.  Добавьте следующий скрипт в верхнюю часть файла, которая определяет функцию `getAADToken`. Если заданы значения *tenant\_domain*, встроенное приложение *client id* и приложение *key*, эта функция предоставляет токен доступа Graph, использованный для чтения сведений в каталоге.

    > [WACOM.NOTE] Следует кэшировать токен вместо того, чтобы создавать новый при каждой проверке доступа. Затем обновите кэш, если попытки использовать токен приведут к ответу 401 Authentication\_ExpiredToken, как показано в [справочнике по ошибкам API Graph][справочнике по ошибкам API Graph]. Для простоты это не показано в коде ниже, но количество дополнительного трафика в Active Directory будет уменьшено.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;
        exports.SalesGroup = appSettings.AAD_GROUP_ID;

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

3.  Добавьте следующий код в *rbac.js*, чтобы определить функцию `getGroupId`. Эта функция использует токен доступа для получения идентификатора группы на основе имени группы, использовавшегося в фильтре.

    > [WACOM.NOTE] Этот код ищет группу Active Directory по имени. Во многих случаях лучше будет сохранить идентификатор группы как параметр приложения мобильной службы и просто использовать этот идентификатор группы. Это нужно, поскольку имя группы может измениться, но идентификатор останется прежним. Однако при изменении имени группы обычно меняется по крайней мере область роли, что может также потребовать обновления кода мобильной службы.

        function getGroupId(groupname, accessToken, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/groups" + 
                      "?$filter=displayName%20eq%20'" + groupname + "'" + 
                  "&api-version=2013-04-05" ,
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + accessToken,
                    "Content-Type": "application/json",
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value[0].objectId);
            });
        }

4.  Добавьте следующий код в *rbac.js*, чтобы определить функцию `isMemberOf`, которая вызывает конечную точку [IsMemberOf][IsMemberOf] API REST Graph.

    Эта функция содержит тонкую оболочку вокруг конечной точки [IsMemberOf][IsMemberOf] API REST Graph. Она использует токен доступа Graph для проверки того, содержит ли идентификатор каталога пользователя членство в группе на основе идентификатора группы.

        function isMemberOf(access_token, userObjectId, groupObjectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/isMemberOf" + "?api-version=2013-04-05",
                method: 'POST',
                headers: {
                    "Authorization": "Bearer " + access_token,
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                "groupId": groupObjectId,
                "memberId": userObjectId
                })
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value);
            });
        };

5.  Добавьте следующую экспортированную функцию `checkGroupMembership` в файл *rbac.js* .

    Эта функция заключает в оболочку использование других функций скрипта и экспортируется из общего скрипта для вызова другими скриптами в целях выполнения фактических проверок доступа. Если объект пользователя мобильной службы и идентификатор группы указаны, скрипт вернет идентификатор объекта Azure Active Directory для удостоверения пользователя и проверит членство в группе.

        exports.checkGroupMembership = function (user, groupName, callback) {
            user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) callback(err);
                else getGroupId(groupName, access_token, function (err, groupId) { 
                            if (err) callback(err);
                            else isMemberOf(access_token, objectId, groupId, function (err, isInGroup) {
                                if (err) errorHandler(err);
                                else callback(null, isInGroup);
                            });
                        });
                    });
                },
                error: callback
            });
        }

6.  Сохраните изменения в файле *rbac.js*.

## <a name="add-access-checking"></a>Добавление проверки доступа на основе ролей в операции базы данных

По завершении учебника [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности] вы сможете задать требование проверки подлинности в операциях таблиц, как показано ниже.

![][0]

С каждой операцией базы данных, требующей проверки подлинности, можно добавить скрипты, которые используют объект пользователя для проверок доступа.

В следующих шагах показано, как развернуть контроль доступа на основе ролей с помощью скриптов для каждой операции таблиц в мобильной службе. Скрипт, который использует общий скрипт *rbac.js*, выполняется для каждой операции таблиц.

1.  Добавьте новый файл скрипта с именем *todoitem.insert.js* в каталог *./service/table/* в локальном репозитории Git для мобильной службы. Вставьте следующий скрипт в этот файл.

        function insert(item, user, request) {

            var RBAC = require('../shared/rbac.js');

            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

2.  Добавьте новый файл скрипта с именем *todoitem.read.js* в каталог *./service/table/* в локальном репозитории Git для мобильной службы. Вставьте следующий скрипт в этот файл.

        function read(query, user, request) {

            var RBAC = require('../shared/rbac.js');

            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

3.  Добавьте новый файл скрипта с именем *todoitem.update.js* в каталог *./service/table/* в локальном репозитории Git для мобильной службы. Вставьте следующий скрипт в этот файл.

        function update(item, user, request) {

            var RBAC = require('../shared/rbac.js');

            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

4.  Добавьте новый файл скрипта с именем *todoitem.delete.js* в каталог *./service/table/* в локальном репозитории Git для мобильной службы. Вставьте следующий скрипт в этот файл.

        function del(id, user, request) {

            var RBAC = require('../shared/rbac.js');

            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

5.  В интерфейсе командной строки в репозитории Git добавьте отслеживание файлов скрипта, включенных с помощью выполнения следующей команды.

        git add .

6.  В интерфейсе командной строки в репозитории Git выполните обновления с помощью следующей команды.

        git commit -m "Added role based access control to table operations"

7.  В интерфейсе командной строки репозитория Git разверните обновления в локальном репозитории Git в мобильной службе, выполнив следующую команду. Команда предполагает, что вы выполнили обновления в ветви *master*.

        git push origin master

8.  На [портале управления Azure][портале управления Azure] вы можете перейти к операциям таблиц для мобильной службы и просмотреть обновления, как показано ниже.

    ![][1]

## <a name="test-client"></a>Тестирование клиентского доступа

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [API Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
  [Начало работы с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Создание группы продаж с членством]: #create-group
  [Создание ключа для встроенного приложения]: #generate-key
  [Добавление общего скрипта, который проверяет членство]: #add-shared-script
  [Добавление проверки доступа на основе ролей в операции базы данных]: #add-access-checking
  [Тестирование клиентского доступа]: #test-client
  [Скрипты сервера хранилища]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control/
  [mobile-services-aad-rbac-create-sales-group]: ../includes/mobile-services-aad-rbac-create-sales-group.md
  [Регистрация для использования входа Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [справочнике по ошибкам API Graph]: http://msdn.microsoft.com/ru-ru/library/azure/hh974480.aspx
  [IsMemberOf]: http://msdn.microsoft.com/ru-ru/library/azure/dn151601.aspx
  [0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
  [портале управления Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
  [mobile-services-aad-rbac-test-app]: ../includes/mobile-services-aad-rbac-test-app.md
