<properties pageTitle="Приступая к работе с мобильными службами Azure для приложений HTML 5" metaKeywords="" description="Чтобы начать работу с мобильными службами Azure для разработки на основе HTML, следуйте инструкциям этого учебника. " metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с мобильными службами" authors="" solutions="" writer="glenga" manager="" editor=""  />


# <a name="getting-started"> </a>Приступая к работе с мобильными службами

<div class="dev-center-tutorial-selector sublanding">
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started" title="Магазин Windows">Магазин Windows</a>
	<a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/ru-ru/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/ru-ru/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/ru-ru/documentation/articles/mobile-services-html-get-started" title="HTML" class="current">HTML</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/ru-ru/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<!--<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-html-get-started/" title="Серверная часть .NET">Серверная часть .NET</a> | 
	<a href="/ru-ru/documentation/articles/mobile-services-html-get-started/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a>
</div>-->

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить серверную службу на основе облака в приложение HTML с помощью мобильных служб Azure. В этом учебнике будет создана новая мобильная служба и простое приложение <em>Список заданий</em>, которое хранит данные приложения в новой мобильной службе. Можно смотреть видеоверсию данного учебника, щелкнув клип справа.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизвести видео</span></a> <span class="time">3:51</span></div>
</div>
 
Снимок экрана завершенного приложения приведен ниже:

![][0]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными приложениям HTML. 

<div class="dev-callout"><strong>Примечание</strong>. <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

###Дополнительные требования

+ В этом учебнике требуется наличие одного из следующих веб-серверов, запущенных на локальном компьютере:

	+  **В Windows**: IIS Express. IIS Express устанавливается [программой установки веб-платформы Майкрософт].   
	+  **На MacOS X**: Python, который уже должен быть установлен.
	+  **В системе Linux**: Python. Необходимо установить [последнюю версию Python]. 
	
	Можно использовать любой веб-сервер для размещения приложения, однако ниже приведены веб-серверы, которые поддерживаются доступными для загрузки сценариями.  

+ Веб-браузер, который поддерживает HTML5.


## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2><span class="short-header">Создание нового приложения</span>Создание нового приложения HTML</h2>

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение HTML, которое будет подключено к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

   
2. На вкладке быстрого запуска нажмите кнопку **Windows** в разделе **Выбор платформы**, а затем разверните пункт **Создать новое приложение HTML**.

   	![][6]

   	Здесь показаны три простых шага для создания и размещения приложения HTML, подключенного к вашей мобильной службе.

  	![][7]

3. Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItems**.

4. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**. 

  	При этом будут загружены файлы веб-сайта для примера по созданию приложения _Список задач_, подключаемого к вашей мобильной службе. Сохраните сжатый файл на локальном компьютере и запомните путь к нему.

5. На вкладке **Настройка** убедитесь, что в списке **Разрешить запросы от имен узлов** в разделе **Общий доступ к ресурсам независимо от источника (CORS)** уже имеется значение `localhost`. В противном случае введите `localhost` в поле **Имя узла**, затем щелкните пункт **Сохранить**.

  	![][9]

	<div class="dev-callout"><b>Примечание.</b>
		<p>Если развернуть приложение quickstart на веб-сервере, отличном от localhost, необходимо добавить имя узла веб-сервера в список <strong>Разрешить запросы имен узлов</strong>. Дополнительные сведения см. в разделе <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn155871.aspx" target="_blank">Общий доступ к ресурсам независимо от источника</a>.</p>
	</div>

## Размещение и запуск приложения HTML

Последний раздел учебника посвящен размещению и запуску нового приложения на локальном компьютере.

1. Перейдите в место, где сохранены сжатые файлы проекта, разверните файлы на своем компьютере и запустите один из следующих файлов команд в подпапке **server**.

	+ **launch-windows** (Компьютеры Windows) 
	+ **launch-mac.command** (Компьютеры Mac OS X)
	+ **launch-linux.sh** (Компьютеры Linux)

	<div class="dev-callout"><b>Примечание.</b>
		<p>На компьютере Windows нажмите клавишу `R`, когда в PowerShell появится запрос на запуск сценария. Веб-браузер может предупредить о том, что не следует запускать этот сценарий, так как он был загружен из Интернета. В этом случае необходимо указать, что браузеру следует продолжить загрузку сценария.</p>
	</div>

	Это приведет к запуску веб-сервера на локальном компьютере, где и будет размещено новое приложение.

2. Откройте URL-адрес <a href="http://localhost:8000/" target="_blank">http://localhost: 8000/</a> в веб-браузере, чтобы запустить приложение.

3. В разделе **Введите новую задачу** введите содержательный текст в приложении, например _Завершить работу с учебником_ in и нажмите кнопку **Добавить**.

   	![][10]

   	При этом в новую мобильную службу, размещенную в Azure, будет отправлен запрос POST. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются во втором столбце в приложении.

	<div class="dev-callout"> 
	<b>Примечание</b> 
   	<p>Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле app.js.</p> 
 	</div>

4. На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

   	![][11]

   	Это позволяет просматривать данные, добавленные в таблицу приложением.

   	![][12]

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* **[Приступая к работе с данными]**
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* **[Приступая к работе с аутентификацией]**
  <br/>Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

* **[Справочник принципов использования мобильных служб HTML/JavaScript]**
  <br/>Дополнительные сведения об использовании мобильных служб с HTML/JavaScript 

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создать мобильную службу]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png





[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-html
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-html

[Портал управления]: https://manage.windowsazure.com/
[программой установки веб-платформы Майкрософт]:  http://go.microsoft.com/fwlink/p/?LinkId=286333
[последнюю версию Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client
[Общий доступ к ресурсам независимо от источника]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn155871.aspx

