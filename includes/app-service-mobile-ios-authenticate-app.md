**Objective-C**:

1. На компьютере Mac откройте файл _QSTodoListViewController.m_ в Xcode и добавьте следующий метод: Измените _google_ на _microsoftaccount_, _twitter_, _facebook_ или _windowsazureactivedirectory_, если вы не используете Google как поставщик удостоверений. Если вы используете Facebook, [потребуется добавить домены Facebook в белый список в приложении](https://developers.facebook.com/docs/ios/ios9#whitelist).

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. Замените `[self refresh]` в `viewDidLoad` в файле _QSTodoListViewController.m_ следующим:

            [self loginAndGetData];

3. Нажмите _Выполнить_, чтобы запустить приложение, и войдите в систему. После входа вы должны увидеть список Todo и сможете вносить изменения.

**Swift**:

1. На компьютере Mac откройте файл _ToDoTableViewController.swift_ в Xcode и добавьте следующий метод: Измените _google_ на _microsoftaccount_, _twitter_, _facebook_ или _windowsazureactivedirectory_, если вы не используете Google как поставщик удостоверений. Если вы используете Facebook, [потребуется добавить домены Facebook в белый список в приложении](https://developers.facebook.com/docs/ios/ios9#whitelist).
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. Удалите строки `self.refreshControl?.beginRefreshing()` и `self.onRefresh(self.refreshControl)` в конце `viewDidLoad()` в _ToDoTableViewController.swift_. Добавьте вызов `loginAndGetData()` вместо них:

            loginAndGetData()

3. Нажмите _Выполнить_, чтобы запустить приложение, и войдите в систему. После входа вы должны увидеть список Todo и сможете вносить изменения.

<!---HONumber=AcomDC_0218_2016-->