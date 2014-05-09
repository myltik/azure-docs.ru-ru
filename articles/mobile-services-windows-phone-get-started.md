<properties pageTitle="Приступая к работе с мобильными службами Windows Azure для приложений Windows Phone" metaKeywords="" description="Чтобы начать работу с мобильными службами Windows Azure для разработки на платформе Windows Phone, следуйте инструкциям этого учебника. " metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с мобильными службами" authors=""  solutions="" writer="glenga" manager="" editor=""  />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started" title="Магазин Windows">Магазин Windows</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-get-started" title="Android">Android</a><a href="/ru-ru/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a><a href="/ru-ru/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/" title="Сервер .NET">Сервер .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started/"  title="Сервер JavaScript" class="current">Сервер JavaScript</a></div>


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить серверную службу на основе облака в приложение, работающее на платформе Windows Phone 8, с помощью мобильных служб Windows Azure. В этом учебнике будет создана новая мобильная служба и простое приложение <em>Список заданий</em>, которое хранит данные приложения в новой мобильной службе.</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике. В этом видео Ника Харриса (Nick Harris) вы найдете вводные сведения о мобильных службах, а также руководство по созданию первой мобильной службы и подключению к ней приложения Windows Store.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">13:18</span></div>
</div>

В этом учебнике будет создана новая мобильная служба и простое приложение _Список заданий_, которое хранит данные приложения в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера. Сведения о создании мобильной службы, которая позволит создавать в Visual Studio бизнес-логику на серверной стороне на языках, поддерживаемых на платформе .NET, см. в подразделе [Серверная версия .NET] этого раздела.

Снимок экрана завершенного приложения приведен ниже:

![][0]

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником потребуется учетная запись Windows Azure с включенной возможностью мобильных служб Windows Azure.</p> <ul> <li>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A30A4DDE2&amp;returnurl=http%3A%2F%2FFen-us%2Fdocumentation%2Farticles%2Fmobile-services-windows-phone-get-started%2F" target="_blank">Бесплатная пробная версия Windows Azure</a>.</li></ul> </div>

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2><span class="short-header">Создание нового приложения</span>Создание нового приложения Windows Phone</h2>

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение Windows Phone 8, которое будет подключаться к вашей мобильной службе.

1.  На портале управления щелкните **Мобильные службы**, затем выберите только что созданную мобильную службу.

2. На вкладке быстрого начала работы щелкните **Windows Phone 8** в разделе **Выбор платформы**, а затем разверните узел **Создание нового приложения Windows Phone 8**.

   	![][6]

   	Здесь показаны три простых шага для создания приложения Windows Phone, подключаемого к вашей мобильной службе.

  	![][7]

3. Если это еще не выполнено, загрузите и установите на локальном компьютере [Visual Studio 2012 Express для Windows Phone].

4. Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.

5. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**. 

  	При этом будут загружены файлы проекта для примера по созданию приложения _Список задач_, подключаемого к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запустите приложение Windows Phone

Последний раздел учебника — построение и выполнение нового приложения.

1. Перейдите в расположение, где были сохранены сжатые файлы проекта, извлеките файлы на компьютере и откройте файл решения в Visual Studio.

2. Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

3. В приложении введите содержательный текст, например _Работа с учебником_, и нажмите кнопку **Сохранить**.

   	![][10]

   	При этом в новую мобильную службу, размещенную в Windows Azure, будет отправлен запрос POST. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	>[WACOM.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле MainPage.xaml.cs.

4. На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

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
[Следующие шаги]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png





[6]: ./media/mobile-services-windows-phone-get-started/mobile-portal-quickstart-wp8.png
[7]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png
[8]: ./media/mobile-services-windows-phone-get-started/mobile-vs-project-wp8.png

[10]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-startup-wp8.png
[11]: ./media/mobile-services-windows-phone-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-windows-phone-get-started/mobile-data-browse.png


<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8
[Visual Studio 2012 Express для Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Пакет SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Портал управления]: https://manage.windowsazure.com/
[Серверная версия .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started

