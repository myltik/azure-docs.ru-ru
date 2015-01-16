<properties pageTitle="Приступая к работе с push-уведомлениями (принудительная отправка устаревшего типа) | Центр мобильных разработок" metaKeywords="" description="Узнайте, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Windows Phone (устаревшая отправка)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="glenga" />


# Приступая к работе с push-уведомлениями в мобильных службах (принудительная отправка устаревшего типа)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" >C# в Магазине Windows</a>
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">JavaScript в Магазине Windows</a>
    <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
    <a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/ru-ru/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    -->
	<a href="/ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET backend">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="JavaScript backend" class="current">Серверная часть JavaScript</a>
</div>

В данном разделе рассматривается использование мобильных служб Azure для отправки push-уведомлений в приложение Windows Phone 8. 
В этом учебнике объясняется добавление push-уведомлений с помощью службы push-уведомлений Microsoft (MPNS) в проекте быстрого запуска. В результате ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

>[WACOM.NOTE]В этом разделе рассматривается поддержка <em>существующих</em> мобильных служб, которые <em>еще не были обновлены</em> для использования интеграции с концентраторами уведомлений. При создании <em>новой</em> мобильной службы интегрированные функции включаются автоматически. Информацию о работе с мобильными службами последних версий см. в разделе [Приступая к работе с push-уведомлениями](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).
>
>Мобильные службы теперь интегрируются с центрами уведомлений Azure для поддержки дополнительных функций push-уведомлений, таких как шаблоны, использование нескольких платформ и улучшенное масштабирование. <em>Если это возможно, обновите имеющиеся мобильные службы, чтобы использовать центры уведомлений</em>. После обновления ознакомьтесь с этой версией раздела [Приступая к работе с push-уведомлениями](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Создание таблицы регистраций]
2. [Добавление push-уведомлений в приложение]
3. [Обновление скриптов для отправки push-уведомлений]
4. [Вставка данных для получения уведомлений]

Для прохождения данного учебника требуется: [Visual Studio 2012 Express для Windows Phone]или более поздняя версия.

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами]. 

   >[WACOM.NOTE]При отправке более 500 сообщений на пользователя в день следует использовать концентраторы уведомлений. Для получения дополнительной информации см. <a href="/ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/">Приступая к работе с концентраторами уведомлений</a>.

## <a name="create-table"></a>Создать таблицу

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a>Добавление push-уведомлений в приложение</h2>
		
1. Откройте в Visual Studio проектный файл MainPage.xaml.cs и добавьте следующий код для создания нового класса **Registrations** :

	    public class Registrations
	    {
	        public string Id { get; set; }
	
	        [JsonProperty(PropertyName = "handle")]
	        public string Handle { get; set; }
	    }
	
	Для хранения URI канала используется свойство Handle.

2. Откройте файл App.xaml.cs и добавьте следующий оператор "using":

        using Microsoft.Phone.Notification;

3. Добавьте в App.xaml.cs следующий код:
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

		private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
	       await registrationsTable.InsertAsync(registration);
        }

   	Этот код получает и сохраняет канал для подписки на push-уведомления, а также привязывает их к плитке приложения по умолчанию.

	<div class="dev-callout"><b>Примечание.</b>
		<p>В этом учебнике мобильная служба отправляет в устройство уведомление перевернутой плитки. При отправке всплывающего уведомления вместо этого необходимо вызвать для канала метод <strong>BindToShellToast</strong>. Чтобы поддерживать одновременно и всплывающие, и мозаичные уведомления, следует вызвать оба метода - <strong>BindToShellTile</strong> и <strong>BindToShellToast</strong>. </p>
	</div>
    
4. В верхней части обработчика событий **Application_Launching** в файле App.xaml.cs добавьте в новый метод **AcquirePushChannel** следующий вызов:

        AcquirePushChannel();

   	Это обеспечит инициализацию свойства **CurrentChannel** при каждом запуске приложения.


5.	 В обозревателе решений разверните узел **Свойства**, откройте файл WMAppManifest.xml file, откройте вкладку **Возможности** и убедитесь, что установлен флажок **ID___CAP___PUSH_NOTIFICATION**.

   	![][1]

   	Это гарантирует, что приложение сможет получать push-уведомления.

