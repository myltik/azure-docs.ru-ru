<properties pageTitle="Приступая к работе с мобильными службами Azure для приложений iOS" metaKeywords="приложение Azure iOS, мобильная служба iOS, начало работы Azure iOS" description="Следуйте инструкциям этого учебника, чтобы приступить к использованию мобильных служб Azure для разработки приложений в iOS. " metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с мобильными службами" authors="glenga" solutions="" manager="" editor="" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

<div class="dev-center-tutorial-selector sublanding">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started" title="Магазин Windows — C#">Магазин Windows — C#</a>
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started" title="Магазин Windows — JavaScript">Магазин Windows — JavaScript</a>
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started" title="iOS" class="current">iOS</a>
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started" title="Android">Android</a>
	<!--<a href="/ru-ru/documentation/articles/get-started-html" title="HTML">HTML</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/ru-ru/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>-->
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started/" title="Серверная версия .NET" class="current">Серверная версия .NET</a> | 
	<a href="/ru-ru/documentation/articles/mobile-services-ios-get-started/"  title="Серверная версия JavaScript" >Серверная версия JavaScript</a>
</div>

В этом учебнике показано, как добавить облачную серверную службу в приложение iOS с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение _To do list_, хранящее данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript] этого раздела.

Снимок экрана завершенного приложения приведен ниже:

![][0]

Для изучения этого учебника необходимы XCode версии 4.5 и iOS версии 5.0 или более поздних версий. 

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Загрузка мобильной службы на локальный компьютер

Теперь, когда мобильная служба создана, загрузите персональный проект мобильной службы, которая может выполняться на локальном компьютере или виртуальной машине.

1. Щелкните только что созданную мобильную службу, затем на вкладке быстрого запуска выберите **iOS** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения iOS**.

	![][1]

2. Если вы еще не сделали этого, загрузите и установите Visual Studio Professional 2013 или более поздней версии.

3. Щелкните **Загрузить** в разделе **Загрузка и публикация службы в облаке**.

	При этом загружается проект Visual Studio, реализующий мобильную службу. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

4. Кроме того, загрузите профиль публикации, сохраните загруженный файл на локальный компьютер и запомните путь к нему.

## Тестирование мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Создание нового приложения iOS

В этом разделе будет создано новое приложение на платформе iOS, которое будет подключено к вашей мобильной службе.

1. На портале управления щелкните **Мобильные службы**, а затем выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска щелкните **iOS** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения iOS**.   

3. Если вы еще не сделали этого, загрузите и установите [Xcode] 4.4 или более поздней версии.	

4. Щелкните **Создание таблицы TodoItems**, чтобы создать таблицу для хранения данных приложения.

5. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**. 

  	При этом загружается проект для примера приложения _To do list_, которое подключается к вашей мобильной службе, а также пакет Mobile Services SDK для iOS. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

## Запустите новое приложение на iOS

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

Здесь показано, как запустить новое клиентское приложение для мобильной службы, работающей в Azure. Перед тестированием приложения iOS с мобильной службой, работающей на локальном компьютере, необходимо настроить веб-сервер и брандмауэр, чтобы разрешить доступ с компьютера разработки iOS. Дополнительные сведения см. в разделе [Настройка локального веб-сервера для подключения к локальной мобильной службе](/ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).

<!--

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

-->

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Портал управления]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Серверная версия JavaScript]: /ru-ru/documentation/articles/mobile-services-ios-get-started 

