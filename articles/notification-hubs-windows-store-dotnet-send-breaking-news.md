<properties 
	pageTitle="Использование центров уведомлений для передачи экстренных новостей (Универсальное приложение Windows)" 
	description="Использование центров уведомлений Azure с тегами в регистрации для передачи экстренных новостей в универсальное приложение для Windows." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="ricksal"/>

# Использование центров уведомлений для передачи экстренных новостей
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" class="current">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a>
		<a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

В этом разделе рассказывается, как использовать центры уведомлений для отправки уведомлений о важных новостях в магазин Windows и приложение Windows 8.1 (без использования Silverlight). Если вы намерены использовать Windows Phone 8.1 Silverlight, см. версию статьи для [Windows Phone](notification-hubs-ios-send-breaking-news.md). По завершении вы сможете зарегистрироваться в интересующих вас категориях экстренных новостей и получать push-уведомления только для этих категорий. Данный сценарий является общеупотребимым шаблоном для многих приложений, где требуется отправлять уведомления группам пользователей, ранее проявивших к ним интерес, например, программы чтения RSS, приложений для музыкальных фанатов и т. д. 

Широковещательные сценарии реализуются путем включения одного или нескольких _тегов_ при создании регистрации в центре уведомлений. Если уведомления отправляются на тег, их получают все устройства, зарегистрированные для данного тега. Поскольку теги представляют собой обычные строки, их не нужно подготавливать заранее. Дополнительную информацию о тегах см. в разделе [Руководство по использованию центров уведомлений]. 

В этом учебнике рассматриваются следующие основные шаги для реализации данного сценария:

1. [Добавление возможности выбора категорий в приложение]
2. [Регистрация для использования уведомлений]
3. [Отправка уведомлений из серверной части]
4. [Запуск приложения и создание уведомлений]

Материал данного раздела основан на приложении, созданном в разделе [Приступая к работе с центрами уведомлений][get-started]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с центрами уведомлений][get-started].

##<a name="adding-categories"></a>Добавление возможности выбора категорий в приложение

Прежде всего, необходимо добавить элементы пользовательского интерфейса для имеющейся главной страницы, позволяющие пользователю выбирать категории для регистрации. Выбранные пользователем категории хранятся на устройстве. При запуске приложения в центре уведомлений создается регистрация устройства с выбранными категориями, представленными в форме тегов. 

1. Откройте файл проекта MainPage.xaml и скопируйте следующий код в элемент **Grid**:
			
		<Grid Margin="120, 58, 120, 80" >
            <Grid.RowDefinitions>
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. В этом проекте создайте новый класс с именем **Notifications**, добавьте к определению класса модификатор **public**, а затем добавьте следующий оператор **using** в новый файл кода:

		using Windows.Networking.PushNotifications;
		using Microsoft.WindowsAzure.Messaging;
		using Windows.Storage;

3. Скопируйте следующий код в новый класс **Notifications**:

		private NotificationHub hub;

        public Notifications()
        {
            hub = new NotificationHub("<hub name>", "<connection string with listen access>");
        }

        public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            await SubscribeToCategories(categories);
        }

        public async Task SubscribeToCategories(IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await hub.RegisterNativeAsync(channel.Uri, categories);
        }

    Этот класс использует локальное хранилище для хранения категорий новостей, которые данное устройство должно получать. Он также содержит методы для регистрации этих категорий.

4. В приведенном выше коде замените заполнители "<имя_центра>" и "<строка_подключения_для_прослушивания_доступа>" именем центра уведомлений и строкой подключения для элемента *DefaultListenSharedAccessSignature*, полученными ранее.

	> [AZURE.NOTE] Так как учетные данные, которые распространяются с помощью клиентского приложения, обычно небезопасны, с помощью вашего клиентского приложения следует распространять только ключ для доступа к прослушиванию. Доступ к прослушиванию позволяет приложению регистрироваться для использования уведомлений, однако при этом нельзя изменять имеющиеся регистрации и отправлять уведомления. Для отправки уведомлений и изменения существующих регистраций используется ключ полного доступа в защищенной серверной службе.

4. В файле проекта App.xaml.cs добавьте в класс **App** следующее свойство:

		public Notifications notifications = new Notifications();

	Это свойство используется для создания экземпляра **Notifications** и доступа к нему.

5. В MainPage.xaml.cs добавьте следующую строку:

		using Windows.UI.Popups;

6. В файле проекта MainPage.xaml.cs добавьте следующий метод:

		private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories));
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
	
	Этот метод создает список категорий и использует класс **Notifications** класс для хранения списка в локальном хранилище и регистрации соответствующих тегов в центре уведомлений. При изменении категорий регистрация создается заново с новыми категориями.

