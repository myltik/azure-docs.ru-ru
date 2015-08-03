

1. Откройте файл **QSTodoListViewController.m** и добавьте следующий метод:


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


    > [AZURE.NOTE]Если используется поставщик удостоверений, отличный от Facebook, измените значение, передаваемое в **loginWithProvider**. Поддерживаются такие значения: _microsoftaccount_, _facebook_, _twitter_, _google_ и _windowsazureactivedirectory_.


2. Измените `viewDidLoad`, заменив `[self refresh]` в конце на следующий код:

        [self loginAndGetData];

3. Нажмите кнопку **Выполнить**, чтобы запустить приложение и войти в систему с помощью выбранного поставщика удостоверений. После входа вы должны увидеть список Todo и сможете вносить изменения.

<!---HONumber=July15_HO4-->