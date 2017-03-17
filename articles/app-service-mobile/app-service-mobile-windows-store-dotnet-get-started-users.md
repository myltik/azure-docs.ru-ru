---
title: "Добавление проверки подлинности в приложение универсальной платформы Windows (UWP) | Документация Майкрософт"
description: "Узнайте, как использовать мобильные приложения службы приложений Azure, чтобы выполнять аутентификацию пользователей приложения универсальной платформы Windows (UWP) с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт."
services: app-service\mobile
documentationcenter: windows
author: adrianhall
manager: adrianha
editor: 
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 96b87d4d6cc1adbc9700102ffd4a989451676d81
ms.lasthandoff: 03/09/2017


---
# <a name="add-authentication-to-your-windows-app"></a>Добавление проверки подлинности в приложение Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

В этом разделе показано, как добавить проверку подлинности на основе облака в мобильное приложение. В этом учебнике вы добавите проверку подлинности в проект быстрого запуска универсальной платформы Windows (UWP) для мобильных приложений, используя поставщик удостоверений, поддерживаемый службой приложений Azure. После успешной проверки подлинности и авторизации сервером мобильных приложений отображается значение идентификатора пользователя.

Этот учебник создан на основе краткого руководства по мобильным приложениям. Вам необходимо сначала изучить учебник [Начало работы с мобильными приложениями](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Регистрация приложения для проверки подлинности и настройка службы приложений
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Теперь можно убедиться, что анонимный доступ к серверной части был отключен. Назначив проект приложения UWP запускаемым проектом, разверните и запустите приложение. Убедитесь в том, что после его запуска порождается необработанное исключение с кодом состояния 401 (не авторизован). Это происходит потому, что приложение пытается получить доступ к коду мобильного приложения от имени пользователя, не прошедшего проверку подлинности, но таблице *TodoItem* теперь требуется проверка подлинности.

Далее вы обновите приложение, чтобы оно выполняло проверку подлинности пользователей перед запросом ресурсов из службы приложений.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение
1. Откройте файл проекта приложения UWP MainPage.cs и добавьте следующий фрагмент кода в класс MainPage:
   
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
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
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
2. Закомментируйте или удалите вызов метода **ButtonRefresh_Click** (или метода **InitLocalStoreAsync**) в имеющемся переопределении метода **OnNavigatedTo**. Это позволяет предотвратить загрузку данных до того, как пользователь прошел проверку подлинности. Затем добавьте кнопку **Вход** в приложение, которое запускает проверку подлинности.
3. Добавьте в класс MainPage следующий фрагмент кода:
   
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
5. Нажмите клавишу F5, чтобы запустить приложение, нажмите кнопку **Вход** и войдите в приложение с помощью выбранного поставщика удостоверений. После успешного входа приложение работает без ошибок, а вы должны быть в состоянии выполнять запросы к серверной части и обновлять данные.

## <a name="tokens"></a>Сохранение токена проверки подлинности в клиенте
В предыдущем примере был показан стандартный вход, при котором клиенту нужно подключаться к поставщику удостоверений и службе приложений каждый раз, когда приложение запускается. Мало того что этот метод неэффективен, вы можете столкнуться с проблемами, связанными с использованием приложения, если большое количество клиентов попытаются запустить приложение одновременно. Лучше кэшировать токен авторизации, который возвратила служба приложений, причем делать это до входа через поставщика.

> [!NOTE]
> Токен, выданный службами приложений, можно кэшировать независимо от используемой аутентификации (управляемая службой либо клиентом). Этот учебник использует управляемую сервером проверку подлинности.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Дальнейшие действия
Вы прошли этот учебник по обычной проверке подлинности и теперь можете перейти к одному из следующих учебников:

* [Добавление push-уведомлений в приложение](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Узнайте, как добавить поддержку push-уведомлений в мобильное приложение и настроить в его серверной части использование концентраторов уведомлений Azure для отправки push-уведомлений.
* [Включение автономной синхронизации для приложения](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Узнайте, как добавить в приложение поддержку автономной работы с помощью серверной части мобильного приложения. Автономная синхронизация позволяет пользователям взаимодействовать с мобильным приложением &mdash; просматривать, добавлять или изменять данные &mdash; даже при отсутствии подключения к сети.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md


