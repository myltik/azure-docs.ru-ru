<properties
	pageTitle="Добавление проверки подлинности в Apache Cordova с помощью мобильных приложений | Служба приложений Azure"
	description="Узнайте, как использовать мобильные приложения в службе приложений Azure для аутентификации пользователей приложения Apache Cordova с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт."
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="glenga"/>

# Добавление проверки подлинности в приложение Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## Сводка

В этом учебнике вы добавляете проверку подлинности в учебный проект ToDoList для Apache Cordova с помощью поддерживаемого поставщика удостоверений. Этот учебник создан на основе учебника [Начало работы с мобильными службами], который необходимо изучить в первую очередь.

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка службы приложений

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Видео, демонстрирующее аналогичные действия](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Теперь можно убедиться, что анонимный доступ к серверной части был отключен. В Visual Studio откройте проект, созданный при изучении учебника [Приступая к работе с мобильными приложениями], запустите свое приложение в **эмуляторе Google Android** и убедитесь в том, что при запуске приложения отображается непредвиденный сбой подключения.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из серверной части мобильного приложения.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

1. Откройте проект в **Visual Studio**, а затем откройте файл `www/index.html` для редактирования.

2. Найдите мета-тег `Content-Security-Policy` в разделе head. Добавьте узел OAuth в список допустимых источников.

    | Поставщик | Имя поставщика SDK | Узел OAuth |
    | :--------------------- | :---------------- | :-------------------------- |
    | Azure Active Directory | aad | https://login.windows.net |
    | Facebook | facebook | https://www.facebook.com |
    | Google | google | https://accounts.google.com |
    | Microsoft | microsoftaccount | https://login.live.com |
    | Twitter | twitter | https://api.twitter.com |

    Пример политики безопасности содержимого (для Azure Active Directory):

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
			data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    Замените `https://login.windows.net` узлом OAuth из приведенной выше таблицы. Дополнительные сведения об этом метатеге см. в [документации по политике безопасности содержимого].

    Обратите внимание на то, что некоторые поставщики проверки подлинности не требуют изменений в политике безопасности содержимого при использовании на соответствующих мобильных устройствах. Например, изменения в политики безопасности содержимого не требуются, если на устройстве Android используется проверка подлинности Google.

3. Откройте файл `www/js/index.js` для редактирования, найдите метод `onDeviceReady()` и добавьте следующее в код создания клиента:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Обратите внимание, что этот код заменит существующий код, который создает ссылку на таблицу и обновляет пользовательский интерфейс.

    Метод login() запускает аутентификацию с использованием поставщика. Метод login() — это асинхронная функция, которая возвращает обещание JavaScript. Остальная часть инициализации помещается в ответ обещания и до завершения метода login() не выполняется.

4. В коде, который вы только что добавили, замените `SDK_Provider_Name` именем своего поставщика входа. Например, для Azure Active Directory используйте `client.login('aad')`.

4. Запустите проект. Когда инициализация проекта будет завершена, в приложении откроется страница входа OAth для выбранного поставщика проверки подлинности.

##<a name="next-steps"></a>Дальнейшие действия

* Узнайте больше об [аутентификации] с использованием службы приложений Azure.
* Продолжите работу с учебником, добавив в приложение Apache Cordova [push-уведомления].

Подробнее об использовании пакетов SDK.

* [Пакет SDK для Apache Cordova]
* [Серверный пакет SDK для ASP.NET]
* [Серверный пакет SDK для Node.js]

.<!-- URLs. -->
[Начало работы с мобильными службами]: app-service-mobile-cordova-get-started.md
[Приступая к работе с мобильными приложениями]: app-service-mobile-cordova-get-started.md
[документации по политике безопасности содержимого]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[push-уведомления]: app-service-mobile-cordova-get-started-push.md
[аутентификации]: app-service-mobile-auth.md
[Пакет SDK для Apache Cordova]: app-service-mobile-codova-how-to-use-client-library.md
[Серверный пакет SDK для ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Серверный пакет SDK для Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0817_2016-->