---
title: Приступая к работе с Azure AD для Cordova | Документация Майкрософт
description: Практическое руководство по созданию приложения Cordova, которое интегрируется с Azure AD для входа в систему и вызывает программные интерфейсы приложения, защищенные Azure AD, по протоколу OAuth.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: b1a8d7bd-7ad6-44d5-8ccb-5255bb623345
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 6d6d514875aa675bf160ee08a3e94b58944020ee
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156737"
---
# <a name="azure-ad-cordova-getting-started"></a>Начало работы с Azure AD для Cordova
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Вы можете использовать Apache Cordova для разработки приложений на HTML5 и JavaScript, которые могут работать на мобильных устройствах как полноценные приложения. Azure Active Directory (Azure AD) позволяет добавлять в приложения Cordova возможности проверки подлинности уровня предприятия.

Подключаемый модуль Cordova использует собственные пакеты SDK Azure AD для приложений iOS, Android, Магазина Windows и Windows Phone. Этот подключаемый модуль позволяет расширить функции приложения, обеспечивая реализацию входа пользователей с учетными записями Windows Server Active Directory, получение доступа к Office 365 и API Azure, а также защиту вызовов пользовательского веб-API.

В данном руководстве подключаемый модуль Apache Cordova будет использоваться для библиотеки аутентификации Active Directory (ADAL), чтобы добавить в простое приложение следующие возможности:

* реализация проверки подлинности пользователя и получения маркера путем добавления всего нескольких строк кода;
* вызов API Graph для запроса сведений из каталога и отображения результатов с использованием этого маркера; 
* минимизация количества запросов пользователя на ввод данных для проверки подлинности за счет использования кэша маркера библиотеки ADAL.

Чтобы получить эти возможности, необходимо:

1. зарегистрировать приложение в Azure AD;
2. Добавить в приложение код для запроса маркеров.
3. Добавить код, использующий маркер для запросов Graph API и отображения результатов.
4. Создать проект развертывания Cordova на всех целевых платформах, добавить подключаемый модуль Cordova ADAL и протестировать решение в эмуляторах.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим учебником необходимы указанные ниже компоненты.

* Клиент Azure AD с учетной записью с правами на разработку приложений.
* Среда разработки, настроенная для использования Apache Cordova. 

При наличии этих компонентов вы можете приступить непосредственно к выполнению этапа 1.

Если у вас еще нет клиента Azure AD, узнайте, [как его получить](active-directory-howto-tenant.md).

Если на компьютере не установлена платформа Apache Cordova, установите следующие компоненты:

