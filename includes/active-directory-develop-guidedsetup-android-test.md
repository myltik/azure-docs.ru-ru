## <a name="test-your-code"></a>Тестирование кода

1. Разверните код на своем устройстве или эмуляторе.

2. Когда вы будете готовы протестировать приложение, войдите в систему с помощью учетной записи Azure Active Directory (рабочую или учебную) или учетной записи Майкрософт (live.com, outlook.com). 

    ![Тестирование приложения](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Введите имя пользователя и пароль](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>Предоставление разрешения на доступ к приложению
Войдя в приложение первый раз, необходимо будет предоставить ему разрешение на использование данных вашего профиля для входа, как показано ниже: 

![Предоставление разрешения на доступ к приложению](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>Просмотр результатов приложения
После входа можно увидеть результаты, полученные после вызова API Microsoft Graph. Вызов конечной точки API Microsoft Graph **me** возвращает [профиль пользователя](https://graph.microsoft.com/v1.0/me). Список общих конечных точек Microsoft Graph см. в [документации разработчика API Microsoft Graph](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Для чтения профиля пользователя API Microsoft Graph требуется область *user.read*. По умолчанию эта область автоматически добавляется в каждое приложение, зарегистрированное на портале регистрации приложений. Для других API Microsoft Graph, а также для пользовательских API вашего внутреннего сервера, могут потребоваться дополнительные области. Для отображения списка календарей пользователя API Microsoft Graph требуется область *Calendars.Read*. 

Чтобы из контекста приложения получить доступ к календарям пользователя, добавьте делегированное разрешение *Calendars.Read* в сведения о регистрации приложения. Затем добавьте область *Calendars.Read* в вызов `acquireTokenSilent`. 

>[!NOTE]
>При увеличении количества областей от пользователя могут потребоваться дополнительные согласия.

<!--end-collapse-->

[!INCLUDE  [Help and support](active-directory-develop-help-support-include.md)]
