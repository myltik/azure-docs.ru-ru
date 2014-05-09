<properties linkid="develop-mobile-tutorials-get-started-with-push-dotnet" urlDisplayName="Приступая к работе с push-уведомлениями" pageTitle="Приступая к работе с push-уведомлениями - мобильные службы" metaKeywords="push-уведомления c#" description="Узнайте, как использовать push-уведомления с мобильными службами Azure." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Приступая к работе с push-уведомлениями в мобильных службах с помощью Visual Studio 2012" authors="" />
# Приступая к работе с push-уведомлениями в мобильных службах с помощью Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012" title="Магазин Windows C#" class="current">Магазин Windows C#</a>
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-js-vs2012" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a>
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a>
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a>
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>	

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение магазина Windows. 
В этом учебнике вам предстоит добавить push-уведомления в проект быстрого начала работы с помощью службы push-уведомлений Windows (WNS). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

<div class="dev-callout"><b>Примечание.</b>
	<p>В этом учебнике push-уведомления добавляются в приложение Магазина Windows, созданное в Visual Studio 2012. Visual Studio 2013 предоставляет новые возможности, которые упрощают установку push-уведомлений в приложение Магазина Windows с помощью мобильных служб. Для версии Visual Studio 2013 см. <a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet">Приступая к работе с push-уведомлениями</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения для push-уведомлений и конфигурация мобильных служб]
2. [Создание таблицы регистраций]
3. [Добавление push-уведомлений в приложение]
4. [Обновление скриптов для отправки push-уведомлений]
5. [Вставка данных для получения уведомлений]

Для работы с данным учебником требуется следующее:

+ Microsoft Visual Studio 2012 Express для Windows 8
+ Активная учетная запись Магазина Windows

Это учебник основан на учебнике [Приступая к работе с данными]. Перед началом работы с этим учебником необходимо изучить [этот учебник][Get started with data]. 

<h2><a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для Магазина Windows</h2>

Чтобы отправлять push-уведомления в приложения Магазина Windows из мобильных служб, необходимо отправить приложение в Магазин Windows. После этого необходимо настроить мобильную службу на интеграцию с WNS.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Мобильная служба и приложение теперь настроены для работы с WNS. Далее нужно создать таблицу для хранения регистраций.

## <a name="create-table"></a>Создание таблицы

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a><span class="short-header">Добавление push-уведомлений</span>Добавление push-уведомлений в приложение</h2>

1. Откройте файл проекта MainPage.xaml.cs и добавьте следующий код, который создает новый класс **Регистрации**:

	    public class Registrations
	    {
	        public string Id { get; set; }
	
	        [JsonProperty(PropertyName = "handle")]
	        public string Handle { get; set; }
	    }
	
	The Handle property is used to store the channel URI.

2. Откройте файл App.xaml.cs и добавьте следующий оператор "using":

        using Windows.Networking.PushNotifications;

3. Добавьте в App.xaml.cs следующий код:
	
        private async void AcquirePushChannel()
	    {
	       CurrentChannel = 
               await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
	
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.Uri };
	       await registrationsTable.InsertAsync(registration);
        }

     Этот код вставляет текущий канал в таблицу «Registrations».
    
4. В верхней части обработчика событий **OnLaunched** в файле App.xaml.cs добавьте в новый метод **AcquirePushChannel** следующий вызов:

        AcquirePushChannel();

   Это гарантирует, что свойство **CurrentChannel** инициализируется при каждом запуске приложения.
		
5. Откройте файл Package.appxmanifest и убедитесь, что во вкладке **Пользовательский интерфейс приложения** значение параметра **Всплывающие уведомления** установлено на **Да**.

   	![][15]

   	Это гарантирует, что приложение сможет создавать всплывающие уведомления. 

<h2><a name="update-scripts"></a><span class="short-header">Обновление скрипта вставки</span>Обновление зарегистрированных скриптов вставки на портале управления</h2>

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

4. Последовательно щелкните **TodoItem**, **Скрипт** и **Вставить**. 

   	![][5]

5. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

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
                    	    push.wns.sendToastText04(registration.handle, {
                        	    text1: item.text
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

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2. В приложении введите текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

   	![][13]

   	Обратите внимание, что после завершения вставки приложение получает push-уведомление из WNS.

   	![][14]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике демонстрируются основные функциональные возможности push-уведомлений, предоставляемые мобильными службами. Если вашему приложению требуются более сложные функции, такие как отправка уведомлений между различными платформами, маршрутизация на основе подписки или очень большие объемы данных, рассмотрите возможность использования концентраторов уведомлений Azure для вашей мобильной службы. Дополнительные сведения см. в следующих разделах, посвященных концентраторам уведомлений:

+ [Приступая к работе с концентраторами уведомлений]
  <br/>Сведения об использовании концентраторов уведомлений в приложении магазина Windows.

+ [Что такое концентраторы уведомлений?]
	<br/>Сведения о создании уведомлений и их доставке пользователям на нескольких платформах.

+ [Рассылка уведомлений подписчикам]
	<br/>Сведения о том, как пользователи могут зарегистрироваться и получать push-уведомления для категорий, в которых они заинтересованы.

+ [Рассылка уведомлений пользователям]
	<br/>Дополнительные сведения о том, как отправлять push-уведомления из мобильной службы конкретным пользователям на любом устройстве.

+ [Отправка пользователям уведомлений между различными платформами]
	<br/>Дополнительные сведения об использовании шаблонов для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.

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
[Регистрация приложения для push-уведомлений и конфигурация мобильных служб]: #register
[Создание таблицы регистраций]: #create-table
[Обновление скриптов для отправки push-уведомлений]: #update-scripts
[Добавление push-уведомлений в приложение]: #add-push
[Вставка данных для получения уведомлений]: #test
[Дальнейшие действия]:#next-steps

<!-- Images. -->





[5]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-insert-script-push2.png







[13]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push2.png
[15]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png



<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: ../tutorials/mobile-services-get-started-with-push-dotnet.md
[Push-уведомлений для пользователей приложений]: ../tutorials/mobile-services-push-notifications-to-app-users-dotnet.md
[Авторизация пользователей с помощью скриптов]: ../tutorials/mobile-services-authorize-users-dotnet.md
[JavaScript и HTML]: ../tutorials/mobile-services-get-started-with-push-js.md

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library/
[Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/
[Что такое концентраторы уведомлений?]: /ru-ru/develop/net/how-to-guides/service-bus-notification-hubs/
[Рассылка уведомлений подписчикам]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet/
[Рассылка уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users/
[Отправка пользователям уведомлений между различными платформами]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/

