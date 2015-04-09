1. Откройте элемент **QSTodoListViewController.m** и в методе **viewDidLoad** удалите следующую строку:

        [self refresh];

2.	Сразу после метода **viewDidLoad** добавьте следующий код:  

        - (void)viewDidAppear:(BOOL)animated
        {
            MSClient *client = self.todoService.client;

            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }

    > [AZURE.NOTE] Если используется поставщик удостоверений, отличный от Facebook, измените значение, передаваемое в **loginWithProvider**. Поддерживаемые значения: _microsoftaccount_, _facebook_, _twitter_, _google_ или _windowsazureactivedirectory_.

3. Нажмите **Запуск**, чтобы запустить приложение и выполнить вход с помощью выбранного поставщика удостоверений. После входа вы должны увидеть список Todo и сможете вносить изменения.

<!--HONumber=49-->