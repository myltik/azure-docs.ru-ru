<properties linkid="develop-mobile-tutorials-get-started-with-data-android" urlDisplayName="Get Started with Data - Android" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="Azure android data, Azure mobile services data, windows droid, windows android, microsoft droid, microsoft android" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal,glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/29/2014" ms.author="ricksal,glenga"></tags>

# Приступая к работе с данными в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-data][mobile-services-selector-get-started-data]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении для Android. В этом учебнике предстоит загрузить проект для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.</p>

</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a><span class="time">15:32:00</span></div>

</div>

<div class="dev-callout"><b>Примечание.</b>
<p>Этот учебник поможет лучше понять, как с помощью мобильных служб можно использовать Azure для хранения данных и их извлечения из приложения для Android. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="/ru-ru/develop/mobile/tutorials/get-started-android">Приступая к работе с мобильными службами</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1.  [Загрузка проекта приложения Android][Загрузка проекта приложения Android]
2.  [Создание мобильной службы][Создание мобильной службы]
3.  [Добавление таблицы для хранения данных][Добавление таблицы для хранения данных]
4.  [Обновление приложения для использования мобильных служб][Обновление приложения для использования мобильных служб]
5.  [Тестирование работы приложения с мобильными службами][Тестирование работы приложения с мобильными службами]

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

Для работы с этим учебником требуется пакет [Android SDK для мобильных служб][Android SDK для мобильных служб]; пакет [Android SDK][Android SDK], который включает интегрированную среду разработки Eclipse и подключаемый модуль средств разработки Android (ADT); а также Android 4.2 или более поздней версии.

<div class="dev-callout"><b>Примечание.</b>
<p>В этом учебнике приведены инструкции по установке пакета SDK для Android и пакета SDK для Android для мобильных служб. Для загружаемого проекта GetStartedWithData требуется Android 4.2 или более поздней версии. Однако для пакета SDK для мобильных служб требуется только Android 2.2 или более поздней версии.</p>
</div>

## <a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData

### Получение кода примера

[WACOM.INCLUDE [download-android-sample-code][download-android-sample-code]]

### Проверка версии Android SDK

[WACOM.INCLUDE [Проверка пакета SDK][Проверка пакета SDK]]

### Проверка и запуск примера кода

[WACOM.INCLUDE [mobile-services-android-run-sample-code][mobile-services-android-run-sample-code]]

## <a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы на портале управления

[WACOM.INCLUDE [mobile-services-create-new-service-data][mobile-services-create-new-service-data]]

## <a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу

[WACOM.INCLUDE [mobile-services-create-new-service-data-2][mobile-services-create-new-service-data-2]]

## <a name="update-app"></a><span class="short-header">Обновление приложения</span>Обновление приложения для использования мобильной службы для доступа к данным

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data][mobile-services-android-getting-started-with-data]]

## <a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с новой мобильной службой

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, можно протестировать работу приложения с мобильными службами, используя либо эмулятор Android, либо телефон Android.

1.  В меню **Выполнить** выберите **Выполнить** для запуска приложения.

    Это приведет к выполнению приложения, построенного с помощью пакета Android SDK и использующего клиентскую библиотеку для отправки запроса, возвращающего элементы из вашей мобильной службы.

2.  Как и ранее, введите содержательный текст, затем нажмите **Добавить**.

    В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3.  На [Портале управления][Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

4.  Откройте вкладку **Данные**, а затем щелкните **Обзор**.

    ![][]

    Обратите внимание, что таблица **TodoItem** теперь содержит данные с рядом значений, созданных мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника **Приступая к работе с данными** для Android.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения возможностей работы с данными из мобильных служб в приложение Android.

Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

-   [Проверка и изменение данных с помощью скриптов][Проверка и изменение данных с помощью скриптов]
   
    Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

-   [Уточнение запросов посредством разбиения по страницам][Уточнение запросов посредством разбиения по страницам]
   
    Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемых в одном запросе.

После изучения серии учебников по работе с данными ознакомьтесь с одним из следующих учебников Android:

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
   
    Дополнительные сведения о проверке подлинности пользователей приложения.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
   
    Сведения об отправке в приложение простейших push-уведомлений с помощью мобильных служб.



  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Просмотр учебника]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-android
  [Загрузка проекта приложения Android]: #download-app
  [Создание мобильной службы]: #create-service
  [Добавление таблицы для хранения данных]: #add-table
  [Обновление приложения для использования мобильных служб]: #update-app
  [Тестирование работы приложения с мобильными службами]: #test-app
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AED8DE357
  [Android SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [download-android-sample-code]: ../includes/download-android-sample-code.md
  [Проверка пакета SDK]: ../includes/mobile-services-verify-android-sdk-version.md
  [mobile-services-android-run-sample-code]: ../includes/mobile-services-android-run-sample-code.md
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [mobile-services-android-getting-started-with-data]: ../includes/mobile-services-android-getting-started-with-data.md
  [Портале управления]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
  [Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-android
  [Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android
