<properties linkid="develop-notificationhubs-tutorials-send-localized-breaking-news-windowsdotnet" urlDisplayName="Localized Breaking News" pageTitle="Notification Hubs Localized Breaking News Tutorial" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send localized breaking news notifications." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send localized breaking news" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Использование концентраторов уведомлений для передачи локализованных экстренных новостей

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Магазин Windows C#" class="current">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS">iOS</a>
</div>

В этом разделе показано, как использовать функцию **template** концентраторов уведомлений Azure для рассылки уведомлений об экстренных новостях, локализованных для языка и устройства. В этом учебнике вы начнете с приложения Магазина Windows, созданного в учебнике [Использование концентраторов уведомлений для передачи экстренных новостей][Использование концентраторов уведомлений для передачи экстренных новостей] По завершении вы сможете регистрировать для категорий, которые вас интересуют, указывать язык уведомлений, и получать push-уведомления только для выбранных категорий на этом языке.

В этом учебнике рассматриваются следующие основные шаги для реализации данного сценария:

1.  [Основные сведения о шаблонах][Основные сведения о шаблонах]
2.  [Пользовательский интерфейс приложения][Пользовательский интерфейс приложения]
3.  [Разработка клиентского приложения Магазина Windows][Разработка клиентского приложения Магазина Windows]
4.  [Отправка уведомлений из серверной части][Отправка уведомлений из серверной части]

Этот сценарий состоит из двух частей:

-   приложение Магазина Windows позволяет клиентским устройствам указывать язык и подписываться на различные категории экстренных новостей;

-   сервер рассылает уведомления, используя функции **tag** и **template** концентраторов уведомлений Azure.

## Предварительные требования

Вы должны предварительно выполнить учебник [Использование концентраторов уведомлений для передачи экстренных новостей][Использование концентраторов уведомлений для передачи экстренных новостей], чтобы у вас был нужный код, так как этот учебник построен непосредственно на этом коде.

Нужен также Visual Studio 2012.

## <a name="concepts"></a><span class="short-header">основные понятия</span>Основные сведения о шаблонах

В учебнике [Использование концентраторов уведомлений для передачи экстренных новостей][Использование концентраторов уведомлений для передачи экстренных новостей] вы построили приложение, которое использовало **теги** для подписки на уведомления для различных категорий новостей.
Однако многие приложения ориентированы на несколько рынков и требуют локализации. Это означает, что содержимое самих уведомлений должно быть локализовано и доставлено в правильный набор устройств.
В этом разделе будут представлены способы использования функции концентраторов уведомлений **template**, которая позволяет легко доставлять локализованные экстренные уведомления.

Примечание. Один из способов отправки локализованных уведомлений — создание нескольких версий каждого тега. Например, для поддержки английского, французского и китайского понадобится три разных тега для мировых новостей: "world\_en", "world\_fr" и "world\_ch". Затем необходимо отправить локализованную версию мировых новостей по каждому из этих тегов. В этом разделе мы используем шаблоны во избежание избыточного количества тегов и необходимости отправки нескольких сообщений.

В общих чертах, шаблоны представляют собой способ указать, как конкретное устройство должно получать уведомления. Шаблон указывает точный формат полезных данных путем обращения к свойствам, которые являются частью сообщения, отправленного сервером вашего приложение. В нашем случае мы отправим независимое от языка сообщение, которое содержит все поддерживаемые языки:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Затем мы убедимся, что устройство зарегистрировано с шаблоном, который ссылается на нужное свойство. Например, приложение Магазина Windows, которое хочет получать простое всплывающее сообщение, будет зарегистрировано для следующего шаблона:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>

Шаблоны — это очень мощная функция, о них можно узнать в [Руководстве по использованию концентраторов уведомлений][Руководстве по использованию концентраторов уведомлений]. Справки по языку выражения шаблона см. в [Инструкциях по использованию концентраторов уведомлений для Магазина Windows][Инструкциях по использованию концентраторов уведомлений для Магазина Windows].

