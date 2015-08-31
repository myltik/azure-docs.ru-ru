1. На компьютере Mac откройте файл **QSTodoListViewController.m** в Xcode и добавьте следующий метод: Измените _facebook_ на _microsoftaccount_, _twitter_, _google_ или _windowsazureactivedirectory_, если вы не используете Facebook как поставщик удостоверений.

        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }

2. Замените `[self refresh]` в `viewDidLoad` следующим кодом:

        [self loginAndGetData];

3. Нажмите **Выполнить**, чтобы запустить приложение, и войдите в систему. После входа вы должны увидеть список Todo и сможете вносить изменения.

<!---HONumber=August15_HO8-->