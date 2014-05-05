<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-android" urlDisplayName="Приступая к работе с проверкой подлинности (Xamarin.Android)" pageTitle="Приступая к работе с проверкой подлинности (Xamarin.Android) - Мобильные службы" metaKeywords="Регистрация приложения Azure, проверка подлинности Azure, проверка подлинности приложения, проверка подлинности мобильных служб, мобильные службы Xamarin.Android" description="Узнайте, как использовать проверку подлинности в приложении Azure Mobile Services для Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Приступая к работе с проверкой подлинности в мобильных службах" documentationCenter="Mobile" authors="" />
# Приступая к работе с проверкой подлинности в мобильных службах
<div class="dev-center-tutorial-selector sublanding">   
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

<p>В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении Xamarin.Android. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска; для этого используется поставщик удостоверений, который поддерживается мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.</p>

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]

Этот учебник основан на возможностях быстрого начала работы с мобильными службами. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

Для изучения этого учебника необходимы [Xamarin.Android] и Android SDK 4.2 или более поздней версии. 

<h2><a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для проверки подлинности и конфигурация мобильных служб</h2>

Чтобы иметь возможность проверять подлинность пользователей, необходимо зарегистрировать приложение у поставщика удостоверений. Затем необходимо зарегистрировать секрет клиента, созданный поставщиком, в мобильных службах.

1. Выполните вход на [портал управления Azure], щелкните пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

   	![][4]

2. Откройте вкладку **Панель мониторинга** и запишите значение **URL-адрес сайта**.

   	![][5]

    От вас может потребоваться предоставить это значение поставщику удостоверений при регистрации приложения.

3. Выберите поддерживаемый поставщик удостоверений из приведенного ниже списка и выполните действия для регистрации приложения у этого поставщика:

 - <a href="/ru-ru/develop/mobile/how-to-guides/register-for-microsoft-authentication/" target="_blank">Учетная запись Майкрософт</a>
 - <a href="/ru-ru/develop/mobile/how-to-guides/register-for-facebook-authentication/" target="_blank">Вход в Facebook</a>
 - <a href="/ru-ru/develop/mobile/how-to-guides/register-for-twitter-authentication/" target="_blank">Вход в Twitter</a>
 - <a href="/ru-ru/develop/mobile/how-to-guides/register-for-google-authentication/" target="_blank">Вход в Google</a>
 - <a href="/ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    Не забудьте записать удостоверение клиента и значения секрета, созданные поставщиком.

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет, созданный поставщиком, входит в важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе с вашим приложением.</p>
    </div>

4. На портале управления откройте вкладку **Удостоверение**, введите идентификатор приложения и значения общего секрета, полученные у поставщика удостоверений, а затем щелкните **Сохранить**.

   	![][13]

Мобильная служба и приложение теперь настроены для работы с выбранным поставщиком проверки подлинности.

<h2><a name="permissions"></a><span class="short-header">Ограничение разрешений</span>Ограничение разрешений зарегистрированными пользователями</h2>

1. На портале управления откройте вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

   	![][14]

2. Откройте вкладку **Разрешения**, установите все разрешения в состояние **Только прошедшие проверку пользователи** и нажмите кнопку **Сохранить**. Это обеспечит, что только прошедший проверку пользователь сможет производить все операции с таблицей **TodoItem**. Это также упрощает сценарии в следующем учебнике, так как в них не нужно учитывать возможность существования анонимных пользователей.

   	![][15]

3. В Eclipse откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами]. 

4. В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный). 

	 Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить проверку подлинности пользователей, прежде чем запрашивать ресурсы из мобильной службы.

<h2><a name="add-authentication"></a><span class="short-header">Добавление проверки подлинности</span>Добавление проверки подлинности для приложения</h2>

1. Добавьте в класс **TodoActivity** следующий метод: 

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

    При этом создается новый метод для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с помощью имени входа в учетную запись Майкрософт. Открывается диалоговое окно, в котором отображается идентификатор пользователя, прошедшего проверку подлинности. Без успешной проверки подлинности продолжение невозможно.

    <div class="dev-callout"><b>Примечание.</b>
	<p>Если используется поставщик идентификаторов, отличный от Майкрософт, измените значение, переданное в методе <strong>login</strong> выше на одно из следующих: <i>Facebook</i>, <i>Googlek</i> или <i>Twitter</i>.</p>
    </div>

2. В методе **OnCreate** добавьте следующую строку после кода, который создает объект `MobileServiceClient`.

		await Authenticate();

	Этот вызов запускает процесс проверки подлинности и асинхронно ждет его.

3. Переместите оставшийся код после `await Authenticate();` в методе **OnCreate** в новый метод **CreateTable**, имеющий следующий вид:

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

4. Затем вызовите новый метод **CreateTable** в **OnCreate** после добавления вызова **Проверка подлинности** в шаге 2:

		await CreateTable();


5. В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение и выполнить вход с помощью выбранного поставщика удостоверений. 

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## Получение полного примера
Загрузите [полный пример проекта]. Не забудьте обновить переменные **applicationURL** и **applicationKey** с использованием своих параметров Azure.

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью сценариев], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. 

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Следующие шаги]:#next-steps

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
[Авторизация пользователей с помощью сценариев]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android

[Портал управления Azure]: https://manage.windowsazure.com/

[Полный пример проекта]: http://go.microsoft.com/fwlink/p/?LinkId=331328