* [Git.](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org/download/)
* [Cordova CLI](https://cordova.apache.org/) (можно легко установить через диспетчер пакетов NPM: `npm install -g cordova`).

Компоненты, описанные выше, должны работать как на ПК, так и на компьютере Mac.

У каждой целевой платформы свои требования.

* Чтобы создать и запустить приложение для планшетов и компьютеров с Windows или Windows Phone, сделайте следующее.
  * Установите [Visual Studio 2013 для Windows с обновлением 2 или более поздней версии](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express или другая версия) или [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-community).

* Чтобы создать и запустить приложение для iOS, сделайте следующее.

  * Установите Xcode 6.x или более поздней версии. Вы можете ее скачать на [сайте Apple Developer](http://developer.apple.com/downloads) или из [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  * Установите [ios-sim](https://www.npmjs.org/package/ios-sim). Его можно использовать для запуска приложений iOS в эмуляторе iOS из командной строки. (Для быстрой установки приложения используйте терминал `npm install -g ios-sim`.)
* Чтобы создать и запустить приложение для Android, сделайте следующее.

  * Установить [пакет средств разработки Java (JDK) версии 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) или более поздней. Убедитесь, что `JAVA_HOME` (переменная среды) указывает путь установки JDK (например, C:\Program Files\Java\jdk1.7.0_75).
  * Установите [пакет SDK для Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) и добавьте `<android-sdk-location>\tools`расположение (например, C:\tools\Android\android-sdk\tools) в переменную среды `PATH`.
  * Откройте диспетчер пакетов SDK для Android (например, через терминал `android`) и установите:
    * *Android 5.0.1 (API уровня 21)* ;
    * *Android SDK Build Tools* версии 19.1.0 или более поздней;
    * *Android Support Repository* (дополнительно).

  Пакет SDK для Android по умолчанию не содержит эмулятор. Чтобы запустить приложение Android в эмуляторе, необходимо создать эмулятор, выполнив команду `android avd` из терминала, а затем выбрав **Create** (Создать). Рекомендуемый уровень API — 19 или выше. Дополнительные сведения об эмуляторе Android и параметрах его создания см. в документе [Create and Manage Virtual Devices](http://developer.android.com/tools/help/avd-manager.html) (Создание и управление виртуальными устройствами) на сайте Android.

## <a name="step-1-register-an-application-with-azure-ad"></a>Шаг 1. Регистрация приложения в Azure AD
Этот шаг не является обязательным. В руководстве представлены заранее подготовленные значения, позволяющие наблюдать пример в действии без какой-либо подготовки вашего клиента. Однако мы все же рекомендуем пройти данный этап и ознакомиться с процессом, так как это потребуется вам при создании собственных приложений.

Azure AD выдает маркеры только для знакомых приложений. Прежде чем использовать службу Azure AD из приложения, вы должны создать для него запись в собственном клиенте. Чтобы зарегистрировать новое приложение в клиенте:

1. Войдите на [портале Azure](https://portal.azure.com).
2. На верхней панели щелкните свою учетную запись. В списке **Каталог** выберите клиент Azure AD, в котором будет зарегистрировано приложение.
3. В области слева щелкните **Все службы** и выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений**, а затем выберите **Добавить**.
5. Следуя инструкциям, создайте **собственное клиентское приложение**. (Хотя приложения Cordova основаны на HTML, мы создаем собственное клиентское приложение. Чтобы оно работало, должен быть выбран параметр **Собственное клиентское приложение**.)
  * **Имя** приложения является его описанием для пользователей.
  * **URI перенаправления** — это универсальный код ресурса (URI), используемый для возврата маркеров приложению. Укажите **http://MyDirectorySearcherApp**.

Когда вы завершите регистрацию, Azure AD присвоит приложению уникальный идентификатор приложения. Это значение понадобится вам в следующих разделах. Его можно найти на вкладке только что созданного приложения.

Для запуска `DirSearchClient Sample` предоставьте созданному приложению разрешение на запрос API Graph Azure AD.

1. На странице **Параметры** выберите **Необходимые разрешения** и щелкните **Добавить**. 
2. Для приложения Azure Active Directory выберите в качестве интерфейса API **Microsoft Graph** и добавьте разрешение **Access the directory as the signed-in user** (Доступ к каталогу в качестве пользователя, выполнившего вход) в списке **Делегированные разрешения**. Это позволит приложению запрашивать API Graph для пользователей.

## <a name="step-2-clone-the-sample-app-repository"></a>Шаг 2. Клонирование примера репозитория приложений
Введите следующую команду из оболочки или командной строки:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="step-3-create-the-cordova-app"></a>Шаг 3. Создание приложения Cordova
Существует несколько способов создания приложений Cordova. В данном руководстве используется интерфейс командной строки (CLI) Cordova.

1. Введите следующую команду из оболочки или командной строки:

        cordova create DirSearchClient

   Эта команда создаст структуру папок и шаблон для проекта Cordova.

2. Перейдите в новую папку DirSearchClient.

        cd .\DirSearchClient

3. Скопируйте содержимое начального проекта во вложенную папку www с помощью диспетчера файлов или следующей команды из оболочки:

  * Windows: `xcopy ..\NativeClient-MultiTarget-Cordova\DirSearchClient www /E /Y`
  * Mac: `cp -r  ../NativeClient-MultiTarget-Cordova/DirSearchClient/* www`

4. Добавьте разрешенный подключаемый модуль. Это необходимо для вызова API Graph.

        cordova plugin add cordova-plugin-whitelist

5. Добавьте все платформы, которые необходимо поддерживать. Чтобы получить работающий пример, необходимо выполнить хотя бы одну из приведенных ниже команд. Имейте в виду, что вы не сможете выполнить эмуляцию iOS в Windows или эмуляцию Windows в Mac.

        cordova platform add android
        cordova platform add ios
        cordova platform add windows

6. Добавьте в проект библиотеку ADAL для подключаемого модуля Cordova.

        cordova plugin add cordova-plugin-ms-adal

## <a name="step-4-add-code-to-authenticate-users-and-obtain-tokens-from-azure-ad"></a>Шаг 4. Добавление кода аутентификации пользователей и получение маркеров из Azure AD
Приложение, которое разрабатывается в данном руководстве, предоставляет базовую функцию поиска в каталоге. Пользователь может ввести псевдоним любого пользователя в каталоге и просмотреть некоторые основные атрибуты. Начальный проект содержит определение базового пользовательского интерфейса приложения (в файле www/index.html) и шаблона, который реализует циклы обработки основных событий приложения, привязку интерфейса пользователя и логику отображения результатов (в файле www/js/index.js). Вам остается только добавить логику, реализующую задачи по работе с удостоверениями.

Первое, что необходимо сделать — это вставить в код значения протокола, используемые Azure AD для идентификации вашего приложения и целевых ресурсов. Позже эти значения будут использоваться для создания запросов маркеров. Вставьте следующий фрагмент в самое начала файла index.js.

```javascript
var authority = "https://login.microsoftonline.com/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

Значения `redirectUri` и `clientId` должны совпадать со значениями, описывающими приложение в Azure AD. Их можно найти на вкладке **Настройка** на портале Azure, как описано на этапе 1.

> [!NOTE]
> Если вы решили не регистрировать новое приложение в собственном клиенте, вы можете просто вставить предварительно настроенные значения "как есть". Это позволит вам увидеть работу примера, но вам всегда придется указывать собственные значения для рабочих приложений.

Далее добавьте код запроса маркера. Вставьте следующий фрагмент кода между определениями `search` и `renderData`.

```javascript
    // Shows the user authentication dialog box if required
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Рассмотрим эту функцию, разбив ее на две основные части.
Данный пример предназначен для работы с любым клиентом, а не определенным. Он использует конечную точку "/common", которая позволяет пользователю ввести данные любой учетной записи во время проверки подлинности и направляет запрос соответствующему клиенту.

Первая часть метода проверяет кэш ADAL на наличие хранимых маркеров. Если таковые имеются, то метод использует клиент, от которого поступил данный маркер, для повторной инициализации ADAL. Необходимо избегать дополнительных запросов, так как использование конечной точки "/common" всегда приводит к просьбе пользователю ввести новые данные учетной записи.

```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
Вторая часть метода выполняет непосредственно запрос маркера. Метод `acquireTokenSilentAsync` запрашивает ADAL вернуть маркер для указанного ресурса без отображения диалогового окна ввода учетных данных. Диалоговое окно не отображается, если в кэше уже имеется подходящий хранимый маркер доступа или если существует маркер обновления, используемый для получения нового токена доступа без запроса пользователя. Если получить действительный маркер не удается, будет вызвана функция `acquireTokenAsync`, которая через диалоговое окно запросит у пользователя данные для проверки подлинности.

```javascript
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Теперь, когда у нас есть маркер, мы можем, наконец, вызвать API Graph и выполнить требуемую операцию поиска. Вставьте следующий фрагмент сразу под определением `authenticate`.

```javascript
// Makes an API call to receive the user list
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Файлы начальной точки содержат соответствующую поддержку для ввода псевдонима пользователя в текстовое поле. Данный метод использует значение псевдонима для создания запроса, сочетания его с маркером доступа, отправки в Microsoft Graph и анализа результатов. Метод `renderData`, который уже присутствует в файле начальной точки, отвечает за отображение результатов.

## <a name="step-5-run-the-app"></a>Шаг 5. Запуск приложения
Теперь вы можете запустить приложение. Процесс работы прост: после запуска приложения введите псевдоним нужного пользователя, а затем нажмите кнопку. Будет предложено ввести учетные данные для проверки подлинности. После ее завершения и успешного поиска будут отображены атрибуты нужного пользователя.

Последующие запуски будут выполнять поиск без отображения запроса благодаря присутствию в кэше маркера, полученного ранее.

Конкретные этапы запуска приложения зависят от платформы.

### <a name="windows-10"></a>Windows 10
   Планшет или ПК: `cordova run windows --archs=x64 -- --appx=uap`

   Мобильное устройство (требуется мобильное устройство с Windows 10, подключенное к компьютеру): `cordova run windows --archs=arm -- --appx=uap --phone`

   > [!NOTE]
   > Во время первого запуска может потребоваться выполнить вход для предоставления системе лицензии разработчика. Дополнительные сведения см. в статье [Получение лицензии разработчика](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-81-tabletpc"></a>Планшеты или компьютеры с Windows 8.1
   `cordova run windows`

   > [!NOTE]
   > Во время первого запуска может потребоваться выполнить вход для предоставления системе лицензии разработчика. Дополнительные сведения см. в статье [Получение лицензии разработчика](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-phone-81"></a>Windows Phone 8.1
   Запустить приложение на подключенном устройстве: `cordova run windows --device -- --phone`

   Запустить приложение в эмуляторе по умолчанию: `cordova emulate windows -- --phone`

   Используйте `cordova run windows --list -- --phone` для просмотра всех доступных целевых объектов и `cordova run windows --target=<target_name> -- --phone` —для запуска приложения на конкретном устройстве или эмуляторе (например, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

### <a name="android"></a>Android
   Запустить приложение на подключенном устройстве: `cordova run android --device`

   Запустить приложение в эмуляторе по умолчанию: `cordova emulate android`

   Убедитесь, что экземпляр эмулятора создан, использовав диспетчер AVD, как описано в разделе "Предварительные требования".

   Используйте `cordova run android --list` для просмотра всех доступных целевых объектов и `cordova run android --target=<target_name>` —для запуска приложения на конкретном устройстве или эмуляторе (например, `cordova run android --target="Nexus4_emulator"`).

### <a name="ios"></a>iOS
   Запустить приложение на подключенном устройстве: `cordova run ios --device`

   Запустить приложение в эмуляторе по умолчанию: `cordova emulate ios`

   > [!NOTE]
   > Убедитесь, что пакет `ios-sim` установлен для запуска в эмуляторе. Дополнительные сведения см. в разделе "Предварительные требования".

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run the application on specific device or emulator (for example, `cordova run android --target="iPhone-6"`).

    Use `cordova run --help` to see additional build and run options.

## <a name="next-steps"></a>Дополнительная информация
Готовый пример (для которого лишь осталось задать конфигурацию) можно найти в [репозитории GitHub](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).

Теперь вы можете приступить к более сложным и содержательным сценариям. Дополнительные сведения см. в статье [Приступая к работе с веб-интерфейсом API для узла](active-directory-devquickstarts-webapi-nodejs.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
