<properties pageTitle="Приступая к работе с мобильными службами в PhoneGap | Центр разработчиков для мобильных устройств" metaKeywords="" description="Следуйте инструкциям в этом учебнике, чтобы начать применять мобильные службы Azure для разработки с использованием PhoneGap для iOS, Android и Windows Phone." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Приступая к работе с мобильными службами" authors="glenga" solutions="" manager="" editor="" />

<div class="dev-center-tutorial-selector sublanding">
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started" title="Магазин Windows">Магазин Windows</a>
	<a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/ru-ru/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/ru-ru/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/ru-ru/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/ru-ru/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap" class="current">PhoneGap</a>
</div>

<!--<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-phonegap-get-started/" title="Серверная версия .NET">Серверная версия .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/"  title="Серверная версия JavaScript" class="current">Серверная версия JavaScript</a>
</div>-->

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

В этом учебнике показано, как добавить облачную серверную службу в приложение с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение _To do list_, хранящее данные приложения в новой мобильной службе. 

Снимок экрана завершенного приложения приведен ниже:

![][3]

### <a name="additional-requirements"></a>Дополнительные требования

Для работы с этим учебником необходимы средства PhoneGap (для проектов Windows Phone 8 требуется версия 3.2+). 

PhoneGap поддерживает разработку для нескольких платформ. Помимо самих средств PhoneGap необходимо установить средства для каждой используемой платформы.

- Windows Phone: установите [Visual Studio 2012 Express для Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374)
- iOS: установите [Xcode] (требуется версия 4.4+)
- Android: установите [средства разработчика Android][Android SDK]
	<br/>(Пакет Mobile Services SDK для Android поддерживает приложения для Android 2.2 или более поздней версии. ОС Android 4.2 или более поздняя версия требуется для запуска приложения быстрого запуска.)

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2><span class="short-header">Создание нового приложения</span>Создание нового приложения PhoneGap</h2>

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение PhoneGap, подключенное к вашей мобильной службе.

1. На портале управления щелкните **Мобильные службы**, а затем выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска щелкните **PhoneGap** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения PhoneGap**.

   	![][0]

   	Здесь показаны три простых шага для создания приложения PhoneGap, подключенного к вашей мобильной службе.

  	![][1]

3. Если вы еще не сделали этого, загрузите и установите PhoneGap и средство разработки хотя бы для одной платформы (Windows Phone, iOS или Android).

4. Щелкните **Создание таблицы TodoItems**, чтобы создать таблицу для хранения данных приложения.

5. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**. 

	При этом загружается проект для примера приложения _To do list_, подключенного к вашей мобильной службе, а также пакет Mobile Services SDK для JavaScript. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

## Запуск нового приложения PhoneGap

Последний раздел учебника — построение и выполнение нового приложения. 

1.	Перейдите в расположение, где сохранены сжатые файлы проекта и извлеките файлы на компьютере. 

2.	Откройте и запустите проект, следуя приведенным ниже инструкциям для каждой платформы.

	+ **Windows Phone 8**

	1. Windows Phone 8: откройте SLN-файл в папке **platforms\wp8** в Visual Studio 2012 Express для Windows Phone.
	
	2. Нажмите клавишу **F5** для повторной сборки проекта, после чего запустите приложение.
	
	  	![][2]

	+ **iOS**

	1. Откройте проект в папке **platforms/ios** в Xcode.
	
	2. Нажмите кнопку **Запуск**, чтобы создать проект и запустить приложение в эмуляторе iPhone, используемом по умолчанию для этого проекта.
	
	  	![][3]

	+ **Android**

		1. В Eclipse щелкните **Файл**, затем щелкните **Импорт**, разверните элемент **Android**, щелкните **Существующий код Android в рабочую область**, а затем нажмите кнопку **Далее**. 
		
		2. Щелкните **Обзор**, перейдите в расположение развернутых файлов проекта, нажмите кнопку **ОК**, убедитесь, что отмечен проект TodoActivity, а затем щелкните **Готово**. <p>При этом файлы проекта импортируются в текущую рабочую область.</p>
		
		3. В меню **Запуск** щелкните **Выполнить**, чтобы запустить проект в эмуляторе Android.
		
			![][4]
	
		>[WACOM.NOTE]Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания этих устройств и управления ими используйте диспетчер AVD.
			
	
3. После запуска приложения в одном из указанных выше мобильных эмуляторов введите какой-либо текст в текстовое поле, а затем щелкните **Добавить**.

	Запрос POST отправляется в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу **TodoItem**. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	<div class="dev-callout"><strong>Важно!</strong> <p>Изменения в этом проекте платформы будут перезаписаны при повторной сборке основного проекта с использованием средств PhoneGap. Вместо этого внесите изменения в корневом каталоге www проекта, как описано в приведенном ниже разделе.</p></div>

4. Вернитесь на портал управления, перейдите на вкладку <strong>Данные</strong> и щелкните таблицу <strong>TodoItems</strong>.

	![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)

	Это позволяет просматривать данные, добавленные в таблицу приложением.

	![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)
	

## Обновление приложения и повторное построение проектов для каждой платформы

1. Внесите изменения в файлы кода в каталоге "www", которым в данном случае является каталог "todolist/www".

2. Убедитесь, что все средства целевой платформы доступны в системном пути. 

2. Откройте окно командной строки в корневом каталоге проекта и выполните одну из следующих команд для конкретной платформы: 

	+ **Windows Phone**

		Выполните следующую команду из командной строки для разработчика Visual Studio:

    		phonegap local build wp8

	+ **iOS**
 
		Откройте терминал и выполните следующую команду:

    		phonegap local build ios

	+ **Android**

		Откройте окно командной строки или терминала и выполните следующую команду: 

		    phonegap local build android


4. Откройте каждый проект в соответствующей среде разработки, как описано в предыдущем разделе.

>[WACOM.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле js/index.js.

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.
  
<!-- Images. -->
[0]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
[1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
[2]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
[3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
[4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png

<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-html-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-html-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/mobile-services-html-get-started-push
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Портал управления]: https://manage.windowsazure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio 2012 Express для Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374

