
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Использование библиотеки проверки подлинности Майкрософт для получения маркера для API Microsoft Graph

Добавьте в страницу `index.html` в теге `<body>` следующий код:

```html
<script type="text/javascript">

    var graphAPIMeEndpoint = "https://graph.microsoft.com/v1.0/me";
    var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

    // Initialize application
    var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID);

    // Set redirect URI
    userAgentApplication.redirectUri = msalconfig.redirectUri;

    displayUserInfo();

    function displayUserInfo() {
        var user = userAgentApplication.getUser();
        if (user) {
            // Display the user info
            var userInfoElement = document.getElementById("userInfo");
            userInfoElement.parentElement.classList.remove("hidden");
            userInfoElement.innerHTML = JSON.stringify(user, null, 4);

            // Show Sign-Out button
            document.getElementById("signOutButton").classList.remove("hidden");
        }
    }

    function callGraphAPI() {
        if (userAgentApplication.getAllUsers().length === 0) {
            userAgentApplication.loginPopup()
                .then(function (token) {
                    console.log(token);
                    displayUserInfo();
                    callGraphAPI();
                }, function (error) {
                    showError("login", error, document.getElementById("errorMessage"));
                });
        } else {
            var responseElement = document.getElementById("graphResponse");
            responseElement.parentElement.classList.remove("hidden");
            responseElement.innerText = "Calling Graph ...";
            callWebApiWithScope(graphAPIMeEndpoint,
                graphAPIScopes,
                responseElement,
                document.getElementById("errorMessage"),
                document.getElementById("accessToken"));
        }
    }

    function callWebApiWithScope(endpoint, scope, responseElement, errorElement, showTokenElement) {
        userAgentApplication.acquireTokenSilent(scope)
            .then(function (token) {
                callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
            }, function (error) {
                if (error.indexOf("interaction_required" !== -1)) {
                    userAgentApplication.acquireTokenPopup(scope).then(function(token) {
                            callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
                        },
                        function(error) {
                            showError(endpoint, error, errorElement);
                        });
                } else {
                    showError(endpoint, error, errorElement);
                }
            });
    }

    function showAPIResponse(data, token, responseElement, showTokenElement) {
        console.log(data);
        responseElement.innerHTML = JSON.stringify(data, null, 4);
        if (showTokenElement) {
            showTokenElement.parentElement.classList.remove("hidden");
            showTokenElement.innerHTML = token;
        }
    }

    function showError(endpoint, error, errorElement) {
        console.error(error);
        var formattedError = JSON.stringify(error, null, 4);
        if (formattedError.length < 3) {
            formattedError = error;
        }
        errorElement.innerHTML = "Error calling " + endpoint + ": " + formattedError;
    }
</script>
```

<!--start-collapse-->
### <a name="more-information"></a>Дополнительные сведения
#### <a name="sign-in-the-user"></a>Вход пользователя
Когда пользователь впервые нажимает кнопку "Call Microsoft Graph API" (Вызвать API Microsoft Graph), выполняется метод `loginPopup` для входа пользователя в систему. При вызове этого метода откроется окно браузера, в котором пользователю предлагается выполнить вход. В результате успешного входа в систему информация о пользователе кэшируется MSAL и возвращается маркер. Этот маркер известен как *маркер идентификатора*. Он содержит основные сведения о пользователе, такие как отображаемое имя пользователя. Если вы планируете использовать какие-либо данные, предоставляемые этим маркером, то необходимо убедиться, что маркер проверен внутренним сервером. Это позволит гарантировать, что маркер был выдан допустимому пользователю для вашего приложения.

