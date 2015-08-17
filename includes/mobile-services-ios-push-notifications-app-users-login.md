
Затем необходимо изменить способ регистрации push-уведомлений, чтобы удостовериться, что перед попыткой регистрации пользователь прошел проверку подлинности.

1. В файле **QSAppDelegate.m** полностью удалите реализацию **didFinishLaunchingWithOptions**.

2. Откройте элемент **QSTodoListViewController.m** и добавьте следующий код в конец метода **viewDidLoad**:

```
// Register for remote notifications
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
```

<!---HONumber=August15_HO6-->