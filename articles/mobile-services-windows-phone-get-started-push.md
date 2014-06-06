<properties pageTitle="Приступая к работе с push-уведомлениями (Windows Phone) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Использование мобильных служб Azure для отправки push-уведомлений в приложение Windows Phone." metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с push-уведомлениями в мобильных службах" authors=""  solutions="" writer="glenga" manager="" editor=""  />


# Приступая к работе с push-уведомлениями в мобильных службах

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Магазин Windows C#" >Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Сервер .NET">Сервер .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="Сервер JavaScript" class="current">Сервер JavaScript</a>
</div>

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Windows Phone 8. 
В этом учебнике вам предстоит добавить push-уведомления в проект быстрого начала работы с помощью службы push-уведомлений Microsoft (MPNS). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

>[WACOM.NOTE]Мобильные службы теперь интегрируются с концентраторами уведомлений Azure для поддержки дополнительных функций push-уведомлений, таких как шаблоны, использование нескольких платформ и масштабирование. Эти интегрированные функции в настоящее время находятся на стадии предварительной версии. Дополнительные сведения см. в этой версии [Приступая к работе с push-уведомлениями](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Создание таблицы регистраций]
2. [Добавление push-уведомлений в приложение]
3. [Обновление скриптов для отправки push-уведомлений]
4. [Вставка данных для получения уведомлений]

Для работы с учебником требуется [Visual Studio 2012 Express для Windows Phone] или более поздняя версия.

Этот учебник основан на возможностях быстрого начала работы с мобильными службами. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами]. 

   >[WACOM.NOTE]При отправке более 500 сообщений на пользователя в день следует использовать концентраторы уведомлений. Дополнительные сведения см. в разделе <a href="/ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/">Приступая к работе с концентраторами уведомлений</a>.

## <a name="create-table"></a>Создание таблицы

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a><span class="short-header">Добавление push-уведомлений</span>Добавление push-уведомлений в приложение</h2>
		
1. В Visual Studio откройте файл проекта MainPage.xaml.cs и добавьте следующий код, который создает новый класс **Регистрации**:

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

		private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.Uri };
	       await registrationsTable.InsertAsync(registration);
        }

   	Этот код получает и сохраняет канал для подписки на push-уведомления, а также привязывает их к плитке приложения по умолчанию.

	<div class="dev-callout"><b>Примечание.</b>
		<p>В этом учебнике мобильная служба отправляет в устройство уведомление перевернутой плитки. При отправке всплывающего уведомления вместо этого необходимо вызвать для канала метод <strong>BindToShellToast</strong>. Чтобы поддерживать одновременно и всплывающие, и мозаичные уведомления, следует вызвать оба метода — <strong>BindToShellTile</strong> и <strong>BindToShellToast</strong>. </p>
	</div>
    
4. В верхней части **Application_Launching** обработчика событий в App.xaml.cs добавьте в новый метод **AcquirePushChannel** следующий вызов:

        AcquirePushChannel();

   	Это гарантирует, что свойство **CurrentChannel** инициализируется при каждом запуске приложения.


5.	В обозревателе решений разверните узел **Свойства**, откройте файл WMAppManifest.xml, откройте вкладку **Возможности** и убедитесь, что установлен флажок для возможности **ID___CAP___PUSH_NOTIFICATION**.

   	![][1]

   	Это гарантирует, что приложение сможет получать push-уведомления.

<h2><a name="update-scripts"></a><span class="short-header">Обновление скрипта вставки</span>Обновление зарегистрированных скриптов вставки на портале управления</h2>

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

4. Последовательно щелкните **TodoItem**, **Скрипт** и **Вставить**. 

   	![][10]

3. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

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
                    	    push.mpns.sendFlipTile(registration.uri, {
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

    Этот скрипт вставки отправляет push-уведомление (с текстом вставленного элемента) по всем каналам, хранящимся в таблице **Регистрации**.

<h2><a name="test"></a><span class="short-header">Тестирование приложения</span>Тестирование push-уведомлений в приложении</h2>

1. В Visual Studio выберите **Развернуть решение** в меню **Сборка**.

2. В эмуляторе проведите пальцем влево, чтобы отобразить список установленных приложений и найти новое приложение **TodoList**.

3. Нажмите и удерживайте значок приложения, а затем выберите в контекстном меню команду **Закрепить в меню пуска**.

  	![][2]

  	В результате в меню "Пуск" будет закреплена плитка с именем **TodoList**.

4. Коснитесь плитки с именем **TodoList**, чтобы запустить приложение. 

  	![][3]

5. В приложении введите в текстовом поле текст "Первое push-уведомление" и нажмите кнопку **Сохранить**.

   	![][4]

  	При этом в мобильную службу будет отправлен запрос на сохранение добавленного элемента.

6. Нажмите клавишу **Пуск**, чтобы вернуться в меню "Пуск". 

  	![][5]

  	Обратите внимание, что приложение получило push-уведомление и теперь на плитке отображается текст **Первое push-уведомление**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике демонстрируются основные функциональные возможности push-уведомлений, предоставляемые мобильными службами. Если вашему приложению требуются более сложные функции, такие как отправка уведомлений между различными платформами, маршрутизация на основе подписки или очень большие объемы данных, рассмотрите возможность использования концентраторов уведомлений Azure для вашей мобильной службы. Дополнительные сведения см. в следующих разделах, посвященных концентраторам уведомлений:

+ [Приступая к работе с концентраторами уведомлений]
  <br/>Сведения об использовании концентраторов уведомлений в приложении магазина Windows.

+ [Что такое концентраторы уведомлений]
	<br/>Сведения о создании уведомлений и их доставке пользователям на нескольких платформах.

+ [Рассылка уведомлений подписчикам]
	<br/>Сведения о том, как пользователи могут зарегистрироваться и получать push-уведомления для категорий, в которых они заинтересованы.

<!--+ [Рассылка уведомлений пользователям]
	<br/>Дополнительные сведения о том, как отправлять push-уведомления из мобильной службы конкретным пользователям на любом устройстве.

+ [Отправка пользователям уведомлений между различными платформами]
	<br/>Дополнительные сведения об использовании шаблонов для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.
-->

Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Дополнительные сведения о выполнении аутентификации учетных данных пользователей приложения с помощью учетной записи Windows.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о регистрации и использовании серверных скриптов.

* [Справочник принципов использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с .NET. 

<!-- Anchors. -->
[Создание таблицы регистраций]: #create-table
[Обновление скриптов для отправки push-уведомлений]: #update-scripts
[Добавление push-уведомлений в приложение]: #add-push
[Вставка данных для получения уведомлений]: #test
[Следующие шаги]:#next-steps

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
[Что такое концентраторы уведомлений]: /ru-ru/develop/net/how-to-guides/service-bus-notification-hubs/
[Рассылка уведомлений подписчикам]: /ru-ru/manage/services/notification-hubs/breaking-news-wp8/
[Рассылка уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users/
[Отправка пользователям уведомлений между различными платформами]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/

