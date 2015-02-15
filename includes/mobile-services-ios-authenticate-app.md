

1. Откройте файл проекта QSTodoListViewController.m и в методе **viewDidLoad** удалите следующий код, который повторно загружает данные в таблицу:

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

    > [AZURE.NOTE] Если используется поставщик удостоверений, отличный от Facebook, измените значение, передаваемое в **loginWithProvider** выше, одним из следующих: _microsoftaccount_, _facebook_, _twitter_, _google_ или _windowsazureactivedirectory_.
		
3. Чтобы выполнить сборку проекта, нажмите кнопку **Запуск**, запустите приложение в эмуляторе iPhone, а затем войдите с использованием выбранного поставщика удостоверений.

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.
<!--HONumber=42-->