<h2><a name="update-scripts"></a>Обновите зарегистрированный вводный скрипт на портале управления</h2>

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

4. Последовательно щелкните **TodoItem**, **Скрипт** и **Вставить**. 

   	![][10]

3. Замените функцию вставки следующим кодом и щелкните **Сохранить**:

	    function insert(item, user, request) {
    	    request.execute({
        	    success: function() {
            	    request.respond();
            	    sendNotifications();
        	    }
    	    });

	        function sendNotifications() {
        	    var registrationsTable = tables.getTable('Registrations');
        	    registrationsTable.read({
            	    success: function(registrations) {
                	    registrations.forEach(function(registration) {
                    	    push.mpns.sendFlipTile(registration.handle, {
                        	    title: item.text
                    	    }, {
                        	    success: function(pushResponse) {
                            	    console.log("Sent push:", pushResponse);
                        	    }
                    	    });
                	    });
            	    }
        	    });
    	    }
	    }

    Данный вводный скрипт отправляет push-уведомление (с текстом введенного элемента) по всем каналам, сохраненным в таблице **Регистрации**.

<h2><a name="test"></a>Тестирование push-уведомлений в приложении</h2>

1. В Visual Studio выберите **Развернуть решение** в меню **Сборка**.

2. В эмуляторе прокрутите влево, чтобы открыть список установленных приложений, и найдите новое приложение **TodoList**.

3. Нажмите и удерживайте значок приложения, а затем выберите в контекстном меню **Закрепить на начальном экране**.

  	![][2]

  	Это позволит закрепить заголовок **TodoList** на начальном экране.

4. Чтобы запустить приложение, коснитесь названия **TodoList**. 

  	![][3]

5. В самом приложении введите текст "hello push" в текстовом окне, а затем нажмите **Сохранить**.

   	![][4]

  	При этом в мобильную службу будет отправлен запрос на сохранение добавленного элемента.

6. Нажмите кнопку **Запуск**, чтобы вернуться в главное меню. 

  	![][5]

  	Обратите внимание, что приложение получило push-уведомление, и что название изменилось на **hello push**.

## <a name="next-steps"> </a>Дальнейшие действия

В данном учебнике демонстрируются базовые функции push-уведомлений, предоставленных мобильными службами. Если вашему приложению требуются более сложные функции, такие как отправка уведомлений между различными платформами, маршрутизация на основе подписки или очень большие объемы данных, рассмотрите возможность использования концентраторов уведомлений Azure для вашей мобильной службы. Дополнительные сведения см. в следующих разделах по теме "Концентраторы уведомлений":

+ [Приступая к работе с концентраторами уведомлений]
  <br/>Сведения об использовании концентраторов уведомлений в приложении Магазина Windows.

+ [Что такое концентраторы уведомлений?]
	<br/>Сведения о создании и отправке уведомлений пользователям, работающим на нескольких платформах.

+ [Рассылка уведомлений подписчикам]
	<br/>Предоставление пользователям возможности регистрироваться и получать push-уведомления только по интересующим их категориям.

<!--+ [Send notifications to users]
	<br/>Learn how to send push notifications from a Mobile Service to specific users on any device.

+ [Send cross-platform notifications to users]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с помощью учетной записи Windows.

* [Справочник серверных скриптов мобильных служб]
  <br/>Регистрация и использование серверных скриптов.

* [Справочник принципов использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с .NET. 

<!-- Anchors. -->
[Создание таблицы регистраций]: #create-table
[Обновление скриптов для отправки push-уведомлений]: #update-scripts
[Добавление push-уведомлений в приложение]: #add-push
[Вставка данных для получения уведомлений]: #test
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png



<!-- URLs. -->
[Пакет SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=268375
[Visual Studio 2012 Express для Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-wp8
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8
[Push-уведомлений для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-wp8
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Портал управления Azure]: https://manage.windowsazure.com/
[Объект mpns]: http://go.microsoft.com/fwlink/p/?LinkId=271130
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library/
[Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Что такое концентраторы уведомлений?]: /ru-ru/develop/net/how-to-guides/service-bus-notification-hubs/
[Рассылка уведомлений подписчикам]: /ru-ru/manage/services/notification-hubs/breaking-news-wp8/
[Рассылка уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users/
[Отправка пользователям уведомлений между различными платформами]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/
