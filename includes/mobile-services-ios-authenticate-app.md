

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

    <div class="dev-callout"><b>Примечание.</b>
	<p>Если используется поставщик удостоверений, отличный от Facebook, замените значение, передаваемое в <strong>loginWithProvider</strong>, одним из следующих: <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em>, <em>google</em> или <em>windowsazureactivedirectory</em>.</p>
    </div>
		
3. Нажмите кнопку **Выполнить**, чтобы построить проект, запустите приложение в эмуляторе iPhone, затем войдите в систему с помощью выбранного поставщика идентификации.

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.
