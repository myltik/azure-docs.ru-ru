<properties urlDisplayName="Get Started with Data" pageTitle="Начало работы с данными (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как приступить к работе с мобильными службами, чтобы использовать данные в приложении магазина Windows." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Добавление мобильных служб к существующему приложению

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

В этом разделе показано, как использовать мобильные службы Azure в качестве серверного источника данных для приложения из Магазина Windows. В этом учебнике вам предстоит загрузить проект Visual Studio 2013 для приложения, хранящего данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением и просмотреть изменения, внесенные в данные во время работы приложения.

В этом учебнике речь идет о создании серверной мобильной службы Microsoft .NET. Серверная часть .NET позволяет использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе, и вы можете запускать и отлаживать мобильную службу на локальном компьютере. Сведения о создании мобильной службы, которая позволяет реализовать серверную бизнес-логику в JavaScript, см. в подразделе "Серверная версия JavaScript" этого раздела.

>[WACOM.NOTE]В этом разделе показано, как использовать средства Visual Studio Professional 2013 с обновлением 3 для подключения новой мобильной службы к универсальному приложению Windows. Если вы не можете выполнить обновление до Visual Studio Professional 2013 с обновлением 3 или предпочитаете вручную добавить проект мобильной службы в приложение для Магазина Windows, см. [эту версию](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data) раздела.

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Магазина Windows]
2. [Создание мобильной службы в Visual Studio]
3. [Локальное тестирование проекта мобильной службы]
4. [Обновление приложения для использования мобильной службы]
5. [Публикация мобильной службы в Azure]
6. [Тестирование приложения со службой, размещенной в Azure]
7. [Просмотр данных, хранящихся в базе данных SQL]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-javascript-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Доступна бесплатная пробная версия.

## <a name="download-app"></a>Загрузка пакета GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-javascript-download-project.md)]

## <a name="create-service"></a>Создание мобильной службы в Visual Studio

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="8"><li><p>В обозревателе решений перейдите во вложенную папку <strong>services\mobileService\scripts</strong>, откройте файл скрипта service.js и обратите внимание на новую глобальную переменную, которая выглядит, как в следующем примере:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью глобальной переменной. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Так как ссылка на этот скрипт была добавлена в файл default.html, эта переменная доступна для всех файлов скриптов, на которые также указывает ссылка на этой странице.</p>
</li>
<li><p>Откройте файл проекта default.html, найдите ссылку на новый файл скрипта service.js и убедитесь в том, что путь ссылки выглядит следующим образом:</p>
<pre><code>&lt;script src="/services/mobileServices/scripts/todolist.js"&gt;</script></code></pre>
<p>В настоящее время в Visual Studio есть ошибка, из-за которой создается путь с неправильным именем папки.</p></li>
<li><p>Щелкните проект приложения Windows Phone правой кнопкой мыши, выберите пункт <strong>Добавить</strong>, щелкните <strong>Подключенная служба...</strong>, выберите только что созданную мобильную службу и нажмите кнопку <strong>OK</strong>. </p>
<p>Тот же новый файл с кодом добавляется в проект приложения для Магазина Windows Phone. Не забудьте также исправить путь ссылки, добавленный в файл default.html.</p></li>
</ol>

Теперь приложение для Магазина Windows и приложение для Магазина Windows Phone подключены к новой мобильной службе. Следующий шаг - тестирование нового проекта мобильной службы.

## <a name="test-the-service-locally"></a>Локальное тестирование проекта мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

## <a name="update-app"></a>Обновление приложения для использования мобильной службы

В этом разделе вы обновите универсальное приложение Windows, чтобы использовать мобильную службу в качестве серверной службы для приложения. Изменения необходимо внести только в файл проекта default.js в папке проекта GetStartedWithData.Shared. 

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

## <a name="publish-mobile-service"></a>Публикация мобильной службы в Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>Тестирование мобильной службы, размещенной в Azure

Теперь мы можем протестировать обе версии универсального приложения Windows с мобильной службой, размещенной в Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

## <a name="view-stored-data"></a>Просмотр данных, хранящихся в базе данных SQL

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]

На этом прохождение учебника **Начало работы с данными** завершается.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в проекте универсального приложения Windows возможности работы с данными из мобильных служб. Далее рассмотрите выполнение одного из следующих учебников, который основан на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Использование скриптов сервера в мобильных службах для проверки и изменения данных, отправленных из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Использование разбивки на страницы в запросах для управления количеством данных, обрабатываемых в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

* [Справочник принципов использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с HTML и JavaScript.
  
<!-- Anchors. -->

[Загрузка проекта приложения для Магазина Windows]: #download-app
[Создание мобильной службы в Visual Studio]: #create-service
[Локальное тестирование проекта мобильной службы]: #test-the-service-locally
[Обновление приложения для использования мобильной службы]: #update-app
[Тестирование приложения со службой, размещенной локально]: #test-locally-hosted
[Публикация мобильной службы в Azure]: #publish-mobile-service
[Тестирование приложения со службой, размещенной в Azure]: #test-azure-hosted
[Просмотр данных, хранящихся в базе данных SQL]: #view-stored-data
[Следующие шаги]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-data-sample-download-javascript-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png

[16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
[20]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png

<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-js
[Начало работы с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Начало работы с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/


[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Сайт с образцами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library/
[Класс MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  
