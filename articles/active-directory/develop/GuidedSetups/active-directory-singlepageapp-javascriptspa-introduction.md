
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Вызов API Microsoft Graph из одностраничного приложения JavaScript

В этом руководстве показано, как одностраничное приложение JavaScript может выполнять вход с помощью личных и рабочих учетных записей, получать маркер доступа и вызывать API Microsoft Graph или другие API, требующие маркеры доступа, из конечной точки Azure Active Directory версии 2.

### <a name="how-this-guide-works"></a>Принцип работы с руководством

![Как работает пример приложения, созданный в этом руководстве](media/active-directory-singlepageapp-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Дополнительные сведения

Пример приложения, созданный в этом руководстве, позволяет одностраничному приложению JavaScript выполнять запрос к API Microsoft Graph или веб-API, принимающему маркеры от конечной точки Azure Active Directory версии 2. В этом сценарии после выполнения входа в систему маркер добавляется в запросы HTTP с помощью заголовка авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).
<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Библиотеки

В этом руководстве используется следующая библиотека:

|Библиотека|Описание|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Библиотека аутентификации Майкрософт для JavaScript (предварительная версия)|
<!--end-collapse-->
