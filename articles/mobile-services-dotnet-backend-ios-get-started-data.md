<properties 
	pageTitle="Начало работы с данными (iOS) | Центр мобильных разработок" 
	description="Узнайте, как приступить к работе с мобильными службами, чтобы использовать данные в приложении iOS." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Добавление мобильных служб к существующему приложению

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении iOS. В этом учебнике вам предстоит загрузить приложение, хранящее данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением и просмотреть изменения, внесенные в данные во время работы приложения.

Мобильная служба, создаваемая в этом учебнике, поддерживает среду выполнения .NET в мобильной службе. Это позволит использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript] этого раздела.

>[AZURE.NOTE]Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Azure для хранения и извлечения данных из приложения для iOS. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами].
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для iOS]
2. [Создание мобильной службы]
3. [Локальная загрузка мобильной службы]
4. [Тестирование мобильной службы]
5. [Публикация мобильной службы в Azure]
7. [Обновление приложения для использования мобильных служб]
8. [Тестирование работы приложения с мобильными службами]

Для работы с данным учебником требуется следующее:

+ [SDK мобильных служб iOS], а также [XCode 4.5][Установка Xcode] и iOS 5.0 или более поздней версии.
+ Visual Studio 2013 (можно бесплатно получить [Visual Studio Express для Web](http://go.microsoft.com/p/?linkid=9832232)).
+ Учетная запись Microsoft Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Бесплатная пробная версия Azure</a>.

##<a name="download-app"></a>Загрузка проекта GetStartedWithData

Этот учебник основан на [приложении GetStartedWithData][GitHub], представляющим собой приложение iOS. Пользовательский интерфейс приложения совпадает с интерфейсом приложения, созданного кратким руководством по мобильным службам iOS. Отличие заключается в том, что добавленные элементы хранятся локально в памяти.

1. Загрузите [пример приложения][GitHub] GetStartedWithData.

2. В Xcode откройте скачанный проект и просмотрите файл TodoService.m.

   	Обратите внимание на восемь комментариев **/ / TODO::**, указывающих на действия, которые необходимо выполнить, чтобы приложение работало с вашей мобильной службой.

3. Нажмите кнопку **Выполнить** (или сочетание клавиш Command + R) для повторного построения проекта и запуска приложения.

4. В приложении введите какой-либо текст в текстовом поле, а затем нажмите кнопку **+**.

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается в списке ниже.

##<a name="create-service"></a>Создание мобильной службы на портале управления

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>Загрузка службы на локальный компьютер

[AZURE.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

##<a name="test-the-service"></a>Тестирование мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-mobile-service"></a>Публикация мобильной службы в Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="update-app"></a>Обновление приложения для доступа к данным с помощью мобильных служб

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Тестирование работы приложения с новой мобильной службой

1. В Xcode выберите эмулятор для развертывания (iPhone или iPad) и нажмите кнопку **Запуск** (или сочетание клавиш Command+R), чтобы повторить сборку проекта и запустить приложение.

   	В результате начнет выполняться клиент мобильных служб Azure, созданный с использованием пакета SDK для iOS, который запрашивает элементы из мобильной службы.

2. Как и ранее, введите текст в текстовом поле и нажмите кнопку **+**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки. Каждый новый элемент todoItem будет храниться и обновляться в базе данных SQL, ранее настроенной вами для мобильной службы на портале управления Azure.

3. Можно перезапустить приложение, чтобы убедиться, что изменения были сохранены в базе данных в Azure.

	Можно также проверить базу данных с помощью портала управления Azure или обозревателя объектов SQL Server в Visual Studio. В следующих двух действиях будет использоваться [портал управления Azure] для просмотра изменений в базе данных.

4. На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.

    ![][17]

5. На портале управления выполните запрос для просмотра изменений, внесенных приложением. Ваш запрос будет аналогичен показанному ниже, но вместо `todolist` укажите имя вашей базы данных.

        SELECT * FROM [todolist].[todoitems]	

    ![][18]

Это заключительный шаг учебника **Приступая к работе с данными**.

##<a name="next-steps"></a>Дальнейшие действия

В этом учебнике показаны основы включения в iOS-приложении возможностей работы с данными в мобильных службах.

Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов c разбиением по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников iOS:

* [Приступая к работе с проверкой подлинности]
	<br/>Сведения о выполнении аутентификации учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями]
  <br/>Сведения об отправке в приложение простейших push-уведомлений с использованием мобильных служб.

<!-- Anchors. -->
[Загрузка проекта приложения для iOS]: #download-app
[Создание мобильной службы]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Дальнейшие действия]:#next-steps
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
[Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-ios
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-ios
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios
[Серверная версия JavaScript]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Установка Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Пакет SDK для мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[Репозиторий GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784


<!--HONumber=42-->
