<properties
	pageTitle="Использование центров уведомлений для передачи экстренных новостей (Универсальное приложение Windows)"
	description="Использование центров уведомлений Azure с тегами в регистрации для передачи экстренных новостей в универсальное приложение для Windows."
	services="notification-hubs"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>


<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/24/2015"
	ms.author="wesmc"/>

# Использование концентраторов уведомлений для передачи экстренных новостей


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Обзор

В этом разделе рассказывается, как использовать центры уведомлений для отправки уведомлений о важных новостях в магазин Windows и приложение Windows 8.1 (без использования Silverlight). Если вы намерены использовать Windows Phone 8.1 Silverlight, см. версию статьи для [Windows Phone](notification-hubs-ios-send-breaking-news.md). По завершении вы сможете зарегистрироваться в интересующих вас категориях экстренных новостей и получать push-уведомления только для этих категорий. Этот сценарий является общим шаблоном для многих приложений, где требуется отправлять уведомления группам пользователей, которые ранее проявили к ним интерес (например, средства чтения RSS, приложения для любителей музыки и т. д.).

Широковещательные сценарии реализуются путем включения одного или нескольких _тегов_ при создании регистрации в концентраторе уведомлений. Если уведомления отправляются на тег, их получают все устройства, зарегистрированные для данного тега. Поскольку теги представляют собой обычные строки, их не нужно подготавливать заранее. Дополнительные сведения о тегах см. в разделе [Руководство по использованию концентраторов уведомлений].

##Предварительные требования

Материал данного раздела основан на приложении, созданном в разделе [Приступая к работе с концентраторами уведомлений][get-started]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с концентраторами уведомлений][get-started].

##Добавление возможности выбора категорий в приложение

Прежде всего необходимо добавить элементы пользовательского интерфейса для имеющейся главной страницы, позволяющие пользователю выбирать категории для регистрации. Выбранные пользователем категории хранятся на устройстве. При запуске приложения в концентраторе уведомлений создается регистрация устройства с выбранными категориями, представленными в форме тегов.

1. Откройте файл проекта MainPage.xam и скопируйте следующий код в элемент **Grid**:

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>


2. Щелкните правой кнопкой мыши проект **Shared** и добавьте новый класс с именем **Notifications**, добавьте в определение класса модификатор **public**, а затем добавьте в новый файл кода следующие операторы **using**:

		using Windows.Networking.PushNotifications;
		using Microsoft.WindowsAzure.Messaging;
		using Windows.Storage;
		using System.Threading.Tasks;

3. Скопируйте следующий код в новый класс **Notifications**:

		private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            await SubscribeToCategories(categories);
        }

		public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                IEnumerable<string> categories = RetrieveCategories();
            }

            await hub.RegisterNativeAsync(channel.Uri, categories);
        }

    Этот класс использует локальное хранилище для хранения категорий новостей, которые данное устройство должно получать. Он также содержит методы для регистрации этих категорий.


4. В файле проекта App.xaml.cs добавьте следующее свойство к классу **App**:

		public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

	Это свойство используется для создания экземпляра **Notifications** и доступа к нему.

	В приведенном выше коде замените заполнители `<hub name>` и `<connection string with listen access>` именем центра уведомлений и строкой подключения для *DefaultListenSharedAccessSignature*, полученными ранее.

	> [AZURE.NOTE]Так как учетные данные, которые распространяются с помощью клиентского приложения, обычно небезопасны, с помощью вашего клиентского приложения следует распространять только ключ для доступа к прослушиванию. Доступ к прослушиванию позволяет приложению регистрироваться для использования уведомлений, однако при этом нельзя изменять имеющиеся регистрации и отправлять уведомления. Для отправки уведомлений и изменения существующих регистраций используется ключ полного доступа в защищенной серверной службе.

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

	Этот метод создает список категорий и использует класс **Notifications** класс для хранения списка в локальном хранилище и регистрации соответствующих тегов в концентраторе уведомлений. При изменении категорий регистрация создается заново с новыми категориями.

Ваше приложение теперь может сохранять набор категорий в локальном хранилище на устройстве и регистрироваться в центре уведомлений всякий раз, когда пользователь изменяет выбранные категории.

##Регистрация для использования уведомлений

Эти действия позволяют зарегистрироваться в центре уведомлений при запуске с использованием категорий, сохраненных в локальном хранилище.

> [AZURE.NOTE]Так как универсальный код ресурса (URI) канала, назначенный службой push-уведомлений Windows (MPNS), может измениться в любое время, следует регулярно производить регистрацию для использования уведомлений, чтобы предотвратить сбои уведомлений. В этом примере регистрация для использования уведомлений осуществляется при каждом запуске приложения. Для приложений, которые запускаются чаще одного раза в день, в целях экономии пропускной способности регистрацию можно пропустить, если с момента предыдущей регистрации прошло менее одного дня.

1. Откройте файл App.xaml.cs и добавьте модификатор **async** к методу **OnLaunched**.

2. В методе **OnLaunched** найдите и замените вызов **InitNotificationsAsync** на следующую строчку:

		await notifications.SubscribeToCategories();

	Это гарантирует, что при каждом запуске приложения оно извлекает категории из локального хранилища и запрашивает для них регистрацию. Метод **InitNotificationsAsync** был создан в ходе выполнения учебника "Приступая к работе с концентраторами уведомлений", но в данном случае он не нужен.

3. В файле проекта MainPage.xaml.cs добавьте следующий код в метод *OnNavigatedTo*:

		var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;

	При этом главная страница обновляется в зависимости от состояния ранее сохраненных категорий.

Теперь приложение готово и может сохранять набор категорий в локальном хранилище устройств и использовать его для регистрации в концентраторе уведомлений всякий раз, когда пользователь изменяет выбранные категории. А сейчас определим серверную часть, которая может отправлять уведомления категорий в это приложение.

##Отправка уведомлений из серверной части

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##Запуск приложения и создание уведомлений

1. В Visual Studio нажмите клавишу F5, чтобы скомпилировать и запустить приложение.

	![][1]

	Обратите внимание, что в пользовательском интерфейсе присутствует набор переключателей, позволяющий выбрать категории для подписки.

2. Включите переключатели одной или нескольких категорий, затем нажмите **Подписаться**.

	Приложение преобразует выбранные категории в теги и запрашивает у концентратора уведомлений новую регистрацию устройств для выбранных тегов. Зарегистрированные категории возвращаются и отображаются в диалоговом окне.

	![][19]

4. Отправьте новое уведомление из серверной части одним из следующих способов:

	+ **Консольное приложение:** запустите консольное приложение.

	+ **Java/PHP:** запустите приложение или сценарий.

	Уведомления для выбранных категорий отображаются в виде всплывающих уведомлений.

	![][14]

##Дальнейшие действия

В этом учебнике мы рассмотрели, как производить рассылку экстренных новостей по категориям. Рекомендуем вам также изучить один из указанных ниже учебников. В них рассматриваются более сложные сценарии использования концентраторов уведомлений.

+ [Использование центров уведомлений для передачи локализованных экстренных новостей]

	Как расширить возможности приложения экстренных новостей для отправки локализованных уведомлений.

+ [Уведомление пользователей с помощью центров уведомлений]

	Узнайте, как рассылать push-уведомления определенным пользователям, прошедшим проверку подлинности. Это хорошее решение для отправки уведомлений только определенным пользователям.


<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Использование центров уведомлений для передачи локализованных экстренных новостей]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Уведомление пользователей с помощью центров уведомлений]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Руководство по использованию концентраторов уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591

<!---HONumber=Oct15_HO3-->