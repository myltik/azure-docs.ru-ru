---
title: "Приступая к работе с Azure AD версии 2 для классического приложения для Windows. Использование | Документация Майкрософт"
description: "Здесь описывается, как классические приложения для Windows .NET (XAML) могут вызвать API, требующий маркеры доступа от конечной точки Azure Active Directory версии 2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 826ba0a00b26993d4f37f0a8ce587d7bb77e7eb4
ms.contentlocale: ru-ru


---

## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Использование библиотеки проверки подлинности Майкрософт для получения маркера для API Microsoft Graph

В этом разделе показано, как использовать MSAL для получения маркера API Microsoft Graph.

1.  В файле `MainWindow.xaml.cs` добавьте ссылку на библиотеку MSAL в класс:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Замените код класса <code>MainWindow</code>:
</li>
</ol>

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
#### <a name="getting-a-user-token-interactive"></a>Интерактивное получение маркера пользователя
При вызове метода `AcquireTokenAsync` появится окно, в котором пользователю предлагается выполнить вход. Приложениям обычно требуется, чтобы пользователь выполнял вход интерактивно при первом доступе к защищенному ресурсу или при сбое автоматической операции для получения маркера (например, срок действия пароля пользователя истек).

#### <a name="getting-a-user-token-silently"></a>Автоматическое получение маркера пользователя
`AcquireTokenSilentAsync` обрабатывает получение и обновление маркера без участия пользователя. После того как `AcquireTokenAsync` будет выполнен в первый раз, обычно используется метод `AcquireTokenSilentAsync`, чтобы получить маркеры для доступа к защищенным ресурсам для последующих вызовов (например, автоматическое выполнение запросов или обновлений маркеров).
В конечном счете в `AcquireTokenSilentAsync` произойдет сбой, например в случае выхода пользователя из системы или смены пароля на другом устройстве. Когда MSAL обнаруживает, что эту проблему можно решить, запросив интерактивное действие, возникает `MsalUiRequiredException`. Приложение может обработать это исключение двумя способами:

1.  Вызвать `AcquireTokenAsync` немедленно, в результате чего пользователю будет предложено выполнить вход. Этот шаблон обычно используется в интерактивных приложениях, где пользователю недоступно автономное содержимое. Пример, созданный в ходе пошаговой настройки, использует этот шаблон. Вы можете увидеть его в действии при первом запуске примера: так как ни один пользователь еще не использовал это приложение, `PublicClientApp.Users.FirstOrDefault()` будет содержать значение NULL и будет выдано исключение `MsalUiRequiredException`. Код в примере обработает исключение, вызвав `AcquireTokenAsync`, в результате чего пользователю будет предложено войти.

2.  Приложения также могут визуально уведомить пользователя, что требуется интерактивный вход, чтобы пользователь мог выбрать подходящее время для входа или приложение могло повторить метод `AcquireTokenSilentAsync` ​​позднее. Обычно это применимо для тех случаев, когда пользователь может использовать другие функции приложения, на которые это не влияет, например, когда в приложении есть автономное содержимое. В этом случае пользователь может решить, когда он хочет войти, чтобы получить доступ к защищенному ресурсу или обновить устаревшие данные, или ваше приложение может решить повторить `AcquireTokenSilentAsync` при восстановлении сети ​​после временной недоступности.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Вызов API Microsoft Graph с помощью полученного маркера

1. Добавьте указанный ниже новый метод в файл `MainWindow.xaml.cs`. Он используется для выполнения запроса `GET` к API Graph с помощью заголовка авторизации:

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
### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Дополнительные сведения о вызове REST через защищенный API

В этом примере приложения метод `GetHttpContentWithToken` выполняет HTTP-запрос `GET` к защищенному ресурсу, требующему маркер, а затем возвращает содержимое вызывающему объекту. Этот метод добавляет полученный маркер в *заголовок авторизации HTTP*. В этом примере ресурс — это конечная точка *me* API Microsoft Graph, которая отображает сведения о профиле пользователя.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Добавление метода для выхода пользователя

1. Добавьте следующий метод в файл `MainWindow.xaml.cs` для выхода пользователя:

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
### <a name="more-info-on-sign-out"></a>Дополнительные сведения о выходе

`SignOutButton_Click` удаляет пользователя из кэша пользователей MSAL. Это фактически заставит MSAL забыть текущего пользователя, поэтому будущий запрос на получение маркера будет успешным только в том случае, если он будет выполнен интерактивно.
Несмотря на то что приложение в этом примере поддерживает одного пользователя, MSAL поддерживает сценарии, где можно войти в несколько учетных записей одновременно, например приложение электронной почты, где у пользователя есть несколько учетных записей.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Отображение основных сведений о маркере

1. Добавьте следующий метод в файл `MainWindow.xaml.cs` для отображения основных сведений о маркере:

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

Маркеры, полученные через *OpenID Connect*, также содержат небольшое подмножество данных, относящихся к пользователю. `DisplayBasicTokenInfo` отображает основные сведения, содержащиеся в маркере, например отображаемое имя и идентификатор пользователя, а также дату истечения срока действия маркера и строку, представляющую сам маркер доступа. Вы должны просмотреть эти сведения. Нажав кнопку *вызова API Microsoft Graph* несколько раз, вы увидите, что для последующих запросов повторно используется тот же маркер. Вы также можете увидеть, что библиотека MSAL продлила срок действия.
<!--end-collapse-->


