<properties pageTitle="Приступая к работе с мобильными службами для Xamarin.Android" metaKeywords="Приложение Azure Xamarin.Android, мобильная служба XamarinAndroid, приступая к работе с Azure Xamarin.Android" writer="craigd" description="Узнайте, как использовать мобильные службы Azure со своим приложением Xamarin.Android." metaCanonical="" 
	documentationCenter="Mobile" title="Приступая к работе с мобильными службами" />

# <a name="getting-started"></a>Приступая к работе с мобильными службами

<div class="dev-center-tutorial-selector sublanding">
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started" title="Магазин Windows">Магазин Windows</a>
	<a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/ru-ru/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/ru-ru/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/ru-ru/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android" class="current">Xamarin.Android</a>
	<a href="/ru-ru/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<!--<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started/" title="Серверная часть .NET">Серверная часть .NET</a> | 
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a>
</div>-->

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить серверную службу на основе облака в приложение Xamarin.Android с помощью мобильных служб Azure. В этом учебнике будет создана новая мобильная служба и простое приложение <em>Список заданий</em>, которое хранит данные приложения в новой мобильной службе.</p>
<p>Снимок экрана завершенного приложения приведен ниже:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-xamarin-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">10:05</span></div>
</div>

![][0]

Для прохождения данного учебника требуется компонент [Xamarin.Android], который устанавливает Xamarin Studio и подключаемый модуль Visual Studio (в Windows), а также самую новую версию платформы Android. Требуется пакет SDK Android 4.2 или более поздней версии. 

Загруженный проект быстрого запуска содержит компонент мобильных служб Azure для Xamarin.Android. Хотя этот проект предназначен для Android 4.2 или более поздней версии, для пакета SDK мобильных служб требуется только Android 2.2 или более поздней версии.

<div class="dev-callout"><strong>Примечание</strong>. <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2><span class="short-header">Создание нового приложения</span>Создание нового приложения Xamarin.Android</h2>

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение Xamarin.Android, которое будет подключено к вашей мобильной службе.

1.  На портале управления щелкните **Мобильные службы**, затем выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска щелкните элемент **Xamarin.Android** в разделе **Выбор платформы**, а затем разверните пункт **Создание нового приложения Android**.

	![][6]

	Здесь показаны три простых шага для создания приложения Xamarin.Android, подключенного к вашей мобильной службе.

	![][7]

3. Чтобы создать таблицу для хранения данных приложения, щелкните элемент **Создание таблицы TodoItem**.

4. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**. 

	При этом будут загружены файлы проекта для примера по созданию приложения _Список задач_, подключаемого к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запустите приложение Android

Последний раздел учебника — построение и выполнение нового приложения.

1. Перейдите в расположение, где сохранены сжатые файлы проекта, и извлеките файлы на компьютере.

2. В Xamarin Studio или Visual Studio щелкните **Файл**, затем **Открыть**, перейдите к несжатым образцам файлов и выберите **XamarinTodoQuickStart.Android.sln** для его открытия.

 	![][8]

	![][9]

3. Нажмите клавишу **Запуск** для построения проекта, после чего запустите приложение. Вам будет предложено выбрать эмулятор или подключенное USB-устройство. 

	<div class="dev-callout"><strong>Примечание</strong>. <p>Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство на платформе Android (AVD). Для создания и управления этими устройствами используйте диспетчер AVD.</p></div>

4. Введите содержательный текст в приложении, например _Завершить работу с учебником_, и нажмите кнопку **Добавить**.

	![][10]

	При этом в новую мобильную службу, размещенную в Azure, будет отправлен запрос POST. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	> [WACOM.NOTE] 
   	> Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле C# ToDoActivity.cs.

6. Вернитесь на портал управления и откройте вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

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
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android
[Xamarin.Android]: http://xamarin.com/download
[Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[WindowsAzure.com]: http://www.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/

