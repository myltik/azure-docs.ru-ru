<properties linkid="notification-hubs-windows-phone-send-breaking-news" pageTitle="Use Notification Hubs to send breaking news (Windows Phone)" metaKeywords="" description="Use  Azure Notification Hubs to use tag in registrations to send breaking news to a Windows Phone app." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Use Notification Hubs to send breaking news" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Использование концентраторов уведомлений для передачи экстренных новостей

<div class="dev-center-tutorial-selector sublanding">

[Windows Universal][Windows Universal][Windows Phone][Windows Phone][iOS][iOS][Android][Android]

</div>

В этом разделе показано, как использовать концентраторы уведомлений Azure для рассылки уведомлений об экстренных новостях в приложение Windows Phone 8.0/8.1 Silverlight. Если вы используете приложение Windows Store или Windows Phone 8.1, см. версию [Windows Universal][1]. По завершении вы сможете зарегистрироваться в интересующих вас категориях экстренных новостей и получать push-уведомления только для этих категорий. Данный сценарий является общеупотребимым шаблоном для многих приложений, где требуется отправлять уведомления группам пользователей, ранее проявивших к ним интерес, например, программы чтения RSS, приложений для музыкальных фанатов и т. д.

Широковещательные сценарии реализуются путем включения одного или нескольких *тегов* при создании регистрации в концентраторе уведомлений. Если уведомления отправляются на тег, их получают все устройства, зарегистрированные для данного тега. Поскольку теги представляют собой обычные строки, их не нужно подготавливать заранее. Дополнительные сведения о тегах см. в разделе [Руководство по использованию концентраторов уведомлений][Руководство по использованию концентраторов уведомлений].

В этом учебнике рассматриваются следующие основные шаги для реализации данного сценария:

1.  [Добавление возможности выбора категорий в приложение][Добавление возможности выбора категорий в приложение]
2.  [Регистрация для использования уведомлений][Регистрация для использования уведомлений]
3.  [Отправка уведомлений из серверной части][Отправка уведомлений из серверной части]
4.  [Запуск приложения и создание уведомлений][Запуск приложения и создание уведомлений]

Материал данного раздела основан на приложении, созданном в разделе [Приступая к работе с концентраторами уведомлений][Приступая к работе с концентраторами уведомлений]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с концентраторами уведомлений][Приступая к работе с концентраторами уведомлений].

## <a name="adding-categories"></a>Добавление возможности выбора категорий в приложение

Прежде всего, необходимо добавить элементы пользовательского интерфейса для имеющейся главной страницы, позволяющие пользователю выбирать категории для регистрации. Выбранные пользователем категории хранятся на устройстве. При запуске приложения в концентраторе уведомлений создается регистрация устройства с выбранными категориями, представленными в форме тегов.

1.  Откройте файл проекта MainPage.xaml, а затем замените элементы **сетки** с именем `TitlePanel` и `ContentPanel` с помощью следующего кода:

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

2.  В этом проекте создайте новый класс с именем **Notifications**, добавьте к определению класса модификатор **public**, а затем добавьте следующий оператор **using** в новый файл кода:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;

3.  Скопируйте следующий код в новый класс **Notifications**:

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

4.  В приведенном выше коде замените заполнители `<hub name>` и `<connection string with listen access>` именем концентратора уведомлений и строкой подключения для элемента *DefaultListenSharedAccessSignature*, полученными ранее.

    <div class="dev-callout">

    **Примечание.**
    Поскольку учетные данные, которые распространяются с помощью клиентского приложения, обычно небезопасны, с помощью вашего клиентского приложения следует распространять только ключ для доступа к прослушиванию. Доступ к прослушиванию позволяет приложению регистрироваться для использования уведомлений, однако при этом нельзя изменять имеющиеся регистрации и отправлять уведомления. Для отправки уведомлений и изменения существующих регистраций используется ключ полного доступа в защищенной серверной службе.

    </div>

5.  В файле проекта App.xaml.cs добавьте в класс **App** следующее свойство:

        public Notifications notifications = new Notifications();

    Это свойство используется для создания экземпляра **Notifications** и доступа к нему.

