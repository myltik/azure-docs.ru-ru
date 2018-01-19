## <a name="test-your-code"></a>Тестирование кода

### <a name="test-with-visual-studio"></a>Тестирование кода в Visual Studio
Если вы используете Visual Studio, то для запуска проекта нажмите клавишу **F5**. В браузере откроется адрес http://<span></span>localhost:{port}, где можно увидеть кнопку **Call Microsoft Graph API** (Вызов Microsoft Graph API).

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>Тестирование кода с помощью Python или других веб-серверов
Если вы не используете Visual Studio, убедитесь, что веб-сервер запущен. Настройте для сервера ожидание передачи данных через TCP-порт на основе расположения файла **index.html**. При использовании Python начните передавать данные через порт, запустив терминал командной строки из папки приложения:
 
```bash
python -m http.server 8080
```
Откройте браузер и введите http://<span></span>localhost:8080 или http://<span></span>localhost:{port}, где **port** соответствует порту, с которого веб-сервер ожидает передачи данных. Появится содержимое файла index.html с кнопкой **Call Microsoft Graph API** (Вызвать Microsoft Graph API).

## <a name="test-your-application"></a>Тестирование приложения

После загрузки файла index.html в браузере выберите **Call Microsoft Graph API** (Вызов API Microsoft Graph). При первом запуске приложения браузер перенаправит вас к конечной точке Microsoft Azure Active Directory (Azure AD) версии 2.0, где вам будет предложено войти:
 
![Вход в учетную запись JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Предоставление разрешения на доступ к приложению

После первого входа в приложение предоставьте ему разрешение на использование данных вашего профиля для входа:

![Предоставление разрешения на доступ к приложению](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Просмотр результатов приложения
После входа информация о профиле пользователя должна отобразиться в поле **Graph API Call Response** (Ответ на вызов API Graph).
 
![Ожидаемые результаты после вызова Microsoft Graph API](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

Должны отобразиться основные сведения о маркере, полученные с помощью полей **Маркер доступа** и **ID Token Claims** (Утверждения маркера идентификатора).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Для чтения профиля пользователя API Microsoft Graph требуется область **user.read**. По умолчанию эта область автоматически добавляется в каждое приложение, зарегистрированное на портале регистрации. Для других API Microsoft Graph, а также для пользовательских API вашего внутреннего сервера, могут потребоваться дополнительные области. Для отображения списка календарей пользователя API Microsoft Graph требуется область **Calendars.Read**.

Чтобы из контекста приложения получить доступ к календарям пользователя, добавьте делегированное разрешение **Calendars.Read** в сведения о регистрации приложения. Затем добавьте область **Calendars.Read** в вызов **acquireTokenSilent**. 

>[!NOTE]
>При увеличении количества областей от пользователя могут потребоваться дополнительные согласия.

Если серверный API не требует области (что не рекомендуется), в качестве области можно использовать **clientId** в вызовах **acquireTokenSilent** и **acquireTokenRedirect**.

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
