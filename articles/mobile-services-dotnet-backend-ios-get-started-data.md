
<properties urlDisplayName="Get Started with Data" pageTitle="Начало работы с данными (iOS) | Центр мобильных разработок" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Добавление мобильных служб к существующему приложению

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении iOS. В этом учебнике вам предстоит загрузить приложение, хранящее данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением и просмотреть изменения, внесенные в данные во время работы приложения.

Мобильная служба, создаваемая в этом учебнике, поддерживает среду выполнения .NET в мобильной службе. Это позволит использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе. Для создания мобильной службы, которая позволяет писать серверную бизнес-логику на языке JavaScript, ознакомьтесь с разделом [Серверная версия базы данных JavaScript] этого раздела.

>[WACOM.NOTE]Этот учебник должен помочь лучше понять, как с помощью мобильных служб использовать Azure для сохранения и получения данных из приложения iOS. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами].
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения iOS]
2. [Создание мобильной службы]
3. [Локальная загрузка мобильной службы]
4. [Тестирование мобильной службы]
5. [Публикация мобильной службы в Azure]
7. [Обновление приложения для использования мобильных служб]
8. [Тестирование работы приложения с мобильными службами]

Для работы с данным учебником требуется следующее:

+ [SDK мобильных служб iOS], а также [XCode 4.5][Установка Xcode] и iOS 5.0 или более поздней версии.
+ Visual Studio 2013 (можно использовать бесплатный [экспресс-выпуск Visual Studio for Web](http://go.microsoft.com/p/?linkid=9832232)).
+ Учетная запись Microsoft Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Бесплатная пробная версия Azure</a>.

##<a name="download-app"></a>Загрузка пакета GetStartedWithData

Этот учебник основан на [приложении GetStartedWithData][GitHub], представляющим собой приложение iOS. Пользовательский интерфейс для этого приложения аналогичен приложению, созданному в кратком руководстве по мобильным службам iOS, за исключением того, что добавленные элементы хранятся локально в памяти.

1. Загрузка GetStartedWithData [образец приложения][GitHub].

2. В Xcode откройте скачанный проект и просмотрите файл TodoService.m.

   	Обратите внимание на наличие восьми комментариев **// TODO**, указывающих шаги, которые необходимо предпринять для обеспечения работы этого приложения с мобильной службой.

3. Нажмите кнопку **Выполнить** (или клавишу Command+R), чтобы перестроить проект и запустить приложение.

4. В приложении введите какой-либо текст в текстовом поле, затем щелкните кнопку **+**.

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается в списке ниже.

##<a name="create-service"></a>Создание новой мобильной службы на портале управления

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>Загрузите службу на локальный компьютер

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

##<a name="test-the-service"></a>Тестирование мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-mobile-service"></a>Публикация мобильной службы в Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="update-app"></a>Обновление приложения для использования мобильной службы для доступа к данным

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Тестирование приложения новой мобильной службы

1. В Xcode выберите эмулятор для развертывания (iPhone или iPad), нажмите кнопку **Выполнить** (или клавишу Command+R), чтобы перестроить проект и запустить приложение.

   	В результате начнет выполняться клиент мобильных служб Azure, созданный с использованием пакета SDK для iOS, который запрашивает элементы из мобильной службы.

2. Как и прежде, введите текст в текстовом поле, затем щелкните кнопку **+**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки. Каждый новый элемент todoItem будет храниться и обновляться в базе данных SQL, ранее настроенной вами для мобильной службы на портале управления Azure.

3. Можно перезапустить приложение, чтобы убедиться, что изменения были сохранены в базе данных в Azure.

	Можно также проверить базу данных с помощью портала управления Azure или обозревателя объектов SQL Server в Visual Studio. В следующих двух действиях будет использоваться [портал управления Azure] для просмотра изменений в базе данных.

4. На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.

    ![][17]

5. На портале управления выполните запрос для просмотра изменений, внесенных приложением. Этот запрос будет аналогичен следующему запросу, но используйте имя своей базы данных вместо `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][18]

На этом прохождение учебника **Начало работы с данными** завершается.

##<a name="next-steps"></a>Дальнейшие действия

В этом учебнике показаны основы включения в iOS-приложении возможностей работы с данными в мобильных службах.

Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Использование скриптов сервера в мобильных службах для проверки и изменения данных, отправленных из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Использование разбивки на страницы в запросах для управления количеством данных, обрабатываемых в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников iOS:

* [Приступая к работе с аутентификацией]
	<br/>Проверка подлинности пользователей приложения.

* [Приступая к работе с push-уведомлениями]
  <br/>Отправка очень простого push-уведомления в приложение с помощью мобильных служб.

<!-- Anchors. -->
[Загрузка проекта приложения iOS]: #download-app
[Создание мобильной службы]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Следующие шаги]:#next-steps
[Локальная загрузка мобильной службы]: #download-the-service-locally
[Тестирование мобильной службы]: #test-the-service
[Публикация мобильной службы в Azure]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-ios
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-ios
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios
[Серверная версия JavaScript]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Установить Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[Репозиторий GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784
