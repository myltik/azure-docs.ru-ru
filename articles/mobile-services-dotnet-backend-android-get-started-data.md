<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Приступая к работе с данными в мобильных службах

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Магазин Windows C#">Магазин Windows C#</a>
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS">iOS</a>
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Сервер .NET" class="current">Сервер .NET</a> | 
    <a href="/en-us/develop/mobile/tutorials/get-started-with-data-android/"  title="Сервер JavaScript">Сервер JavaScript</a>
</div>

В этом разделе показывается, как использовать мобильные службы Azure в качестве серверного источника данных для приложения Android. В этом учебнике вы будете создавать новую мобильную службу, загружать проект Eclipse Android для приложения, которое хранит данные в памяти, интегрировать мобильную службу с приложением и просматривать изменения, внесенные в данные во время работы приложения.

Создаваемая в этом учебнике мобильная служба будет поддерживать среду выполнения .NET в компоненте мобильных служб. Это позволит использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript][Серверная версия JavaScript] этого раздела.

<div class="dev-callout"><b>Примечание.</b>
<p>Для работы с этим учебником требуется Visual Studio 2013.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1.  [Создание мобильной службы][Создание мобильной службы]
2.  [Локальная загрузка мобильной службы][Локальная загрузка мобильной службы]
3.  [Тестирование мобильной службы][Тестирование мобильной службы]
4.  [Публикация мобильной службы в Azure][Публикация мобильной службы в Azure]
5.  [Загрузка пакета GetStartedWithData][Загрузка пакета GetStartedWithData]
6.  [Обновление приложения для использования мобильной службы для доступа к данным][Обновление приложения для использования мобильной службы для доступа к данным]
7.  [Тестирование приложения с помощью опубликованной мобильной службы][Тестирование приложения с помощью опубликованной мобильной службы]

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-service"></a><span class="short-header">Создание новой мобильной службы</span>Создание новой мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## <a name="download-the-service"></a><span class="short-header"> Загрузка службы</span>Загрузка службы на локальный компьютер

[WACOM.INCLUDE [mobile-services-download-service-locally][mobile-services-download-service-locally]]

## <a name="test-the-service"></a><span class="short-header">Тестирование службы</span>Тестирование мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## <a name="publish-the-service"></a><span class="short-header">Публикация службы</span>Публикация мобильной службы в Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## <a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData

### Получение кода примера

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][1]]

### Проверка версии Android SDK

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version][mobile-services-verify-android-sdk-version]]

### Проверка и запуск примера кода

[WACOM.INCLUDE [mobile-services-android-run-sample-code][mobile-services-android-run-sample-code]]

## <a name="update-app"></a><span class="short-header">Обновление приложения</span>Обновление приложения для использования мобильной службы для доступа к данным

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data][mobile-services-android-getting-started-with-data]]

## <a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с опубликованной мобильной службой

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, можно протестировать работу приложения с мобильными службами, используя либо эмулятор Android, либо телефон Android.

1.  В меню **Выполнить** выберите **Выполнить** для запуска приложения.

    Это приведет к выполнению приложения, построенного с помощью пакета Android SDK и использующего клиентскую библиотеку для отправки запроса, возвращающего элементы из вашей мобильной службы.

2.  Как и ранее, введите содержательный текст, затем нажмите **Добавить**.

    В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

    Можно перезапустить приложение, чтобы убедиться, что изменения были сохранены в базе данных в Azure. Можно также проверить базу данных с помощью портала управления Azure: в следующих двух шагах это будет делаться для просмотра изменений в базе данных.

3.  На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.

    ![][]

4.  На портале управления выполните запрос для просмотра изменений, внесенных приложением Магазина Windows. Ваш запрос будет аналогичен показанному ниже, но вместо `todolist` укажите имя вашей базы данных.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

Это заключительный шаг учебника **Приступая к работе с данными** для Android.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения возможностей работы с данными из мобильных служб в приложение Android.

<!--Next, consider completing one of the following tutorials that is based on the GetStartedWithData app that you created in this tutorial:  * [Validate and modify data with scripts]   <br/>Learn more about using server scripts in Mobile Services to validate and change data sent from your app.  * [Refine queries with paging]   <br/>Learn how to use paging in queries to control the amount of data handled in a single request.  Once you have completed the data series, try -->

Попробуйте один из следующих учебников:

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    Дополнительные сведения о проверке подлинности пользователей приложения.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    Сведения об отправке в приложение простейших push-уведомлений.

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]
    Дополнительные сведения об использовании мобильных служб в .NET.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Магазин Windows C\#]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ "Магазин Windows C#"
  [Магазин Windows JavaScript]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ "Магазин Windows JavaScript"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/ "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ "iOS"
  [Android]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ "Android"
  [Сервер .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ "Сервер .NET"
  [Сервер JavaScript]: /en-us/develop/mobile/tutorials/get-started-with-data-android/ "Сервер JavaScript"
  [Серверная версия JavaScript]: /en-us/develop/mobile/tutorials/get-started-with-data-android
  [Создание мобильной службы]: #create-service
  [Локальная загрузка мобильной службы]: #download-the-service-locally
  [Тестирование мобильной службы]: #test-the-service
  [Публикация мобильной службы в Azure]: #publish-mobile-service
  [Загрузка пакета GetStartedWithData]: #download-app
  [Обновление приложения для использования мобильной службы для доступа к данным]: #update-app
  [Тестирование приложения с помощью опубликованной мобильной службы]: #test-app
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [mobile-services-download-service-locally]: ../includes/mobile-services-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [1]: ../includes/download-android-sample-code.md
  [mobile-services-verify-android-sdk-version]: ../includes/mobile-services-verify-android-sdk-version.md
  [mobile-services-android-run-sample-code]: ../includes/mobile-services-android-run-sample-code.md
  [mobile-services-android-getting-started-with-data]: ../includes/mobile-services-android-getting-started-with-data.md
  []: ./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png
  [Приступая к работе с проверкой подлинности]: /en-us/develop/mobile/tutorials/get-started-with-users-android
  [Приступая к работе с push-уведомлениями]: /en-us/develop/mobile/tutorials/get-started-with-push-android
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
