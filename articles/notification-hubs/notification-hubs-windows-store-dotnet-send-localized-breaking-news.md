<properties 
	pageTitle="Передача локализованных экстренных новостей с помощью центров уведомлений" 
	description="Узнайте, как использовать центры уведомлений Azure Service Bus для отправки уведомлений о локализованных экстренных новостях." 
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
	ms.date="04/27/2015" 
	ms.author="wesmc"/>

# Использование концентраторов уведомлений для отправки локализованных экстренных новостей

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Магазин Windows — C#" class="current">Магазин Windows — C#</a><a href="/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS">iOS</a>
</div>

##Обзор

В этом разделе показано, как использовать функцию **template** концентраторов уведомлений Azure для рассылки уведомлений об экстренных новостях, локализованных для языка и устройства. В этом учебнике вы начнете с приложения Магазина Windows, созданного в учебнике [Использование концентраторов уведомлений для передачи экстренных новостей] По завершении вы сможете регистрировать для категорий, которые вас интересуют, указывать язык уведомлений, и получать push-уведомления только для выбранных категорий на этом языке.


Этот сценарий состоит из двух частей:

- приложение Магазина Windows позволяет клиентским устройствам указывать язык и подписываться на различные категории экстренных новостей; 

- сервер рассылает уведомления, используя функции **tag** и **template** концентраторов уведомлений Azure.



##Предварительные требования

Вы должны предварительно выполнить учебник [Использование концентраторов уведомлений для передачи экстренных новостей], чтобы у вас был нужный код, так как этот учебник построен непосредственно на этом коде.

Нужен также Visual Studio 2012.


##Основные сведения о шаблонах

В учебнике [Использование центров уведомлений для передачи экстренных новостей] вы создали приложение, которое использовало **теги** для подписки на уведомления для различных категорий новостей. Однако многие приложения ориентированы на несколько рынков и требуют локализации. Это означает, что само содержимое уведомлений должно быть локализовано и доставлено в правильный набор устройств. В этом разделе будут продемонстрированы способы использования **шаблонов** центров уведомлений, которые позволяют легко доставлять уведомления о локализованных экстренных новостях.

Примечание. Один из способов отправки локализованных уведомлений — создание нескольких версий каждого тега. Например, для поддержки английского, французского и китайского нам понадобится три разных тега для мировых новостей: "world_en", "world_fr" и "world_ch". Затем необходимо отправить локализованную версию мировых новостей по каждому из этих тегов. В этом разделе мы используем шаблоны во избежание избыточного количества тегов и необходимости отправки нескольких сообщений.

В общих чертах, шаблоны представляют собой способ указать, как конкретное устройство должно получать уведомления. Шаблон указывает точный формат полезных данных путем обращения к свойствам, которые являются частью сообщения, отправленного сервером вашего приложение. В нашем случае мы отправим независимое от языка сообщение, которое содержит все поддерживаемые языки:

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



Шаблоны — это очень мощная функция, о них можно узнать в [Руководстве по использованию концентраторов уведомлений]. Справки по языку выражения шаблона см. в [Инструкциях по использованию концентраторов уведомлений для Магазина Windows].


##Пользовательский интерфейс приложения

Теперь изменим приложение "Экстренные новости", созданное в разделе [Использование концентраторов уведомлений для передачи экстренных новостей] для отправки локализованных экстренных новостей с помощью шаблонов.


Для адаптации приложений клиента для получения локализованных сообщений необходимо заменить *собственные* регистрации (т. е. регистрации с указанием шаблона) на регистрации шаблона.


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

##Разработка клиентского приложения Магазина Windows

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

	Обратите внимание, что вместо вызова метода *RegisterNativeAsync* мы вызываем метод *RegisterTemplateAsync*: мы регистрируем специальный формат уведомлений, в котором шаблон зависит от языкового стандарта. Мы также предоставляем имя шаблона ("newsTemplate"), потому что нам может понадобиться зарегистрировать более одного шаблона (например один для всплывающих уведомлений и один для плиток) и нам нужно назвать их, чтобы иметь возможность обновлять или удалять.

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

4. Наконец, в файле App.xaml.cs обновите вызов одноэлементного экземпляра Notifications в методе *OnLaunched*.

		Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());


##Отправка локализованных уведомлений из серверной части

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]





## Дальнейшие действия

Дополнительную информацию об использовании шаблонов см. в разделах [Уведомление пользователей с помощью центров уведомлений: ASP.NET], [Уведомление пользователей с помощью центров уведомлений: мобильные службы] и [руководстве по использованию центров уведомлений]. Справочник по языку выражений шаблона см. в учебнике [Инструкциях по использованию концентраторов уведомлений для Магазина Windows].

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

<!-- Images. -->





















<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Уведомление пользователей с помощью центров уведомлений: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Уведомление пользователей с помощью центров уведомлений: мобильные службы]: /manage/services/notification-hubs/notify-users
[Использование концентраторов уведомлений для передачи экстренных новостей]: /manage/services/notification-hubs/breaking-news-dotnet
[Использование центров уведомлений для передачи экстренных новостей]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководстве по использованию концентраторов уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[руководстве по использованию центров уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Инструкциях по использованию концентраторов уведомлений для Магазина Windows]: http://msdn.microsoft.com/library/jj927172.aspx
 

<!---HONumber=July15_HO1-->