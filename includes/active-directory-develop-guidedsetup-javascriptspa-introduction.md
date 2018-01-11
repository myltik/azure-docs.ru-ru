# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Вызов API Microsoft Graph из одностраничного приложения JavaScript

В этом руководстве показано, как одностраничное приложение JavaScript может выполнять вход с помощью личных, рабочих или учебных учетных записей, получать маркер доступа и вызывать API Microsoft Graph или другие API, требующие маркеры доступа, из конечной точки Azure Active Directory версии 2.

### <a name="how-this-guide-works"></a>Принцип работы с руководством

![Как работает пример приложения, созданный в этом руководстве](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Дополнительные сведения

Пример приложения, созданный в этом руководстве, позволяет одностраничному приложению JavaScript выполнять запрос к API Microsoft Graph или веб-API, принимающему маркеры от конечной точки Azure Active Directory версии 2. В этом сценарии после выполнения входа в систему маркер доступа запрашивается и добавляется в запросы HTTP с помощью заголовка авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Библиотеки

В этом руководстве используется следующая библиотека:

|Библиотека|ОПИСАНИЕ|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Библиотека аутентификации Майкрософт для JavaScript (предварительная версия)|

> [!NOTE]
> В качестве целевого объекта в библиотеке *msal.js* задана *конечная точка Azure Active Directory версии 2*, что позволяет выполнять вход и запрашивать маркеры, используя личные, рабочие и учебные учетные записи. В отношении *конечной точки Azure Active Directory версии 2* применяется [ряд ограничений](..\articles\active-directory\develop\active-directory-v2-limitations.md). Если вы заинтересованы только в учебной и рабочей учетных записях, используйте библиотеку *adal.js* и *конечную точку версии 1*. Чтобы понять различия между конечными точками версий 1 и 2, ознакомьтесь со [сравнением версий 1 и 2](..\articles\active-directory\develop\active-directory-v2-compare.md).

<!--end-collapse-->
