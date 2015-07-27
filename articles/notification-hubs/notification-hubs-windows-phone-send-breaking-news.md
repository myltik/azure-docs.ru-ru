<properties 
	pageTitle="Использование центров уведомлений для передачи экстренных новостей (Windows Phone)" 
	description="Использование центров уведомлений Azure для применения тегов в регистрациях, чтобы передавать экстренные новости в приложение Windows Phone." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="wesmc"/>

# Использование концентраторов уведомлений для передачи экстренных новостей

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Обзор

В этом разделе показано, как использовать центры уведомлений Azure для рассылки уведомлений об экстренных новостях в приложение Windows Phone 8.0/8.1 Silverlight. Если вы используете приложение Windows Store или Windows Phone 8.1, см. версию [Windows Universal](notification-hubs-windows-store-dotnet-send-breaking-news.md). По завершении вы сможете зарегистрироваться в интересующих вас категориях экстренных новостей и получать push-уведомления только для этих категорий. Данный сценарий является общеупотребимым шаблоном для многих приложений, где требуется отправлять уведомления группам пользователей, ранее проявивших к ним интерес, например, программы чтения RSS, приложений для музыкальных фанатов и т. д.

Широковещательные сценарии реализуются путем включения одного или нескольких _тегов_ при создании регистрации в концентраторе уведомлений. Если уведомления отправляются на тег, их получают все устройства, зарегистрированные для данного тега. Поскольку теги представляют собой обычные строки, их не нужно подготавливать заранее. Дополнительные сведения о тегах см. в разделе [Руководство по использованию концентраторов уведомлений].

##Предварительные требования

Материал данного раздела основан на приложении, созданном в разделе [Приступая к работе с концентраторами уведомлений]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с концентраторами уведомлений].

##Добавление возможности выбора категорий в приложение

Прежде всего необходимо добавить элементы пользовательского интерфейса для имеющейся главной страницы, позволяющие пользователю выбирать категории для регистрации. Выбранные пользователем категории хранятся на устройстве. При запуске приложения в центре уведомлений создается регистрация устройства с выбранными категориями, представленными в форме тегов.

1. Откройте файл проекта MainPage.xaml, а затем замените элементы **Grid** с именами `TitlePanel` и `ContentPanel` следующим кодом:
			
        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. Создайте в проекте новый класс **Notifications**, добавьте к определению класса модификатор **public**, затем добавьте в файл оператор **using**.

		using Microsoft.Phone.Notification;
		using Microsoft.WindowsAzure.Messaging;
		using System.IO.IsolatedStorage;

3. Скопируйте следующий код в новый класс **Notifications**:

		private NotificationHub hub;

		public Notifications()
		{
		    hub = new NotificationHub("<hub name>", "<connection string with listen access>");
		}
		
		public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
		{
		    var categoriesAsString = string.Join(",", categories);
		    var settings = IsolatedStorageSettings.ApplicationSettings;
		    if (!settings.Contains("categories"))
		    {
		        settings.Add("categories", categoriesAsString);
		    }
		    else
		    {
		        settings["categories"] = categoriesAsString;
		    }
		    settings.Save();
		
		    await SubscribeToCategories(categories);
		}
		
		public async Task SubscribeToCategories(IEnumerable<string> categories)
		{
		    var channel = HttpNotificationChannel.Find("MyPushChannel");
		
		    if (channel == null)
		    {
		        channel = new HttpNotificationChannel("MyPushChannel");
		        channel.Open();
		        channel.BindToShellToast();
		    }
		
		    await hub.RegisterNativeAsync(channel.ChannelUri.ToString(), categories);
		}

    Этот класс использует локальное хранилище для хранения категорий новостей, которые данное устройство должно получать. Он также содержит методы для регистрации этих категорий.