Ваше приложение теперь может сохранять набор категорий в локальном хранилище на устройстве и регистрироваться в центре уведомлений всякий раз, когда пользователь изменяет выбранные категории. 

##<a name="register"></a>Регистрация для использования уведомлений

Эти действия позволяют зарегистрироваться в центре уведомлений при запуске с использованием категорий, сохраненных в локальном хранилище. 

> [AZURE.NOTE] Т. к. URI-идентификатор канала, назначенный службой уведомлений Windows (WNS), может измениться в любое время, следует часто регистрироваться на получение уведомлений, чтобы получать их без ошибок. В этом примере регистрация для использования уведомлений осуществляется при каждом запуске приложения. Для приложений, которые запускаются чаще одного раза в день, в целях экономии пропускной способности регистрацию можно пропустить, если с момента предыдущей регистрации прошло менее одного дня.

1. Добавьте в класс **Notifications** следующий код:

		public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

	Это возвращает категории, определенные в данном классе.

1. Откройте файл App.xaml.cs и добавьте модификатор **async** в метод **OnLaunched**.

2. В методе **OnLaunched** найдите и замените имеющийся вызов метода **InitNotificationsAsync** на следующую строку кода:

		await notifications.SubscribeToCategories(notifications.RetrieveCategories());

	Это гарантирует, что при каждом запуске приложения оно извлекает категории из локального хранилища и запрашивает для них регистрацию. Метод **InitNotificationsAsync** был создан в рамках работы с учебником [Приступая к работе с центрами уведомлений], однако в данном разделе он не требуется.

3. В файле проекта MainPage.xaml.cs добавьте следующий код в метод  *OnNavigatedTo*:

		var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;

	При этом главная страница обновляется в зависимости от состояния ранее сохраненных категорий. 

Теперь приложение готово и может сохранять набор категорий в локальном хранилище устройств и использовать его для регистрации в центре уведомлений каждый раз, когда пользователь изменяет выбранные категории. А сейчас определим серверную часть, которая может отправлять уведомления категорий в это приложение.

<h2><a name="send"></a>Отправка уведомлений из серверной части</h2>

[AZURE.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

##<a name="test-app"></a>Запуск приложения и создание уведомлений

1. В Visual Studio нажмите клавишу F5, чтобы скомпилировать и запустить приложение.

	![][1] 

	Обратите внимание, что в пользовательском интерфейсе присутствует набор переключателей, позволяющий выбрать категории для подписки. 

2. Включите переключатель для одной или нескольких категорий, а затем щелкните элемент **Подписаться**.

	Приложение преобразует выбранные категории в теги и запрашивает у центра уведомлений новую регистрацию устройств для выбранных тегов. Зарегистрированные категории возвращаются и отображаются в диалоговом окне.

	![][19]

4. Отправьте новое уведомление из серверной части одним из следующих способов:

	+ **Консольное приложение:** запустите консольное приложение.

	+ **Java/PHP:** запустите приложение или сценарий.

	Уведомления для выбранных категорий отображаются в виде всплывающих уведомлений.

	![][14]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике мы рассмотрели, как производить рассылку экстренных новостей по категориям. Далее вам рекомендуется изучить один из следующих учебников, в которых рассматриваются более сложные сценарии использования центров уведомлений:

+ [Использование центров уведомлений для вещания локализованных экстренных новостей]

	Узнайте, как расширить возможности приложения экстренных новостей для отправки локализованных уведомлений. 

+ [Уведомление пользователей с помощью центров уведомлений]

	Узнайте, как рассылать push-уведомления определенным пользователям, прошедшим проверку подлинности. Это хорошее решение для отправки уведомлений только определенным пользователям.


<!-- Anchors. -->
[Добавление возможности выбора категорий в приложение]: #adding-categories
[Регистрация для использования уведомлений]: #register
[Отправка уведомлений из серверной части]: #send
[Запуск приложения и создание уведомлений]: #test-app
[Дальнейшие действия]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/
[Использование центров уведомлений для вещания локализованных экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-localized-dotnet/ 
[Уведомление пользователей с помощью центров уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users
[Мобильная служба]: /ru-ru/develop/mobile/tutorials/get-started/
[Руководство по использованию центров уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Инструкции по использованию центров уведомлений для магазина Windows]: http://msdn.microsoft.com/library/jj927172.aspx
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Портал управления Azure]: https://manage.windowsazure.com/
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591






<!--HONumber=45--> 
