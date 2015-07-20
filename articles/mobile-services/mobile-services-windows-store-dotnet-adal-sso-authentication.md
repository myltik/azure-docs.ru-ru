<properties 
	pageTitle="Проверка подлинности приложения с помощью библиотеки проверки подлинности Active Directory с единым входом (Магазин Windows) | Центр мобильных разработок" 
	description="Узнайте, как аутентифицировать пользователей для единого входа с помощью ADAL в приложении магазина Windows." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="wesmc"/>

# Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##Обзор

В этом учебнике вы научитесь добавлять проверку подлинности в проект быстрого запуска с помощью библиотеки проверки подлинности Active Directory, чтобы оказывать поддержку [операциям входа, ориентированным на клиента](http://msdn.microsoft.com/library/azure/jj710106.aspx), с помощью Azure Active Directory. Чтобы научиться с помощью Azure Active Directory оказывать поддержку [операциям входа, ориентированным на службы](http://msdn.microsoft.com/library/azure/dn283952.aspx), выполните задания учебника [Добавление проверки подлинности в приложение мобильных служб](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md).

Чтобы иметь возможность проверки подлинности пользователей необходимо зарегистрировать приложение в Azure Active Directory (AAD). Это делается в два этапа. Сначала следует зарегистрировать мобильную службу и предоставить в ней разрешения. Затем нужно зарегистрировать приложение Магазина Windows и предоставить ему доступ к этим разрешениям.


>[AZURE.NOTE]Этот учебник поможет лучше понять, как мобильные службы позволяют выполнять проверку подлинности Azure Active Directory единого входа для приложений Магазина Windows с помощью [операции входа, ориентированной на клиента](http://msdn.microsoft.com/library/azure/jj710106.aspx). Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами].


##Предварительные требования

Для работы с данным учебником требуется следующее:

* Visual Studio 2013 в Windows 8.1.
* Изучение учебника [Приступая к работе с мобильными службами] или [Приступая к работе с данными].
* Пакет NuGet пакета SDK мобильных служб Microsoft Azure
* Пакет NuGet библиотеки проверки подлинности Active Directory 

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../../includes/mobile-services-dotnet-adal-register-service.md)]

##Регистрация приложения в Azure Active Directory

Чтобы зарегистрировать приложение в Azure Active Directory, необходимо сопоставить его с Магазином Windows и получить идентификатор безопасности пакета (SID) для приложения. SID пакета регистрируется с параметрами собственного приложения в Azure Active Directory.


###Сопоставление приложения с новым именем приложения Магазина

1. В Visual Studio щелкните правой кнопкой мыши проект клиентского приложения, а затем выберите пункты **Магазин** и **Связать приложение с Магазином**.

    ![][1]

2. Войдите в свою учетную запись в Центре разработки.

3. Введите имя, которое планируется зарезервировать для приложения, и нажмите кнопку **Зарезервировать**.

    ![][2]

4. Выберите новое имя приложения и нажмите кнопку **Далее**.

5. Нажмите **Сопоставить**, чтобы сопоставить приложение с именем в Магазине.


###Получение SID пакета для приложения

Теперь необходимо получить SID пакета, который будет настроен с параметрами собственного приложения.

1. Войдите в свою [панель мониторинга Центра разработки для Windows] и нажмите кнопку **Правка** в приложении.

    ![][3]

2. Затем нажмите **Службы**.

    ![][4]

3. Затем нажмите **Сайт служб Live**.

    ![][5]

4. Скопируйте SID пакета, который находится вверху страницы.

    ![][6]

###Создание регистрации собственного приложения

1. На **портале управления Azure** перейдите в раздел [Active Directory] и щелкните свой каталог.

    ![][7]

2. Щелкните вкладку **Приложения** наверху, затем нажмите кнопку **ДОБАВИТЬ**, чтобы добавить приложение.

    ![][8]

3. Выберите команду **Добавить приложение, разрабатываемое моей организацией**.

4. В мастере добавления приложения введите **имя** для приложения и выберите тип **Собственное клиентское приложение**. Затем щелкните "Далее".

    ![][9]

5. В поле **URI перенаправления** вставьте SID пакета приложения, скопированный ранее, а затем нажмите кнопку завершения регистрации собственного приложения.

    ![][10]

6. Перейдите на вкладку **Конфигурация** собственного приложения и скопируйте **ИД клиента**. Этот идентификатор потребуется позднее.

    ![][11]

7. Прокрутите страницу вниз до раздела **разрешений для других приложений** и предоставьте полный доступ к приложению мобильной службы, зарегистрированному ранее. Затем нажмите кнопку **Сохранить**.

    ![][12]

Теперь мобильная служба настроена в AAD для принятия попыток единого входа из приложения.



##Настройка мобильной службы для требования проверки подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##Добавление кода проверки подлинности в клиентское приложение

1. Откройте проект клиентского приложения Магазина Windows в Visual Studio.

[AZURE.INCLUDE [mobile-services-dotnet-adal-install-nuget](../../includes/mobile-services-dotnet-adal-install-nuget.md)]

4. В окне обозревателя решений Visual Studio откройте файл MainPage.xaml.cs и добавьте следующие директивы using.

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;


5. Добавьте следующий фрагмент кода в класс MainPage, объявляющий метод `AuthenticateAsync`.

        private MobileServiceUser user; 
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>"; 
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID, (Uri) null);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                } 
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            } 
        }

6. В коде для метода `AuthenticateAsync` выше замените **INSERT-AUTHORITY-HERE** именем клиента, в котором приложение подготавливалось к работе, используя формат https://login.windows.net/tenant-name.onmicrosoft.com. Это значение можно скопировать со вкладки "Домен" в Azure Active Directory на [портале управления Azure].

7. В коде для метода `AuthenticateAsync` выше замените **INSERT-RESOURCE-URI-HERE** значением **URI кода приложения** для своей мобильной службы. Если вы выполнили инструкции из раздела [Регистрация в Azure Active Directory], то URI кода приложения должен быть аналогичен https://todolist.azure-mobile.net/login/aad.

8. В коде для метода `AuthenticateAsync` выше замените **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.

9. В окне обозревателя решений в Visual Studio откройте файл Package.appxmanifest в клиентском проекте. Перейдите на вкладку **Возможности** и установите флажки **Корпоративное приложение** и **Частные сети (клиент и сервер)**. Сохраните файл.

    ![][14]

10. В файле MainPage.cs обновите обработчик событий `OnNavigatedTo` для вызова метода `AuthenticateAsync` следующим образом:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            await RefreshTodoItems();
        }


##Тестирование клиента с использованием проверки подлинности

1. Запустите клиентское приложение в Visual Studio.
2. Появится приглашение выполнить вход в Azure Active Directory.  
3. Приложение выполняет проверку подлинности и возвращает элементы списка дел.

    ![][15]




<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-app-manage-manifest.png
[1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-vs-associate-app.png
[2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-vs-reserve-store-appname.png
[3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-edit.png
[4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-services.png
[5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-live-services-site.png
[6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-package-sid.png
[7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-select-aad.png
[8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-aad-applications-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-selection.png
[10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-sid-redirect-uri.png
[11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-client-id.png
[12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-add-permissions.png
[14]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-package-appxmanifest.png
[15]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-app-run.png

<!-- URLs. -->
[Регистрация в Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Active Directory]: https://manage.windowsazure.com/
[портале управления Azure]: https://manage.windowsazure.com/
[Приступая к работе с данными]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[панель мониторинга Центра разработки для Windows]: http://go.microsoft.com/fwlink/p/?LinkID=266734

<!---HONumber=July15_HO2-->