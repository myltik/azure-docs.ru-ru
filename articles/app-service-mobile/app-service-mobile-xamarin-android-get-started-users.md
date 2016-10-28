<properties
	pageTitle="Начало работы с проверкой подлинности для мобильных приложений Xamarin для Android"
	description="Узнайте, как использовать мобильные приложения для проверки подлинности пользователей приложения Xamarin для Android с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="mattchenderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/28/2016"
	ms.author="mahender"/>

# Добавление проверки подлинности в приложение Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

В этом разделе показано, как аутентифицировать пользователей мобильного приложения из клиентского приложения. В этом учебнике вы добавите аутентификацию в проект быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными приложениями Azure. После успешной аутентификации и авторизации в мобильном приложении отображается значение идентификатора пользователя.

Этот учебник создан на основе краткого руководства по мобильным приложениям. Необходимо также сначала пройти учебник [Создание приложения Xamarin.Android]. Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений для аутентификации. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка служб приложений

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

В Visual Studio или Xamarin Studio запустите клиентский проект на устройстве или в эмуляторе. Убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный). Это вызвано тем, что приложение пытается получить доступ к серверной части мобильного приложения от имени неаутентифицированного пользователя. Теперь для таблицы *TodoItem* требуется аутентификация.

Далее вы обновите клиентское приложение для запроса ресурсов из серверной части мобильного приложения прошедшим аутентификацию пользователем.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

Для отображения данных в обновленном приложении пользователи должны будут коснуться кнопки **Вход** и пройти проверку подлинности.

1. Добавьте в класс **TodoActivity** следующий код:

	    // Define a authenticated user.
	    private MobileServiceUser user;
	    private async Task<bool> Authenticate()
	    {
	            var success = false;
	            try
	            {
	                // Sign in with Facebook login using a server-managed flow.
	                user = await client.LoginAsync(this,
	                    MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}",
	                    user.UserId), "Logged in!");

	                success = true;
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	            return success;
	    }

        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;

                // Load the data.
                OnRefreshItemsSelected();
            }
        }

    При этом создается новый метод для проверки подлинности пользователя и метод обработчика для новой кнопки **Вход**. Пользователь в примере кода выше аутентифицируется с помощью имени для входа Facebook. Диалоговое окно используется для отображения идентификатора пользователя после аутентификации.

    > [AZURE.NOTE] Если вы используете поставщик удостоверений, отличный от Facebook, замените значение, передаваемое в метод **LoginAsync** выше, одним из следующих: _MicrosoftAccount_, _Twitter_, _Google_ или _WindowsAzureActiveDirectory_.

3. В методе **OnCreate** удалите или закомментируйте следующую строку кода:

		OnRefreshItemsSelected ();

4. В файле Activity\_To\_Do.axml вставьте определение кнопки *LoginUser* перед уже существующей кнопкой *AddItem*:

      	<Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

5. Добавьте следующий элемент в файл ресурсов Strings.xml:

		<string name="login_button_text">Sign in</string>

6. В Visual Studio или Xamarin Studio запустите клиентский проект на устройстве или в эмуляторе либо выполните вход с помощью выбранного поставщика удостоверений.

   	После успешного выполнения входа в приложении отобразится ваш идентификатор для входа и список элементов задач, и вы сможете внести изменения в данные.


<!-- URLs. -->
[Создание приложения Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md

<!---HONumber=AcomDC_0706_2016-->