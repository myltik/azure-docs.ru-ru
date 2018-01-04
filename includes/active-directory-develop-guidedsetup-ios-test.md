## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Отправка тестового запроса к API Microsoft Graph из приложения iOS

Чтобы запустить код в имитаторе, нажмите клавиши **команда** + **R**.

![Протестируйте приложения в симуляторе](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

Когда вы будете переходить к тестированию, выберите **вызова API Graph Microsoft**. При появлении запроса введите имя пользователя и пароль.

### <a name="provide-consent-for-application-access"></a>Предоставить разрешения для доступа к приложениям
При первом входе в приложение, появится запрос на подтверждение, чтобы разрешить приложению получить доступ к профиль и вход в систему:

![Предоставить разрешения пользователя для доступа к приложениям](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Просмотр результатов приложения
После входа вы увидите данные профиля пользователя, возвращенный из вызова API-Интерфейс Microsoft Graph в **входа** раздела. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Требуется Microsoft Graph API **user.read** область чтение профиля пользователя. Эта область автоматически добавляется в каждое приложение, регистрируется на портал регистрации по умолчанию. Другие интерфейсы API для Microsoft Graph, а также пользовательские интерфейсы API для вашего внутреннего сервера могут потребоваться дополнительные области. Требуется Microsoft Graph API **Calendars.Read** область, чтобы вывести список календарей пользователя.

Чтобы получить доступ к пользовательским календарям в контексте приложения, добавьте **Calendars.Read** делегировать разрешения на сведения о регистрации приложения. Затем добавьте **Calendars.Read** выборку по **acquireTokenSilent** вызова. 

>[!NOTE]
>При увеличении количества областей, пользователю может быть предложено для дополнительных согласие.

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
