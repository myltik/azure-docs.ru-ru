<properties pageTitle="Приступая к работе с мобильными службами Windows Azure для приложений iOS" metaKeywords="Приложения iOS для Windows Azure, мобильная служба iOS, начало работы Azure iOS" description="Следуйте инструкциям этого учебника, чтобы приступить к использованию мобильных служб Windows Azure для разработки iOS. " metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с мобильными службами" authors=""  solutions="" writer="glenga" manager="" editor=""  />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами
<div class="dev-center-tutorial-selector sublanding">
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started" title="Магазин Windows">Магазин Windows</a>
	<a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/ru-ru/documentation/articles/mobile-services-ios-get-started" title="iOS" class="current">iOS</a>
	<a href="/ru-ru/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/ru-ru/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/ru-ru/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started/" title="Сервер .NET">Сервер .NET</a> | 
	<a href="/ru-ru/documentation/articles/mobile-services-ios-get-started/"  title="Сервер JavaScript" class="current">Сервер JavaScript</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить серверную службу на основе облака в приложение, работающее на платформе iOS, с помощью мобильных служб Windows Azure.</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">9:38</span></div>
</div>

В этом учебнике будет создана новая мобильная служба и простое приложение _Список заданий_, которое хранит данные приложения в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера. Сведения о создании мобильной службы, которая позволит создавать в Visual Studio бизнес-логику на серверной стороне на языках, поддерживаемых на платформе .NET, см. в подразделе [Серверная версия .NET] этого раздела.

Снимок экрана завершенного приложения приведен ниже:

![][0]

Для изучения этого учебника необходимы XCode версии 4.5 и iOS версии 5.0 или более поздних версий. 

<div class="dev-callout"><strong>Примечание</strong>. <p>Для работы с этим учебником необходима учетная запись Windows Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Бесплатная пробная версия Windows Azure</a>.</p></div>

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2><span class="short-header">Создание нового приложения</span>Создание нового приложения на платформе iOS</h2>

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение на платформе iOS, которое будет подключено к вашей мобильной службе.

1.  На портале управления щелкните **Мобильные службы**, затем выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска нажмите кнопку **iOS** в разделе **Выбор платформы**, а затем разверните пункт **Создать новое приложение на Android**.

   	![][6]

   	Здесь показаны три простых шага для создания приложения на платформе iOS, подключенного к вашей мобильной службе.

  	![][7]

3. Если вы еще не сделали этого, загрузите и установите [Xcode] v4.4 или более поздней версии.

4. Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItems**.

5. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**. 

  	При этом загрузятся файлы проекта для примера по созданию приложения _Список задач_, подключенного к вашей мобильной службе, а также SDK мобильных служб для iOS. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запустите новое приложение на iOS

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

<ol start="4">
<li><p>На портале управления перейдите на вкладку <strong>Данные</strong>, а затем щелкните таблицу <strong>TodoItems</strong>.<p>

<p><img src="./media/mobile-services-ios-get-started/mobile-data-tab.png" alt=""></p>

   	<p>Это позволяет просматривать данные, добавленные в таблицу приложением.</p>

   	<p><img src="./media/mobile-services-ios-get-started/mobile-data-browse.png" alt=""></p></li>
</ol>


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
[Следующие шаги]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png

[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios

[SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Портал управления]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Серверная версия .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started