4. В приведенном выше коде замените заполнители `<hub name>` и `<connection string with listen access>` именем центра уведомлений и строкой подключения для *DefaultListenSharedAccessSignature*, полученными ранее.

	> [AZURE.NOTE]Так как учетные данные, которые распространяются с помощью клиентского приложения, обычно небезопасны, с помощью вашего клиентского приложения следует распространять только ключ для доступа к прослушиванию. Доступ к прослушиванию позволяет приложению регистрироваться для использования уведомлений, однако при этом нельзя изменять имеющиеся регистрации и отправлять уведомления. Полный ключ доступа используется в защищенной серверной службе для отправки уведомлений и смены существующих регистраций.

4. В файле проекта App.xaml.cs добавьте следующее свойство к классу **App**:

		public Notifications notifications = new Notifications();

	Это свойство используется для создания экземпляра **Notifications** и доступа к нему.

5. В файле MainPage.xaml.cs добавьте следующую строчку:

		using Windows.UI.Popups;

6. В файле проекта MainPage.xaml.cs добавьте следующий метод:

		private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
		{
		    var categories = new HashSet<string>();
		    if (WorldCheckBox.IsChecked == true) categories.Add("World");
		    if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
		    if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
		    if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
		    if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
		    if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
		
		    await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
		
		    MessageBox.Show("Subscribed to: " + string.Join(",", categories));
		}
	
	Этот метод создает список категорий и использует класс **Notifications** класс для хранения списка в локальном хранилище и регистрации соответствующих тегов в концентраторе уведомлений. При изменении категорий регистрация создается заново с новыми категориями.

Ваше приложение теперь может сохранять набор категорий в локальном хранилище на устройстве и регистрироваться в центре уведомлений всякий раз, когда пользователь изменяет выбранные категории.

##Регистрация для использования уведомлений

Эти действия позволяют зарегистрироваться в центре уведомлений при запуске с использованием категорий, сохраненных в локальном хранилище.

> [AZURE.NOTE]Поскольку URI канала, назначенный службой push-уведомлений Windows (MPNS), может измениться в любое время, следует регулярно производить регистрацию для использования уведомлений, чтобы предотвратить сбои уведомлений. В этом примере регистрация для использования уведомлений осуществляется при каждом запуске приложения. Для тех приложений, которые запускаются часто, более одного раза в день, возможно, лучше пропустить регистрацию, чтобы сэкономить трафик, если с момента прошлой регистрации прошло меньше суток.

1. Добавьте следующий код в класс **Notifications**:

		public IEnumerable<string> RetrieveCategories()
		{
		    var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
		    return categories != null ? categories.Split(',') : new string[0];
		}

	Это возвращает категории, определенные в данном классе.

1. Откройте файл App.xaml.cs и добавьте модификатор **async** в метод **Application_Launching**.

2. В методе **Application_Launching** найдите и замените код регистрации концентраторов уведомлений, добавленный в разделе [Приступая к работе с концентраторами уведомлений], с помощью следующей строки кода:

		await notifications.SubscribeToCategories(notifications.RetrieveCategories());

	Это гарантирует, что при каждом запуске приложения оно извлекает категории из локального хранилища и запрашивает для них регистрацию.

3. В файле проекта MainPage.xaml.cs добавьте следующий код, который выполняет метод **OnNavigatedTo**:

		protected override void OnNavigatedTo(NavigationEventArgs e)
		{
		    var categories = ((App)Application.Current).notifications.RetrieveCategories();
		
		    if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
		    if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
		    if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
		    if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
		    if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
		    if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
		}

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

	![][2]

4. Отправьте новое уведомление из серверной части одним из следующих способов:

	+ **Консольное приложение:** запустите консольное приложение.

	+ **Java/PHP:** запустите приложение или сценарий.

	Уведомления для выбранных категорий отображаются в виде всплывающих уведомлений.

	![][3]

Вы завершили эту тему.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

	Learn how to expand the breaking news app to enable sending localized notifications. 

+ [Notify users with Notification Hubs]

	Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Приступая к работе с концентраторами уведомлений]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Руководство по использованию концентраторов уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

[Azure Management Portal]: https://manage.windowsazure.com/




 

<!---HONumber=July15_HO3-->