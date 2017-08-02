
### <a name="create-an-application-express"></a>Создание приложения (экспресс)
Теперь необходимо зарегистрировать приложение на портале регистрации приложений Майкрософт:

1.  Зарегистрируйте свое приложение на [портале регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Введите имя для приложения и адрес электронной почты.
3.  Выберите параметр *Guided Setup* (Пошаговая настройка).
4.  Следуйте инструкциям, чтобы получить идентификатор приложения. Затем вставьте его в свой код.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Добавление сведений о регистрации приложения в решение (дополнительно)

1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app) для регистрации приложения.
2. Введите имя для приложения и адрес электронной почты. 
3. Убедитесь, что параметр *Guided Setup* (Пошаговая настройка) не выбран.
4.  Щелкните `Add Platform`, а затем выберите `Web`.
5. Добавьте URL-адрес перенаправления в приложение. URL-адрес перенаправления — это URL-адрес вашей страницы `index.html` на основе веб-сервера.
6. Нажмите кнопку *Сохранить*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>Инструкции Visual Studio для получения URL-адреса перенаправления с помощью протокола SSL
> Если вы работаете с Visual Studio, то настройте проект для использования SSL, а затем воспользуйтесь URL-адресом SSL для настройки сведений о регистрации приложения. Для этого используйте следующие инструкции:
> 1.    В обозревателе решений выберите проект и просмотрите окно `Properties` (если окно свойств не отображается, нажмите клавишу F4).
> 2.    Измените `SSL Enabled` на `True`.
> 3.    Скопируйте значение `SSL URL` в буфер обмена:<br/> ![Свойства проекта](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    Выберите меню `Project`, а затем выберите `{Project} Properties...` (где {Project} — это имя проекта).
> 5.    Откройте вкладку `Web`.
> 6.    Вставьте значение `SSL URL` в поле `Project Url`.
> 7.    Вернитесь на портал регистрации приложений и вставьте значение в поле `Redirect URL` как URL-адрес перенаправления, а затем нажмите кнопку *Сохранить*.


#### <a name="configure-your-javascript-spa-application"></a>Настройка одностраничного приложения JavaScript

1.  Создайте файл с именем `msalconfig.js`, в котором будут содержаться сведения о регистрации приложения. Если вы работаете с Visual Studio, выберите проект (корневую папку проекта), щелкните правой кнопкой мыши и выберите `Add` > `New Item` > `JavaScript File`. Назовите контроллер `msalconfig.js`.
2.  Добавьте в файл `msalconfig.js` следующий код:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Замените <code>Enter_the_Application_Id_here</code> зарегистрированным идентификатором приложения.
</li>
</ol>