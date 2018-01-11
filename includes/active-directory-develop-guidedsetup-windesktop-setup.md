
## <a name="set-up-your-project"></a>Настройка проекта

В этом разделе создайте новый проект, чтобы продемонстрировать, как интегрировать приложение .NET рабочего стола Windows (XAML) с *вход с помощью Microsoft* , чтобы приложение может запросить веб-API, требующие маркера.

Приложения, созданного с помощью этого руководства отображает кнопку, которая используется для вызова граф области для отображения результатов на экране и кнопку выхода.

> [!NOTE]
> Предпочитаете скачать этот пример проекта Visual Studio? [Загрузка проекта](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)и перейдите к разделу [шаг настройки](#create-an-application-express) Настройка образца кода перед его выполнением.
>

Для создания приложения, выполните следующие действия.
1. В Visual Studio, выберите **файл** > **New** > **проекта**.
2. В разделе **шаблоны**выберите **Visual C#**.
3. Выберите **приложение WPF** или **приложение WPF**, в зависимости от версии вы используете версию Visual Studio.

## <a name="add-msal-to-your-project"></a>Добавьте в проект MSAL
1. В Visual Studio, выберите **средства** > **диспетчера пакетов NuGet**> **консоль диспетчера пакетов**.
2. В окне консоли диспетчера пакетов вставьте следующую команду Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Эта команда устанавливает библиотеки проверки подлинности Microsoft. MSAL обрабатывает получение, кэширование и обновление пользователя токены, которые используются для доступа к API, которые защищаются с помощью Azure Active Directory v2.
    >

## <a name="add-the-code-to-initialize-msal"></a>Добавление кода для инициализации MSAL
На этом шаге создается класс для управления взаимодействием с MSAL, такие как обработка токенов.

1. Откройте *App.xaml.cs* файл, а затем добавьте ссылку для MSAL для класса:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Обновите класс приложения следующее:

    ```csharp
    public partial class App : Application
    {
        //Below is the clientId of your app registration. 
        //You have to replace the below with the Application Id for your app registration
        private static string ClientId = "your_client_id_here";

        public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

    }
    ```

## <a name="create-the-application-ui"></a>Создание пользовательского интерфейса приложения
В этом разделе показано, как приложения могут запрашивать защищенного внутреннего сервера, такие как Microsoft Graph. 

Объект *MainWindow.xaml* файла должны создаваться автоматически в составе шаблон проекта. Открыть этот файл, а затем заменить приложения  *\<сетки >* узла с помощью следующего кода:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

