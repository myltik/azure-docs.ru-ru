<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить облачную серверную службу в приложение Android с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение <em>To do list</em>, хранящее данные приложения в новой мобильной службе.</p>
<p>Снимок экрана завершенного приложения приведен ниже:</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a><span class="time">7:26:00</span></div>

</div>

![][]

Чтобы изучить этот учебник, необходимы [средства разработчика Android][средства разработчика Android], что включает интегрированную среду разработки Eclipse (IDE), подключаемый модуль средств разработки Android (ADT) и последнюю версию платформы Android. Требуется Android 4.2 или более поздняя версия.

Загруженный проект быстрого запуска содержит пакет SDK мобильных служб для платформы Android. Хотя для этого проекта требуется Android 4.2 или более поздней версии, для пакета SDK мобильных служб требуется только Android 2.2 или более поздней версии.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a> Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service][mobile-services-create-new-service]]

## 

## <span class="short-header">Создание нового приложения</span>Создание нового приложения для Android

</h2>
После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение на платформе Android, которое будет подключено к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого запуска нажмите кнопку **Android** в разделе **Выбор платформы**, а затем разверните раздел **Создать новое приложение на Android**.

    ![][1]

    Здесь показаны три простых шага для создания приложения на платформе Android, подключенного к вашей мобильной службе.

    ![][2]

3.  Если вы еще не сделали этого, загрузите и установите [Средств разработчика Android][средства разработчика Android] на ваш локальный компьютер или виртуальную машину.

4.  Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.

5.  В разделе **Загрузка и запуск приложения** щелкните **Загрузить**.

    При этом будут загружены файлы проекта для примера по созданию приложения *To do list*, подключаемого к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запуск приложения для Android

Последний раздел учебника — построение и выполнение нового приложения.

1.  Перейдите в расположение, где сохранены сжатые файлы проекта и извлеките файлы на компьютере.

2.  В Eclipse нажмите кнопку **Файл**, затем щелкните **Импорт**, разверните **Android**, щелкните пункт **Существующий код Android в рабочую область**, а затем нажмите кнопку **Далее.**

    ![][3]

3.  Нажмите кнопку **Обзор**, перейдите в расположение развернутых файлов проекта, нажмите кнопку **ОК**, убедитесь, что отмечен проект TodoActivity, а затем нажмите кнопку **Готово**.

    ![][4]

    При этом файлы проекта импортируются в текущую рабочую область.

    ![][5]

4.  Для запуска проекта в эмуляторе Android в меню **Запуск** щелкните **Выполнить**.

    <div class="dev-callout"><strong>Примечание.</strong> <p>Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания этих устройств и управления ими используйте диспетчер AVD.</p></div>

5.  В приложении введите содержательный текст, например *Завершение работы с учебником*, и нажмите кнопку **Добавить**.

    ![][6]

    Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

    <div class="dev-callout"><strong>Примечание.</strong> 
<p>Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле ToDoActivity.java.</p>
</div>

6.  На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

    ![][7]

    Это позволяет просматривать данные, добавленные в таблицу приложением.

    ![][8]

## <a name="next-steps"> </a> Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   [Приступая к работе с данными][Приступая к работе с данными]
   
    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
   
    Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
   
    Сведения об отправке в приложение простейших push-уведомлений.



  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Просмотр учебника]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services
  []: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
  [средства разработчика Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
  [2]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
  [3]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
  [4]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png
  [5]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png
  [6]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
  [7]: ./media/mobile-services-android-get-started/mobile-data-tab.png
  [8]: ./media/mobile-services-android-get-started/mobile-data-browse.png
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-android-get-started-data/
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-android-get-started-users/
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
