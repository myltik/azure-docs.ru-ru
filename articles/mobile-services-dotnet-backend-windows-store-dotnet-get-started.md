<properties pageTitle="Приступая к работе с мобильными службами для приложений из Магазина Windows | Центр разработчиков для мобильных устройств" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C#, VB, or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/18/2014" ms.author="glenga" />


# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

В этом учебнике показано, как добавить облачную серверную службу в универсальное приложение Windows с помощью мобильных служб Azure. Решения для универсальных приложений Windows включают проекты для приложений Магазинов Windows 8.1 и Windows Phone 8.1 и общий проект. Дополнительные сведения см. в разделе [Создание универсальных приложений для Windows и Windows Phone](http://msdn.microsoft.com/ru-ru/library/windows/apps/xaml/dn609832.aspx).

В этом учебнике будет создана новая мобильная служба и простое приложение *To do list*, которое хранит свои данные в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Сведения о создании мобильной службы, которая позволяет реализовать серверную бизнес-логику в JavaScript, см. в подразделе "Серверная версия JavaScript" этого раздела.

>[WACOM.NOTE]В этом разделе показано, как создать новый проект мобильной службы и универсальное приложение Windows с помощью портала управления Azure. Используя Visual Studio 2013 с обновлением 3, можно также добавить новый проект мобильной службы в существующее решение Visual Studio. Дополнительные сведения см. на странице [Краткое руководство: добавление мобильной службы (серверной части .NET)](http://msdn.microsoft.com/ru-ru/library/windows/apps/dn629482.aspx).

>Чтобы добавить мобильную службу в проект приложения для Windows Phone 8.0 или Windows Phone Silverlight 8.1, см. раздел [Приступая к работе с данными для Windows Phone](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Доступна бесплатная пробная версия.

## Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Создание нового универсального приложения для Windows

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение для магазина Windows, подключаемое к вашей мобильной службе.

1. На портале управления щелкните **Мобильные службы**, затем щелкните только что созданную вами мобильную службу.
   
2. На вкладке быстрого запуска щелкните **Windows** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения для Магазина Windows**.

   	![][6]

   	Здесь показаны три простых шага по созданию приложения для магазина Windows, подключаемого к вашей мобильной службе.

  	![][7]

3. Если вы еще не сделали этого, загрузите и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальном компьютере или виртуальной машине.

4. В разделе **Загрузка и запуск приложения и службы локально** выберите язык для приложения Магазина Windows, затем щелкните **Загрузить**. 

  	При этом загружается решение, содержащее проекты для мобильной службы и примера приложения "To do list", которое подключается к вашей мобильной службе. Сохраните упакованный файл проекта на своем локальном компьютере и зафиксируйте, в каком месте он был сохранен.

## Тестирование приложения с помощью локальной мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[WACOM.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле MainPage.xaml.cs.


## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


<ol start="4">
<li><p>В проекте общего кода откройте файл App.xaml.cs, найдите код, создающий экземпляр <a href="http://msdn.microsoft.com/ru-ru/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>, закомментируйте код, который создает этот клиент с использованием <em>localhost</em>, и раскомментируйте код, который создает клиент с использованием URL-адреса удаленной мобильной службы. Он выглядит следующим образом:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXX-APPLICATION-KEY-XXXXX");</code></pre>

	<p>Теперь клиент будет обращаться к мобильной службе, опубликованной в Azure.</p></li>
</ol>

## Тестирование приложения с мобильной службой, размещенной в Azure

После того, как мобильная служба опубликована, а клиент подключен к удаленной мобильной службе, размещенной в Azure, мы можем запустить это приложение с использованием Azure для хранения элементов.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]


## Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Начало работы с автономной синхронизацией данных]
  <br/>Использование автономной синхронизации данных для повышения быстроты отклика и надежности приложения.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с помощью поставщика идентификации.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

* [Устранение неполадок сервера базы данных мобильных служб .NET]
  <br/> Диагностирование и устранение проблем, которые могут возникать при работе сервера базы данных мобильных служб .NET. 

Дополнительные сведения об универсальных приложениях Windows см. в разделе [Поддержка нескольких платформ устройств из одной мобильной службы](/ru-ru/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs).

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-startup.png



<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Начало работы с автономной синхронизацией данных]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript и HTML ]: mobile-services-win8-javascript/
[Портал управления]: https://manage.windowsazure.com/
[Серверная версия JavaScript]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started
[Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012
[Устранение неполадок сервера базы данных мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
