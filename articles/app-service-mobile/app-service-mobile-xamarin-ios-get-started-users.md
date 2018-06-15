---
title: Начало работы с проверкой подлинности для мобильных приложений в Xamarin iOS
description: Использование мобильных приложений для проверки подлинности пользователей приложения Xamarin iOS с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: crdun
ms.openlocfilehash: c2b77d7c9caab1c7ac13af5ccd50e992bcce1caf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31787664"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Добавление проверки подлинности в приложение Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

В этом разделе показано, как выполнить проверку подлинности пользователей мобильного приложения службы приложений из клиентского приложения. В этом учебнике приведены инструкции, позволяющие добавить проверку подлинности в ознакомительный проект Xamarin.Forms, используя поставщик удостоверений, поддерживаемый службой приложений. После успешной проверки подлинности и авторизации мобильным приложением отображается значение идентификатора пользователя, и вы сможете получить доступ к закрытым табличным данным.

Сначала необходимо выполнить инструкции из руководства [Создание приложения Xamarin.iOS]. Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений для аутентификации. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Регистрация приложения для проверки подлинности и настройка служб приложений
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Добавление приложения в список разрешенных URL-адресов внешнего перенаправления

Для безопасной аутентификации требуется определить новую схему URL-адресов для своего приложения. Это позволяет системе аутентификации выполнять перенаправление обратно в приложение после завершения процесса аутентификации. В этом руководстве мы повсеместно используем схему URL-адресов _appname_. Тем не менее можно использовать любую схему URL-адресов на свой выбор. Она должна быть уникальной для мобильного приложения. Вот как можно включить перенаправление на стороне сервера.

1. На портале Azure выберите свою службу приложений.

2. Выберите пункт меню **Аутентификация или авторизация**.

3. В поле **Разрешенные URL-адреса внешнего перенаправления** введите `url_scheme_of_your_app://easyauth.callback`.  **url_scheme_of_your_app** в этой строке — это схема URL-адресов для вашего мобильного приложения.  Она должна соответствовать обычной спецификации URL-адресов для протокола (можно использовать буквы и цифры, и адрес должен начинаться с буквы).  Необходимо записать выбранную строку, так как потребуется в нескольких местах настроить код мобильного приложения с использованием схемы URL-адресов.

4. Последовательно выберите **ОК**.

5. Выберите команду **Сохранить**.

## <a name="restrict-permissions-to-authenticated-users"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. В Visual Studio или Xamarin Studio запустите клиентский проект на устройстве или в эмуляторе. Убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный). Ошибка записывается в консоль отладчика. Поэтому в Visual Studio вы увидите ошибку в окне вывода.

&nbsp;&nbsp;Эта нештатная ошибка происходит потому, что приложение пытается получить доступ к серверной части мобильного приложения от имени не прошедшего проверку подлинности пользователя. Теперь для таблицы *TodoItem* требуется аутентификация.

Далее вы обновите клиентское приложение для запроса ресурсов из серверной части мобильного приложения прошедшим аутентификацию пользователем.

## <a name="add-authentication-to-the-app"></a>Добавление проверки подлинности в приложение
В этом разделе предстоит изменить приложение для отображения экрана входа до отображения данных. При запуске приложение не подключится к службе приложений и не отобразит никаких данных. После первого обновления пользователем появится экран входа; список задач появится после успешного входа.

1. В проекте клиента откройте файл **QSTodoService.cs** и добавьте следующий оператор using и объект `MobileServiceUser` с методом доступа к классу QSTodoService:
 
        using UIKit;
       
        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }
2. Добавьте новый метод **Authenticate** в **QSTodoService** со следующим определением:

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                AppDelegate.ResumeWithURL = url => url.Scheme == "zumoe2etestapp" && client.ResumeWithURL(url);
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Если у вас поставщик удостоверений, отличный от Facebook, замените значение, передаваемое в метод **LoginAsync** выше, одним из следующих: _MicrosoftAccount_, _Twitter_, _Google_ или _WindowsAzureActiveDirectory_.

3. Откройте файл **QSTodoListViewController.cs**. Измените определение метода **ViewDidLoad**, удалив вызов **RefreshAsync()** в конце:
   
        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();
   
            todoService = QSTodoService.DefaultService;
            await todoService.InitializeStoreAsync();
   
            RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync();
            }
   
            // Comment out the call to RefreshAsync
            // await RefreshAsync();
        }
4. Измените метод **RefreshAsync** для проверки подлинности, если для свойства **User** задано значение null. Добавьте следующий код в верхнюю часть определения метода:
   
        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate(this);
            if (todoService.User == null) {
                Console.WriteLine("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method
5. Откройте файл **AppDelegate.cs** и добавьте следующий метод:

        public static Func<NSUrl, bool> ResumeWithURL;

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return ResumeWithURL != null && ResumeWithURL(url);
        }
6. Откройте файл **Info.plist** и перейдите к записи **Типы URL-адресов** в разделе **Дополнительно**. Теперь настройте **идентификатор** и **схемы URL-адресов** для типов URL-адресов, а затем щелкните **Добавить тип URL-адресов**. **Схемы URL-адресов** должны совпадать с {url_scheme_of_your_app}.
7. В среде Visual Studio, подключенной к узлу Mac или Visual Studio для Mac, запустите клиентский проект, указав устройство или эмулятор. Убедитесь, что в приложении не отображаются данные.
   
    Обновите, потянув вниз список элементов, чтобы появился экран входа. После успешного ввода допустимых учетных данных в приложении отобразится список элементов задач и вы сможете внести изменения в данные.

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Создание приложения Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
