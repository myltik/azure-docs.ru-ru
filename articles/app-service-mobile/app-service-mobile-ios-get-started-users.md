---
title: Добавление проверки подлинности на iOS с помощью мобильных приложений Azure
description: Узнайте, как использовать мобильные приложения для проверки подлинности пользователей приложения iOS с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: crdun
ms.openlocfilehash: e0eeee05ebad2e8148752f988bbbc2f6a0d7c296
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
ms.locfileid: "27592700"
---
# <a name="add-authentication-to-your-ios-app"></a>Добавление проверки подлинности в приложение iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

В этом учебнике описывается добавление проверки подлинности в проект учебника по [быстрому запуску iOS] с помощью поддерживаемого поставщика удостоверений. Этот учебник использует материал учебника по [быстрому запуску iOS] , который необходимо пройти в первую очередь.

## <a name="register"></a>Регистрация приложения для проверки подлинности и настройка службы приложений
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Добавление приложения в список разрешенных URL-адресов внешнего перенаправления

Для безопасной аутентификации требуется определить новую схему URL-адресов для своего приложения.  Это позволяет системе аутентификации выполнять перенаправление обратно в приложение после завершения процесса аутентификации.  В этом руководстве мы повсеместно используем схему URL-адресов _appname_.  Тем не менее можно использовать любую схему URL-адресов на свой выбор.  Она должна быть уникальной для мобильного приложения.  Вот как можно включить перенаправление на стороне сервера.

1. На [портале Azure] выберите свою службу приложений.

2. Выберите пункт меню **Аутентификация или авторизация**.

3. В разделе **Поставщики проверки подлинности** щелкните **Azure Active Directory**.

4. Для параметра **Режим управления** задайте значение **Дополнительно**.

5. В поле **Разрешенные URL-адреса внешнего перенаправления** введите `appname://easyauth.callback`.  _appname_ в этой строке — это схема URL-адресов для вашего мобильного приложения.  Она должна соответствовать обычной спецификации URL-адресов для протокола (можно использовать буквы и цифры, и адрес должен начинаться с буквы).  Необходимо записать выбранную строку, так как потребуется в нескольких местах настроить код мобильного приложения с использованием схемы URL-адресов.

6. Последовательно выберите **ОК**.

7. Выберите команду **Сохранить**.

## <a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

В Xcode нажмите кнопку **Выполнить** , чтобы запустить приложение. Будет порождено исключение, так как приложение попытается получить доступ к серверной части как пользователь, не прошедший аутентификацию, а для таблицы *TodoItem* теперь требуется аутентификация.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение
**Objective-C**:

1. На компьютере Mac откройте файл *QSTodoListViewController.m* в Xcode и добавьте следующий метод.

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Измените *google* на *microsoftaccount*, *twitter*, *facebook* или *windowsazureactivedirectory*, если Google не используется как поставщик удостоверений. Если используется Facebook, то [требуется добавить домены Facebook в список разрешений][1] в приложении.

    Замените **urlScheme** уникальным именем своего приложения.  Значение urlScheme должно быть таким же, как и для протокола схемы URL-адресов, указанного в поле **Разрешенные URL-адреса внешнего перенаправления** на портале Azure. urlScheme используется при обратном вызове аутентификации для переключения на приложение после завершения запроса аутентификации.

2. Замените `[self refresh]` в `viewDidLoad` в файле *QSTodoListViewController.m* следующим кодом.

    ```Objective-C
    [self loginAndGetData];
    ```

3. Откройте файл `QSAppDelegate.h` и добавьте в него следующий код.

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Откройте файл `QSAppDelegate.m` и добавьте в него следующий код.

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Добавьте этот код непосредственно перед строкой `#pragma mark - Core Data stack`.  Замените _appname_ значением urlScheme, указанным на шаге 1.

5. Откройте файл `AppName-Info.plist` (замените AppName именем своего приложения) и добавьте в него следующий код.

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Этот код должен быть помещен в элемент `<dict>`.  Замените строку _appname_ (в массиве для **CFBundleURLSchemes**) именем приложения, выбранным на шаге 1.  Внести эти изменения можно также в редакторе PList. Щелкните файл `AppName-Info.plist` в XCode, чтобы открыть редактор PList.

    Замените строку `com.microsoft.azure.zumo` для **CFBundleURLName** идентификатором своего пакета Apple.

6. Нажмите кнопку *Выполнить*, чтобы запустить приложение, и войдите в систему. После входа вы должны увидеть список Todo и сможете вносить изменения.

**Swift**:

1. На компьютере Mac откройте файл *ToDoTableViewController.swift* в Xcode и добавьте следующий метод.

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Измените *google* на *microsoftaccount*, *twitter*, *facebook* или *windowsazureactivedirectory*, если Google не используется как поставщик удостоверений. Если используется Facebook, то [требуется добавить домены Facebook в список разрешений][1] в приложении.

    Замените **urlScheme** уникальным именем своего приложения.  Значение urlScheme должно быть таким же, как и для протокола схемы URL-адресов, указанного в поле **Разрешенные URL-адреса внешнего перенаправления** на портале Azure. urlScheme используется при обратном вызове аутентификации для переключения на приложение после завершения запроса аутентификации.

2. Удалите строки `self.refreshControl?.beginRefreshing()` и `self.onRefresh(self.refreshControl)` в конце `viewDidLoad()` в файле *ToDoTableViewController.swift*. Добавьте вызов `loginAndGetData()` вместо них:

    ```swift
    loginAndGetData()
    ```

3. Откройте файл `AppDelegate.swift` и добавьте следующую строку в класс `AppDelegate`.

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Замените _appname_ значением urlScheme, указанным на шаге 1.

4. Откройте файл `AppName-Info.plist` (замените AppName именем своего приложения) и добавьте в него следующий код.

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Этот код должен быть помещен в элемент `<dict>`.  Замените строку _appname_ (в массиве для **CFBundleURLSchemes**) именем приложения, выбранным на шаге 1.  Внести эти изменения можно также в редакторе PList. Щелкните файл `AppName-Info.plist` в XCode, чтобы открыть редактор PList.

    Замените строку `com.microsoft.azure.zumo` для **CFBundleURLName** идентификатором своего пакета Apple.

5. Нажмите кнопку *Выполнить*, чтобы запустить приложение, и войдите в систему. После входа вы должны увидеть список Todo и сможете вносить изменения.

Для аутентификации службы приложений используется технология взаимодействия приложений Apple.  Дополнительные сведения по этой теме доступны в [документации Apple][2].
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[портале Azure]: https://portal.azure.com

[быстрому запуску iOS]: app-service-mobile-ios-get-started.md

