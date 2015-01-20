<properties linkid="develop-mobile-tutorials-get-started-with-data-android" urlDisplayName="Get Started with Data - Android" pageTitle="Приступая к работе с данными (Android) | Центр мобильных разработок" metaKeywords="данные android в Azure, данные мобильных служб Azure, windows droid, windows android, microsoft droid, microsoft android" description="Узнайте, как приступить к работе с мобильными службами, чтобы использовать данные в приложении Android." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal,glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal,glenga" />

# Добавление мобильных служб к существующему приложению

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении для Android. В этом учебнике предстоит загрузить проект для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a><span class="time">15:32</span></div>
</div>

<div class="dev-callout"><b>Примечание.</b>
<p>Этот учебник поможет лучше понять, как с помощью мобильных служб можно использовать Azure для хранения данных и их извлечения из приложения для Android. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="/ru-ru/develop/mobile/tutorials/get-started-android">Приступая к работе с мобильными службами</a>.</p>
<p>Просмотреть исходный код завершенного приложения можно <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData">здесь</a>.
</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Android] 
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения можно найти в статье <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Бесплатное пробное использование Azure</a>.</p></div> 

Для работы с этим учебником требуется пакет [Android SDK для мобильных служб], пакет <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">Android SDK</a>, который включает интегрированную среду разработки Eclipse и подключаемый модуль средств разработки Android, а также Android 4.2 или более поздней версии. 

<div class="dev-callout"><b>Примечание.</b>
<p>В этом учебнике приведены инструкции по установке пакета SDK для Android и пакета SDK для Android для мобильных служб. Для загружаемого проекта GetStartedWithData требуется Android 4.2 или более поздней версии. Однако для пакета SDK для мобильных служб требуется только Android 2.2 или более поздней версии.</p>
</div>

>[AZURE.NOTE] В этом учебнике используется последняя версия пакета SDK для мобильных служб. Предыдущую версию этого пакета можно найти <a href="http://go.microsoft.com/fwlink/p/?LinkID=280126"> здесь</a> (если требуется обеспечить обратную совместимость). Однако код, содержащийся в этих учебных примерах, с этой версией не работает.

<h2><a name="download-app"></a>Загрузка проекта GetStartedWithData</h2>

###Получение кода примера

[WACOM.INCLUDE [download-android-sample-code](../includes/download-android-sample-code.md)]

###Проверка версии Android SDK

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]


###Проверка и запуск примера кода

[WACOM.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

<h2><a name="create-service"></a>Создание мобильной службы на портале управления</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Добавление новой таблицы в мобильную службу</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>Обновление приложения для доступа к данным с помощью мобильной службы</h2>

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]


<h2><a name="test-app"></a>Тестирование работы приложения с новой мобильной службой</h2>

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, можно протестировать работу приложения с мобильными службами, используя либо эмулятор Android, либо телефон Android.

1. В меню **Запуск** выберите **Запуск**, чтобы проект начал работу.

	Это приведет к выполнению приложения, построенного с помощью пакета Android SDK и использующего клиентскую библиотеку для отправки запроса, возвращающего элементы из вашей мобильной службы.

5. Как и ранее, введите содержательный текст, затем нажмите **Добавить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

4. Откройте вкладку **Данные**, а затем щелкните **Обзор**.

   	![][9]
  
   	Обратите внимание, что таблица **TodoItem** теперь содержит данные с рядом значений, созданных мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника **Приступая к работе с данными** для Android.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения возможностей работы с данными из мобильных служб в приложение Android. 

Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов c разбиением по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После изучения серии учебников по работе с данными ознакомьтесь с одним из следующих учебников Android:

* [Приступая к работе с проверкой подлинности] 
	<br/>Сведения о выполнении аутентификации учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений с использованием мобильных служб.

<!-- Anchors. -->
[Загрузка проекта приложения Android]: #download-app
[Создание мобильной службы]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-android
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-android
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-android
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Android SDK для мобильных служб]: http://aka.ms/Iajk6q
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!--HONumber=35.2-->