6.  В MainPage.xaml.cs добавьте следующую строку:

        using Windows.UI.Popups;

7.  В файле проекта MainPage.xaml.cs добавьте следующий метод:

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

Ваше приложение теперь может сохранять набор категорий в локальном хранилище на устройстве и регистрироваться в концентраторе уведомлений всякий раз, когда пользователь изменяет выбранные категории.

## <a name="register"></a>Регистрация для использования уведомлений

Эти действия позволяют зарегистрироваться в концентраторе уведомлений при запуске с использованием категорий, сохраненных в локальном хранилище.

<div class="dev-callout">

**Примечание.**
Поскольку URI канала, назначенный службой push-уведомлений Windows (MPNS), может измениться в любое время, следует регулярно производить регистрацию для использования уведомлений, чтобы предотвратить сбои уведомлений. В этом примере регистрация для использования уведомлений осуществляется при каждом запуске приложения. Для приложений, которые запускаются чаще одного раза в день, в целях экономии пропускной способности регистрацию можно пропустить, если с момента предыдущей регистрации прошло менее одного дня.

</div>

1.  Добавьте в класс **Notifications** следующий код:

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }

    Это возвращает категории, определенные в данном классе.

2.  Откройте файл App.xaml.cs и добавьте модификатор **async** в метод **Application\_Launching**.

3.  В методе **Application\_Launching** найдите и замените код регистрации концентраторов уведомлений, добавленный в разделе [Приступая к работе с концентраторами уведомлений][Приступая к работе с концентраторами уведомлений], с помощью следующей строки кода:

        await notifications.SubscribeToCategories(notifications.RetrieveCategories());

    Это гарантирует, что при каждом запуске приложения оно извлекает категории из локального хранилища и запрашивает для них регистрацию.

4.  В файле проекта MainPage.xaml.cs добавьте следующий код, который выполняет метод **OnNavigatedTo**:

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

## <a name="send"></a><span class="short-header">Отправка уведомлений</span>Отправка уведомлений из серверной части

</h2>
[WACOM.INCLUDE [концентраторы-уведомлений-серверная-часть][концентраторы-уведомлений-серверная-часть]]

## <a name="test-app"></a>Запуск приложения и создание уведомлений

1.  В Visual Studio нажмите клавишу F5, чтобы скомпилировать и запустить приложение.

    ![][]

    Обратите внимание, что в пользовательском интерфейсе присутствует набор переключателей, позволяющий выбрать категории для подписки.

2.  Включите переключатель для одной или нескольких категорий, а затем щелкните по элементу **Подписаться**.

    Приложение преобразует выбранные категории в теги и запрашивает у концентратора уведомлений новую регистрацию устройств для выбранных тегов. Зарегистрированные категории возвращаются и отображаются в диалоговом окне.

    ![][2]

3.  Отправьте новое уведомление из серверной части одним из следующих способов:

    -   **Консольное приложение:** запустите консольное приложение.

    -   **Java/PHP:** запустите свое приложение/сценарий.

    Уведомления для выбранных категорий отображаются в виде всплывающих уведомлений.

    ![][3]

Вы завершили эту тему.

<!--## <a name="next-steps"> </a>Next steps  In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:  + [Use Notification Hubs to broadcast localized breaking news]      Learn how to expand the breaking news app to enable sending localized notifications.   + [Notify users with Notification Hubs]      Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users. --> <!-- Anchors. --> <!-- Images. --> <!-- URLs.-->

  [Windows Universal]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/ "Windows Universal"
  [Windows Phone]: /ru-ru/documentation/articles/notification-hubs-windows-phone-send-breaking-news/ "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/notification-hubs-ios-send-breaking-news/ "iOS"
  [Android]: /ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/ "Android"
  [1]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
  [Руководство по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
  [Добавление возможности выбора категорий в приложение]: #adding-categories
  [Регистрация для использования уведомлений]: #register
  [Отправка уведомлений из серверной части]: #send
  [Запуск приложения и создание уведомлений]: #test-app
  [Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/get-started-notification-hubs-wp8/
  [концентраторы-уведомлений-серверная-часть]: ../includes/notification-hubs-back-end.md
  []: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
  [2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
  [3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png