## <a name="ui"></a><span class="short-header">Пользовательский интерфейс приложения</span>Пользовательский интерфейс приложения

Теперь изменим приложение "Экстренные новости", созданное в разделе [Использование концентраторов уведомлений для передачи экстренных новостей][Использование концентраторов уведомлений для передачи экстренных новостей] для отправки локализованных экстренных новостей с помощью шаблонов.

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

## <a name="building-client"></a><span class="building app">Пользовательский интерфейс приложения</span>Разработка клиентского приложения Магазина Windows

1.  В классе Notifications добавьте параметр языкового стандарта к методам *StoreCategoriesAndSubscribe* и *SubscribeToCategories*.

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

    Обратите внимание, что вместо вызова метода *RegisterNativeAsync* вызывается *RegisterTemplateAsync*: регистрируется конкретный формат уведомления, в котором шаблон зависит от языкового стандарта. Мы также предоставляем имя шаблона ("newsTemplate"), потому что нам может понадобиться зарегистрировать более одного шаблона (например один для всплывающих уведомлений и один для плиток) и нам нужно назвать их, чтобы иметь возможность обновлять или удалять.

    Обратите внимание, что если устройство регистрирует несколько шаблонов с тем же тегом, одно входящее сообщение для этого тега приведет к передаче нескольких уведомлений на устройство (по одному для каждого шаблона). Это полезно, когда одного логическое сообщение должно привести к нескольким визуальным уведомлениям в приложении Магазина Windows, например в виде эмблемы и во всплывающем окне.

2.  Для получения сохраненного языкового стандарта добавьте следующий метод:

        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3.  В MainPage.xaml.cs обновите обработчик нажатия кнопки, чтобы он получал текущее значение языкового стандарта (поля со списком Locale) и передавал его в вызове класса Notifications, как показано.

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

4.  Наконец, в файле App.xaml.cs обновите вызов одноэлементного экземпляра
    Notifications в методе *OnLaunched*.

        Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());

## <a name="send"></a><span class="short-header">Отправка локализованных уведомлений</span>Отправка локализованных уведомлений из серверной части

[WACOM.INCLUDE [notification-hubs-localized-back-end][notification-hubs-localized-back-end]]

## Дальнейшие действия

Дополнительные сведения об использовании шаблонов см. в разделе [Уведомление пользователей с помощью концентраторов уведомлений: ASP.NET][Уведомление пользователей с помощью концентраторов уведомлений: ASP.NET], [Уведомление пользователей с помощью концентраторов уведомлений: мобильные службы][Уведомление пользователей с помощью концентраторов уведомлений: мобильные службы], также см. [Руководство по использованию концентраторов уведомлений][Руководстве по использованию концентраторов уведомлений]. Справочник по языку выражений шаблона см. в учебнике [Инструкциях по использованию концентраторов уведомлений для Магазина Windows][Инструкциях по использованию концентраторов уведомлений для Магазина Windows].



  [Магазин Windows C#]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/ "Магазин Windows C#"
  [iOS]: /ru-ru/documentation/articles/notification-hubs-ios-send-localized-breaking-news/ "iOS"
  [Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
  [Основные сведения о шаблонах]: #concepts
  [Пользовательский интерфейс приложения]: #ui
  [Разработка клиентского приложения Магазина Windows]: #building-client
  [Отправка уведомлений из серверной части]: #send
  [Руководстве по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
  [Инструкциях по использованию концентраторов уведомлений для Магазина Windows]: http://msdn.microsoft.com/ru-ru/library/jj927172.aspx
  [notification-hubs-localized-back-end]: ../includes/notification-hubs-localized-back-end.md
  [Уведомление пользователей с помощью концентраторов уведомлений: ASP.NET]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
  [Уведомление пользователей с помощью концентраторов уведомлений: мобильные службы]: /ru-ru/manage/services/notification-hubs/notify-users
