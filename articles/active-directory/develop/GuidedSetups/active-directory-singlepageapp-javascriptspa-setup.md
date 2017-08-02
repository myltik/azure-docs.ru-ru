
## <a name="setting-up-your-web-server-or-project"></a>Настройка веб-сервера или проекта

> Предпочитаете скачать этот пример проекта Visual Studio? [Скачайте проект](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/master.zip) и перейдите к [настройке](#create-an-application-express), чтобы настроить пример кода перед выполнением.

## <a name="prerequisites"></a>Предварительные требования
Для выполнения этой пошаговой установки требуется локальный веб-сервер, такой как [http-server](https://www.npmjs.com/package/http-server/), [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) или интеграция IIS Express с [Visual Studio 2017](https://www.visualstudio.com/downloads/). 

Инструкции в этом руководстве основаны на Visual Studio 2017, но вы можете использовать любую другую среду разработки или редактор HTML/JavaScript.


## <a name="create-your-project-visual-studio-only"></a>Создание проекта (только Visual Studio)

Если вы создаете новый проект, используя Visual Studio, то выполните следующие действия, чтобы создать новое решение Visual Studio:
1.  В Visual Studio: `File` > `New` > `Project`.
2.  В разделе `Visual C#\Web` выберите `ASP.NET Web Application (.NET Framework)`.
3.  Присвойте имя приложению и нажмите кнопку *ОК*.
4.  В разделе `New ASP.NET Web Application` выберите `Empty`.


## <a name="create-your-single-page-applications-ui"></a>Создание пользовательского интерфейса одностраничного приложения
1.  Создайте файл index.html для своего одностраничного приложения JavaScript. Если вы работаете с Visual Studio, выберите проект (корневую папку проекта), щелкните правой кнопкой мыши и выберите `Add` > `New Item` > `HTML page`. Присвойте странице имя index.html.
2.  Добавьте в страницу следующий код:
```html
<!DOCTYPE html>
<html>
    <head>
        <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
        <title>JavaScript SPA Guided Setup</title>
    </head>
    <body style="margin: 40px">
        <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphAPI()">Call Microsoft Graph API</button>
        <div id="errorMessage" class="text-danger"></div>
        <div class="hidden">
            <h3>Graph API Call Response</h3>
            <pre class="well" id="graphResponse"></pre>
        </div>
        <div class="hidden">
            <h3>Access Token</h3>
            <pre class="well" id="accessToken"></pre>
        </div>
        <div class="hidden">
            <h3>ID Token Claims</h3>
            <pre class="well" id="userInfo"></pre>
        </div>
        <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

        <script src="//secure.aadcdn.microsoftonline-p.com/lib/0.1.1/js/msal.min.js"></script>
        <script type="text/javascript" src="msalconfig.js"></script>
    
        <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
        <script src="//cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
        <script src="//cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>
    </body>
</html>
````
