<properties pageTitle="Get Started with Mobile Services for Xamarin iOS apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="craigd" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="craigd"></tags>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

В этом учебнике показано, как с помощью мобильных служб Azure добавить облачную серверную службу в приложение Xamarin.iOS. В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе.

Кроме того, можно посмотреть видеоклип (см. ниже), где приведены те же действия, что и в учебнике.

Видео: «Приступая к работе с мобильными службами Xamarin и Azure» с участием Крейга Данна (Craig Dunn), ИТ-пропагандиста продукции Xamarin (длительность: 10:05 мин)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Снимок экрана завершенного приложения приведен ниже:

![][]

Для изучения этого учебника необходимы XCode 4.5 и iOS 5.0 или более поздние их версии, а также [Xamarin Studio][Xamarin Studio] для OS X или подключаемый модуль Xamarin Visual Studio для Visual Studio в Windows.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a> Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service][mobile-services-create-new-service]]

## <span class="short-header">Создание нового приложения</span>Создание нового приложения Xamarin.iOS

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение Xamarin.iOS, которое будет подключено к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого запуска нажмите кнопку **Xamarin.iOS** в меню **Выбор платформы**, а затем разверните элемент **Создание нового приложения Xamarin.iOS**.

    ![][1]

    После этого будут показаны три простых шага для создания приложения Xamarin.iOS, подключенного к вашей мобильной службе.

    ![][2]

3.  Если вы еще не сделали этого, скачайте и установите [Xcode] версии 4.4 или более поздней, а также [Xamarin Studio][Xamarin Studio].

4.  Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItems**.

5.  В разделе **Загрузка и запуск приложения** щелкните **Загрузить**.

    При этом будет скачан проект с примером приложения *To do list*, которое подключается к вашей мобильной службе и содержит ссылки на компонент мобильных служб Azure для Xamarin.iOS. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

## <span class="short-header">Запуск приложения</span>Запуск нового приложения Xamarin.iOS

Последний раздел учебника — построение и выполнение нового приложения.

1.  Перейдите к месту хранения сжатых файлов проекта, извлеките файлы на компьютер и откройте файл решения **XamarinTodoQuickStart.iOS.sln** в Xamarin Studio или Visual Studio.

    ![][3]

    ![][4]

2.  Нажмите кнопку **Запуск**, чтобы создать проект и запустить приложение в эмуляторе iPhone, используемом по умолчанию для этого проекта.

3.  В приложении введите осмысленный текст, например *Работа с учебником*, и щелкните значок «плюс» (**+**).

    ![][5]

    Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

    <div class="dev-callout"> 
<b>Примечание.</b> 
<p>Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле C# TodoService.cs.</p> 
</div>

4.  На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

    ![][6]

    Это позволяет просматривать данные, добавленные в таблицу приложением.

    ![][7]

## <a name="next-steps"> </a> Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   [Приступая к работе с данными][Приступая к работе с данными]
    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с синхронизацией автономных данных][Приступая к работе с синхронизацией автономных данных]
    Узнайте, как использовать синхронизацию автономных данных, чтобы приложения были более быстрыми и надежными.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
    Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    Сведения об отправке в приложение простейших push-уведомлений.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  []: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
  [Xamarin Studio]: http://xamarin.com/download
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
  [2]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
  [3]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
  [4]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
  [5]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
  [6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
  [7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Приступая к работе с синхронизацией автономных данных]: /ru-ru/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios
