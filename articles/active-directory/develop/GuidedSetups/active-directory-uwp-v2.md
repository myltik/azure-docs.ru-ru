---
title: Начало работы с Azure AD версии 2 для универсальной платформы Windows | Документация Майкрософт
description: В этой статье описано, как приложения для универсальной платформы Windows (XAML) могут вызвать API, которому требуются маркеры доступа от конечной точки Azure Active Directory версии 2.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 390559922b3b8fb293d1c8b38f36dfd0a1df9ebd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763379"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Вызов API Microsoft Graph из приложения для универсальной платформы Windows (UWP)

В этом руководстве описывается, как приложение для универсальной платформы Windows (XAML) может получить маркер доступа и с его помощью вызывать API Microsoft Graph или другие API, которым требуются маркеры доступа от конечной точки Azure Active Directory версии 2.

В конце этого руководства ваше приложение сможет вызывать защищенный API с помощью личных учетных записей (включая outlook.com, live.com и другие), а также рабочих и учебных учетных записей из любой компании или организации, которая использует Azure Active Directory.  

> Для работы с этим руководством требуется Visual Studio 2017 с установленным компонентом "Разработка приложений для универсальной платформы Windows". Ознакомьтесь со [статьей](https://docs.microsoft.com/windows/uwp/get-started/get-set-up "Настройка Visual Studio для UWP"), чтобы получить инструкции по скачиванию и настройке Visual Studio для разработки приложений для универсальной платформы Windows.

### <a name="how-this-guide-works"></a>Принцип работы с руководством

![Принцип работы с руководством](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

Пример приложения для UWP, созданный в рамках этого руководства, позволяет выполнять запрос к API Microsoft Graph или веб-API, принимающему маркеры от конечной точки Azure Active Directory версии 2. В этом сценарии маркер добавляется в запросы HTTP с помощью заголовка авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).

### <a name="nuget-packages"></a>Пакеты NuGet

В этом руководстве используются следующие пакеты NuGet:

|Библиотека|ОПИСАНИЕ|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Библиотека проверки подлинности Майкрософт (MSAL)|


## <a name="set-up-your-project"></a>Настройка проекта

В этом разделе содержатся пошаговые инструкции по интеграции классического приложения для Windows .NET (XAML) с *входом с учетной записью Майкрософт*, что позволит выполнять запрос к веб-API, требующим маркер, например API Microsoft Graph.

В приложении, создаваемом в рамках этого руководства, отображается кнопка для запроса API Graph, кнопка выхода и текстовые поля с результатами вызовов.

> Предпочитаете скачать этот пример проекта Visual Studio? [Скачайте проект](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) и перейдите к шагу [регистрации приложения](#register-your-application "application registration step"), чтобы настроить пример кода перед выполнением.


### <a name="create-your-application"></a>Создание приложения
1. В Visual Studio выберите **Файл** > **Создать** > **Проект**.<br/>
2. В разделе *Шаблоны* выберите **Visual C#**.
3. Выберите **Пустое приложение (универсальное приложение Windows)**
4. Назначьте ему имя и нажмите кнопку "ОК".
5. Когда появится соответствующий запрос, выберите любую *целевую* и *минимальную* версию и нажмите кнопку "ОК":<br/><br/>![Минимальная и целевая версии](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Добавление библиотеки проверки подлинности Майкрософт (MSAL) в проект
1. В Visual Studio выберите **Сервис** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.
2. Скопируйте и вставьте следующую команду в окно "Консоль диспетчера пакетов":

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> Приведенный выше пакет устанавливает [библиотеку проверки подлинности Майкрософт (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL обрабатывает получение, кэширование и обновление маркеров пользователей, которые используются для доступа к API, защищенным конечной точкой Azure Active Directory версии 2.

## <a name="initialize-msal"></a>Инициализация MSAL
На этом шаге вы сможете создать класс для обработки взаимодействия с библиотекой MSAL, например обработки маркеров.

1. Откройте файл **App.xaml.cs** и добавьте ссылку для библиотеки MSAL в класс:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Добавьте в класс App следующие две строки (в блоке <code>sealed partial class App : Application</code>):

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Создание пользовательского интерфейса приложения

Файл **MainPage.xaml** должен создаваться автоматически как часть шаблона проекта. Откройте этот файл и выполните инструкции:

1.  Замените узел **<Grid>** приложения следующим:

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Использование библиотеки проверки подлинности Майкрософт для получения маркера для API Microsoft Graph

В этом разделе показано, как с помощью MSAL получить маркер для API Microsoft Graph.

1.  В файле **MainPage.xaml.cs** добавьте ссылку на библиотеку MSAL в класс:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Замените код класса <code>MainPage</code> следующим:

    ```csharp
    public sealed partial class MainPage : Page
    {
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }
    
            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

### <a name="more-information"></a>Дополнительные сведения
#### <a name="get-a-user-token-interactively"></a>Интерактивное получение маркера пользователя
При вызове метода `AcquireTokenAsync` появится окно, в котором пользователю предлагается выполнить вход. Когда пользователь впервые запрашивает доступ к защищенному ресурсу, приложение обычно требует выполнить интерактивный вход. Пользователям также может потребоваться выполнить вход при сбое автоматической операции получения маркера (например, по истечении срока действия пароля пользователя).

#### <a name="get-a-user-token-silently"></a>Автоматическое получение маркера пользователя
Метод `AcquireTokenSilentAsync` обрабатывает получение и обновление маркера без участия пользователя. После первого выполнения метода `AcquireTokenAsync` обычно используется метод `AcquireTokenSilentAsync`, чтобы получить маркеры для доступа к защищенным ресурсам для последующих вызовов, так как вызовы для запроса или обновления маркеров выполняются автоматически.

Иногда метод `AcquireTokenSilentAsync` завершается ошибкой. Причина может заключаться в том, что пользователь вышел из системы или изменил пароль на другом устройстве. Когда MSAL обнаруживает, что эту проблему можно решить, запросив интерактивное действие, возникает исключение `MsalUiRequiredException`. Приложение может обработать это исключение двумя способами:

* Может немедленно вызвать `AcquireTokenAsync`. Этот вызов приводит к появлению запроса на вход пользователя. Этот шаблон обычно используется в интерактивных приложениях, где пользователю недоступно автономное содержимое. Этот шаблон используется в примере, созданном в ходе настройки с помощью этих инструкций. Вы увидите его в действии при первом запуске примера. 
    * Так как приложение еще не использовалось, `PublicClientApp.Users.FirstOrDefault()` будет содержать значение NULL и будет выдано исключение `MsalUiRequiredException`. 
    * Код в примере обработает исключение, вызвав `AcquireTokenAsync`, в результате чего пользователю будет предложено войти.

* Также приложение может визуально уведомить пользователей, что требуется интерактивный вход, чтобы они могли выбрать подходящее время для входа. Либо приложение может попытаться повторно выполнить метод `AcquireTokenSilentAsync` позже. Этот шаблон часто применяется, когда пользователи могут использовать другие функциональные возможности приложения без прерывания работы — например, если в приложении доступно автономное содержимое. В этом случае пользователи могут решать, что им нужно сделать после входа — получить доступ к защищенному ресурсу или обновить устаревшие данные. Кроме того, приложение может попытаться повторно выполнить `AcquireTokenSilentAsync` при восстановлении сети после временной недоступности.

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Вызов API Microsoft Graph с помощью полученного маркера

1. Добавьте приведенный ниже новый метод в файл **MainPage.xaml.cs**. Он используется для выполнения запроса `GET` к API Graph с помощью заголовка авторизации:

    ```csharp
    /// <summary>
    /// Perform an HTTP GET request to a URL using an HTTP Authorization header
    /// </summary>
    /// <param name="url">The URL</param>
    /// <param name="token">The token</param>
    /// <returns>String containing the results of the GET operation</returns>
    public async Task<string> GetHttpContentWithToken(string url, string token)
    {
        var httpClient = new System.Net.Http.HttpClient();
        System.Net.Http.HttpResponseMessage response;
        try
        {
            var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
            // Add the token in Authorization header
            request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
            response = await httpClient.SendAsync(request);
            var content = await response.Content.ReadAsStringAsync();
            return content;
        }
        catch (Exception ex)
        {
            return ex.ToString();
        }
    }
    ```

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Дополнительные сведения о вызове REST через защищенный API

В этом примере приложения метод `GetHttpContentWithToken` выполняет HTTP-запрос `GET` к защищенному ресурсу, требующему маркер, а затем возвращает содержимое вызывающему объекту. Этот метод добавляет полученный маркер в *заголовок авторизации HTTP*. В этом примере ресурс — это конечная точка *me* API Microsoft Graph, которая отображает сведения о профиле пользователя.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Добавление метода для выхода пользователя

1. Для выхода пользователя добавьте следующий метод в файл **MainPage.xaml.cs**:

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        if (App.PublicClientApp.Users.Any())
        {
            try
            {
                App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
                this.ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    }
    ```

### <a name="more-info-on-sign-out"></a>Дополнительные сведения о выходе

Метод `SignOutButton_Click` удаляет пользователя из кэша пользователей MSAL. Это фактически указывает MSAL забыть текущего пользователя, поэтому будущий запрос на получение маркера может быть успешным только в том случае, если он будет выполнен интерактивно.
Несмотря на то что приложение в этом примере поддерживает одного пользователя, MSAL поддерживает сценарии, где можно войти в несколько учетных записей одновременно, например приложение электронной почты, где у пользователя есть несколько учетных записей.

## <a name="display-basic-token-information"></a>Отображение основных сведений о маркере

1. Добавьте следующий метод в файл **MainPage.xaml.cs** для отображения основных сведений о маркере:

    ```csharp
    /// <summary>
    /// Display basic information contained in the token
    /// </summary>
    private void DisplayBasicTokenInfo(AuthenticationResult authResult)
    {
        TokenInfoText.Text = "";
        if (authResult != null)
        {
            TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
            TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
            TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
            TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
        }
    }
    ```

### <a name="more-information"></a>Дополнительные сведения

Маркеры идентификаторов, полученные через *OpenID Connect*, также содержат небольшой набор данных, относящихся к пользователю. `DisplayBasicTokenInfo` отображает основные сведения, содержащиеся в маркере, например отображаемое имя и идентификатор пользователя, а также дату истечения срока действия маркера и строку, представляющую сам маркер доступа. Вы должны просмотреть эти сведения. Нажав кнопку **вызова API Microsoft Graph** несколько раз, вы увидите, что для последующих запросов повторно используется тот же маркер. Вы также можете увидеть, что библиотека MSAL продлила срок действия.

## <a name="register-your-application"></a>Регистрация приложения

Теперь вам необходимо зарегистрировать приложение на *портале регистрации приложений Майкрософт*:
1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app) для регистрации приложения.
2. Введите имя приложения. 
3. Убедитесь, что параметр Guided Setup (Пошаговая настройка) не выбран.
4. Щелкните **Add Platforms** (Добавление платформ), **Собственное приложение**, а затем нажмите кнопку "Сохранить".
5. Скопируйте GUID в идентификатор приложения, вернитесь в Visual Studio, откройте файл **App.xaml.cs** и замените `your_client_id_here` зарегистрированным идентификатором приложения:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Включение встроенной проверки подлинности в федеративных доменах (необязательно)

Чтобы включить встроенную проверку подлинности Windows при использовании с федеративным доменом Azure Active Directory, необходимо включить дополнительные возможности в манифест приложения:

1. Дважды щелкните **Package.appxmanifest**.
2. Выберите вкладку **Возможности** и убедитесь, что включены следующие параметры:

    - Корпоративная проверка подлинности
    - Частные сети (клиент и сервер)
    - Общие сертификаты пользователей 

3. Затем откройте файл **App.xaml.cs** и добавьте следующую строку в конструкторе приложений:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> Встроенная проверка подлинности Windows не настроена по умолчанию для этого примера, так как приложениям, запрашивающим возможности *Корпоративная проверка подлинности* или *Общие сертификаты пользователей*, требуется более высокий уровень проверки с помощью магазина Windows. Но не все разработчики хотят проводить проверку более высокого уровня. Этот параметр следует включать только в том случае, если требуется встроенная проверка подлинности Windows с федеративным доменом Azure Active Directory.


## <a name="test-your-code"></a>Тестирование кода

Чтобы протестировать приложение, нажмите клавишу `F5` для запуска проекта в Visual Studio. Откроется главное окно.

![Пользовательский интерфейс приложения](media/active-directory-uwp-v2.md/testapp-ui.png)

Когда вы будете готовы к тестированию, щелкните *Call Microsoft Graph API* (Вызвать API Microsoft Graph) и используйте Microsoft Azure Active Directory (учетная запись организации) или учетную запись Майкрософт (live.com, outlook.com) для входа. Если вход выполняется впервые, появится окно, предлагающее пользователю войти в систему:

![Страница входа](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Согласие на предоставление разрешений
При первом входе в приложение появится экран согласия, как показано ниже, где необходимо явно дать согласие:

![Экран согласия](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Ожидаемые результаты
На экране результатов вызова API должны отобразиться сведения о профиле пользователя, возвращенные вызовом API Microsoft Graph:

![Экран результатов](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

Вы также должны увидеть основные сведения о маркере, полученные с помощью `AcquireTokenAsync` или `AcquireTokenSilentAsync`, в соответствующем окне:

|Свойство  |Формат  |ОПИСАНИЕ |
|---------|---------|---------|
|**Имя** |Полное имя пользователя |Имя и фамилия пользователя.|
|**Имя пользователя** |<span>user@domain.com</span> |Имя пользователя, которое используется для идентификации пользователя.|
|**Истечение срока действия маркера** |Datetime |Время окончания срока действия маркера. MSAL продлевает срок действия, по мере необходимости обновляя маркер.|
|**Маркер доступа** |Строка |Строка маркера, которая отправляется к HTTP-запросам, требующим *Заголовок авторизации*.|

#### <a name="see-what-is-in-the-access-token-optional"></a>Просмотр содержимого маркера доступа (необязательно)
При необходимости можно скопировать значение маркера доступа и вставить его в https://jwt.ms для декодирования и просмотра списка утверждений.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Для чтения профиля пользователя API Microsoft Graph требуется область *user.read*. По умолчанию эта область автоматически добавляется в каждое приложение, зарегистрированное на портале регистрации приложений. Для других API Microsoft Graph, а также для пользовательских API вашего внутреннего сервера, могут потребоваться дополнительные области. Для отображения списка календарей пользователя API Microsoft Graph требуется область *Calendars.Read*.

Чтобы из контекста приложения получить доступ к календарям пользователя, добавьте делегированное разрешение *Calendars.Read* в сведения о регистрации приложения. Затем добавьте область *Calendars.Read* в вызов `acquireTokenSilent`. 

> [!NOTE]
> При увеличении количества областей от пользователя могут потребоваться дополнительные согласия.

## <a name="known-issues"></a>Известные проблемы

### <a name="issue-1"></a>Проблема 1.
При входе в приложение в федеративном домене Azure Active Directory может появиться одна из следующих ошибок:
 - No valid client certificate found in the request (В запросе отсутствует действительный клиентский сертификат).
 - No valid certificates found in the user's certificate store (В хранилище сертификатов пользователя отсутствуют действительные сертификаты).
 - Попробуйте еще раз, выбрав другой метод проверки подлинности.

**Причина.** Не включены возможности корпоративного уровня и сертификатов.

**Решение.** Выполните действия из раздела о [встроенной проверки подлинности в федеративных доменах](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Проблема 2.
После включения [встроенной проверки подлинности в федеративных доменах](#enable-integrated-authentication-on-federated-domains-optional) и попытки использовать Windows Hello на компьютере Windows 10, чтобы войти в среду с настроенной многофакторной проверкой подлинности, предоставляется список сертификатов. Но если выбрать использование ПИН-кода, окно ПИН-кода никогда не откроется.

**Причина.** Известное ограничение с веб-брокером проверки подлинности в приложениях UWP, выполняемых в настольной версии Windows 10 (работает нормально в Windows 10 Mobile).

**Возможное решение.** Пользователям необходимо выбрать вход с другими параметрами, а затем вместо этого выбрать *Sign-in with a username and password* (Вход с помощью имени пользователя и пароля), указать предоставление собственного пароля и пройти проверку подлинности по телефону.

