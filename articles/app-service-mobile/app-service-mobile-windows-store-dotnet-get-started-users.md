---
title: Добавление проверки подлинности в приложение универсальной платформы Windows (UWP) | Документация Майкрософт
description: Узнайте, как использовать мобильные приложения службы приложений Azure, чтобы выполнять аутентификацию пользователей приложения универсальной платформы Windows (UWP) с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт.
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: panarasi
editor: ''
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 4cc597f8aca13445034c8a1691b41018d4d9bc4b
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
ms.locfileid: "27592150"
---
# <a name="add-authentication-to-your-windows-app"></a>Добавление проверки подлинности в приложение Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

В этом разделе показано, как добавить проверку подлинности на основе облака в мобильное приложение. В этом учебнике вы добавите проверку подлинности в проект быстрого запуска универсальной платформы Windows (UWP) для мобильных приложений, используя поставщик удостоверений, поддерживаемый службой приложений Azure. После успешной проверки подлинности и авторизации сервером мобильных приложений отображается значение идентификатора пользователя.

Этот учебник создан на основе краткого руководства по мобильным приложениям. Вам необходимо сначала изучить учебник [Начало работы с мобильными приложениями](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Регистрация приложения для проверки подлинности и настройка службы приложений
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Добавление приложения в список разрешенных URL-адресов внешнего перенаправления

Для безопасной аутентификации требуется определить новую схему URL-адресов для своего приложения. Это позволяет системе аутентификации выполнять перенаправление обратно в приложение после завершения процесса аутентификации. В этом руководстве мы повсеместно используем схему URL-адресов _appname_. Тем не менее можно использовать любую схему URL-адресов на свой выбор. Она должна быть уникальной для мобильного приложения. Вот как можно включить перенаправление на стороне сервера.

1. На портале Azure выберите свою службу приложений.

2. Выберите пункт меню **Аутентификация или авторизация**.

3. В поле **Разрешенные URL-адреса внешнего перенаправления** введите `url_scheme_of_your_app://easyauth.callback`.  **url_scheme_of_your_app** в этой строке — это схема URL-адресов для вашего мобильного приложения.  Она должна соответствовать обычной спецификации URL-адресов для протокола (можно использовать буквы и цифры, и адрес должен начинаться с буквы).  Необходимо записать выбранную строку, так как потребуется в нескольких местах настроить код мобильного приложения с использованием схемы URL-адресов.

4. Последовательно выберите **ОК**.

5. Выберите команду **Сохранить**.

## <a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Теперь можно убедиться, что анонимный доступ к серверной части был отключен. Назначив проект приложения UWP запускаемым проектом, разверните и запустите приложение. Убедитесь в том, что после его запуска порождается необработанное исключение с кодом состояния 401 (не авторизован). Это происходит потому, что приложение пытается получить доступ к коду мобильного приложения от имени пользователя, не прошедшего проверку подлинности, но таблице *TodoItem* теперь требуется проверка подлинности.

Далее вы обновите приложение, чтобы оно выполняло проверку подлинности пользователей перед запросом ресурсов из службы приложений.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение
1. В файле MainPage.xaml.cs проекта приложения UWP добавьте следующий фрагмент кода:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Этот код выполняет проверку подлинности пользователя с помощью имени входа в Facebook. Если используется поставщик удостоверений, отличный от Facebook, измените значение **MobileServiceAuthenticationProvider** выше на значение для вашего поставщика.
2. Замените метод **OnNavigatedTo()** в файле MainPage.xaml.cs. Затем добавьте кнопку **Вход** в приложение, которое запускает проверку подлинности.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Добавьте в файл MainPage.xaml.cs следующий фрагмент кода:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Откройте файл проекта MainPage.xaml, найдите элемент, который определяет кнопку **Сохранить** и замените его следующим кодом:
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Добавьте в файл App.xaml.cs следующий фрагмент кода:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Откройте файл Package.appxmanifest, перейдите к разделу **Объявления**, в раскрывающемся списке **Доступные объявления** выберите пункт **Протокол** и нажмите кнопку **Добавить**. Далее настройте **свойства** объявления **протокола**. В поле **Отображаемое имя** добавьте имя, которое должно отображаться для пользователей приложения. В поле **Имя** добавьте {url_scheme_of_your_app}.
7. Нажмите клавишу F5, чтобы запустить приложение, нажмите кнопку **Вход** и войдите в приложение с помощью выбранного поставщика удостоверений. После успешного входа приложение работает без ошибок, а вы должны быть в состоянии выполнять запросы к серверной части и обновлять данные.

## <a name="tokens"></a>Сохранение токена проверки подлинности в клиенте
В предыдущем примере был показан стандартный вход, при котором клиенту нужно подключаться к поставщику удостоверений и службе приложений каждый раз, когда приложение запускается. Мало того что этот метод неэффективен, вы можете столкнуться с проблемами, связанными с использованием приложения, если большое количество клиентов попытаются запустить приложение одновременно. Лучше кэшировать токен авторизации, который возвратила служба приложений, причем делать это до входа через поставщика.

> [!NOTE]
> Токен, выданный службами приложений, можно кэшировать независимо от используемой аутентификации (управляемая службой либо клиентом). Этот учебник использует управляемую сервером проверку подлинности.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Дополнительная информация
Вы прошли этот учебник по обычной проверке подлинности и теперь можете перейти к одному из следующих учебников:

* [Добавление push-уведомлений в приложение](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Узнайте, как добавить поддержку push-уведомлений в мобильное приложение и настроить в его серверной части использование концентраторов уведомлений Azure для отправки push-уведомлений.
* [Включение автономной синхронизации для приложения](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Узнайте, как добавить в приложение поддержку автономной работы с помощью серверной части мобильного приложения. Автономная синхронизация позволяет пользователям взаимодействовать с мобильным приложением &mdash; просматривать, добавлять или изменять данные &mdash; даже при отсутствии подключения к сети.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
