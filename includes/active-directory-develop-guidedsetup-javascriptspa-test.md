## <a name="test-your-code"></a>Тестирование кода

### <a name="test-with-visual-studio"></a>Тестирование с помощью Visual Studio
Если вы используете Visual Studio, нажмите клавишу **F5** для запуска проекта. Откроется браузер http://<span></span>localhost: {port} расположение отобразится **вызова API Graph Microsoft** кнопки.

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>Тестирование с помощью Python или других веб-сервера
Если вы не используете Visual Studio, убедитесь, что веб-сервера запущена. Настройка сервера для прослушивания TCP-порт, который основан на расположение вашей **index.html** файла. Для Python начало прослушивает порт, выполнив терминалов командной строки из папки приложения.
 
```bash
python -m http.server 8080
```
Откройте браузер и введите http://<span></span>localhost: 8080 или http://<span></span>localhost: {port} где **порт** — это порт, слушать веб-сервера. Вы увидите содержимое файла index.html и **вызова API Graph Microsoft** кнопки.

## <a name="test-your-application"></a>Тестирование приложения

После загрузки файла index.html обозревателя выберите **вызова API Graph Microsoft**. При первом запуске приложения, браузер перенаправляет v2.0 конечной точки Microsoft Azure Active Directory (Azure AD) и будет предложено войти в:
 
![Войдите учетную запись JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Предоставить разрешения для доступа к приложениям

При первом входе в приложение, появится запрос на подтверждение, чтобы разрешить приложению получить доступ к профиль и вход в систему:

![Предоставить разрешения пользователя для доступа к приложениям](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Просмотр результатов приложения
После входа вы увидите данные профиля пользователя в **ответ вызова API Graph** поле.
 
![Ожидаемые результаты из вызова API-Интерфейс Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

Вы также увидите основные сведения о маркере, который был получен в **токена доступа** и **идентификатор токена утверждений** поля.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Требуется Microsoft Graph API **user.read** область чтение профиля пользователя. Эта область автоматически добавляется в каждое приложение, регистрируется на портал регистрации по умолчанию. Другие интерфейсы API для Microsoft Graph, а также пользовательские интерфейсы API для вашего внутреннего сервера могут потребоваться дополнительные области. Требуется Microsoft Graph API **Calendars.Read** область, чтобы вывести список календарей пользователя.

Чтобы получить доступ к пользовательским календарям в контексте приложения, добавьте **Calendars.Read** делегировать разрешения на сведения о регистрации приложения. Затем добавьте **Calendars.Read** выборку по **acquireTokenSilent** вызова. 

>[!NOTE]
>При увеличении количества областей, пользователю может быть предложено для дополнительных согласие.

Если API внутреннего сервера не требует область (не рекомендуется), можно использовать **clientId** область, что в **acquireTokenSilent** и **acquireTokenRedirect** вызовов.

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
