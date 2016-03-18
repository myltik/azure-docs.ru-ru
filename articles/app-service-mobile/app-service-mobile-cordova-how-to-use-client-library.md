<properties
	pageTitle="Использование подключаемого модуля Apache Cordova для мобильных приложений Azure"
	description="Использование подключаемого модуля Apache Cordova для мобильных приложений Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="adrianha"/>

# Использование клиентской библиотеки Apache Cordova для мобильных приложений Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

В данном руководстве показано, как реализовать типичные сценарии с использованием последней версии [подключаемого модуля Apache Cordova для мобильных приложений Azure]. Если вы не знакомы с мобильными приложениями Azure, изучите статью [Быстрый запуск мобильного приложения Azure], чтобы создать серверную часть и таблицу, а также скачать предварительно собранный проект Apache Cordova. В данном руководстве мы сосредоточимся на клиентской части подключаемого модуля Apache Cordova.

##<a name="Setup"></a>Настройка и необходимые компоненты

В данном руководстве предполагается, что вы уже создали серверную часть с таблицей. В этом руководстве предполагается, что в таблице используется та же схему, что и в таблицах, приведенных в этих учебниках. В этом руководстве также предполагается, что подключаемый модуль Apache Cordova уже добавлен в код. Если это не так, добавьте подключаемый модуль Apache Cordova в проект через командную строку:

```
cordova plugin add ms-azure-mobile-apps
```

Дополнительные сведения о создании [первого приложения Apache Cordova] см. в соответствующей документации.

##<a name="create-client"></a>Создание клиента

Создание подключения клиента путем создания объекта `WindowsAzure.MobileServicesClient`. Замените `appUrl` на URL-адрес в своем мобильном приложении.

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>Практическое руководство. Создание ссылки на таблицу

Для доступа к данным или их обновления создайте ссылку на таблицу серверной части. Замените `tableName` на имя таблицы.

```
var table = client.getTable(tableName);
```

##<a name="querying"></a>Практическое руководство. Запрос ссылки на таблицу

Ссылку на таблицу можно использовать для запроса данных на сервере. Запросы осуществляются на языке типа LINQ. Чтобы вернуть все данные из таблицы, используйте следующий код:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

Выполненная функция вызывается с результатами. Не используйте в этой функции свойство `for (var i in results)` — оно вызывает перебор данных, включаемых в результаты применения других функций запросов (таких как `.includeTotalCount()`).

Дополнительные сведения о синтаксисе запроса см. в [документации по объектам запросов].

### Фильтрация данных на сервере

Используйте условие `where` в ссылке на таблицу:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Также можно использовать функцию фильтрации объектов. В этом случае переменной `this` присваивается текущий объект фильтрации. Представленный ниже код функционально эквивалентен предыдущему примеру:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

### Разбиение данных по страницам

Используйте методы take() и skip(). Например, если вы хотите разбить на строки таблицу, содержащую 100 записей:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

Метод `.includeTotalCount()` добавляет в объект результатов поле totalCount. В этом поле указывается общее количество записей, которое будет возвращено, если данные не разбиты на страницы.

После этого можно настроить список страниц, используя переменную pages и определенные кнопки пользовательского интерфейса, и загрузить новые данные на каждой странице с помощью метода loadPage(). Чтобы ускорить доступ к уже загруженным записям, следует добавить кэширование.


###<a name="sorting-data"></a>Практическое руководство. Возврат отсортированных данных

Используйте методы запросов .orderBy() или .orderByDescending():

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Дополнительные сведения об объекте запроса см. в [документации по объектам запросов].

##<a name="inserting"></a>Практическое руководство. Вставка данных

Создайте объект JavaScript с соответствующей датой и асинхронно вызовите метод table.insert():

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Вставленный элемент возвращается с дополнительными полями, необходимыми для операций синхронизации. Обновите свой кэш, используя эти данные для последующих обновлений.

Обратите внимание на то, что серверный пакет SDK мобильных приложений Azure для Node.js поддерживает динамическую схему для целей разработки. При использовании динамической схемы схема таблицы обновляется на ходу, что позволяет добавлять в нее столбцы, просто указав их в операции вставки или обновления данных. Перед переносом приложения в рабочую среду динамическую схему рекомендуется отключить.

##<a name="modifying"></a>Практическое руководство. Изменение данных

Как и при использовании метода .insert(), в данном случае следует создать объект обновления и вызвать метод .update(). Объект обновления должен содержать идентификатор обновляемой записи обновления, получаемый при чтении записи или при вызове метода .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

##<a name="deleting"></a>Практическое руководство. Удаление данных

