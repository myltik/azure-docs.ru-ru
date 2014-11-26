<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

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
    <a href="/ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title="Сервер .NET">Сервер .NET</a> | 
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started/"  title="Сервер JavaScript" class="current">Сервер JavaScript</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить облачную серверную службу в приложение Магазина Windows с помощью мобильных служб Azure.</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике. В этом видео Скотта Гатри (Scott Guthrie) приведена вводная информация о мобильных службах, а также указания по созданию первой мобильной службы и подключению к ней из приложения для Магазина Windows.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">10:08:00</span></div>

</div>

В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера. Сведения о создании мобильной службы, которая позволит создавать в Visual Studio бизнес-логику на серверной стороне на языках, поддерживаемых на платформе .NET, см. в разделе о серверной версии .NET.

Снимок экрана завершенного приложения приведен ниже:

![][0]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными использованию мобильных служб для магазина Windows.

> [WACOM.NOTE] Чтобы выполнить работу с этим учебником, необходимо использовать учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].

> Для работы с данным учебником требуется Visual Studio 2013. Для подключения к приложению для Магазина Windows с помощью Visual Studio 2012 выполните действия, описанные в разделе [Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012][Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012].

## Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Создание нового приложения для Магазина Windows

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение для магазина Windows, подключаемое к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого начала работы щелкните **Windows** в разделе **Выбор платформы**, а затем разверните узел **Создание нового приложения для магазина Windows**.

    ![][1]

    Здесь показаны три простых шага по созданию приложения для магазина Windows, подключаемого к вашей мобильной службе.

    ![][2]

3.  Если это еще не выполнено, загрузите и установите на локальном компьютере или виртуальной машине [Visual Studio 2013 Express для Windows][Visual Studio 2013 Express для Windows].

4.  Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.

5.  В разделе **Загрузить и запустить приложение** выберите язык для своего приложения, а затем нажмите кнопку **Загрузить**.

    При этом будут загружены файлы проекта для примера по созданию приложения *To do list*, подключаемого к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запустите приложение Windows

Последний раздел учебника — построение и выполнение нового приложения.

1.  Перейдите в расположение, где были сохранены сжатые файлы проекта, извлеките файлы на компьютере и откройте файл решения в Visual Studio 2013 Express для Windows.

2.  Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

3.  В приложении введите содержательный текст, например *Работа с учебником*, в поле **Вставить в TodoItem**, затем щелкните **Сохранить**.

    ![][3]

    Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются во втором столбце в приложении.

    > [WACOM.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле MainPage.xaml.cs (проект C#/XAML) или файле default.js (проект JavaScript/HTML).

4.  На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

    ![][4]

    Это позволяет просматривать данные, добавленные в таблицу приложением.

    ![][5]

## Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   **Приступая к работе с данными** ( [C#][C#] / [JavaScript][JavaScript] )
    Узнайте больше о хранении и запросе данных с помощью мобильных служб.

-   **Приступая к работе с аутентификацией** ( [C#][6] / [JavaScript][7] )
    Узнайте больше об аутентификации пользователей приложения с помощью поставщика удостоверений.

-   **Приступая к работе с push-уведомлениями** ( [C#][8] / [JavaScript][9] )
    Узнайте больше об отправке в приложение простых push-уведомлений.

 
 


  [0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/
  [1]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
  [2]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
  [Visual Studio 2013 Express для Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [3]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
  [4]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png
  [C#]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
  [JavaScript]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js
  [6]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
  [7]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js
  [8]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet
  [9]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
