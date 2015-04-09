<properties
	pageTitle="Приступая к работе с проверкой подлинности (Xamarin.iOS) - мобильные службы" 
	description="Узнайте, как использовать проверку подлинности в приложении мобильных служб Azure для Xamarin.iOS."
	documentationCenter="xamarin"
	services="mobile-services"
	manager="dwrede"
	authors="lindydonna"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/23/2014"
	ms.author="donnam"/>

# Добавление проверки подлинности в приложение мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении.  В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.  

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и настройка мобильных служб]
2. [Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]
3. [Добавление проверки подлинности в приложение]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами].

Для изучения этого учебника необходимы [Xamarin.iOS], XCode версии 6.0 и iOS версии 7.0 или более поздней.

<h2><a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб</h2>

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

<h2><a name="permissions"></a> Ограничение разрешений для пользователей, прошедших проверку подлинности</h2>


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. В Xcode откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами].

4. Нажмите кнопку **Выполнить** для построения проекта и запуска приложения в эмуляторе iPhone; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (Не санкционировано).

   	Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица **TodoItem** теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

<h2><a name="add-authentication"></a>Добавление проверки подлинности в приложение</h2>

1. Откройте файл проекта **TodoService** и добавьте следующие переменные

		// Mobile Service logged in user
		private MobileServiceUser user;
		public MobileServiceUser User { get { return user; } }

2. Добавьте новый метод с именем **Authenticate** в **TodoService**, определяемый как:

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

	> [AZURE.NOTE] Если вы используете поставщик удостоверений, отличный от вашей учетной записи Майкрософт, измените значение, передаваемое в **LoginAsync**, на одно из нижеследующих: **Facebook**, **Twitter**, **Google** или **WindowsAzureActiveDirectory**.

3. Переместите запрос для таблицы **TodoItem** из конструктора **TodoService** в новый метод с именем **CreateTable**:

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetTable<TodoItem>();
        }

4. Создайте новый асинхронный открытый метод с именем **LoginAndGetData** определяемый как:

        public async Task LoginAndGetData(UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5. В **TodoListViewController** переопределите метод **ViewDidAppear**, как указано ниже. Это позволяет пользователю войти, даже если **TodoService** еще не может обработать пользователя:

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (TodoService.DefaultService.User == null)
            {
                await TodoService.DefaultService.LoginAndGetData(this);
            }

            if (TodoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            }

            RefreshAsync();
        }
6. Удалите исходный вызов **RefreshAsync** из **TodoListViewController.ViewDidLoad**.

7. Чтобы выполнить сборку проекта, нажмите кнопку **Запуск**, запустите приложение в эмуляторе iPhone, а затем войдите с использованием выбранного поставщика удостоверений.

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## Получение полного примера
Загрузите [полный пример проекта]. Не забудьте обновить переменные **applicationURL** и **applicationKey** с использованием своих параметров Azure.

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение идентификатора пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами.

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. TODO:: update completed example project link with project download -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Приступая к работе с мобильными службами]: /develop/mobile/tutorials/get-started-xamarin-ios
[Приступая к работе с данными]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Приступая к работе с аутентификацией]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Приступая к работе с push-уведомлениями]: /develop/mobile/tutorials/-get-started-with-push-xamarin-ios
[Авторизация пользователей с помощью скриптов]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios

[Портал управления Azure]: https://manage.windowsazure.com/
[Полный пример проекта]: http://go.microsoft.com/fwlink/p/?LinkId=331328
[Xamarin.iOS]: http://xamarin.com/download
"<!--HONumber=49-->" 

<!--HONumber=49-->