<properties 
	pageTitle="Передача локализованных экстренных новостей с помощью центров уведомлений" 
	description="Узнайте, как использовать центры уведомлений Azure Service Bus для отправки уведомлений о локализованных экстренных новостях." 
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
# Использование концентраторов уведомлений для отправки локализованных экстренных новостей

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#" class="current">Магазин Windows C#</a><a href="/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS">iOS</a>
</div>

В этом разделе показано, как использовать функцию **template** центров уведомлений Azure для рассылки уведомлений об экстренных новостях, локализованных для языка и устройства. В этом учебнике вы начнете с приложения Магазина Windows, созданного в учебнике [Использование концентраторов уведомлений для передачи экстренных новостей] По завершении вы сможете регистрировать для категорий, которые вас интересуют, указывать язык уведомлений, и получать push-уведомления только для выбранных категорий на этом языке.

В этом учебнике рассматриваются следующие основные шаги для реализации данного сценария:

1. [Основная информация о шаблонах] 
2. [Пользовательский интерфейс приложения]
3. [Разработка клиентского приложения Магазина Windows]
4. [Отправка уведомлений из серверной части]


Этот сценарий состоит из двух частей:

- приложение Магазина Windows позволяет клиентским устройствам указывать язык и подписываться на различные категории экстренных новостей; 

- сервер рассылает уведомления с помощью функций **tag** и **template** центров уведомлений Azure.



##Предварительные требования ##

Вы должны предварительно выполнить учебник [Использование концентраторов уведомлений для передачи экстренных новостей], чтобы у вас был нужный код, так как этот учебник построен непосредственно на этом коде. 

Нужен также Visual Studio 2012.


<h2><a name="concepts"></a>Основные сведения о шаблонах</h2>

В учебнике [Использование концентраторов уведомлений для передачи экстренных новостей] вы построили приложение, которое использовало **теги** для подписки на уведомления для различных категорий новостей.
Однако многие приложения ориентированы на несколько рынков и требуют локализации. Это означает, что само содержимое уведомлений должно быть локализовано и доставлено в правильный набор устройств.
В этом разделе будут продемонстрированы способы использования функции центров уведомлений **template**, которая позволяет легко доставлять локализованные экстренные уведомления.

Примечание. Один из способов отправки локализованных уведомлений - создание нескольких версий каждого тега. Например, для поддержки английского, французского и китайского нам понадобится три разных тега для мировых новостей: world_en, world_fr и world_ch. Затем необходимо отправить локализованную версию мировых новостей по каждому из этих тегов. В этом разделе мы используем шаблоны во избежание избыточного количества тегов и необходимости отправки нескольких сообщений.

В общих чертах, с помощью шаблонов можно указать, как конкретное устройство должно получать уведомления. Шаблон указывает точный формат полезных данных путем обращения к свойствам, которые являются частью сообщения, отправленного сервером вашего приложение. В нашем случае мы отправим независимое от языка сообщение, которое содержит все поддерживаемые языки:

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

Затем мы убедимся, что устройство зарегистрировано с шаблоном, который ссылается на нужное свойство. Например, приложение Магазина Windows, которое хочет получать простое всплывающее сообщение, будет зарегистрировано для следующего шаблона:

	<toast>
	  <visual>
	    <binding template="ToastText01">
	      <text id="1">$(News_English)</text>
	    </binding>
	  </visual>
	</toast>



Шаблоны - это очень мощная функция, о них можно узнать в [Руководстве по использованию центров уведомлений]. Справки по языку выражения шаблона см. в [Инструкциях по использованию центров уведомлений для Магазина Windows].


<h2><a name="ui"></a>Пользовательский интерфейс приложения</h2>

Теперь изменим приложение "Экстренные новости", созданное в разделе [Использование центров уведомлений для передачи экстренных новостей] для отправки локализованных экстренных новостей с помощью шаблонов.


Чтобы адаптировать свои клиентские приложения для получения локализованных сообщений, вам нужно заменить *собственные* регистрации (т. е. регистрации с указанием шаблона) на регистрации шаблона.


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

1. В классе Notifications добавьте параметр языкового стандарта к методам *StoreCategoriesAndSubscribe* и *SubscribeToCateories*.

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

	Обратите внимание, что вместо вызова метода *RegisterNativeAsync* мы вызываем метод *RegisterTemplateAsync*: мы регистрируем формат уведомлений, в котором шаблон зависит от языкового стандарта. Мы также предоставляем имя шаблона ("newsTemplate"), потому что нам может понадобиться зарегистрировать более одного шаблона (например один для всплывающих уведомлений и один для плиток) и нам нужно назвать их, чтобы иметь возможность обновлять или удалять.

	Обратите внимание, что если устройство регистрирует несколько шаблонов с тем же тегом, одно входящее сообщение для этого тега приведет к передаче нескольких уведомлений на устройство (по одному для каждого шаблона). Это полезно, когда одного логическое сообщение должно привести к нескольким визуальным уведомлениям в приложении Магазина Windows, например в виде эмблемы и во всплывающем окне.

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


<h2><a name="send"></a>Отправка локализованных уведомлений из серверной части</h2>

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]





## Дальнейшие действия

Дополнительную информацию об использовании шаблонов см. в разделе [Уведомление пользователей с помощью центров уведомлений: ASP.NET], [Уведомление пользователей с помощью центров уведомлений: мобильные службы], также см. [Руководство по использованию центров уведомлений]. Справочник по языку выражений шаблона см. в учебнике [Инструкциях по использованию центров уведомлений для Магазина Windows].

<!-- Anchors. -->
[Основная информация о шаблонах]: #concepts
[Пользовательский интерфейс приложения]: #ui
[Разработка клиентского приложения Магазина Windows]: #building-client
[Отправка уведомлений из серверной части]: #send
[Дальнейшие действия]:#next-steps

<!-- Images. -->





















<!-- URLs. -->
[Мобильная служба]: /develop/mobile/tutorials/get-started
[Уведомление пользователей с помощью центров уведомлений: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Уведомление пользователей с помощью центров уведомлений: Мобильные службы]: /manage/services/notification-hubs/notify-users
[Использование концентраторов уведомлений для передачи экстренных новостей]: /manage/services/notification-hubs/breaking-news-dotnet 

[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе с данными]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Приступая к работе с аутентификацией]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push-уведомления для пользователей приложений]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Авторизация пользователей с помощью скриптов]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript и HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Портал управления Azure]: https://manage.windowsazure.com/
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководство по использованию концентраторов уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Инструкции по использованию центров уведомлений для iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Инструкции по использованию концентраторов уведомлений для Магазина Windows]: http://msdn.microsoft.com/library/jj927172.aspx

<!--HONumber=49--> 