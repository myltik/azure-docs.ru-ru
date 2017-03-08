
Предыдущий пример обращается к поставщику удостоверений и к мобильной службе при каждом запуске приложения. Вместо этого можно кэшировать маркер авторизации и попытаться сначала использовать его.

* Для шифрования и хранения маркеров авторизации для клиента iOS рекомендуется использовать цепочку ключей iOS. Мы будем использовать [SSKeychain](https://github.com/soffes/sskeychain) — простую оболочку для iOS Keychain. Выполните инструкции на странице SSKeychain и добавьте эту цепочку в свой проект. Убедитесь, что параметр **Включить модули** в разделе проекта **Параметры сборки** включен (раздел **Apple LLVM — Языки — Модули**).
* Откройте файл **QSTodoListViewController.m** и добавьте следующий код:

```
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
```

* В `loginAndGetData` измените блок завершения `loginWithProvider:controller:animated:completion:`. Добавьте следующую строку прямо перед `[self refresh]` , чтобы сохранить свойства идентификатора пользователя и маркера:

```
                [self saveAuthInfo];
```

* Будем загружать идентификатор пользователя и маркер при запуске приложения. В `viewDidLoad` в файле **QSTodoListViewController.m** добавьте эту строку сразу после инициализации `self.todoService`.

```
                [self loadAuthInfo];
```


<!--HONumber=Jan17_HO3-->


