<properties
	pageTitle="Приступая к работе с данными в Android (серверная часть JavaScript) | Microsoft Azure"
	description="Узнайте, как приступить к работе с мобильными службами, чтобы использовать данные в приложении Android (серверная часть JavaScript)."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="ricksal"/>

# Добавление мобильных служб в существующее приложение Android (серверная часть JavaScript)

## Сводка

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как использовать мобильные службы Azure для добавления постоянных данных в приложение Android. В этом учебнике предстоит скачать приложение, которое хранит данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением, чтобы оно хранило и обновляло данные в мобильных службах Azure, а не на локальном компьютере, а затем воспользоваться порталом управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.</p>

</div>


<div class="dev-onpage-video-wrapper">
<a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a><span class="time">15:32:00</span></div>
</div>


<p>Этот учебник поможет лучше понять, как мобильные службы Azure могут сохранять данные приложения Android и извлекать их из него. В этом разделе рассматриваются многие действия, которые уже выполнялись в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="/ru-RU/develop/mobile/tutorials/get-started-android">Приступая к работе с мобильными службами</a>.</p>

## Предварительные требования

Для работы с этим учебником требуется:

- учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Бесплатная пробная версия Azure</a>;


- [пакет SDK для Android для мобильных служб Azure];
- <a  href="https://developer.android.com/sdk/index.html" target="_blank">интегрированная среда разработки Android Studio</a>, которая включает в себя пакет SDK для Android, а также Android 4.2 или более поздней версии. Для загружаемого проекта GetStartedWithData требуется Android 4.2 или более поздней версии. Однако для пакета SDK для мобильных служб требуется только Android 2.2 или более поздней версии.

## Пример кода

Просмотреть полный исходный код можно <a href="https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/AndroidStudio">здесь</a>.

## Загрузка проекта GetStartedWithData

###Получение кода примера

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code.md)]

### Проверка и запуск примера кода

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

## Создание мобильной службы на портале управления

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## Добавление новой таблицы в мобильную службу

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

## Обновление приложения для доступа к данным с помощью мобильной службы

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]


## Тестирование работы приложения с новой мобильной службой

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, можно протестировать работу приложения с мобильными службами, используя либо эмулятор Android, либо телефон Android.

1. В меню **Запуск** выберите **Запуск приложения**, чтобы запустить проект.

	Это приведет к выполнению приложения, построенного с помощью пакета Android SDK и использующего клиентскую библиотеку для отправки запроса, возвращающего элементы из вашей мобильной службы.

5. Как и ранее, введите содержательный текст, затем нажмите **Добавить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

4. Откройте вкладку **Данные**, а затем щелкните **Обзор**.

   	![][9]

   	Обратите внимание, что таблица **TodoItem** теперь содержит данные с рядом значений, созданных мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника **Приступая к работе с данными** для Android.

## Устранение неполадок

### Проверка версии Android SDK

[AZURE.INCLUDE [Проверка пакета SDK](../../includes/mobile-services-verify-android-sdk-version.md)]



## Дальнейшие действия

В этом учебнике показаны основы включения возможностей работы с данными из мобильных служб в приложение Android.

Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов] <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов посредством подкачки] <br/>Узнайте, как использовать подкачку в запросах для управления объемом данных, обрабатываемых в одном запросе.

После изучения серии учебников по работе с данными ознакомьтесь с одним из следующих учебников Android:

* [Приступая к работе с проверкой подлинности]<br/> Дополнительные сведения о проверке подлинности пользователей приложения.

* [Приступая к работе с push-уведомлениями] <br/>Сведения об отправке в приложение простейших push-уведомлений с помощью мобильных служб.

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Уточнение запросов посредством подкачки]: /develop/mobile/tutorials/add-paging-to-data-android
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-android
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-android
[Приступая к работе с проверкой подлинности]: /develop/mobile/tutorials/get-started-with-users-android
[Приступая к работе с push-уведомлениями]: /develop/mobile/tutorials/get-started-with-push-android

[Azure Management Portal]: https://manage.windowsazure.com/
[Портале управления]: https://manage.windowsazure.com/
[пакет SDK для Android для мобильных служб Azure]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!---HONumber=Oct15_HO3-->