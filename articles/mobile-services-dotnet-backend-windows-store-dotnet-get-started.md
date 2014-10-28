<properties linkid="mobile-services-dotnet-backend-windows-store-dotnet-get-started" pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C#, VB, or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/18/2014" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

В этом учебнике показано, как добавить облачную серверную службу в универсальное приложение Windows с помощью мобильных служб Azure. Решения для универсальных приложений Windows включают проекты для приложений Магазинов Windows 8.1 и Windows Phone 8.1 и общий проект. Дополнительные сведения см. в разделе [Создание универсальных приложений Windows для Windows и Windows Phone](http://msdn.microsoft.com/ru-ru/library/windows/apps/xaml/dn609832.aspx).

В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Информацию о создании мобильной службы, которая позволяет создавать в JavaScript серверную бизнес-логику, см. в соответствующем подразделе этого раздела о серверной части JavaScript.

> В этом разделе показано, как создать новый проект мобильной службы и приложение Магазина Windows с помощью портала управления Azure. Используя Visual Studio 2013 с обновлением 3, можно также добавить новый проект мобильной службы в существующее решение Visual Studio. Дополнительные сведения см. на странице [Краткое руководство: добавление мобильной службы (серверной части .NET)](http://msdn.microsoft.com/ru-ru/library/windows/apps/dn629482.aspx).

> Чтобы добавить мобильную службу в проект приложения для Windows Phone 8.0 или Windows Phone Silverlight 8.1, см. раздел [Приступая к работе с данными для Windows Phone](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Для работы с этим учебником требуется:

-   Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
-   <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Доступна бесплатная пробная версия.

## Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Создание нового универсального приложения для Windows

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение для магазина Windows, подключаемое к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого начала работы щелкните **Windows** в разделе **Выбор платформы**, а затем разверните узел **Создание нового приложения для магазина Windows**.

    ![][6]

    Здесь показаны три простых шага по созданию приложения для магазина Windows, подключаемого к вашей мобильной службе.

    ![][7]

3.  Если вы еще не сделали этого, скачайте и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальном компьютере или виртуальной машине.

4.  В разделе **Загрузите и локально запустите ваше приложение и службу** выберите язык приложения для Магазина Windows, затем щелкните **Загрузить**.

    При этом загружается решение, содержащее проекты для мобильной службы и примера приложения *To do list*, которое подключается к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Тестирование приложения с помощью локальной мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

> [WACOM.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле MainPage.xaml.cs.

## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="1">
<li><p>В проекте общего кода откройте файл App.xaml.cs, найдите код, создающий экземпляр <a href="http://msdn.microsoft.com/ru-ru/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a> , закомментируйте код, который создает этот клиент с использованием <em>localhost</em>, и раскомментируйте код, который создает клиент с использованием URL-адреса удаленной мобильной службы. Он выглядит следующим образом:</p>

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

-   [Приступая к работе с данными]

    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с синхронизацией автономных данных]

    Узнайте, как использовать синхронизацию автономных данных, чтобы приложения были более быстрыми и надежными.

-   [Приступая к работе с аутентификацией]

    Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

-   [Приступая к работе с push-уведомлениями]

    Сведения об отправке в приложение простейших push-уведомлений.

-   [Устранение неполадок серверной части в мобильных службах .NET]

     Узнайте, как определять и исправлять неполадки, которые могут возникнуть в серверной части мобильных служб для .NET.

Дополнительные сведения об универсальных приложениях Windows см. в разделе [Поддержка нескольких клиентов из одной мобильной службы](/ru-ru/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs).



  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Создание универсальных приложений Windows для Windows и Windows Phone]: http://msdn.microsoft.com/ru-ru/library/windows/apps/xaml/dn609832.aspx
  [Краткое руководство: добавление мобильной службы (серверной части .NET)]: http://msdn.microsoft.com/ru-ru/library/windows/apps/dn629482.aspx
  [Приступая к работе с данными для Windows Phone]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [mobile-services-windows-universal-get-started]: ../includes/mobile-services-windows-universal-get-started.md
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png
  [7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png
  [mobile-services-dotnet-backend-test-local-service-dotnet]: ../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [MobileServiceClient]: http://msdn.microsoft.com/ru-ru/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [Приступая к работе с синхронизацией автономных данных]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Устранение неполадок серверной части в мобильных службах .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
  [Поддержка нескольких клиентов из одной мобильной службы]: /ru-ru/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs
