## <a name="test-your-code"></a>Тестирование кода

1. Разверните код на своем устройстве или эмуляторе.
2. Когда вы будете готовы тестировать приложение, используйте для входа в учетную запись Microsoft Azure Active Directory (рабочая или учебная учетная запись) или учетной записи Майкрософт (live.com, outlook.com). 

    ![Тестирование приложения](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Введите имя пользователя и пароль](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>Предоставить разрешения для доступа к приложениям
При первом входе в приложение, появится запрос на подтверждение, чтобы разрешить приложению получить доступ к профиль и вход в систему: 

![Предоставить разрешения пользователя для доступа к приложениям](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>Просмотр результатов приложения
После входа вы увидите результатов, возвращаемых вызовом Microsoft Graph API. Вызов API-Интерфейс Microsoft Graph **мне** конечная точка возвращает https://graph.microsoft.com/v1.0/me профиля пользователя. Список общих конечных точек Microsoft Graph см. в разделе [документации для разработчиков Microsoft Graph API](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Требуется Microsoft Graph API **user.read** область чтение профиля пользователя. Эта область автоматически добавляется в каждое приложение, регистрируется на портал регистрации по умолчанию. Другие интерфейсы API для Microsoft Graph, а также пользовательские интерфейсы API для вашего внутреннего сервера могут потребоваться дополнительные области. Требуется Microsoft Graph API **Calendars.Read** область, чтобы вывести список календарей пользователя.

Чтобы получить доступ к пользовательским календарям в контексте приложения, добавьте **Calendars.Read** делегировать разрешения на сведения о регистрации приложения. Затем добавьте **Calendars.Read** выборку по **acquireTokenSilent** вызова. 

>[!NOTE]
>При увеличении количества областей, пользователю может быть предложено для дополнительных согласие.

<!--end-collapse-->

[!INCLUDE  [Help and support](active-directory-develop-help-support-include.md)]
