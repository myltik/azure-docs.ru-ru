<properties urlDisplayName="Get Started with Data" pageTitle="Начало работы с данными (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc" />

# Добавление мобильных служб к существующему приложению

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

В этом разделе показано, как использовать мобильные службы Azure в качестве серверного источника данных для приложения из Магазина Windows. В этом учебнике вам предстоит загрузить проект Visual Studio 2013 для приложения, хранящего данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением и просмотреть изменения, внесенные в данные во время работы приложения.

В этом учебнике речь идет о создании серверной мобильной службы Microsoft .NET. Серверная часть .NET позволяет использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе, и вы можете запускать и отлаживать мобильную службу на локальном компьютере. Сведения о создании мобильной службы, которая позволяет реализовать серверную бизнес-логику в JavaScript, см. в подразделе "Серверная версия JavaScript" этого раздела.

>[WACOM.NOTE]В этом разделе показано, как использовать средства Visual Studio Professional 2013 с обновлением 3 для подключения новой мобильной службы к универсальному приложению Windows. Эти же инструкции можно использовать для подключения мобильной службы к приложению Магазина Windows или Магазина Windows Phone 8.1. Сведения о подключении мобильной службы к приложению Windows Phone 8.0 или Windows Phone Silverlight 8.1 см. в разделе [Приступая к работе с данными для Windows Phone](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

> Если вы не можете выполнить обновление до Visual Studio Professional 2013 с обновлением 3 или предпочитаете вручную добавить проект мобильной службы в приложение для Магазина Windows, см. [эту версию](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data) раздела.

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Магазина Windows]
2. [Создание мобильной службы в Visual Studio]
3. [Локальное тестирование проекта мобильной службы]
4. [Обновление приложения для использования мобильной службы]
5. [Публикация мобильной службы в Azure]
6. [Тестирование приложения со службой, размещенной в Azure]
7. [Просмотр данных, хранящихся в базе данных SQL]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (с обновлением 3 или более поздней версии). <br/>Доступна бесплатная пробная версия. 

##<a name="download-app"></a>Загрузка пакета GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##<a name="create-service"></a>Создание мобильной службы в Visual Studio

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>В обозревателе решений откройте файл кода App.xaml.cs из папки проекта GetStartedWithData.Shared и обратите внимание на новое статическое поле, добавленное в класс <strong>App</strong> в блоке условной компиляции приложения для Магазина Windows, которое выглядит как в следующем примере:</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью экземпляра <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">класса MobileServiceClient</a>. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Это статическое поле доступно для всех страниц в приложении.</p>
</li>
<li><p>Щелкните проект приложения Windows Phone правой кнопкой мыши, выберите пункт <strong>Добавить</strong>, щелкните <strong>Подключенная служба...</strong>, выберите только что созданную мобильную службу и нажмите кнопку <strong>OK</strong>. </p>
<p>Этот же код будет добавлен в общий файл App.xaml.cs, но на этот раз - в блок условной компиляции приложения Windows Phone.</p></li>
</ol>

Теперь приложение для Магазина Windows и приложение для Магазина Windows Phone подключены к новой мобильной службе. Следующий шаг - тестирование нового проекта мобильной службы.


##<a name="test-the-service-locally"></a>Локальное тестирование проекта мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##<a name="update-app"></a>Обновление приложения для использования мобильной службы

В этом разделе вы обновите универсальное приложение Windows, чтобы использовать мобильную службу в качестве серверной службы для приложения. Изменения необходимо внести только в файл проекта MainPage.xaml.cs в папке проекта GetStartedWithData.Shared. 

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]


##<a name="publish-mobile-service"></a>Публикация мобильной службы в Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Тестирование мобильной службы, размещенной в Azure

Теперь мы можем протестировать обе версии универсального приложения Windows с мобильной службой, размещенной в Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

##<a name="view-stored-data"></a>Просмотр данных, хранящихся в базе данных SQL

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
  <br/>Использование мобильных служб с .NET.
  
<!-- Anchors. -->

[Загрузка проекта приложения для Магазина Windows ]: #download-app
[Создание новой мобильной службы в Visual Studio]: #create-service
[Локальное тестирование проекта мобильной службы]: #test-the-service-locally
[Обновление приложения для использования мобильной службы]: #update-app
[Тестирование приложения со службой, размещенной локально]: #test-locally-hosted
[Публикация мобильной службы в Azure]: #publish-mobile-service
[Тестирование приложения со службой, размещенной в Azure]: #test-azure-hosted
[Просмотр данных, хранящихся в базе данных SQL]: #view-stored-data
[Следующие шаги]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Уточнение запросов посредством разбиения на страницы]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Приступая к проверке подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/

[Приступая к работе с автономными данными синхронизацией]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Сайт с образцами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Концептуальный справочник по мобильным службам на платформе .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[Класс MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 