Одностраничное приложение, создаваемое в этом руководстве, не использует маркер идентификатора напрямую. Вместо этого оно вызывает `acquireTokenSilent` и (или) `acquireTokenPopup` для получения *маркера доступа*, используемого для запроса API Microsoft Graph. Если вам нужен пример, проверяющий маркер идентификатора, то ознакомьтесь с [этим](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Пример active-directory-javascript-singlepageapp-dotnet-webapi-v2 на портале GitHub") примером приложения на портале GitHub. В нем для проверки маркеров используется веб-API ASP.NET.

#### <a name="getting-a-user-token-interactively"></a>Интерактивное получение маркера пользователя
При вызове метода `acquireTokenPopup(scope)` откроется окно браузера, в котором пользователю предлагается выполнить вход. Приложениям обычно требуется, чтобы пользователь выполнял вход интерактивно при первом доступе к защищенному ресурсу или при сбое автоматической операции для получения маркера (например, срок действия пароля пользователя истек).

#### <a name="getting-a-user-token-silently"></a>Автоматическое получение маркера пользователя
Метод ` acquireTokenSilent` обрабатывает получение и обновление маркера без участия пользователя. После того как `loginPopup` будет выполнен в первый раз, обычно используется метод `acquireTokenSilent`, чтобы получить маркеры для доступа к защищенным ресурсам для последующих вызовов (например, автоматическое выполнение запросов или обновлений маркеров).
В конечном счете в `acquireTokenSilent` произойдет сбой, например в случае смены пароля пользователем на другом устройстве. Приложение может обработать это исключение двумя способами:

1.  Вызвать `acquireTokenPopup` немедленно, в результате чего пользователю будет предложено выполнить вход. Этот шаблон обычно используется в интерактивных приложениях, где пользователю недоступно автономное содержимое. Пример, созданный в ходе пошаговой настройки, использует этот шаблон.
2. Приложения также могут визуально уведомить пользователя, что требуется интерактивный вход, чтобы пользователь мог выбрать подходящее время для входа или приложение могло повторить метод `acquireTokenSilent` ​​позднее. Обычно это применимо для тех случаев, когда пользователь может использовать другие функции приложения, на которые это не влияет, например, когда в приложении есть автономное содержимое. В этом случае пользователь может решить, когда он хочет войти, чтобы получить доступ к защищенному ресурсу или обновить устаревшие данные, или ваше приложение может решить повторить `acquireTokenSilent` при восстановлении сети ​​после временной недоступности.

Другой сценарий, при котором метод `acquireTokenSilent` заканчивается сбоем, это когда пользователь еще не дал согласие на область, запрошенную с помощью вызова. Например, когда пользователь впервые выполняет приложение, созданное в данном руководстве, область *user.read* предоставляется в качестве параметра. Так как выполнивший вход пользователь никогда не давал согласие на чтение своего профиля, то метод `acquireTokenSilent` создаст исключение, содержащее `interaction_required`. Это, как правило, указывает на то, что для решения данной проблемы необходимо вызвать метод `acquireTokenPopup`. В этом случае всплывающее окно будет создано, чтобы отобразить экран согласия для необходимой области.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Вызов API Microsoft Graph с помощью полученного маркера

Добавьте в страницу index.html в теге `<body>` следующий код:

```html
 <script type="text/javascript">
function callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    // Note that fetch API is not available in all browsers
    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showAPIResponse(data, token, responseElement, showTokenElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showError(endpoint, data, errorElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error, errorElement);
        });
}
</script>
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Дополнительные сведения о вызове REST через защищенный API

В примере приложения, созданном в этом руководстве, метод `callWebApiWithToken()` выполняет HTTP-запрос `GET` к защищенному ресурсу, требующему маркер, а затем возвращает содержимое вызывающему объекту. Этот метод добавляет полученный маркер в *заголовок авторизации HTTP*. Для примера приложения, созданного в этом руководстве, ресурс — это конечная точка *me* API Microsoft Graph, которая отображает сведения о профиле пользователя.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Добавление метода для выхода пользователя

Добавьте в страницу index.html в теге `<body>` следующий код:

```html
 <script type="text/javascript">
    function signOut() {
        userAgentApplication.logout();
    }
</script>
```
