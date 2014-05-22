<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Приступая к работе (WP8)" pageTitle="Приступая к работе с мобильными службами Azure для приложений Windows Phone" metaKeywords="" description="Следуйте инструкциям этого учебника, чтобы начать работу с мобильными службами Azure для разработки на платформе Windows Phone. " metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с мобильными службами" authors="glenga" solutions="" manager="" editor="" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started" title="Магазин Windows — C#">Магазин Windows — C#</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started" title="Магазин Windows — JavaScript">Магазин Windows — JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started" title="iOS">iOS</a>	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started" title="Android">Android</a>
	<!--<a href="/ru-ru/documentation/articles/get-started-android" title="Android">Android</a>
		<a href="/ru-ru/documentation/articles/get-started-html" title="HTML">HTML</a>
		<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
		<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
		<a href="/ru-ru/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
		<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>-->
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/" title="Серверная версия .NET" class="current">Серверная версия .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started/"  title="Серверная версия JavaScript" >Серверная версия JavaScript</a>
</div>

В этом учебнике показано, как добавить облачную серверную службу в приложение Windows Phone 8 с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение _To do list_, хранящее данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript] этого раздела.

Снимок экрана завершенного приложения приведен ниже:

![][0]

>[WACOM.NOTE]Для работы с этим учебником необходима учетная запись Azure с включенным компонентом мобильных служб Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A30A4DDE2&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F" target="_blank">Бесплатная пробная версия Azure</a>. <br />Для работы с этим учебником требуется <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Доступна бесплатная пробная версия.

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Создание нового приложения для Windows Phone

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение Windows Phone 8, которое будет подключаться к вашей мобильной службе.

1. На портале управления щелкните **Мобильные службы**, а затем выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска щелкните **Windows Phone 8** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения Windows Phone 8**.

   	![][6]

   	Здесь показаны три простых шага для создания приложения Windows Phone, подключаемого к вашей мобильной службе.

  	![][7]

3. Если вы еще не сделали этого, загрузите и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальном компьютере или виртуальной машине.

4. В разделе **Загрузка и публикация службы в облаке** щелкните **Загрузить**. 

  	При этом загружается решение, содержащее проекты для мобильной службы и примера приложения _To do list_, которое подключается к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

5. В разделе **Публикация службы в облаке** загрузите профиль публикации, сохраните загруженный файл на локальный компьютер и запомните путь к нему.

## Тестирование мобильной службы на локальном компьютере

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

>[WACOM.NOTE]Для запуска приложения Windows Phone, подключающегося к локальной службе, необходимо выполнить дополнительные действия по настройке. В этом разделе мы не показываем, как это сделать, но вы можете найти дополнительные сведения в разделе [Подключение к локальной веб-службе из эмулятора Windows Phone 8]. 

## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="4">
<li><p>В проекте приложения Windows Phone откройте файл App.xaml.cs, найдите код, создающий экземпляр <a href="http://msdn.microsoft.com/ru-ru/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>, закомментируйте код, который создает этот клиент с использованием <em>localhost</em>, и раскомментируйте код, который создает клиент с использованием URL-адреса удаленной мобильной службы. Он выглядит следующим образом:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXXX-APPLICATION-KEY-XXXXXXXX");</code></pre>

	<p>Теперь клиент будет обращаться к мобильной службе, опубликованной в Azure.</p></li>

<li><p>Нажмите клавишу <strong>F5</strong> для повторной сборки проекта, затем запустите приложение.</p></li>

<li><p>В приложении введите содержательный текст, например <em>Завершение работы с учебником</em>, и щелкните <strong>Сохранить</strong>.</p>

<p>Запрос POST отправляется в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.</p>
	<div class="dev-callout"> 
	<b>Примечание</b> 
   	<p>Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле MainPage.xaml.cs.</p> 
 	</div>
</li>
</ol>

![][10]

Здесь показано, как запустить новое клиентское приложение для мобильной службы, работающей в Azure. Перед тестированием приложения Windows Phone с мобильной службой, работающей на локальном компьютере, необходимо настроить веб-сервер и брандмауэр, чтобы разрешить доступ с устройства или эмулятора Windows Phone. Дополнительные сведения см. в разделе [Настройка локального веб-сервера для подключения к локальной мобильной службе](/ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png

[6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
[7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
[8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-service-startup.pnG

[10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
[11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-data-browse.png


<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
[Visual Studio 2012 Express для Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Пакет SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Портал управления]: https://manage.windowsazure.com/
[Серверная версия JavaScript]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started
[Подключение к локальной веб-службе из эмулятора Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkId=391930

