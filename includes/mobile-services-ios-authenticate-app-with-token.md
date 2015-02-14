
В предыдущем примере был показан стандартный вход, который требует, чтобы клиент подключался как к поставщику удостоверений, так и к мобильной службе каждый раз, когда приложение запускается. Мало того что этот метод неэффективен, вы можете столкнуться с проблемами, связанными с использованием приложения, если запустить его пытаются одновременно большое количество пользователей. Несколько лучшим подходом является проверка подлинности удостоверений с кэшированием маркера, возвращенного мобильной службой, которая будет использоваться до входа на основе поставщика. 


>[AZURE.NOTE] Можно кэшировать маркер, выданный мобильной службой, независимо от того, используете ли вы аутентификацию, управляемую клиентом или сервером. Этот учебник использует управляемую сервером проверку подлинности.

1. Рекомендуется для шифрования и хранения токенов проверки подлинности для клиента iOS использовать цепочку ключей. Для этого создайте класс KeychainWrapper, скопировав [KeychainWrapper.m](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.m) и [KeychainWrapper.h](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.h) из примера [LensRocket](https://github.com/WindowsAzure-Samples/iOS-LensRocket). Данный KeychainWrapper используется в этом случае потому, что KeychainWrapper, определенный в документациии Apple, не годится для автоматического подсчета ссылок (ARC).


2. Откройте файл проекта **QSTodoListViewController.m** и добавьте следующий код:

		
		- (void) saveAuthInfo{
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.userId
				 forIdentifier:@"userid"];
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.mobileServiceAuthenticationToken
				 forIdentifier:@"token"];
		}
		
		
		- (void)loadAuthInfo {
		    NSString *userid = [KeychainWrapper keychainStringFromMatchingIdentifier:@"userid"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		        self.todoService.client.currentUser.mobileServiceAuthenticationToken = [KeychainWrapper keychainStringFromMatchingIdentifier:@"token"];
		    }
		}
		


3. В конце метода **viewDidAppear** в **QSTodoListViewController.m** добавьте вызов saveAuthInfo. Этим вызовом мы просто сохраняем свойства userId и token.  



		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		
		        [self saveAuthInfo];
		        [self refresh];
		    }];
		}

  
4. Итак, мы выяснили, как можно кэшировать маркер и идентификатор пользователя. Давайте посмотрим, как загрузить их при запуске приложения. В методе **viewDidLoad** в **QSTodoListViewController.m** добавьте вызов loadAuthInfo после инициализации **self.todoService**. 
		
		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    
		    // Create the todoService - this creates the Mobile Service client inside the wrapped service
		    self.todoService = [QSTodoService defaultService];

			[self loadAuthInfo];
		    
		    // Set the busy method
		    UIActivityIndicatorView *indicator = self.activityIndicator;
		    self.todoService.busyUpdate = ^(BOOL busy)
		    {
		        if (busy)
		        {
		            [indicator startAnimating];
		        } else
		        {
		            [indicator stopAnimating];
		        }
		    };
		    
		    // have refresh control reload all data from server
		    [self.refreshControl addTarget:self
		                            action:@selector(onRefresh:)
		                  forControlEvents:UIControlEventValueChanged];
		
		    // load the data
		    [self refresh];
		}

5. Если приложение делает запрос к мобильной службе, который должен пройти (поскольку пользователь прошел проверку подлинности), но вы получаете ответ 401 (ошибка "Не санкционировано"), это означает, что передаваемый маркер пользователя просрочен. Мы можем выполнять проверку на ответ 401 в завершающих обработчиках всех методов, которые взаимодействуют с нашей мобильной службой, а можно обработать это в одном месте: в методе handleRequest в MSFilter.  Обработка данного сценария описана в этой [записи блога](http://www.thejoyofcode.com/Handling_expired_tokens_in_your_application_Day_11_.aspx).

<!--HONumber=42-->
