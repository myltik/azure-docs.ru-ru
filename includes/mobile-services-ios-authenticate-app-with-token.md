В предыдущем примере показан стандартный вход, который требует, чтобы клиент подключался как к поставщику удостоверений, так и к мобильной службе каждый раз, когда приложение запускается. Этот метод нельзя масштабировать, он является неэффективным.

Несколько лучшим подходом является кэширование маркера авторизации, возвращенного мобильной службой Azure, которая будет использоваться до входа на основе поставщика.

1. Для шифрования и хранения маркеров авторизации для клиента iOS рекомендуется использовать цепочку ключей iOS. Мы будем использовать [SSKeychain](https://github.com/soffes/sskeychain) - простую оболочка для iOS Keychain. Выполните инструкции на странице SSKeychain и добавьте эту цепочку в свой проект. Убедитесь, что параметр **Включить модули** в разделе проекта **Параметры сборки** включен (раздел **Apple LLVM - Языки - Модули**).

2. Откройте **QSTodoListViewController.m** и добавьте следующий код:


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

3. В конце метода **viewDidAppear** в **QSTodoListViewController.m** добавьте вызов **saveAuthInfo** сразу перед строкой [self refresh]. Этим вызовом мы просто сохраняем свойства идентификатора пользователя и токена:

				[self saveAuthInfo];

4. Давайте также настроим загрузку идентификатора пользователя и токена при запуске приложения. В методе **viewDidLoad** в **QSTodoListViewController.m** добавьте вызов loadAuthInfo сразу после инициализации **self.todoService**.

				[self loadAuthInfo];

<!--HONumber=49-->