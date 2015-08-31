<properties
	pageTitle="Приступая к работе с аутентификацией (Xamarin.Android) — мобильные службы"
	description="Узнайте, как использовать аутентификацию в приложении мобильных служб Azure для Xamarin.Android."
	services="mobile-services"
	documentationCenter="xamarin"
	manager="dwrede"
	authors="lindydonna"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="donnam"/>

# Добавление проверки подлинности к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

<p>В этом разделе показано, как аутентифицировать пользователей в мобильных службах Azure с помощью приложения Xamarin.Android. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.</p>

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и настройка мобильных служб]
2. [Предоставление разрешений на таблицу только пользователям, прошедшим проверку подлинности]
3. [Добавление проверки подлинности в приложение]

Этот учебник создан на основе краткого руководства по мобильным службам. Кроме того, вам необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами].

Для изучения этого учебника необходимы Xamarin.Android и пакет SDK для Android версии 4.2 или более поздней.

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. В Eclipse откройте проект, созданный после прохождения учебника [Приступая к работе с мобильными службами].

4. В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (Не санкционировано).

	 Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

1. Добавьте в класс **TodoActivity** следующее свойство:

		private MobileServiceUser user;

2. Добавьте в класс **ToDoActivity** следующий метод:

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

    > [AZURE.NOTE]Если вы используете поставщик идентификаторов, отличный от Майкрософт, замените значение, переданное в метод **login** выше, одним из следующих: _Facebook_, _Google_, _Twitter_ или _WindowsAzureActiveDirectory_.

3. Добавьте в метод **onCreate** следующую строку после кода, который формирует экземпляр объекта `MobileServiceClient`.

		await Authenticate();

	Этот вызов запускает процесс проверки подлинности и асинхронно ждет его.

4. Переместите оставшийся код после `await Authenticate();` в методе **OnCreate** в новый метод **CreateTable**, который выглядит следующим образом:

        private async Task CreateTable()
        {

            await InitLocalStoreAsync();

            // Get the Mobile Service Table instance to use
            toDoTable = client.GetTable<ToDoItem>();

            textNewToDo = FindViewById<EditText>(Resource.Id.textNewToDo);

            // Create an adapter to bind the items with the view
            adapter = new ToDoItemAdapter(this, Resource.Layout.Row_List_To_Do);
            var listViewTodo = FindViewById<ListView>(Resource.Id.listViewToDo);
            listViewTodo.Adapter = adapter;

            // Load the items from the Mobile Service
            await RefreshItemsFromTableAsync();
        }

5. Затем вызовите новый метод **CreateTable** в **OnCreate** после вызова метода **Authenticate**, добавленного на шаге 2:

		await CreateTable();


6. В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение и выполнить вход с помощью выбранного поставщика удостоверений.

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
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png

[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Авторизация пользователей с помощью скриптов]: mobile-services-javascript-backend-service-side-authorization.md
[Azure Management Portal]: https://manage.windowsazure.com/
[полный пример проекта]: http://go.microsoft.com/fwlink/p/?LinkId=331328

<!---HONumber=August15_HO8-->