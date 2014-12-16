
Затем необходимо изменить способ регистрации push-уведомлений с целью контроля прохождения пользователем проверки подлинности перед попыткой регистрации. 

1. В файле **QSAppDelegate.m** полностью удалите реализацию **didFinishLaunchingWithOptions**:

		
		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
		(NSDictionary *)launchOptions
		{
		    // Register for remote notifications
		    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
		    UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
		    return YES;
		}

2. Откройте файл проекта **QSTodoListViewController.m** и добавьте в метод **viewDidLoad** код, удаленный ранее:

	
		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		        [self refresh];
		    }];
		
		    // Register for remote notifications
		    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
		    UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
		}
