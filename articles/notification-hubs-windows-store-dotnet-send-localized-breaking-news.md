<properties urlDisplayName="Localized Breaking News" pageTitle="Концентраторы уведомлений для передачи локализованных экстренных новостей" metaKeywords="" description="Узнайте, как использовать центры уведомлений Azure Service Bus для отправки уведомлений о локализованных экстренных новостях." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send localized breaking news" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />
# Использование концентраторов уведомлений для передачи локализованных экстренных новостей

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#" class="current">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS">iOS</a>
</div>

В этом разделе показано, как использовать функцию **template** концентраторов уведомлений Azure для рассылки уведомлений об экстренных новостях, локализованных для языка и устройства. В этом учебнике вы начнете с приложения Магазина Windows, созданного в учебнике [Использование концентраторов уведомлений для передачи экстренных новостей] По завершении вы сможете регистрироваться для категорий, которые вас интересуют, указывать язык уведомлений и получать push-уведомления только для выбранных категорий на этом языке.

В этом учебнике рассматриваются следующие основные шаги для реализации данного сценария:

1. [Основные сведения о шаблонах] 
2. [Пользовательский интерфейс приложения]
3. [Разработка клиентского приложения Магазина Windows]
4. [Отправка уведомлений из серверной части]


Этот сценарий состоит из двух частей:

- приложение Магазина Windows позволяет клиентским устройствам указывать язык и подписываться на различные категории экстренных новостей; 

- сервер рассылает уведомления с помощью функций **tag** и **template** концентраторов уведомлений Azure.



##Предварительные требования ##

Необходимо выполнить задания учебника [Использование концентраторов уведомлений для передачи экстренных новостей], чтобы получить созданный в нем код, поскольку задания данного учебника основаны непосредственно на нем. 

Нужен также Visual Studio 2012.


<h2><a name="concepts"></a>Основные сведения о шаблонах</h2>

В учебнике [Использование концентраторов уведомлений для передачи экстренных новостей] вы построили приложение, которое использовало **теги** для подписки на уведомления для различных категорий новостей.
Однако многие приложения ориентированы на несколько рынков и требуют локализации. Это означает, что само содержимое уведомлений должно быть локализовано и доставлено в правильный набор устройств.
В этом разделе будут продемонстрированы способы использования функции концентраторов уведомлений **template**, которая позволяет легко доставлять локализованные экстренные уведомления.

Примечание. Один из способов отправки локализованных уведомлений - создание нескольких версий каждого тега. Например, для поддержки английского, французского и китайского нам понадобится три разных тега для мировых новостей: world_en, world_fr и world_ch. Затем необходимо отправить локализованную версию мировых новостей по каждому из этих тегов. В этом разделе мы используем шаблоны во избежание избыточного количества тегов и необходимости отправки нескольких сообщений.

В общих чертах, с помощью шаблонов можно указать, как конкретное устройство должно получать уведомления. Шаблон указывает точный формат полезных данных путем обращения к свойствам, которые являются частью сообщения, отправленного сервером вашего приложение. В нашем случае мы отправим независимое от языка сообщение, которое содержит все поддерживаемые языки:

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

Затем мы убедимся, что устройство зарегистрировано с шаблоном, который ссылается на нужное свойство. Например, приложение магазина Windows, которое хочет получать простое всплывающее сообщение, будет зарегистрировано для следующего шаблона:

	<toast>
	  <visual>
	    <binding template=\"ToastText01\">
	      <text id=\"1\">$(News_English)</text>
	    </binding>
	  </visual>
	</toast>



Шаблоны - это очень мощная функция, о них можно узнать в [Руководстве по использованию концентраторов уведомлений]. Справки по языку выражений шаблонов см. в [Инструкциях по использованию концентраторов уведомлений для Магазина Windows].


<h2><a name="ui"></a>Пользовательский интерфейс приложения</h2>

Теперь изменим приложение Breaking News, созданное в разделе [Использование концентраторов уведомлений для передачи экстренных новостей] для отправки локализованных экстренных новостей с помощью шаблонов.


Чтобы адаптировать приложения клиента для получения локализованных сообщений, необходимо заменить *собственные* регистрации (т. е. регистрации с указанием шаблона) на регистрации шаблона.


В приложении Магазина Windows:

Измените свой MainPage.xaml, чтобы включить поле языкового стандарта:

	<Grid Margin="120, 58, 120, 80"  
			Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
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
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="Button_Click" />
    </Grid>

