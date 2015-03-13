<properties 
	pageTitle="Начало работы с данными (Android) | Центр мобильных разработок" 
	description="Узнайте, как приступить к работе с мобильными службами, чтобы использовать данные в приложении Android." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="Mobile-Android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="ricksal"/>

# Добавление мобильных служб к существующему приложению

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

В этом разделе показывается, как использовать мобильные службы Azure в качестве серверного источника данных для приложения Android. В этом учебнике вы будете создавать новую мобильную службу, загружать проект Eclipse Android для приложения, которое хранит данные в памяти, интегрировать мобильную службу с приложением и просматривать изменения, внесенные в данные во время работы приложения.

Мобильная служба, создаваемая в этом учебнике, поддерживает среду выполнения .NET в мобильной службе. Это позволит использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript] этого раздела.

> [AZURE.IMPORTANT] Для работы с этим учебником требуется Visual Studio 2013.

В этом учебнике рассматриваются следующие основные действия:


1. [Создание мобильной службы]
2. [Локальная загрузка мобильной службы]
3. [Тестирование мобильной службы]
4. [Публикация мобильной службы в Azure]
5. [Загрузка проекта GetStartedWithData]
4. [Обновление приложения для доступа к данным с помощью мобильных служб]
5. [Тестирование приложения с помощью опубликованной мобильной службы]


> [AZURE.IMPORTANT] Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F"%20target="_blank). 


<h2><a name="create-service"></a>Создание мобильной службы</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]


<h2><a name="download-the-service"></a>Загрузка службы на локальный компьютер</h2>

[AZURE.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>Тестирование мобильной службы</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

<h2><a name="publish-the-service"></a>Публикация мобильной службы в Azure</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<h2><a name="download-app"></a>Загрузка проекта GetStartedWithData</h2>

###Получение кода примера

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/download-android-sample-code.md)]

###Проверка версии Android SDK

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]


###Проверка и запуск примера кода

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

<h2><a name="update-app"></a>Обновление приложения для доступа к данным с помощью мобильных служб</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]

<h2><a name="test-app"></a>Тестирование приложения с помощью опубликованной мобильной службы</h2>


Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, можно протестировать работу приложения с мобильными службами, используя либо эмулятор Android, либо телефон Android.

1. В меню **Запуск** выберите **Запуск**, чтобы проект начал работу.

	Это приведет к выполнению приложения, построенного с помощью пакета Android SDK и использующего клиентскую библиотеку для отправки запроса, возвращающего элементы из вашей мобильной службы.

5. Как и ранее, введите содержательный текст, затем нажмите **Добавить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

    Можно перезапустить приложение, чтобы убедиться, что изменения были сохранены в базе данных в Azure. Можно также проверить базу данных с помощью портала управления Azure:  в следующих двух шагах это будет делаться для просмотра изменений в базе данных.


4. На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png)

5. На портале управления выполните запрос для просмотра изменений, внесенных приложением Магазина Windows. Ваш запрос будет аналогичен показанному ниже, но вместо `todolist` укажите имя вашей базы данных.

        SELECT * FROM [todolist].[todoitems]	

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png)

Это заключительный шаг учебника **Приступая к работе с данными** для Android.



## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения возможностей работы с данными из мобильных служб в приложение Android. 

<!--Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов c разбиением по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения ряда данных попробуйте
-->

Попробуйте один из следующих учебников:

* [Приступая к работе с проверкой подлинности]
  <br/>Сведения о выполнении аутентификации учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник по принципам использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с .NET.
  
<!-- Anchors. -->

[Создание мобильной службы]: #create-service
[Локальная загрузка мобильной службы]: #download-the-service-locally
[Тестирование мобильной службы]: #test-the-service
[Загрузка проекта GetStartedWithData]: #download-app
[Обновление приложения для доступа к данным с помощью мобильных служб]: #update-app
[Тестирование приложения Android с помощью службы, размещенной локально]: #test-locally-hosted
[Публикация мобильной службы в Azure]: #publish-mobile-service
[Тестирование приложения Android с помощью службы, размещенной в Azure]: #test-azure-hosted
[Тестирование приложения с помощью опубликованной мобильной службы]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-ite
	ms.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js
[Серверная версия JavaScript]: /ru-ru/develop/mobile/tutorials/get-started-with-data-android

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Справочник по принципам использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
[Класс MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
[Справочник по принципам использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  

<!--HONumber=42-->
