### <a name="server-auth"></a>Практическое руководство. Проверка подлинности с помощью поставщика (серверный поток)
Чтобы мобильные приложения могли выполнять процесс проверки подлинности в вашем приложении, необходимо зарегистрировать приложение у поставщика удостоверений. Затем в службе приложений Azure необходимо настроить код приложения и секретный код, предоставленный поставщиком.
Дополнительные сведения см. в учебнике [Добавление проверки подлинности в приложение](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md).

После регистрации у поставщика удостоверений вызовите метод `.login()` с указанием имени вашего поставщика. Например, для входа в систему через Facebook используйте следующий код:

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Допустимые для поставщика значения: aad, facebook, google, microsoftaccount и twitter.

> [!NOTE]
> Сейчас проверка подлинности Google не выполняется через серверный поток.  Для проверки подлинности с помощью Google необходимо использовать [метод клиентского потока](#client-auth).

В этом случае служба приложений Azure управляет потоком проверки подлинности согласно протоколу OAuth 2.0.  Она отображает страницу входа выбранного поставщика и создает маркер проверки подлинности службы приложений после успешного входа с помощью поставщика удостоверений. Функция login после завершения работы возвращает объект JSON, который содержит и идентификатор пользователя, и маркер проверки подлинности службы приложений в полях userId и authenticationToken соответственно. Этот маркер можно поместить в кэш и повторно использовать, пока не истечет срок его действия.

###<a name="client-auth"></a>Практическое руководство. Проверка подлинности с помощью поставщика (клиентский поток)

Приложение может также независимо связаться с поставщиком удостоверений и сообщить возвращаемый маркер вашей службе приложений для проверки подлинности. Этот клиентский поток позволяет пользователям выполнять единый вход или получать дополнительные данные о пользователе от поставщика удостоверений.

#### <a name="social-authentication-basic-example"></a>Простой пример проверки подлинности на основе учетной записи социальной сети

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

#### <a name="microsoft-account-example"></a>Пример учетной записи Майкрософт

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

###<a name="auth-getinfo"></a>Практическое руководство. Получение сведений о пользователе, прошедшем аутентификацию

Сведения о проверке подлинности можно получить из конечной точки `/.auth/me`, выполнив вызов HTTP с помощью любой библиотеки AJAX.  Обязательно настройте заголовок `X-ZUMO-AUTH` для маркера аутентификации.  Маркер аутентификации хранится в `client.currentUser.mobileServiceAuthenticationToken`.  Например, чтобы использовать API выборки:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Компонент выборки предоставляется в виде [пакета NPM](https://www.npmjs.com/package/whatwg-fetch) или для скачивания браузером из [CDNJS](https://cdnjs.com/libraries/fetch). Для получения информации можно также использовать jQuery или другой API AJAX.  Данные получаются в виде объекта JSON.
