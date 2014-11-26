<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Приступая к работе с данными в мобильных службах

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS" class="current">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="Серверная часть .NET" class="current">Серверная часть .NET</a> | <a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-ios/"  title="Серверная часть JavaScript">Серверная часть JavaScript</a></div>

В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении iOS. В этом учебнике вам предстоит загрузить приложение, хранящее данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением и просмотреть изменения, внесенные в данные во время работы приложения.

Создаваемая в этом учебнике мобильная служба будет поддерживать среду выполнения .NET в компоненте мобильных служб. Это позволит использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript][Серверная версия JavaScript] этого раздела.

<div class="dev-callout"><b>Примечание.</b>
<p>Для работы с этим учебником требуется Visual Studio 2013.</p>
</div>

<div class="dev-callout"><b>Примечание.</b>
<p>Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Azure для хранения и извлечения данных из приложения iOS. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="/ru-ru/develop/mobile/tutorials/get-started-ios">Приступая к работе с мобильными службами</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1.  [Загрузка проекта приложения iOS][Загрузка проекта приложения iOS]
2.  [Создание мобильной службы][Создание мобильной службы]
3.  [Локальная загрузка мобильной службы][Локальная загрузка мобильной службы]
4.  [Тестирование мобильной службы][Тестирование мобильной службы]
5.  [Публикация мобильной службы в Azure][Публикация мобильной службы в Azure]
6.  [Обновление приложения для использования мобильных служб][Обновление приложения для использования мобильных служб]
7.  [Тестирование работы приложения с мобильными службами][Тестирование работы приложения с мобильными службами]

Для работы с данным учебником требуется следующее:

-   [Пакет Mobile Services iOS SDK][Пакет Mobile Services iOS SDK], [XCode 4.5][XCode 4.5] и iOS 5.0 или более поздней версии.
-   Visual Studio 2013 (можно использовать бесплатный [экспресс-выпуск Visual Studio for Web][экспресс-выпуск Visual Studio for Web]).
-   Учетная запись Microsoft Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].

## <a name="download-app"></a>Загрузите проект GetStartedWithData

Этот учебник основан на приложении [GetStartedWithData][GetStartedWithData], которое является приложением iOS. Пользовательский интерфейс приложения совпадает с интерфейсом приложения, созданного кратким руководством по мобильным службам iOS. Отличие заключается в том, что добавленные элементы хранятся локально в памяти.

1.  Загрузите [образец приложения][GetStartedWithData] GetStartedWithData.

2.  В Xcode откройте загруженный проект и просмотрите файл TodoService.m.

    Обратите внимание на восемь комментариев **/ / TODO::**, указывающих на действия, которые необходимо выполнить, чтобы приложение работало с вашей мобильной службой.

3.  Нажмите кнопку **Выполнить** (или сочетание клавиш Command + R) для повторного построения проекта и запуска приложения.

4.  В приложении введите какой-либо текст в текстовом поле, а затем нажмите кнопку **+**.

    ![][0]

    Обратите внимание, что сохраненный текст отображается в списке ниже.

## <a name="create-service"></a>Создайте новую мобильную службу на портале управления

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a>Загрузите мобильную службу на локальный компьютер

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

## <a name="test-the-service"></a> Тестирование мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <a name="publish-mobile-service"></a>Опубликуйте мобильную службу в Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="update-app"></a>Обновите приложение для использования мобильной службы для доступа к данным

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

## <a name="test-app"></a>Протестируйте приложение с новой мобильной службой

1.  В Xcode выберите эмулятор для развертывания (iPhone или iPad), нажмите кнопку **Пуск** (или клавишу Command+R), чтобы заново собрать проект и запустить приложение.

    При этом будет запущен клиент мобильных служб Azure, созданный с использованием iOS SDK, который запрашивает элементы из вашей мобильной службы.

2.  Как и ранее, введите текст в текстовом поле и нажмите кнопку **+**.

    В результате в мобильную службу будет отправлен новый элемент в качестве вставки. Каждый новый элемент todoItem будет храниться и обновляться в базе данных SQL, ранее настроенной вами для мобильной службы на портале управления Azure.

3.  Можно перезапустить приложение, чтобы убедиться, что изменения были сохранены в базе данных в Azure.

    Можно также проверить базу данных с помощью портала управления Azure или обозревателя объектов SQL Server в Visual Studio. В следующих двух действиях будет использоваться [Портал управления Azure][Портал управления Azure] для просмотра изменений в базе данных.

4.  На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.

    ![][1]

5.  На портале управления выполните запрос для просмотра изменений, внесенных приложением. Ваш запрос будет аналогичен показанному ниже, но вместо `todolist` укажите имя вашей базы данных.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике показаны основы включения в iOS-приложении возможностей работы с данными в мобильных службах.

Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

-   [Проверка и изменение данных с помощью скриптов][Проверка и изменение данных с помощью скриптов]
    Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

-   [Уточнение запросов посредством разбиения по страницам][Уточнение запросов посредством разбиения по страницам]
    Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемых в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников iOS:

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    Дополнительные сведения о проверке подлинности пользователей приложения.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    Сведения об отправке в приложение простейших push-уведомлений с помощью мобильных служб.





  [Серверная версия JavaScript]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
  [Загрузка проекта приложения iOS]: #download-app
  [Создание мобильной службы]: #create-service
  [Локальная загрузка мобильной службы]: #download-the-service-locally
  [Тестирование мобильной службы]: #test-the-service
  [Публикация мобильной службы в Azure]: #publish-mobile-service
  [Обновление приложения для использования мобильных служб]: #update-app
  [Тестирование работы приложения с мобильными службами]: #test-app
  [Пакет Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [экспресс-выпуск Visual Studio for Web]: http://go.microsoft.com/p/?linkid=9832232
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  [0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
  [Портал управления Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png
  [Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-ios
  [Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios
