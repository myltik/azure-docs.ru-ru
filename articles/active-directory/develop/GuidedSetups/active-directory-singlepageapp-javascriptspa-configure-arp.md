
## <a name="add-the-applications-registration-information-to-your-app"></a>Добавление в приложение сведений о его регистрации

На этом шаге необходимо настроить URL-адрес перенаправления сведений о регистрации приложения, а затем добавить идентификатор приложения в одностраничное приложение JavaScript.

### <a name="configure-redirect-url"></a>Настройка URL-адреса перенаправления

Настройте указанное выше поле `Redirect URL`, используя URL-адрес страницы index.html на основе веб-сервера, а затем нажмите кнопку *Обновить*.

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>Инструкции Visual Studio для получения URL-адреса перенаправления с помощью протокола SSL
> Если вы работаете с Visual Studio, то настройте проект для использования SSL, а затем воспользуйтесь URL-адресом SSL для настройки сведений о регистрации приложения. Для этого используйте следующие инструкции:
> 1.    В обозревателе решений выберите проект и просмотрите окно `Properties` (если окно свойств не отображается, нажмите клавишу F4).
> 2.    Измените `SSL Enabled` на `True`.
> 3.    Скопируйте значение `SSL URL` в буфер обмена:<br/> ![Свойства проекта](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    Выберите меню `Project`, а затем выберите `{Project} Properties...` (где {Project} — это имя проекта).
> 5.    Откройте вкладку `Web`.
> 6.    Вставьте значение `SSL URL` в поле `Project Url`.
> 7.    Также вставьте это значение в поле `Redirect URL` в верхней части этой страницы, а затем нажмите кнопку *Обновить*.


### <a name="configure-your-javascript-spa-application"></a>Настройка одностраничного приложения JavaScript

1.  Создайте файл с именем `msalconfig.js`, в котором будут содержаться сведения о регистрации приложения. Если вы работаете с Visual Studio, выберите проект (корневую папку проекта), щелкните правой кнопкой мыши и выберите `Add` > `New Item` > `JavaScript File`. Назовите контроллер `msalconfig.js`.
2.  Добавьте в файл `msalconfig.js` следующий код:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
