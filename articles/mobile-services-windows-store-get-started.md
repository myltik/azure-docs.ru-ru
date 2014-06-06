<properties pageTitle="Приступая к работе с мобильными службами для приложений Магазина Windows | Центр разработчиков для мобильных устройств" metaKeywords="" description="Изучите этот учебник, чтобы начать использовать мобильные службы Azure при разработке приложений для Магазина Windows с использованием C# или JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с мобильными службами" authors="glenga" solutions="" manager="" editor="" />


# <a name="getting-started"> </a>Приступая к работе с мобильными службами

<div class="dev-center-tutorial-selector sublanding">
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started" title="Магазин Windows" class="current">Магазин Windows</a>
	<a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/ru-ru/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/ru-ru/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/ru-ru/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/ru-ru/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title="Сервер .NET">Сервер .NET</a> | 
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started/"  title="Сервер JavaScript" class="current">Сервер JavaScript</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить облачную серверную службу в приложение Магазина Windows с помощью мобильных служб Azure.</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике. В этом видео Скотта Гатри (Scott Guthrie) вы найдете вводные сведения о мобильных службах, а также руководство по созданию первой мобильной службы и подключению к ней приложения магазина Windows.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">10:08</span></div>
</div>

В этом учебнике вы создадите новую мобильную службу и простое приложение _To do list_, хранящее данные приложения в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера. Сведения о создании мобильной службы, которая позволит создавать в Visual Studio бизнес-логику на серверной стороне на языках, поддерживаемых на платформе .NET, см. в разделе о серверной версии .NET.

Снимок экрана завершенного приложения приведен ниже:

![][0]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными использованию мобильных служб для магазина Windows. 

>[WACOM.NOTE]Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

>Для работы с этим учебником требуется Visual Studio 2013. Для подключения к приложению Магазина Windows с помощью Visual Studio 2012 выполните действия, описанные в разделе [Приступая к работе с данными в мобильных службах с использованием Visual Studio 2012](/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/).

## Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Создание нового приложения для магазина Windows

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение для магазина Windows, подключаемое к вашей мобильной службе.

1. На портале управления щелкните **Мобильные службы**, а затем выберите только что созданную мобильную службу.

   
2. На вкладке быстрого запуска щелкните **Windows** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения Магазина Windows**.

   	![][6]

   	Здесь показаны три простых шага по созданию приложения для магазина Windows, подключаемого к вашей мобильной службе.

  	![][7]

3. Если это еще не сделано, загрузите и установите [Visual Studio 2013 Express для Windows] на локальном компьютере или виртуальной машине.

4. Щелкните **Создание таблицы TodoItem**, чтобы создать таблицу для хранения данных приложения.

5. В разделе **Загрузка и запуск приложения** выберите язык для своего приложения, а затем щелкните **Загрузить**. 

  	При этом будут загружены файлы проекта для примера по созданию приложения _To do list_, подключаемого к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запустите приложение Windows

Последний раздел учебника — построение и выполнение нового приложения.

1. Перейдите в расположение, где были сохранены сжатые файлы проекта, извлеките файлы на своем компьютере и откройте файл решения в Visual Studio 2013 Express для Windows.

2. Нажмите клавишу **F5** для повторной сборки проекта, после чего запустите приложение.

3. В приложении введите содержательный текст, например _Работа с учебником_, в поле **Вставить в TodoItem**, затем щелкните **Сохранить**.

   	![][10]

   	Запрос POST отправляется в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются во втором столбце в приложении.

	>[WACOM.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле MainPage.xaml.cs (проект C#/XAML) или default.js (проект JavaScript/HTML).

4. Вернитесь на портал управления, перейдите на вкладку **Данные** и щелкните таблицу **TodoItems**.

   	![][11]

   	Это позволяет просматривать данные, добавленные в таблицу приложением.

   	![][12]

## Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* **Приступая к работе с данными** ([C#][Приступая к работе с данными] или [JavaScript][Приступая к работе с данными JS])
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* **Приступая к работе с проверкой подлинности** ([C#][Приступая к работе с проверкой подлинности] или [JavaScript][Приступая к работе с проверкой подлинности JS])
  <br/>Дополнительные сведения о проверке подлинности пользователей приложения с использованием поставщика удостоверений.

* **Приступая к работе с push-уведомлениями** ([C#][Приступая к работе с push-уведомлениями] или [JavaScript][Приступая к работе с push-уведомлениями JS])
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-windows-store-get-started/mobile-vs2013-project.png

[10]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png


<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet
[Приступая к работе с данными JS]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js
[Приступая к работе с проверкой подлинности JS]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js
[Приступая к работе с push-уведомлениями JS]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[Visual Studio 2013 Express для Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript и HTML]: mobile-services-win8-javascript/
[Портал управления]: https://manage.windowsazure.com/
[Серверная версия .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012

