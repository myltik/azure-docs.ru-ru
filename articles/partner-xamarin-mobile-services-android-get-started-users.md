<properties urlDisplayName="Get Started with Authentication (Xamarin.Android)" pageTitle="Приступая к работе с проверкой подлинности (Xamarin.Android) - мобильные службы" metaKeywords ="Регистрация приложения Azure, проверка подлинности Azure, проверка подлинности приложения, проверка подлинности мобильных служб, мобильные службы Xamarin.Android" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with authentication in Mobile Services" services="mobile-services" documentationCenter="Mobile" manager="dwrede" authors="donnam"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Добавление проверки подлинности к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<p>В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении Xamarin.Android. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.</p>

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

Для изучения этого учебника потребуются Xamarin.Android и пакет SDK для Android версии 4.2 или более поздней. 

<h2><a name="register"></a>Регистрация приложения для проверки подлинности и конфигурация мобильных служб</h2>

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности</h2>


[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. В Eclipse откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами]. 

4. В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный). 

	 Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

<h2><a name="add-authentication"></a>Добавление проверки подлинности к приложению</h2>

1. Добавьте в класс **TodoActivity** следующее свойство:

			private MobileServiceUser user;

2. Добавьте в класс **TodoActivity** следующий метод: 

	        private async Task Authenticate()
	        {
	            try
	            {
	                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.MicrosoftAccount);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	        }

    При этом создается новый метод для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с помощью имени входа в учетную запись Майкрософт. Открывается диалоговое окно, в котором отображается идентификатор пользователя, прошедшего проверку подлинности. Не получив положительные результаты проверки подлинности, нельзя продолжить работу.

    <div class="dev-callout"><b>Примечание.</b>
	<p>Если используется поставщик удостоверений, отличный от поставщика Майкрософт, то замените значение, передаваемое в метод <strong>login</strong> (см. выше), одним из следующих: <i>Facebook</i>, <i>Google</i>, <i>Twitter</i> или <i>WindowsAzureActiveDirectory</i>.</p>
    </div>

3. В методе **OnCreate** добавьте следующую строку после кода, который создает объект `MobileServiceClient`.

		await Authenticate();

	Этот вызов запускает процесс проверки подлинности и асинхронно ждет его.

4. Переместите оставшийся код после `await Authenticate();` в методе **OnCreate** в новый метод **CreateTable**, имеющий следующий вид:

	        private async Task CreateTable()
	        {
	            // Get the Mobile Service Table instance to use
	            todoTable = client.GetTable<TodoItem>();

	            textNewTodo = FindViewById<EditText>(Resource.Id.textNewTodo);

	            // Create an adapter to bind the items with the view
	            adapter = new TodoItemAdapter(this, Resource.Layout.Row_List_To_Do);
	            var listViewTodo = FindViewById<ListView>(Resource.Id.listViewTodo);
	            listViewTodo.Adapter = adapter;

	            // Load the items from the Mobile Service
	            await RefreshItemsFromTableAsync();
	        }

5. Затем вызовите новый метод **CreateTable** в **OnCreate** после добавления вызова **Проверка подлинности** в шаге 2:

		await CreateTable();


6. В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение и выполнить вход с помощью выбранного поставщика удостоверений. 

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## Получение полного примера
Загрузите [полный пример проекта]. Не забудьте обновить переменные **applicationURL** и **applicationKey** с использованием своих параметров Azure.

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение идентификатора пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. 

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png

[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->

[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-android
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android

[Портал управления Azure]: https://manage.windowsazure.com/

[Полный пример проекта]: http://go.microsoft.com/fwlink/p/?LinkId=331328
