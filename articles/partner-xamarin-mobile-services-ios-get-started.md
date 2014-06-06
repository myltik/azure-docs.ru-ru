<properties pageTitle="Приступая к работе с мобильными службами для приложений Xamarin iOS" metaKeywords="" description="Изучите этот учебник, чтобы начать использовать мобильные службы Azure для разработки на базе Xamarin iOS." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Приступая к работе с мобильными службами" authors="craigd" solutions="" writer="glenga" manager="" editor="" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

<div class="dev-center-tutorial-selector sublanding">
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started" title="Магазин Windows">Магазин Windows</a>
	<a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/ru-ru/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/ru-ru/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/ru-ru/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/ru-ru/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<!--<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/" title="Серверная часть .NET">Серверная часть .NET</a> | 
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a>
</div>-->

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить серверную службу на основе облака в приложение Xamarin.iOS с помощью мобильных служб Azure. В этом учебнике будет создана новая мобильная служба и простое приложение <em>Список заданий</em>, которое хранит данные приложения в новой мобильной службе.</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-xamarin-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">10:05</span></div>
</div>

Снимок экрана завершенного приложения приведен ниже:

![][0]

Для изучения этого учебника необходимы XCode 4.5 и iOS 5.0 или более поздние их версии, а также [Xamarin Studio] для OS X или подключаемый модуль Xamarin Visual Studio для Visual Studio в Windows.

<div class="dev-callout"><strong>Примечание</strong>. <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2><span class="short-header">Создание нового приложения</span>Создание нового приложения Xamarin.iOS</h2>

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение Xamarin.iOS, которое будет подключено к вашей мобильной службе.

1.  На портале управления щелкните **Мобильные службы**, затем выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска щелкните элемент **Xamarin.iOS** в разделе **Выбор платформы**, а затем разверните пункт **Создать новое приложение Xamarin.iOS**.

	![][6]

	Здесь показаны три простых шага для создания приложения Xamarin.iOS, подключенного к вашей мобильной службе.

  	![][7]

3. Если вы еще не сделали этого, загрузите и установите [Xcode] версии 4.4 или более поздней, а также [Xamarin Studio].

4. Чтобы создать таблицу для хранения данных приложения, щелкните элемент **Создание таблицы TodoItems**.

5. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**. 

	При этом загружаются файлы проекта для примера по созданию приложения _Список задач_, подключенного к вашей мобильной службе, и указывается ссылка на компонент мобильных служб Azure для Xamarin.iOS. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

<h2><span class="short-header">Запуск приложения</span>Запуск нового приложения Xamarin.iOS</h2>

Последний раздел учебника — построение и выполнение нового приложения.

1. Перейдите в расположение, где были сохранены сжатые файлы проекта, извлеките файлы на компьютере и откройте файл решения **XamarinTodoQuickStart.iOS.sln** в Xamarin Studio или Visual Studio.

	![][8]

	![][9]

2. Нажмите клавишу кнопку **Запуск**, чтобы построить проект и запустить приложение в эмуляторе iPhone, используемом по умолчанию для данного проекта.

3. В приложении введите содержательный текст, например _Работа с учебником_, и щелкните значок "плюс" (**+**).

	![][10]

	При этом в новую мобильную службу, размещенную в Azure, будет отправлен запрос POST. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	<div class="dev-callout"> 
	<b>Примечание</b> 
   	<p>Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле C# TodoService.cs.</p> 
 	</div>

4. Вернитесь на портал управления и откройте вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

	![][11]

	Это позволяет просматривать данные, добавленные в таблицу приложением.

	![][12]

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
[Создать мобильную службу]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Xamarin Studio]: http://xamarin.com/download
[SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Портал управления]: https://manage.windowsazure.com/

