
В предыдущем примере показан стандартный вход, для которого нужно, чтобы клиент подключался как к поставщику удостоверений, так и к службе приложений каждый раз, когда приложение запускается. Этот метод неэффективен. Лучше кэшировать токен авторизации, который возвратила служба приложений, причем делать это до входа через поставщика.

1. Для шифрования и хранения маркеров авторизации для клиента iOS рекомендуется использовать цепочку ключей iOS. В этом учебнике используется [SSKeychain](https://github.com/soffes/sskeychain) — простая оболочка для оболочки ключей iOS. Выполните инструкции на странице SSKeychain и добавьте эту цепочку в свой проект. Убедитесь, что параметр **Включить модули** в разделе проекта **Параметры сборки** включен (раздел **Apple LLVM — Языки — Модули**).

2. Откройте файл **QSTodoListViewController.m** и добавьте следующий код:


		- (void) saveAuthInfo {
				[SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
		}


		- (void)loadAuthInfo {
				NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		         self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

		    }
		}

3. В методе `loginAndGetData` измените блок completion вызова `loginWithProvider:controller:animated:completion:`. Для этого добавьте вызов `saveAuthInfo` прямо перед строкой `[self refresh]`. Этим вызовом мы просто сохраняем свойства идентификатора пользователя и токена:

				[self saveAuthInfo];

4. Давайте также настроим загрузку идентификатора пользователя и токена при запуске приложения. В методе `viewDidLoad` в файле **QSTodoListViewController.m** добавьте вызов loadAuthInfo сразу после инициализации `self.todoService`.

				[self loadAuthInfo];

<!---HONumber=July15_HO3-->