
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Используйте MSAL, чтобы получить маркер для Microsoft Graph API

В этом разделе MSAL используется для получения маркера для Microsoft Graph API.

1.  В *MainWindow.xaml.cs* файл, добавьте в класс ссылки для MSAL:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Замените `MainWindow` класса код следующим:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] _scopes = new string[] { "user.read" };

        public MainWindow()
        {
            InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
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
                ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Дополнительные сведения
#### <a name="get-a-user-token-interactively"></a>Получение токена пользователя в интерактивном режиме
Вызов `AcquireTokenAsync` метод результатов в окне с запросом на вход. Приложения обычно требуется пользователям для интерактивного входа в первый раз, необходимые для доступа к защищенному ресурсу. Они могут также необходимо выполнить вход, при сбое операции в фоновом режиме, чтобы получить токен (например, по истечении срока действия пароля пользователя).

#### <a name="get-a-user-token-silently"></a>Автоматическое получение токена пользователя
`AcquireTokenSilentAsync` Метод обрабатывает приобретения токена и обновления без вмешательства пользователя. После `AcquireTokenAsync` выполняется в первый раз `AcquireTokenSilentAsync` является обычный метод следует использовать для получения токенов, доступ к защищенным ресурсам для последующих вызовов, так как для запроса или обновления токенов вызовов без вмешательства пользователя.

Со временем `AcquireTokenSilentAsync` метод завершится с ошибкой. Причины сбоя может быть, что пользователь выход или изменил свой пароль на другом устройстве. Когда MSAL обнаруживает, что эту проблему можно решить, запросив интерактивное действие, возникает исключение `MsalUiRequiredException`. Приложение может обработать это исключение двумя способами:

* Он может вызвать для `AcquireTokenAsync` немедленно. Этот вызов приводит подтверждения от пользователя для входа. Этот шаблон обычно используется в веб-приложений, где нет доступных автономного содержимого для пользователя. Образец, созданные этой интерактивной программы установки следует этого шаблона, в котором вы увидите на время действия первого выполнения образца. 
    * Поскольку пользователь не использовал приложение, `PublicClientApp.Users.FirstOrDefault()` содержит значение null и `MsalUiRequiredException` исключения. 
    * В примере кода затем обрабатывает исключение, вызвав `AcquireTokenAsync`, что приводит к подтверждения от пользователя для входа в.

* Вместо этого он может представлять визуальное пользователям, которые интерактивный вход не требуется, чтобы они могли выбирать для входа в нужное время. Или можно повторить попытку приложения `AcquireTokenSilentAsync` позже. Данный шаблон часто используется, когда пользователи могут использовать другие функциональные возможности приложения без прерывания — например, при это содержимое в приложении. В этом случае пользователи можно решить, когда им требуется вход для доступа к защищенному ресурсу или обновление устаревшей информации. Кроме того, приложение может решать повторить попытку `AcquireTokenSilentAsync` при восстановлении сети после будучи временно недоступна.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Вызов API-Интерфейс Microsoft Graph с помощью маркера, который был получен

Добавьте следующий новый метод для вашего `MainWindow.xaml.cs`. Этот метод используется, чтобы сделать `GET` запрос к Graph API, используя заголовок авторизации:

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
        //Add the token in Authorization header
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
<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Дополнительные сведения о вызове REST через защищенный API

В этом образце приложения используется `GetHttpContentWithToken` метод HTTP `GET` запрос к защищенному ресурсу, маркера и возвращается вызывающему объекту содержимое. Этот метод добавляет полученные токена в заголовок авторизации HTTP. Для этого образца, то ресурс является Microsoft Graph API *мне* конечную точку, которая отображает сведения о профиле пользователя.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Добавьте метод для выхода пользователя

Чтобы выйти из системы пользователя, добавьте следующий метод к `MainWindow.xaml.cs` файла:

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
<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>Дополнительные сведения о выхода пользователя

`SignOutButton_Click` Метод удаляет пользователей из кэша MSAL пользователя, который фактически сообщает MSAL забыть текущего пользователя, чтобы будущих запрос для получения токена успешно завершится только в том случае, если он станет будут интерактивными.

Несмотря на то, что приложение в этом образце поддерживает одиночные пользователи, MSAL поддерживает сценарии, где несколько учетных записей может быть подписана одновременно. Примером является приложение электронной почты, где у пользователя есть несколько учетных записей.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Выводит основные сведения маркера

Чтобы отобразить основные сведения о маркере, добавьте следующий метод к *MainWindow.xaml.cs* файла:

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
<!--start-collapse-->
### <a name="more-information"></a>Дополнительные сведения

Помимо, используемый для вызова Microsoft Graph API, после пользователь входит в токен доступа MSAL также получает маркер идентификатора. Этот маркер содержит небольшое подмножество сведений, которые имеют отношение к пользователям. `DisplayBasicTokenInfo` Метод отображает основные сведения, содержащиеся в маркере. Например отображает отображаемое имя и идентификатор, а также дату истечения срока действия маркера и строка, представляющая сам маркер доступа. Можно выбрать *вызова API Graph Microsoft* кнопку несколько раз и увидеть, что тот же токен был использован повторно для последующих запросов. Вы также можете увидеть, что библиотека MSAL продлила срок действия.
<!--end-collapse-->