<h2><a name="building-client"></a><span class="building app">Пользовательский интерфейс приложения</span>Разработка клиентского приложения Магазина Windows</h2>

1. В классе Notifications добавьте параметр языкового стандарта к методам *StoreCategoriesAndSubscribe* и *SubscribeToCategories*.

		public async Task StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            await SubscribeToCategories(locale, categories);
        }

        public async Task SubscribeToCategories(string locale, IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            var template = String.Format(@"<toast><visual><binding template=""ToastText01""><text id=""1"">$(News_{0})</text></binding></visual></toast>", locale);

            await hub.RegisterTemplateAsync(channel.Uri, template, "newsTemplate", categories);
        }

	Обратите внимание, что вместо вызова метода *RegisterNativeAsync* мы вызываем метод *RegisterTemplateAsync*: регистрируется конкретный формат уведомления, в котором шаблон зависит от языкового стандарта. Мы также указываем имя шаблона ("newsTemplate"), так как нам может понадобиться зарегистрировать несколько шаблонов (например, один для всплывающих уведомлений и один для плиток) и поэтому нужно назвать их так, чтобы иметь возможность обновлять или удалять.

	Обратите внимание, что если для устройства зарегистрировано несколько шаблонов с одним и тем же тегом, то одно входящее сообщение для этого тега приведет к передаче нескольких уведомлений на устройство (по одному для каждого шаблона). Это может оказаться полезным, когда одного логическое сообщение должно породить несколько визуальных уведомлений в приложении Магазина Windows, например в виде эмблемы и во всплывающем окне.

2. Для получения сохраненного языкового стандарта добавьте следующий метод:

		public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. В MainPage.xaml.cs обновите обработчик нажатия кнопки, чтобы он получал текущее значение языкового стандарта (поля со списком Locale) и передавал его в вызове класса Notifications, как показано.

		 var locale = (string)Locale.SelectedItem;
            
         var categories = new HashSet<string>();
         if (WorldToggle.IsOn) categories.Add("World");
         if (PoliticsToggle.IsOn) categories.Add("Politics");
         if (BusinessToggle.IsOn) categories.Add("Business");
         if (TechnologyToggle.IsOn) categories.Add("Technology");
         if (ScienceToggle.IsOn) categories.Add("Science");
         if (SportsToggle.IsOn) categories.Add("Sports");

         await ((App)Application.Current).Notifications.StoreCategoriesAndSubscribe(locale, categories);

         var dialog = new MessageDialog(String .Format("Locale: {0}; Subscribed to: {1}", locale, string.Join(",", categories)));
         dialog.Commands.Add(new UICommand("OK"));
         await dialog.ShowAsync();

4. Наконец, в файле App.xaml.cs обязательно обновите вызов 
Одноэлементные уведомления в методе *OnLaunched*:

		Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());


<h2><a name="send"></a>Отправка уведомлений из серверной части</h2>

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]





## Дальнейшие действия

Дополнительные сведения об использовании шаблонов см. в разделе [Уведомление пользователей с помощью концентраторов уведомлений: ASP.NET], [Уведомление пользователей с помощью концентраторов уведомлений: мобильные службы], также см. [Руководство по использованию концентраторов уведомлений]. Справочник по языку выражений шаблонов см. в [Инструкциях по использованию концентраторов уведомлений для Магазина Windows].

<!-- Anchors. -->
[Основные сведения о шаблонах]: #concepts
[Пользовательский интерфейс приложения]: #ui
[Разработка клиентского приложения Магазина Windows]: #building-client
[Отправка уведомлений из серверной части]: #send
[Дальнейшие действия]:#next-steps

<!-- Images. -->





















<!-- URLs. -->
[Мобильная служба]: /ru-ru/develop/mobile/tutorials/get-started
[ Уведомление пользователей с помощью концентраторов уведомлений: ASP.NET]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[ Уведомление пользователей с помощью концентраторов уведомлений: мобильные службы]: /ru-ru/manage/services/notification-hubs/notify-users
[Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet 

[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet
[Push-уведомлений для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js

[Портал управления Azure]: https://manage.windowsazure.com/
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководства по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
[Инструкции по использованию концентраторов уведомлений для iOS]: http://msdn.microsoft.com/ru-ru/library/jj927168.aspx
[Инструкции по использованию концентраторов уведомлений для магазина Windows]: http://msdn.microsoft.com/ru-ru/library/jj927172.aspx