Для удаления записи вызовите метод .del(). В ссылке на объект укажите идентификатор:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

##<a name="auth"></a>Практическое руководство. Проверка подлинности пользователей

Служба приложений Azure поддерживает проверку подлинности и авторизацию пользователей с помощью различных внешних поставщиков идентификаторов: Facebook, Google, учетная запись Майкрософт и Twitter. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в учебнике [Приступая к работе с проверкой подлинности].

Если в приложении Apache Cordova используется проверка подлинности, должны быть доступны следующие подключаемые модули Cordova:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Поддерживается два потока аутентификации: серверный и клиентский. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, такими как единый вход, так как использует пакеты SDK конкретного поставщика для конкретного устройства.

##<a name="server-auth"></a>Практическое руководство. Проверка подлинности с помощью поставщика (серверный поток)

Чтобы мобильная служба могла выполнять процесс проверки подлинности в вашем приложении для магазина Windows или для приложения HTML 5, необходимо зарегистрировать приложение у поставщика удостоверений. Затем в службе приложений Azure необходимо настроить код приложения и секретный код, предоставленный поставщиком. Дополнительные сведения см. в учебнике [Добавление проверки подлинности в приложение].

После регистрации у поставщика удостоверений просто вызовите метод .login() с указанием имени вашего поставщика. Например, для входа в систему через Facebook используйте следующий код.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Если используется поставщик удостоверений, отличный от Facebook, измените значение, передаваемое в метод login выше, на одно из следующих: `microsoftaccount`, `facebook`, `twitter`, `google` или `aad`.

В этом случае служба приложений Azure управляет потоком проверки подлинности OAuth 2.0, отображая страницу входа выбранного поставщика и генерируя маркер проверки подлинности службы приложений после успешного соединения с поставщиком удостоверений. Функция login после завершения работы возвращает объект JSON (user), который содержит и идентификатор пользователя, и маркер проверки подлинности службы приложений в полях userId, и authenticationToken соответственно. Этот маркер можно кэшировать и повторно использовать до истечения срока его действия.

##<a name="client-auth"></a>Практическое руководство. Проверка подлинности с помощью поставщика (клиентский поток)

Приложение может также независимо связаться с поставщиком удостоверений и сообщить возвращаемый маркер вашей службе приложений для проверки подлинности. Этот клиентский поток позволяет пользователям выполнять единый вход или получать дополнительные данные о пользователе от поставщика удостоверений.

### Простой пример проверки подлинности на основе учетной записи социальной сети

В этом примере используется пакет SDK для клиента Facebook для проверки подлинности:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
В этом примере предполагается, что маркер, предоставленный соответствующим поставщиком SDK, сохраняется в переменной token.

### Пример учетной записи Майкрософт

В следующем примере используется пакет SDK Live, поддерживающий единый вход в приложения Магазина Windows с использованием учетной записи Майкрософт:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

Этот пример получает маркер из Live Connect, который предоставляется вашей службе приложений путем вызова функции login.

##<a name="auth-getinfo"></a>Практическое руководство. Получение сведений о пользователе, прошедшем проверку подлинности

Сведения о проверке подлинности текущего пользователя можно получить из конечной точки `/.auth/me` с помощью метода AJAX. Например, чтобы использовать API выборки:

```
var url = client.applicationUrl + '/.auth/me';
fetch(url)
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Для получения информации можно также использовать jQuery или другой API AJAX. Данные будут получены в виде объекта JSON.

##<a name="register-for-push"></a>Практическое руководство. Регистрация для получения push-уведомлений

Для обработки push-уведомлений установите [phonegap plugin-push]. Его можно легко добавить с помощью команды `cordova plugin add` в командной строке или установщика подключаемых модулей Git в Visual Studio. Следующий код в приложении Apache Cordova регистрирует устройство для получения push-уведомлений:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Для отправки push-уведомления с сервера используется пакет SDK для центров уведомлений. Не следует отправлять push-уведомление непосредственно из клиентов, так как это может использоваться для проведения атак типа "отказ в обслуживании" на центры уведомлений или PNS.

<!-- URLs. -->
[Быстрый запуск мобильного приложения Azure]: app-service-mobile-cordova-get-started.md
[Приступая к работе с проверкой подлинности]: app-service-mobile-cordova-get-started-users.md
[Добавление проверки подлинности в приложение]: app-service-mobile-cordova-get-started-users.md

[подключаемого модуля Apache Cordova для мобильных приложений Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[первого приложения Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[документации по объектам запросов]: https://msdn.microsoft.com/ru-RU/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0224_2016-->