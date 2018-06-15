---
title: Отправка push-уведомлений на конкретные устройства (универсальная платформа Windows) | Документация Майкрософт
description: Использование Центров уведомлений Azure с тегами в регистрации для отправки экстренных новостей в приложение универсальной платформы Windows.
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 9b9e3b910162653c14c398e2c3392709abcd5fd8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776691"
---
# <a name="tutorial-push-notifications-to-specific-windows-devices-running-universal-windows-platform-applications"></a>Руководство по отправке push-уведомлений на конкретные устройства Windows с запущенными приложениями универсальной платформы Windows
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Обзор
В этом руководстве объясняется, как использовать Центры уведомлений Azure для отправки уведомлений об экстренных новостях в приложения из магазина Windows или на платформе Windows Phone 8.1 (без Silverlight). Если вы намерены использовать платформу Windows Phone 8.1 Silverlight, перейдите к версии этой статьи для [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md). 

Из этого руководства вы узнаете, как с помощью Центров уведомлений Azure отправлять push-уведомления на конкретные устройства Windows с запущенными приложениями универсальной платформы Windows. Завершив работу с этим руководством, вы сможете зарегистрироваться в интересующих вас категориях экстренных новостей и получать push-уведомления только для этих категорий. 

Широковещательные сценарии реализуются путем включения одного или нескольких *тегов* при регистрации в Центре уведомлений. Если уведомления отправляются на тег, их получают все устройства, зарегистрированные для данного тега. Дополнительные сведения о тегах см. в статье [Маршрутизация и выражения тегов](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Проекты для Магазина Windows и проекты Windows Phone 8.1 и более ранней версии не поддерживаются в Visual Studio 2017. Дополнительные сведения см. в статье [Целевая платформа и совместимость для Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs). 

При работе с этим руководством вы выполните следующие задачи:

> [!div class="checklist"]
> * добавление возможности выбора категорий в мобильное приложение;
> * Регистрация для использования уведомлений
> * отправка уведомлений с тегами;
> * Запуск приложения и создание уведомлений

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этим руководством выполните инструкции из статьи [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows][get-started].  

## <a name="add-category-selection-to-the-app"></a>Добавление возможности выбора категорий в приложение
Прежде всего необходимо добавить элементы пользовательского интерфейса для имеющейся главной страницы, чтобы пользователь имел возможность выбирать категории для регистрации. Выбранные категории хранятся на устройстве. При запуске приложения в центре уведомлений создается регистрация устройства с выбранными категориями, представленными в форме тегов.

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

2. В **обозревателе решений** щелкните проект правой кнопкой мыши и добавьте новый класс **Notifications**. Добавьте к определению этого класса модификатор **public**, а затем добавьте в файл кода следующие операторы **using**.

    ```csharp   
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

3. Скопируйте следующий код в новый класс **Notifications**:
   
    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```
   
    Этот класс использует локальное хранилище для хранения категорий новостей, которые данное устройство должно получать. Вместо вызова метода *RegisterNativeAsync* мы вызываем метод *RegisterTemplateAsync* для регистрации в категориях с помощью шаблонной регистрации. 
   
    Если вы намерены зарегистрировать несколько шаблонов (например, один для всплывающих уведомлений и один для плиток), введите имя шаблона (например, simpleWNSTemplateExample). Назовите шаблоны таким образом, чтобы можно было обновить или удалить их.
   
    >[!NOTE]
    >Если устройство регистрирует несколько шаблонов с тем же тегом, одно входящее сообщение для этого тега приведет к передаче нескольких уведомлений на устройство (по одному для каждого шаблона). Это полезно, когда одно логическое сообщение должно привести к нескольким визуальным уведомлениям в приложении Магазина Windows, например в виде эмблемы и во всплывающем окне.
   
    Дополнительные сведения см. в статье [Шаблоны](notification-hubs-templates-cross-platform-push-messages.md).

4. В файле проекта App.xaml.cs добавьте следующее свойство к классу **App** :

    ```csharp   
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```
   
    Это свойство используется для создания экземпляра **Notifications** и доступа к нему.
   
    В коде замените заполнители `<hub name>` и `<connection string with listen access>` именем центра уведомлений и строкой подключения для *DefaultListenSharedAccessSignature*, полученными ранее.
   
   > [!NOTE]
   > Так как учетные данные, которые распределены с помощью клиентского приложения, обычно небезопасны, следует распространять только ключ для доступа к *прослушиванию*. Доступ к прослушиванию позволяет приложению регистрироваться для использования уведомлений, однако при этом нельзя изменять имеющиеся регистрации и отправлять уведомления. Для отправки уведомлений и изменения существующих регистраций используется ключ полного доступа в защищенной серверной службе.
   > 
   > 
5. В файле проекта MainPage.xaml.cs добавьте следующую строку:
   
    ```csharp
    using Windows.UI.Popups;
    ```

6. В файле проекта MainPage.xaml.cs добавьте следующий метод:
   
    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    Этот метод создает список категорий и использует класс **Notifications** для хранения списка в локальном хранилище. Он также регистрирует соответствующие теги в центре уведомлений. При изменении категорий регистрация создается заново с новыми категориями.

Теперь приложение может сохранять набор категорий в локальном хранилище устройства. Приложение регистрируется в центре уведомлений всякий раз, когда пользователи выбирают другую категорию.

## <a name="register-for-notifications"></a>Регистрация для использования уведомлений
В этом разделе приведены действия, позволяющие зарегистрироваться в центре уведомлений при запуске с использованием категорий, сохраненных в локальном хранилище.

> [!NOTE]
> Так как универсальный код ресурса (URI) канала, назначенный службой уведомлений Windows (WNS), может измениться в любое время, следует регулярно производить регистрацию для использования уведомлений, чтобы предотвратить сбои уведомлений. В этом примере регистрация для использования уведомлений осуществляется при каждом запуске приложения. Для тех приложений, которые запускаются часто, (более одного раза в день), возможно, лучше пропустить регистрацию, чтобы не изменять пропускную способность, если с момента прошлой регистрации прошло меньше суток.
> 
> 

1. Откройте файл App.xaml.cs и обновите метод `notifications`InitNotificationsAsync **, чтобы использовать класс**  для подписки по категориям.
   
    ```csharp
    // *** Remove or comment out these lines *** 
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
   ```
    Этот процесс гарантирует, что при каждом запуске приложения оно извлекает категории из локального хранилища и запрашивает для них регистрацию. Вы создали метод **InitNotificationsAsync** в ходе изучения одной из частей руководства по [началу работы с центрами уведомлений][get-started].
2. В файле проекта MainPage.xaml.cs добавьте следующий код в метод *OnNavigatedTo* :
   
    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    В соответствии с этим кодом главная страница обновляется в зависимости от состояния ранее сохраненных категорий.

Приложение готово. Теперь приложение может сохранять набор категорий в локальном хранилище устройства и использовать его для регистрации в центре уведомлений всякий раз, когда пользователи выбирают другую категорию. В следующем разделе мы определим серверную часть, которая может отправлять уведомления категорий в это приложение.

## <a name="send-tagged-notifications"></a>Отправка уведомлений с тегами
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Запуск приложения и создание уведомлений
1. В Visual Studio нажмите клавишу **F5**, чтобы скомпилировать и запустить приложение. В пользовательском интерфейсе присутствует набор переключателей, позволяющий выбрать категории для подписки. 
   
    ![Приложение экстренных новостей][1]

2. Включите переключатели одной или нескольких категорий, а затем нажмите **Подписаться**.
   
    Приложение преобразует выбранные категории в теги и запрашивает у концентратора уведомлений новую регистрацию устройств для выбранных тегов. Зарегистрированные категории возвращаются и отображаются в диалоговом окне.
   
    ![Переключатели категорий и кнопка подписки][19]

3. Отправьте новое уведомление из серверной части одним из следующих способов:

   * **Консольное приложение.** Запустите консольное приложение.
   * **Java/PHP.** Запустите приложение или скрипт.
     
     Уведомления для выбранных категорий отображаются в виде всплывающих уведомлений.
     
     ![Всплывающие уведомления][14]

## <a name="next-steps"></a>Дополнительная информация
В этой статье вы рассмотрели, как производить рассылку экстренных новостей по категориям. Приложение серверной части отправляет уведомления с тегами на устройства, зарегистрированные для получения уведомлений с этим тегом. Сведения о том, как отправлять push-уведомления конкретным пользователям независимо от того, какое устройство они используют, см. в следующем руководстве:

> [!div class="nextstepaction"]
> [Отправка локализованных push-уведомлений](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md).

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
[get-started]: /azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification
[Use Notification Hubs to broadcast localized breaking news]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
