<properties pageTitle="Get Started with Mobile Services for Xamarin.Android" metaKeywords="Azure Xamarin.Android application, mobile service XamarinAndroid, getting started Azure Xamarin.Android" writer="craigd" description="Learn how to use Azure Mobile Services with your Xamarin.Android app." metaCanonical="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="donnam"></tags>

# <a name="getting-started"></a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как с помощью мобильных служб Azure добавить облачную серверную службу в приложение Xamarin.Android. В этом учебнике вы создадите новую мобильную службу и простое приложение <em>To do list</em>, хранящее данные приложения в новой мобильной службе.</p>
<p>Снимок экрана завершенного приложения приведен ниже:</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-xamarin-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">10:05</span></div>

</div>

![][]

Для изучения этого учебника требуется компонент [Xamarin.Android][Xamarin.Android], который устанавливает Xamarin Studio и подключаемый модуль Visual Studio (в Windows), а также последнюю версию платформы Android. Требуется пакет SDK Android 4.2 или более поздней версии.

Скачанный проект быстрого запуска содержит компонент мобильных служб Azure для Xamarin.Android. Несмотря на то что этот проект предназначен для Android 4.2 или более поздней версии, для пакета SDK мобильных служб требуется только Android 2.2 или более поздней версии.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a> Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service][mobile-services-create-new-service]]

## 

## <span class="short-header">Создание нового приложения</span>Создание нового приложения Xamarin.Android

</h2>
После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение Xamarin.Android, которое будет подключено к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого запуска в меню **Выбор платформы** нажмите кнопку **Xamarin.Android**, а затем разверните элемент **Создание нового приложения Android**.

    ![][1]

    После этого будут показаны три простых шага для создания приложения Xamarin.Android, подключенного к вашей мобильной службе.

    ![][2]

3.  Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.

4.  В разделе **Загрузка и запуск приложения** щелкните **Загрузить**.

    При этом будут загружены файлы проекта для примера по созданию приложения *To do list*, подключаемого к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запуск приложения для Android

Последний раздел учебника — построение и выполнение нового приложения.

1.  Перейдите в расположение, где сохранены сжатые файлы проекта и извлеките файлы на компьютере.

2.  В Xamarin Studio или Visual Studio последовательно щелкните **Файл** и **Открыть**, перейдите к несжатым файлам примеров и выберите для открытия файл **XamarinTodoQuickStart.Android.sln**.

    ![][3]

    ![][4]

3.  Нажмите кнопку **Запуск**, чтобы выполнить сборку проекта, и после этого запустите приложение. Вам будет предложено выбрать эмулятор или подключенное USB-устройство.

    <div class="dev-callout"><strong>Примечание.</strong> <p>Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания этих устройств и управления ими используйте диспетчер AVD.</p></div>

4.  В приложении введите содержательный текст, например *Завершение работы с учебником*, и нажмите кнопку **Добавить**.

    ![][5]

    Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

    > [WACOM.NOTE]
    > Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле C# ToDoActivity.cs.

5.  На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

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
  [Просмотр учебника]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services
  []: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
  [Xamarin.Android]: http://xamarin.com/download
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A9C9624B5
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
  [2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
  [3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
  [4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
  [5]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
  [6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
  [7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [Приступая к работе с синхронизацией автономных данных]: /ru-ru/documentation/articles/mobile-services-xamarin-android-get-started-offline-data
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android
