<properties linkid="develop-mobile-tutorials-get-started" pageTitle="Приступая к работе с мобильными службами для приложений Магазина Windows | Центр разработчиков для мобильных устройств" metaKeywords="" description="Изучите этот учебник, чтобы начать использовать мобильные службы Azure при разработке приложений для Магазина Windows с использованием C#, VB или JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с мобильными службами" authors="glenga" solutions="" manager="" editor="" />


# <a name="getting-started"> </a>Приступая к работе с мобильными службами

<div class="dev-center-tutorial-selector sublanding">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started" title="Магазин Windows — C#" class="current">Магазин Windows — C#</a>
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started" title="Магазин Windows — JavaScript">Магазин Windows — JavaScript</a>
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started" title="iOS">iOS</a>
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started" title="Android">Android</a>
	<!--<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/ru-ru/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>-->
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title="Серверная версия .NET" class="current">Серверная версия .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started/"  title="Серверная версия JavaScript" >Серверная версия JavaScript</a>
</div>

В этом учебнике показано, как добавить облачную серверную службу в приложение Магазина Windows с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение _To do list_, хранящее данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript] этого раздела.

>[WACOM.NOTE]В этом разделе показано, как создать новый проект мобильной службы и приложение Магазина Windows с помощью портала управления Azure. Используя Visual Studio 2013 с обновлением 2, можно также добавить новый проект мобильной службы в существующее решение Visual Studio. Дополнительные сведения см. в разделе [Быстрый запуск: добавление мобильной службы (серверная версия .NET)](http://msdn.microsoft.com/ru-ru/library/windows/apps/dn629482.aspx)

Снимок экрана завершенного приложения приведен ниже:

![][0]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными использованию мобильных служб для магазина Windows. 

>[WACOM.NOTE]Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-get-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.<br />Для работы с этим учебником требуется <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Доступна бесплатная пробная версия.

## Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Создание нового приложения для магазина Windows

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение для магазина Windows, подключаемое к вашей мобильной службе.

1. На портале управления щелкните **Мобильные службы**, а затем выберите только что созданную мобильную службу.
   
2. На вкладке быстрого запуска щелкните **Windows** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения Магазина Windows**.

   	![][6]

   	Здесь показаны три простых шага по созданию приложения для магазина Windows, подключаемого к вашей мобильной службе.

  	![][7]

3. Если вы еще не сделали этого, загрузите и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальном компьютере или виртуальной машине.

4. В разделе **Загрузите и локально запустите ваше приложение и службу** выберите язык для приложения Магазина Windows, затем щелкните **Загрузить**. 

  	При этом загружается решение, содержащее проекты для мобильной службы и примера приложения _To do list_, которое подключается к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

5. В разделе **Публикация службы в облаке** загрузите профиль публикации, сохраните загруженный файл на локальный компьютер и запомните путь к нему.

## Тестирование приложения с помощью локальной мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[WACOM.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле MainPage.xaml.cs.


## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


<ol start="4">
<li><p>В проекте приложения Магазина Windows откройте файл App.xaml.cs, найдите код, создающий экземпляр <a href="http://msdn.microsoft.com/ru-ru/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>, закомментируйте код, который создает этот клиент с использованием <em>localhost</em>, и раскомментируйте код, который создает клиент с использованием URL-адреса удаленной мобильной службы. Он выглядит следующим образом:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXX-APPLICATION-KEY-XXXXX");</code></pre>

	<p>Теперь клиент будет обращаться к мобильной службе, опубликованной в Azure.</p></li>

<li><p>Нажмите клавишу <strong>F5</strong> для повторной сборки проекта, затем запустите приложение.</p></li>

<li><p>В приложении введите содержательный текст, например <em>Работа с учебником</em>, в поле <strong>Вставить в TodoItem</strong>, затем щелкните <strong>Сохранить</strong>.</p>

<p>Запрос POST отправляется в новую мобильную службу, размещенную в Azure.</p>
</li>
</ol>

<!--This should be in the list HTMLK, but there is a bug-->
![][10]

## Дальнейшие действия
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
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-startup.png



<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript и HTML]: mobile-services-win8-javascript/
[Портал управления]: https://manage.windowsazure.com/
[Серверная версия JavaScript]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started
[Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012

