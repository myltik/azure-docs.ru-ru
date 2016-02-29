<properties
	pageTitle="Приступая к работе с аутентификацией (Xamarin.iOS) — мобильные службы"
	description="Узнайте, как использовать аутентификацию в приложении мобильных служб Azure для Xamarin.iOS."
	documentationCenter="xamarin"
	services="mobile-services"
	manager="dwrede"
	authors="lindydonna"
	editor=""/>


<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="donnam"/>

# Добавление проверки подлинности к приложению мобильных служб

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и настройка мобильных служб]
2. [Предоставление разрешений на таблицу только пользователям, прошедшим проверку подлинности]
3. [Добавление проверки подлинности в приложение]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами].

Для прохождения этого учебника требуется следующее ПО: [Xamarin.iOS], XCode 6.0 и iOS 7.0 или более поздних версий.

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. В Xcode откройте проект, созданный после завершения изучения учебника [Приступая к работе с мобильными службами].

4. Нажмите кнопку **Запуск**, чтобы выполнить сборку проекта и запустить приложение в эмуляторе iPhone. Убедитесь, что после запуска приложения порождается необработанное исключение с кодом состояния «401 (Не авторизовано)».

   	Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

1. Откройте файл проекта **QSToDoService** и добавьте следующие переменные:

		// Mobile Service logged in user
		private MobileServiceUser user;
		public MobileServiceUser User { get { return user; } }

2. Добавьте в **TodoService** новый метод **Authenticate**, определенный как:

        private async Task Authenticate(MonoTouch.UIKit.UIViewController view)
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

	> [AZURE.NOTE] Если вы используете поставщик удостоверений, отличный от учетной записи Майкрософт, замените значение, передаваемое в метод **LoginAsync** выше, одним из следующих: _Facebook_, _Twitter_, _Google_ или _WindowsAzureActiveDirectory_.

3. Переместите запрос для таблицы **TodoItem** из конструктора **TodoService** в новый метод с именем **CreateTable**:

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the ToDoItem table
            todoTable = client.GetSyncTable<ToDoItem>();
        }

4. Создайте новый асинхронный открытый метод с именем **LoginAndGetData**, определенный следующим образом:

        public async Task LoginAndGetData(MonoTouch.UIKit.UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5. В **TodoListViewController** переопределите метод **ViewDidAppear**, как указано ниже. Это позволяет пользователю войти, даже если у **TodoService** еще нет дескриптора пользователя:

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (QSTodoService.DefaultService.User == null)
            {
                await QSTodoService.DefaultService.LoginAndGetData(this);
            }

            if (QSTodoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            }


            await RefreshAsync();
        }
6. Удалите исходный вызов **RefreshAsync** из **TodoListViewController.ViewDidLoad**.

7. Чтобы выполнить сборку проекта, нажмите кнопку **Запуск**, запустите приложение в эмуляторе iPhone, а затем войдите с использованием выбранного поставщика удостоверений.

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## Получение полного примера
Загрузите [полный пример проекта]. Не забудьте обновить переменные **applicationURL** и **applicationKey** с использованием своих параметров Azure.

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами.

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Предоставление разрешений на таблицу только пользователям, прошедшим проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. TODO:: update completed example project link with project download -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Приступая к работе с мобильными службами]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /develop/mobile/tutorials/-get-started-with-push-xamarin-ios
[Авторизация пользователей с помощью скриптов]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[полный пример проекта]: http://go.microsoft.com/fwlink/p/?LinkId=331328
[Xamarin.iOS]: http://xamarin.com/download

<!---HONumber=AcomDC_0218_2016-->